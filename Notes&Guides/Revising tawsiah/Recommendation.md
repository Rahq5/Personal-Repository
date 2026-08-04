# RecommendationController

> **What is the Recommendation feature?** It returns a personalized list of events/places for a user based on their saved interests and their current city. Unlike Masar which is user-driven (you pick what you want), Recommendations are system-driven (the system decides what you might like). It tries to use a Python AI engine first, and falls back to its own scoring logic if the AI is unavailable.

---

## GetRecommendationsByUser (GET)

- Description: Returns a personalized list of events for a specific user filtered by city. Tries the Python AI engine first, falls back to the top 10 rated events in that city if the AI fails or returns nothing. Returns 204 No Content if the result is empty.
    
- Method Signature:
    
    - Public
    - Returns: `List<Event>`
    - Parameters: `@PathVariable Long userId`, `@RequestParam String city`
- Flow:
    
    - Scenario: Controller receives the request with userId and city → passes both to the service → service checks if the user exists
        
        - Scenario ❌ (User not found): User ID doesn't exist in the database → throws `UserNotFoundException` → error response returned.
            
        - Scenario ✅ (User exists, AI responds): Service fetches the user's saved interests → separates them into categories and cities → sends both to the Python AI engine at `/api/recommendation/{userId}` → AI returns a list of event IDs → service fetches those events from the database by ID → returns them to the controller → controller returns 200 OK.
            
        - Scenario ✅ (User exists, AI fails or returns empty): AI is unreachable or returns nothing → service falls back to fetching the top 10 highest-rated events in that city directly from the database → returns them → controller returns 200 OK.
            
        - Scenario ✅ (Both AI and fallback return empty): Controller returns 204 No Content.
            
- Extra notes:
    
    **This endpoint returns raw `Event` entities, not DTOs.**
    
    > Unlike most endpoints that return safe response objects, this one returns the full entity. This is a known inconsistency in the code — the other recommendation endpoint (`POST /api/recommendations`) returns proper DTOs.
    

---

## GetRecommendations (POST)

- Description: The main recommendation endpoint. Accepts user ID, interests/categories, cities, and current city. Tries the Python AI engine first with a full scoring payload. Falls back to a proximity-based scoring algorithm if the AI is unavailable. Returns both events and places mixed together.
    
- Method Signature:
    
    - Public
    - Returns: `RecommendationResponseDTO` (scored list + strategy label)
    - Parameters: `@RequestBody RecommendationRequestDTO request`
- Flow:
    
    - Scenario: Controller receives the full request → passes to the service → service attempts the Python AI engine
        
        - Scenario ✅ (AI responds with scored items): Service sends categories, cities, userId, and city to Python at `/recommend` → AI returns a scored list of items with their IDs and types (event or place) → service fetches the matching events and places from the database → converts them to summary DTOs → preserves the AI's original ordering → returns the result with strategy label from the AI → controller returns 200 OK.
            
        - Scenario ✅ (AI is down or fails): AI unreachable or throws an error → service falls back to the proximity-based algorithm (described below) → returns the result with a strategy label explaining which fallback was used → controller returns 200 OK.
            
- Extra notes:
    
    **What is the proximity-based fallback?**
    
    > When the AI is unavailable, the service scores events itself using 3 tiers:
    > 
    > - **Tier 0** — events in the user's exact city (highest priority)
    > - **Tier 1** — events in nearby cities (based on a hardcoded Saudi Arabia proximity map)
    > - **Tier 2** — all remaining cities that match the user's categories
    > 
    > Each event then gets a combined score:
    > 
    > - Category match weight → 45%
    > - Event rating → 35%
    > - Proximity (same city = 5, nearby = 3, distant = 1) → 20%
    > 
    > The final `relevanceScore` is the combined score expressed as 0–100 for the frontend to display.
    
    **What is the strategy label?**
    
    > The response always includes a string like `"hybrid_city_category"`, `"city_based"`, `"category_based"`, or `"top_rated_fallback"`. This tells the frontend (and developers) which path produced the result — useful for debugging and for potentially showing the user why they're seeing these recommendations.
    
    **The proximity map is hardcoded for Saudi Arabia.**
    
    > Cities like Mecca, Jeddah, Taif, Medina, Riyadh, Dammam etc. are mapped to their nearest neighbours. So a user in Jeddah can receive recommendations for events in Mecca and Taif even if no exact Jeddah match exists.
    

---

## SyncProfile (POST /sync)

- Description: Sends the user's latest interests and recent session activity to the Python AI engine so it can update its internal user profile for future recommendations. This is a fire-and-forget call — if it fails, nothing breaks.
    
- Method Signature:
    
    - Public
    - Returns: `ResponseEntity<Void>` (empty 200 OK)
    - Parameters: `@RequestBody Map` containing `userId`, `interests`, `sessionCategories`
- Flow:
    
    - Scenario: Controller receives the request → extracts userId, interests, and sessionCategories from the map → passes to the service
        
        - Scenario ✅ (AI engine accepts the sync): Service splits interests into categories and cities → sends the full profile payload to Python at `/update-profile` → Python updates the user's profile in the vector store → returns 200 OK.
            
        - Scenario ✅ (AI engine fails): Sync throws any error → service catches it silently, prints a warning, and continues → controller still returns 200 OK. The sync failure is non-critical.
            
- Extra notes:
    
    **Why is sync failure non-critical?**
    
    > The sync just updates the AI's understanding of the user. If it fails, the next recommendation call will still work — it'll just use a slightly older profile. The user won't notice anything.
    

---

## TriggerByUser (POST /{userId})

- Description: A more advanced version of the GET recommendation endpoint. Resolves the user's city through 4 fallback tiers before fetching recommendations — URL param, then cookie memory, then GPS coordinates, then a hard default. This is the endpoint the frontend calls with live location data.
    
- Method Signature:
    
    - Public
    - Returns: `List<Event>`
    - Parameters: `@PathVariable Long userId`, `@RequestParam(required = false) String city`, `@RequestBody(required = false) Location userLocation`
- Flow:
    
    - Scenario: Controller receives the request → resolves the target city through 4 tiers in order
        
        - Tier 1 — URL city param provided: Uses the city from the URL → saves it to a cookie for next time → fetches recommendations for that city.
            
        - Tier 2 — No URL param, but cookie exists: Reads the city from the cookie that was saved during a previous visit → uses it.
            
        - Tier 3 — No cookie, but GPS coordinates sent in the body: Sends latitude and longitude to the Nominatim reverse geocoding service → gets back the city name in English → saves it to a cookie → uses it.
            
        - Tier 4 — Nothing available: Defaults to `"makkah"` as the hard fallback city.
            
        
        After city is resolved → calls `getPersonalizedEvents(userId, targetCity)` → same AI-then-fallback flow as the GET endpoint → controller returns 200 OK or 204 No Content.
        
- Extra notes:
    
    **What is a cookie here?**
    
    > A small piece of data saved in the user's browser by the server. Here it stores the last known city. On the next request, the browser automatically sends it back — so the system "remembers" the user's city without them having to send it again.
    
    **What is Nominatim?**
    
    > A free reverse geocoding service from OpenStreetMap. Given GPS coordinates (latitude, longitude), it returns a human-readable location name. This is how the app converts "24.7136° N, 46.6753° E" into "Riyadh".
    
    **Why 4 tiers instead of just asking for the city?**
    
    > Mobile users may not always type their city. GPS is more accurate but requires permission. Cookies reduce the need to ask repeatedly. The hard default ensures the app never crashes when all else fails.