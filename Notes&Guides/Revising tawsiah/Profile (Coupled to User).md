# ProfileController

## GetProfile

- Description: Fetches a user's full profile by their ID. Returns a rich response that includes personal info, interests, activity stats, and their submitted reviews. Throws an exception if the user doesn't exist.
    
- Method Signature:
    
    - Public
    - Returns: `UserProfileResponseDTO`
    - Parameters: `@PathVariable Long id`
- Flow:
    
    - Scenario: Controller receives the request with a user ID in the URL → passes it to the service → service asks the database for the user
        
        - Scenario ❌ (User not found): Optional comes back empty → throws `ResourceNotFoundException` "User not found" → global handler returns 404.
            
        - Scenario ✅ (User exists): Service fetches the user → builds the full profile response by pulling data from 4 sources:
            
            - User's own fields (name, email, language, avatar, last name update timestamp)
            - Counts from the database: how many favorites, how many visited places, how many reviews
            - Their selected interests as a list
            - Their submitted reviews as a list → returns the assembled profile to the controller → controller returns it with 200 OK.
- Extra notes:
    
    **The profile response also tells the frontend whether the user is allowed to change their name right now.**
    
    > The field `canUpdateName` is a boolean computed on the fly — if the user last changed their name less than 7 days ago, it's `false`. The frontend can use this to grey out the name edit button without needing to make a separate request.
    
    **Stats are counts, not full lists.**
    
    > `favoritesCount`, `visitedCount`, `reviewsCount` are just numbers. The full lists of favorites and visited places exist as fields in the response DTO but are currently not populated in the code — they're left as null for now, likely to be implemented later.
    

---

## UpdateName

- Description: Updates the display name of a user. Enforces a 7-day cooldown between name changes. Validates that the new name is not blank and is at least 2 characters. Returns the updated full profile. Throws an exception if the user doesn't exist or the cooldown hasn't passed.
    
- Method Signature:
    
    - Public
    - Returns: `UserProfileResponseDTO`
    - Parameters: `@PathVariable Long id`, `@RequestBody UserUpdateNameRequestDTO request`
- Flow:
    
    - Scenario: Controller receives the request with a user ID and new name in the body → passes both to the service
        
        - Scenario ❌ (Name is blank or too short): Service checks the new name before touching the database → if it's empty or less than 2 characters → throws `IllegalArgumentException` "Name must be at least 2 characters" → error response returned, nothing is saved.
            
        - Scenario ❌ (User not found): Name passes validation → service asks the database for the user → Optional comes back empty → throws `ResourceNotFoundException` "User not found" → 404 returned.
            
        - Scenario ❌ (Cooldown not passed): User is found → service checks `nameLastUpdatedAt` → if it was set less than 7 days ago → throws `IllegalStateException` "Name can only be updated once every 7 days" → error response returned, nothing is saved.
            
        - Scenario ✅ (All checks pass): Service updates the user's name (trimmed of extra spaces) → sets `nameLastUpdatedAt` to right now → saves to the database → builds and returns the full updated profile → controller returns it with 200 OK.
            
- Extra notes:
    
    **First-time name change is always allowed.**
    
    > If `nameLastUpdatedAt` is null (the user never changed their name before), the cooldown check is skipped entirely. The 7-day rule only kicks in after the first change.
    
    **The cooldown is enforced server-side, not just frontend.**
    
    > Even if the frontend hides the button, the backend will still reject the request if the 7 days haven't passed. The `canUpdateName` flag from `GetProfile` is just a convenience for the UI.
    

---

## UpdateAvatar

- Description: Updates the user's profile picture. The image is sent as a Base64 string directly in the request body — not as a file upload. Validates the format before saving. Returns the updated full profile. Throws an exception if the user doesn't exist or the image data is invalid.
    
- Method Signature:
    
    - Public
    - Returns: `UserProfileResponseDTO`
    - Parameters: `@PathVariable Long id`, `@RequestBody Map<String, String> request` (key: `avatarBase64`)
- Flow:
    
    - Scenario: Controller receives the request with a user ID and a map containing the key `avatarBase64` → extracts the Base64 string from the map → passes both to the service
        
        - Scenario ❌ (User not found): Service asks the database for the user → Optional comes back empty → throws `ResourceNotFoundException` "User not found" → 404 returned.
            
        - Scenario ❌ (Avatar data is blank): Service checks if the Base64 string is empty → throws `IllegalArgumentException` "Avatar data is required" → error response returned.
            
        - Scenario ❌ (Invalid image format): String is not blank but doesn't start with `data:image/` → throws `IllegalArgumentException` "Invalid image format" → error response returned.
            
        - Scenario ✅ (Valid image data): Service sets the user's `avatarUrl` to the full Base64 string → saves to the database → builds and returns the full updated profile → controller returns it with 200 OK.
            
- Extra notes:
    
    **What is Base64?**
    
    > A way to represent binary data (like an image file) as a plain text string. Instead of uploading a file, the frontend converts the image into a long string that starts with `data:image/png;base64,...` and sends it as text. The backend stores that string directly in the database column.
    
    **The avatar is stored in the database, not on a file server.**
    
    > Most production systems store images on something like AWS S3 and save just the URL. Here the full Base64 string is stored directly in the `avatar_url` column. This works but means the database row for a user with an avatar is significantly larger than one without.
    
    **Why `Map<String, String>` instead of a proper DTO?**
    
    > The controller just reads one key (`avatarBase64`) from a plain map instead of defining a dedicated request class. It works the same way but is less structured — likely written quickly and could be refactored to a proper DTO later.