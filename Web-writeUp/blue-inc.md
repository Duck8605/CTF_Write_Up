# Challenge 7: Blue Inc

## Initial Analysis

### Functionality Description
The website provides a login form with a pre-supplied account: `demo/demo`. After logging in, users can access their personal profile page.

### Identifying Anomalies
- **URL Parameter:** After logging in, the profile page URL is `.../profile.php?user=demo`. The user's identity is passed directly through the `user` parameter in the URL.
- **Cookie:** Inspecting the browser's cookies reveals a cookie also named `user` with the value `demo`.

### Vulnerability Hypothesis
- **Insecure Direct Object Reference (IDOR):** The application uses the `user` parameter from the URL to retrieve a profile without checking if the current user is authorized to view it.
- **Broken Access Control:** The user's identity and authorization are determined by client-controlled values (URL parameter and cookie) instead of being strictly validated on the server-side.

## Exploitation Steps

1.  **Log in with the demo account:** Use `demo/demo` to log in.

2.  **Observe URL and Cookie:** After logging in, go to the profile page and observe:
    - URL: `.../profile.php?user=demo`
    - Cookie: `user=demo`
    
    

3.  **Perform the IDOR Attack:** Proceed to change both client-controlled values to impersonate the admin.
    - **Change URL:** Modify the URL parameter from `user=demo` to `user=admin`.
    - **Change Cookie:** Use DevTools (F12) to edit the value of the `user` cookie from `demo` to `admin`.

4.  **Get the Flag:** After changing both values, navigate to the modified URL (or reload the page). The system will believe we are the `admin` user and display the admin's profile, which contains the flag.
    

## Impact
- **Severe:** An attacker can access and view the information of any user in the system simply by guessing or changing a URL parameter and a cookie value.
- The vulnerability allows for both **Horizontal Privilege Escalation** (viewing other users' data) and **Vertical Privilege Escalation** (accessing an admin account from a regular user account).

## Remediation
- **Don't Trust User Input:** The golden rule is to never trust any data coming from the client (URL, cookies, form data) to make access control decisions.
- **Enforce Server-Side Authorization:** When a user requests to view a profile, the server must:
    1.  Identify the currently logged-in user based on its own secure session information.
    2.  Check if that user has permission to view the requested profile. For a profile page, the answer is usually "No, users can only view their own profile."
- **Avoid Direct References:** Avoid using guessable values (like usernames) in URLs. If they must be used, always accompany them with a strict server-side authorization check.