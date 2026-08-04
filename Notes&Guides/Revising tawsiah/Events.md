# Intro
in this section i will textually revise tawsiah project by feature 
## msg for AI
to write documents you have to follow this structre as same mentioned in getallevents

```
### {method action name}
- Description: {what this method tries to do}
- method signature:
	- type (public/private/protected)
	- returns: {what is returns}
	- parameters {what it should receive}
	  
- flow: 
  - Scenario {number of scenario of occured}: 
    {what happens until splitting of data flow to multi scenarios (like found\not found records)}
    
	    - scenario {state}{small brief description}: 
	      {rest of the flow until the end}
	      
		- scenario {state}{small brief description}: 
			      {rest of the flow until the end}
- any extra things: 
  {mention in paragraphs and bullet point what may need calrification}
    
```

# EventController

## GetAllEvents

- Description: Fetches a lightweight summary list of all events in the database. Returns an empty list if no events exist never throws an error for this case.
  
- Requiers Auth?: NO
  
- Method Signature:
    
    - Public
    - Returns: `List<EventSummaryDTO>`
    - Parameters: none
- Flow:
    
    - Scenario: Controller receives the request → asks the service for all events → service asks the database for everything in the events table
        
        - Scenario ✅ (Records found): Database returns a list of event objects → service converts each one into a lightweight summary → builds a list of summaries → returns the list to the controller → controller returns it with 200 OK.
            
        - Scenario ✅ (No records): Database returns an empty list → service returns it as-is to the controller → controller returns 200 OK with an empty body.
            
- Extra notes:
    
    **Why a summary and not full details?**
    
    > Summary carries only what a list view needs: name, city, image, rating, category. Loading every field (schedule, location, description, etc.) for hundreds of events at once is wasteful. Full details are only fetched when the user opens a specific event.
    

---

## GetEventByID

- Description: Fetches a single event's full details using its ID. Returns a detailed response (not a summary). Throws a custom exception if the ID doesn't exist in the database.
  
- Requiers Auth?: NO
    
- Method Signature:
    
    - Public
    - Returns: `EventDetailedResponseDTO`
    - Parameters: `@PathVariable Long id`
- Flow:
    
    - Scenario: Controller receives the request with an ID in the URL → asks the service to find that event → service asks the database for an event matching that ID
        
        - Scenario ✅ (ID exists): Database finds the event → service converts it into a full detailed response (all fields: location, schedule, price, description, etc.) → hands it back to the controller → controller returns it with 200 OK.
            
        - Scenario ❌ (ID not found): Database finds nothing → the Optional comes back empty → service throws an `EventNotFoundException` carrying the message "Event not found with this ID: {id}" → the global exception handler catches it and returns a 404 error response to the caller.
            
- Extra notes:
    
    **What is Optional?**
    
    > When the database is asked for an event by ID, it doesn't crash if nothing is found — it wraps the result in an Optional, which is just a container that either holds the event or holds nothing. The service then says: give me the event if it's there, otherwise throw the exception.
    
    **What is EventNotFoundException?**
    
    > A custom exception this project defined specifically for a missing event. Instead of a raw server crash, it sends a clean readable error message. The global exception handler catches it automatically and maps it to a proper 404 response.
    

---

## CreateEvent

- Description: Creates a new event record from the data sent in the request body. Validates the input before processing. Returns the newly created event in full detail. Rejects duplicate events that violate the unique constraint (same name + city + date).

- Requiers Auth?: YES
  
- Method Signature:
    
    - Public
    - Returns: `EventDetailedResponseDTO`
    - Parameters: `@RequestBody @Valid EventRequestDTO request`
- Flow:
    
    - Scenario: Controller receives the request with event data in the body → `@Valid` checks the input fields before anything else runs
        
        - Scenario ❌ (Validation fails): A required field is missing or invalid → Spring rejects the request immediately → global exception handler returns a 400 Bad Request with a list of which fields failed and why — the service is never reached.
            
        - Scenario ✅ (Validation passes, no duplicate): Controller passes the request to the service → service converts the request into an Event entity → saves it to the database → then pushes the saved event into the Redis queue so the AI layer knows a new event was added → service converts the saved event into a detailed response → controller returns it with 201 Created.
            
        - Scenario ❌ (Duplicate event): Database detects that the combination of name + city + date already exists → throws a `DataIntegrityViolationException` → controller catches it and returns 409 Conflict.
            
- Extra notes:
    
    **What is the Redis queue here?**
    
    > After saving to the database, the service also pushes the new event into a Redis list called `event_sync_queue`. This is how the AI recommendation layer (FastAPI + Qdrant) stays in sync — it reads from that queue and adds the new event to its own vector store. If Redis is down, a warning is printed and the save still succeeds — Redis failure does not block the creation.
    
    **What is `@Valid`?**
    
    > An annotation that tells Spring: before this method runs, check that all the fields in the incoming request follow their validation rules (not null, not blank, etc.). If anything fails, Spring rejects the request before it even reaches the service.
    
    **What is `DataIntegrityViolationException`?**
    
    > A Spring exception that fires when the database rejects a save due to a constraint violation — in this case the unique constraint on name + city + date. The controller catches it and converts it to a 409 Conflict response.
    

---

## UpdateEvent

- Description: Updates an existing event's fields using its ID. Overwrites all editable fields with whatever is sent in the request body. Throws an exception if the event ID doesn't exist.

- Requiers Auth?: YES

- Method Signature:
    
    - Public
    - Returns: `EventDetailedResponseDTO`
    - Parameters: `@PathVariable Long id`, `@RequestBody @Valid EventUpdateDTO update`
- Flow:
    
    - Scenario: Controller receives the request with an ID in the URL and updated data in the body → `@Valid` checks the input fields
        
        - Scenario ❌ (Validation fails): Invalid or missing fields → Spring rejects immediately → 400 Bad Request returned, service never reached.
            
        - Scenario ❌ (ID not found): Service asks the database for the event by ID → Optional comes back empty → throws `EventNotFoundException` → global handler returns 404.
            
        - Scenario ✅ (ID exists, validation passes): Database returns the existing event → service overwrites its fields one by one with the new values from the update request → saves it back to the database → pushes the updated event into the Redis queue so the AI layer gets the latest version → converts the updated event into a detailed response → controller returns it with 200 OK.
            
- Extra notes:
    
    **This is a full overwrite, not a partial update.**
    
    > Every editable field (name, description, category, city, price, imageUrl, location, schedule) gets replaced with whatever was sent. If the caller doesn't send a value for one of them, it will be set to null. There is no partial update logic here — that would require a PATCH method.
    
    **Why push to Redis again after update?**
    
    > The AI recommendation layer stores its own copy of events. If an event changes in the database but the AI doesn't know, it would keep recommending stale data. Pushing to the queue tells the AI: this event changed, update your records.
    

---

## DeleteEvent

- Description: Deletes an event by its ID. Returns the deleted event's details in the response before it's gone. Throws an exception if the ID doesn't exist.

- Requiers Auth?: YES 
  
- Method Signature:
    
    - Public
    - Returns: `EventDetailedResponseDTO`
    - Parameters: `@PathVariable Long id`
- Flow:
    
    - Scenario: Controller receives the request with an ID in the URL → asks the service to delete that event
        
        - Scenario ❌ (ID not found): Service asks the database for the event → Optional comes back empty → throws `EventNotFoundException` → global handler returns 404.
            
        - Scenario ✅ (ID exists): Database returns the event → service holds onto it in memory → deletes it from the database by ID → pushes a delete signal string `"DELETE_EVENT:{id}"` into the Redis queue so the AI layer knows to remove it → converts the now-deleted event into a detailed response → controller returns it with 204 No Content.
            
- Extra notes:
    
    **Why return the deleted event's data?**
    
    > The service fetches and converts the event before deleting it. This lets the caller see exactly what was removed — useful for confirmation in the UI or for logging purposes.
    
    **What is the delete signal in Redis?**
    
    > Instead of pushing a full event object, this time the service pushes a plain string: `"DELETE_EVENT:{id}"`. The AI layer reads this from the queue and knows to remove that event from its vector store. This keeps the AI's data in sync with the database on deletions.
    
    **204 No Content**
    
    > Technically a 204 means "success, but no body". However this controller returns the deleted event object with it — that's a slight inconsistency in the code. Conventionally 204 should have no body, but it works in practice.
    

---

## FilterEvents


- Description: Returns a filtered list of events based on optional query parameters. Any combination of filters can be used — filters that are not provided are simply ignored. Returns summaries, not full details.

- Requiers Auth?: NO

- Method Signature:
    
    - Public
    - Returns: `List<EventSummaryDTO>`
    - Parameters: `@RequestParam(required = false) String category`, `String city`, `Double minRating`, `String search`
- Flow:
    
    - Scenario: Controller receives the request with any combination of query params (all optional) → passes them all to the service → service passes them to `EventSpecification.buildSpec()` which builds a dynamic database query using only the filters that were actually provided → database runs the query and returns matching events → service converts each to a summary → controller returns the list with 200 OK.
        
        - Scenario ✅ (Some filters provided): Only the provided filters are applied to the query. For example: city=Jeddah + minRating=4.0 → database returns only Jeddah events rated 4.0 or above.
            
        - Scenario ✅ (No filters provided): All parameters are null → `buildSpec()` produces no conditions → query returns all events → same as GetAllEvents in result.
            
        - Scenario ✅ (No matches): Filters are valid but no events match → database returns empty list → controller returns 200 OK with empty body.
            
- Extra notes:

	**Reason of using dynamic quieries?**
	
	using this feature makes queries dynamic, means instead of writing 10+ custom static quieries in eventRepo for each filter, i will just use this feature and it will make me a dynamic query based on user's filter needs 
    
    **What is `buildSpec()` doing exactly?**
    
    > It builds the SQL WHERE clause at runtime based on what was sent. Each filter is a separate condition — if its value is null or empty, that condition is skipped (treated as always-true). The final query only contains the conditions for filters the user actually provided.
    
    **What is `@RequestParam(required = false)`?**
    
    > Tells Spring that this query parameter is optional. If the caller doesn't include it in the URL, Spring sets it to null instead of rejecting the request.
    
    **`search` filter is special — it searches across 4 fields at once.**
    
    > Unlike the other filters which target a single column, the search parameter checks if the keyword appears in the name, description, city, or category of any event. So searching "park" could match an event named "Park Festival" or one described as "held in a city park".