## PlaceController

### GetAllPlaces

- Description: Fetches a lightweight summary list of all places in the database. Returns an empty list if no places exist.

- Requiers Auth?: NO
  
- method signature:
    
    - type: public
        
    - returns: `List<PlaceSummaryDTO>`
        
    - parameters: none
        
- flow:
    
    - Scenario: Controller receives the request → asks the service for all places → service asks the database for everything in the places table
        
        
        - scenario ✅ (Records found): Database returns a list of place objects → service converts each one into a lightweight summary DTO → builds a list of summaries → returns the list to the controller → controller returns it with 200 OK.
        
        - scenario ✅ (No records): Database returns an empty list → service returns it as-is to the controller → controller returns 200 OK with an empty body.
        
        
- any extra things:
    
    - Why a summary and not full details?
        
        Summary carries only what a list view needs like name, city, image, and category to save bandwidth. Full details are only fetched when the user opens a specific place.
        

### GetPlaceByID

- Description: Fetches a single place's full details using its unique ID. Returns a detailed response. Throws a custom exception if the ID does not exist in the database.

- Requiers Auth?: NO    
  
- method signature:
    
    - type: public
        
    - returns: `PlaceDetailedResponseDTO`
        
    - parameters: `@PathVariable Long id`
        
- flow:
    
    - Scenario: Controller receives the request with an ID in the URL → asks the service to find that place → service asks the database for a place matching that ID
        
        
        - scenario ✅ (ID exists): Database finds the place → service converts it into a full detailed response DTO containing all fields → hands it back to the controller → controller returns it with 200 OK.
        
        - scenario ❌ (ID not found): Database finds nothing → the Optional container comes back empty → service throws a `PlaceNotFoundException` carrying a clear error message → the global exception handler catches it and returns a 404 Not Found error response.
        
        
- any extra things:
    
    - What is the reason for using Optional?
        
        The database result is wrapped inside an Optional container to prevent raw application crashes when an invalid ID is requested.
        
    - What is PlaceNotFoundException?
        
        A custom exception that ensures the API client receives a structured, predictable 404 error contract instead of an unhandled internal server error.
        

### CreatePlace

- Description: Creates a new place record from the data sent in the request body. Validates the input before processing. Returns the newly created place in full detail. Rejects duplicate places that violate unique constraints.

- Requiers Auth?: YES

- method signature:
    
    - type: public
        
    - returns: `PlaceDetailedResponseDTO`
        
    - parameters: `@RequestBody @Valid PlaceRequestDTO request`
        
- flow:
    
    - Scenario: Controller receives the request with place data in the body → `@Valid` checks the input fields before anything else runs
        
        
        - scenario ❌ (Validation fails): A required field is missing or invalid → Spring rejects the request immediately → global exception handler returns a 400 Bad Request with validation errors, and the service layer is never reached.
        
        - scenario ✅ (Validation passes, no duplicate): Controller passes request to service → service converts request into a Place entity → saves it to the database → pushes the saved place into the Redis queue for the AI layer → service converts the saved place into a detailed response DTO → controller returns it with 201 Created.
        
        - scenario ❌ (Duplicate place): Database detects that the unique constraints are violated → throws a `DataIntegrityViolationException` → controller catches it and returns a 409 Conflict response.
        
        
- any extra things:
    
    - What is the purpose of the Redis queue here?
        
        The service pushes the new place into a Redis sync queue so the AI recommendation layer can read it and update its vector store database.
        
    - What does @Valid do?
        
        It enforces constraints on the DTO fields before the controller method executes, blocking invalid data early.
        

### UpdatePlace

- Description: Updates an existing place's fields using its ID. Overwrites all editable fields with whatever is sent in the request body. Throws an exception if the place ID doesn't exist.

- Requiers Auth?: YES
    
- method signature:
    
    - type: public
    - returns: `PlaceDetailedResponseDTO`
    - parameters: `@PathVariable Long id`, `@RequestBody @Valid PlaceUpdateDTO update`
        
- flow:
    
    - Scenario: Controller receives the request with an ID in the URL and updated data in the body → `@Valid` checks the input fields
        
        
        - scenario ❌ (Validation fails): Invalid or missing fields → Spring rejects immediately → 400 Bad Request is returned and the service is never reached.
        
        - scenario ❌ (ID not found): Service asks database for the place by ID → Optional comes back empty → service throws `PlaceNotFoundException` → global handler returns 404 Not Found.
        
        - scenario ✅ (ID exists, validation passes): Database returns existing place → service overwrites its fields with the values from the update request → saves it back to the database → pushes the updated place to the Redis queue for the AI layer → converts it to a detailed response DTO → controller returns 200 OK.
        
        
- any extra things:
    
    - Why push to Redis again after an update?
        
        This tells the AI recommendation layer that the place data changed, preventing the AI from using stale or old information.
        
    - Is this a partial or full update?
        
        This is a full overwrite; fields not sent in the payload will be set to null because it handles a complete put-style replacement.
        

### DeletePlace

- Description: Deletes a place by its ID. Returns the deleted place's details in the response before it is removed. Throws an exception if the ID doesn't exist.

- Requiers Auth?: YES

- method signature:
    
    - type: public
        
    - returns: `PlaceDetailedResponseDTO`
        
    - parameters: `@PathVariable Long id`
        
- flow:
    
    - Scenario: Controller receives the request with an ID in the URL → asks the service to delete that place
        
        
        - scenario ❌ (ID not found): Service asks database for the place → Optional comes back empty → throws `PlaceNotFoundException` → global handler returns 404 Not Found.
        
        - scenario ✅ (ID exists): Database returns the place → service holds it in memory → deletes it from the database by ID → pushes a delete signal string `"DELETE_PLACE:{id}"` into the Redis queue → converts the place into a detailed response DTO → controller returns 204 No Content.
        
        
- any extra things:
    
    - What is the delete signal in Redis?
        
        It is a simple string containing the operation and the ID that tells the AI layer to remove that specific place from its vector indices.
        
    - Why does a 204 response return a body?
        
        While 204 typically means no content, returning the DTO allows the client or UI to confirm exactly which entity data was removed.
        

### FilterPlaces

- Description: Returns a filtered list of places based on optional query parameters. Filters that are not provided are ignored. Returns summaries instead of full details.

- Requiers Auth?: NO

- method signature:
    
    - type: public
        
    - returns: `List<PlaceSummaryDTO>`
        
    - parameters: `@RequestParam(required = false) String category`, `String city`, `String search`
        
- flow:
    
    - Scenario: Controller receives request with optional query params → passes them to the service → service uses a specification builder to create a dynamic database query matching only the provided parameters → database runs the query
        
        
        - scenario ✅ (Filters provided): Only the active filters are applied to the SQL query clause, restricting the database records returned to match criteria.
        
        - scenario ✅ (No filters provided): All params are null → dynamic query generates no conditions → returns all places as summaries.
        
        - scenario ✅ (No matches found): Filters are valid but match zero rows → database returns an empty list → controller returns 200 OK with an empty body.
        
        
- any extra things:
    
    - Why use dynamic queries here instead of multiple repository methods?
        
        It avoids writing many static query methods inside the repository layer for every combination of parameters, combining them into one flexible execution block instead.