# MasarController

> **What is Masar?** Masar (مسار) is the trip planning feature of Tawsiah. Given a budget and a set of interests, it generates either a flat list of matching places/events or up to 3 curated trip plans. It works entirely from the local database — no AI engine involved.

---

## Discovery

- Description: Returns all unique categories and cities available across all places and events in the database. Used by the frontend to populate the filter dropdowns before the user builds their trip. Never throws an error — if the database is empty, it returns two empty lists.
    
- Method Signature:
    
    - Public
    - Returns: `MasarDiscoveryDTO` (two lists: categories, cities)
    - Parameters: none
- Flow:
    
    - Scenario: Controller receives the request → asks the service for all distinct categories and cities
        
        - Scenario ✅ (Data exists): Service queries the places table for all distinct categories → queries the events table for all distinct categories → merges both lists, removes nulls and blanks, lowercases, deduplicates, and sorts alphabetically → does the exact same thing for cities → returns both clean sorted lists to the controller → controller returns 200 OK.
            
        - Scenario ✅ (No data): Both lists come back empty → controller returns 200 OK with two empty lists.
            
- Extra notes:
    
    **Why merge places and events together?**
    
    > Masar deals with both places and events at the same time. The user doesn't pick "I want event categories" vs "I want place categories" — they just pick interest topics. So categories from both tables are pooled into one list for the UI.
    
    **Why lowercase and deduplicate?**
    
    > Different records might store the same category as "Beach", "beach", or "BEACH". Lowercasing and deduplicating ensures the user sees each option only once, cleanly.
    

---

## GenerateItinerary

- Description: Returns a flat numbered list of all matching places and events based on the user's selected interests, cities, and budget. Results are sorted by rating — highest rated first. Returns 204 No Content if nothing matches. Falls back to the top 10 rated places if all filters produce zero results.
    
- Method Signature:
    
    - Public
    - Returns: `MasarItineraryResponseDTO` (flat list of stops + total count + total cost)
    - Parameters: `@RequestBody MasarRequestDTO request` (budget, interests, cities)
- Flow:
    
    - Scenario: Controller receives the request with budget, interests, and optional cities → passes to the service
        
        - Scenario ✅ (Matches found): Service defaults budget to 1,000 if not provided → fetches all places from the database and filters by: category matching any of the interests, city matching any of the selected cities (skipped if no cities provided), price within budget → sorts by rating descending → does the same for events (with price parsed from string since event prices are stored as text) → combines both into a numbered stop list → calculates total cost → returns the assembled response → controller returns 200 OK.
            
        - Scenario ✅ (No matches, fallback triggered): Filters produce zero results → service ignores all filters and fetches the top 10 highest-rated places as a fallback → builds the stop list from those → returns it.
            
        - Scenario ✅ (Fallback also empty): Database has no places at all → list stays empty → controller returns 204 No Content.
            
- Extra notes:
    
    **Why is event price a string that needs parsing?**
    
    > In the Event entity, `price` is stored as a `String` (e.g., "free", "50 SAR", "$30"). The service strips currency symbols, handles the word "free", and converts to a number. If parsing fails, it defaults to 0.0 so the event is never excluded due to a bad price format.
    
    **Descriptions are trimmed to 120 characters.**
    
    > Each stop's description is cut off at 120 characters with "..." appended. This keeps the response lightweight for list views — the full description is available when the user opens a specific event or place.
    
    **Cities filter is optional.**
    
    > If the user sends no cities, the city filter is skipped entirely and matches from all cities are returned. This means "interests only" is a valid request.
    

---

## Generate

- Description: Returns up to 3 curated named trip plans based on the user's interests and budget. Each plan is a different angle on the same pool of matching places/events. Returns 204 No Content if no plans could be built. Falls back to top-rated places if no category matches exist.
    
- Method Signature:
    
    - Public
    - Returns: `MasarResponseDTO` (list of up to 3 `MasarPlanDTO` objects)
    - Parameters: `@RequestBody MasarRequestDTO request` (budget, interests)
- Flow:
    
    - Scenario: Controller receives the request → passes to the service → service builds the same filtered pool as GenerateItinerary (matching category + within budget), then builds 3 plans from it
        
        - Scenario ✅ (Pool has enough items): Service builds Plan A → Plan B → Plan C from the same pool, each using a different selection strategy (described below) → returns up to 3 plans → controller returns 200 OK.
            
        - Scenario ✅ (Pool is empty, fallback triggered): No category matches found → service fetches top 6 highest-rated places regardless of category → uses those as the pool for all plans.
            
        - Scenario ✅ (Plans list is empty after building): Pool too small to build anything → controller returns 204 No Content.
            
- Extra notes:
    
    **The 3 plan strategies:**
    
    > **Plan A — "The Grand Tour"**: Takes the highest-rated items and spreads them across multiple cities. No more than 2 items from the same city. Up to 6 stops.
    
    > **Plan B — "City Deep Dive" or "Hidden Gems"**: Excludes everything already in Plan A. If one city has 3 or more remaining items, it becomes a focused single-city plan ("City Deep Dive"). Otherwise, the lowest-rated remaining items are surfaced as lesser-known picks ("Hidden Gems").
    
    > **Plan C — "Best Value"**: Excludes everything in Plans A and B. Takes the cheapest remaining items sorted by price ascending — maximizing the number of stops within budget.
    
    **Each plan is independent — no item appears in two plans.**
    
    > The service tracks used IDs and filters them out when building the next plan. This ensures each plan feels like a genuinely different trip, not a reshuffling of the same stops.
    
    **Default interests if none provided.**
    
    > If the user sends no interests, the service defaults to `["nature", "historical", "beach"]` as a reasonable starting point for Saudi tourism.