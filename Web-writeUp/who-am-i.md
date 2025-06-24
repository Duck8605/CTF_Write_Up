# Challenge 6: Who am I?

## Initial Analysis

### Functionality Description
The website features a login form. The objective is to access content reserved for the administrator (admin).

### Identifying Anomalies
- **HTML Source Code:** Inspecting the login page's source code reveals the credentials for the Guest account in an HTML comment.
  ```html
  <!--
  Guest Account:
  Username: Guest
  Password: Guest
  -->
  ```
- **Cookies:** After logging in with the Guest account, inspecting the browser's cookies (F12 -> Application -> Cookies) shows an `Authentication` cookie with a value that appears to be Base64 encoded.

### Vulnerability Hypothesis
- **Broken Access Control:** A user's authorization level is determined entirely by a client-side cookie value.
- This cookie is not securely signed or encrypted, allowing an attacker to easily decode, modify, and re-encode it to impersonate another user, specifically the admin.

## Exploitation Steps

1.  **Log in with the Guest Account:** Use the credentials `Guest:Guest` found in the source code to log in. The page displays an "Access Denied" message.

2.  **Retrieve and Decode the Cookie:**
    - Open DevTools (F12) and go to the Application -> Cookies tab.
    - Copy the value of the `Authentication` cookie: `bG9naW49R3Vlc3Q%3D`.
    - Use a tool (e.g., CyberChef) to first URL Decode and then Base64 Decode the value.
    - **Decoded result:** `login=Guest`
    

3.  **Modify and Re-encode the Cookie:**
    - Change the decoded string from `login=Guest` to `login=admin`.
    - Re-encode the string `login=admin` back to Base64 format.
    - **Encoded result:** `bG9naW49YWRtaW4=`
    

4.  **Replace the Cookie and Get the Flag:**
    - Return to the browser and, in DevTools, edit the value of the `Authentication` cookie to the new Base64 string: `bG9naW49YWRtaW4=`.
    - Reload the page.
    - The server will read the new cookie, validate you as the admin, and display the flag.
    

## Impact
- **Critical:** An attacker can easily escalate privileges from a regular user to an administrator by simply modifying a cookie value.
- This could lead to a full application takeover, with the ability to read/write/delete data and attack other systems.

## Remediation
- **Server-Side Authorization:** All authorization logic must be performed on the server, based on a securely stored session state. Never trust data from the client (like cookies) for access control decisions.
- **Use Secure Sessions:** Instead of storing permissions directly in a cookie, store a random, unpredictable session ID. The server will use this ID to look up the user's information and permissions.
- **Sign Cookies:** If state must be stored in a cookie, use a cryptographic signature (e.g., HMAC) to ensure its integrity and prevent tampering.