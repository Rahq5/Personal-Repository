# UserController

## GetAllUsers

- Description: Fetches a list of all registered users in the system. Returns a safe response that excludes sensitive data like passwords and tokens. Never throws an error — returns an empty list if no users exist.
    
- Method Signature:
    
    - Public
    - Returns: `List<UserRegistrationResponseDTO>`
    - Parameters: none
- Flow:
    
    - Scenario: Controller receives the request → asks the service for all users → service asks the database for everything in the users table
        
        - Scenario ✅ (Users exist): Database returns a list of user objects → service converts each one into a safe response (id, email, name, role, status, createdAt only) → returns the list to the controller → controller returns it with 200 OK.
            
        - Scenario ✅ (No users): Database returns an empty list → service returns it as-is → controller returns 200 OK with an empty body.
            
- Extra notes:
    
    **Why not return the full User object?**
    
    > The User entity in the database holds very sensitive fields: password (hashed), reset token, reset token expiry, suspension timestamps. Returning the raw entity would expose all of that. The `UserRegistrationResponseDTO` is a stripped-down version that only carries what's safe to send outside the system.
    

---

## SaveUserInterests

- Description: Saves a user's selected interest IDs to their profile. Replaces whatever interests they had before with the new selection. Marks the user's interest setup as completed. Throws an exception if the user ID doesn't exist.
    
- Method Signature:
    
    - Public
    - Returns: `ResponseEntity<?>` (plain text message)
    - Parameters: `@PathVariable Long id`, `@RequestBody List<Long> interestIds`
- Flow:
    
    - Scenario: Controller receives the request with a user ID in the URL and a list of interest IDs in the body → passes both to the service
        
        - Scenario ❌ (User ID not found): Service asks the database for the user → Optional comes back empty → throws a `RuntimeException` with "User not found with id: {id}" → bubbles up to the global exception handler → returns an error response.
            
        - Scenario ✅ (User exists): Service fetches the user → fetches the interest objects matching the provided IDs from the interests table → clears the user's existing interests completely → adds the new ones → marks `interestsCompleted = true` on the user → saves the updated user back to the database → controller returns 200 OK with the message "Interests saved".
            
- Extra notes:
    
    **This is a full replacement, not an addition.**
    
    > The service calls `user.getInterests().clear()` before adding the new ones. So if a user had 3 interests before and sends 2 new ones, the old 3 are gone. It's designed for the onboarding flow where the user picks their interests fresh.
    
    **What is `interestsCompleted`?**
    
    > A boolean flag on the User entity. Once interests are saved, it's flipped to `true`. Other parts of the system (likely the recommendation engine) use this flag to know whether to serve personalized recommendations or not yet.
    
    **What is the `user_interests` table?**
    
    > Users and interests have a many-to-many relationship — one user can have many interests, and one interest can belong to many users. The database stores this link in a separate join table called `user_interests` with two columns: `user_id` and `interest_id`.
    

---

## SuspendUser

- Description: Temporarily suspends a user account for a given number of days. Defaults to 7 days if no duration is specified. The user's status is set to SUSPENDED and a timestamp is recorded marking when the suspension ends. Throws an exception if the user ID doesn't exist.
    
- Method Signature:
    
    - Public
    - Returns: `ResponseEntity<?>` (plain text message)
    - Parameters: `@PathVariable Long id`, `@RequestParam(defaultValue = "7") int days`
- Flow:
    
    - Scenario: Controller receives the request with a user ID in the URL and an optional number of days as a query param → passes both to the service
        
        - Scenario ❌ (User ID not found): Service asks the database for the user → Optional comes back empty → throws `RuntimeException` "User not found" → error response returned.
            
        - Scenario ✅ (User exists): Service fetches the user → sets their status to `SUSPENDED` → sets `suspendedUntil` to today's date/time plus the given number of days → saves the updated user to the database → controller returns 200 OK with "User suspended for {days} days".
            
- Extra notes:
    
    **What is `UserStatus`?**
    
    > An enum on the User entity with three possible values: `ACTIVE`, `SUSPENDED`, `BANNED`. Setting the status to `SUSPENDED` doesn't delete the account — it just flags it. Other parts of the system (likely the security/auth layer) are responsible for checking this flag and blocking the user from logging in.
    
    **`suspendedUntil` is just a timestamp — nothing enforces it automatically.**
    
    > The database just stores the date the suspension should end. There's no background job that auto-lifts it. The system needs to either check this field during login or a manual `unblockUser` call needs to be made.
    

---

## BanUser

- Description: Permanently bans a user account. Sets their status to BANNED and clears any existing suspension timestamp. There is no time limit — the ban stays until manually reversed. Throws an exception if the user ID doesn't exist.
    
- Method Signature:
    
    - Public
    - Returns: `ResponseEntity<?>` (plain text message)
    - Parameters: `@PathVariable Long id`
- Flow:
    
    - Scenario: Controller receives the request with a user ID → passes it to the service
        
        - Scenario ❌ (User ID not found): Service asks the database for the user → Optional comes back empty → throws `RuntimeException` "User not found" → error response returned.
            
        - Scenario ✅ (User exists): Service fetches the user → sets their status to `BANNED` → sets `suspendedUntil` to null (clears any previous suspension end date since a ban has no expiry) → saves the updated user → controller returns 200 OK with "User permanently banned".
            
- Extra notes:
    
    **Why clear `suspendedUntil` on a ban?**
    
    > If the user was previously suspended, their `suspendedUntil` field would still have a date in it. A ban is permanent, so there's no end date — clearing the field avoids any confusion where the system might misread a leftover suspension timestamp as an expiry for the ban.
    

---

## UnblockUser

- Description: Restores a suspended or banned user back to active status. Clears the suspension timestamp. Throws an exception if the user ID doesn't exist.
    
- Method Signature:
    
    - Public
    - Returns: `ResponseEntity<?>` (plain text message)
    - Parameters: `@PathVariable Long id`
- Flow:
    
    - Scenario: Controller receives the request with a user ID → passes it to the service
        
        - Scenario ❌ (User ID not found): Service asks the database for the user → Optional comes back empty → throws `RuntimeException` "User not found" → error response returned.
            
        - Scenario ✅ (User exists): Service fetches the user → sets their status back to `ACTIVE` → sets `suspendedUntil` to null → saves the updated user → controller returns 200 OK with "User unblocked".
            
- Extra notes:
    
    **This works for both suspended and banned users.**
    
    > There's no check on what the current status is before setting it to `ACTIVE`. Whether the user was `SUSPENDED` or `BANNED`, calling this endpoint brings them back to normal. One single method handles both cases.