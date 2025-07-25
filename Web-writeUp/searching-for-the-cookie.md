# Searching for the cookie

## Description:

Our new 'Cookie Search Engine' can find pictures of any cookie you can imagine! But we've been told there's a special, hidden cookie on this page. Can your search query find it?

## Initial Analysis

### Functionality Description
The website has a single feature: an image search box. A user enters a keyword and clicks search.

### Identifying Anomalies
The challenge name ("Searching for the cookie") suggests the goal is to find a cookie, and the vulnerability is likely Cross-Site Scripting (XSS). The search box is the most probable injection point.
When viewing the source code of the results page after searching for "test", we see that this string is inserted directly into a `<script>` tag without any encoding.

![image](https://github.com/user-attachments/assets/29a2c0f2-589e-4840-beb0-340ab24227a5)

This is a form of **DOM-based XSS**.

### Vulnerability Hypothesis
The website is vulnerable to DOM-based XSS. The input from the search box is not properly sanitized/encoded and is reflected back, executing immediately in the user's browser within the context of a JavaScript snippet.

## Exploitation Steps

1.  **Test a Basic XSS Payload:**
    Since the input value is inside a JavaScript string, we need a special payload to break out of the current script's context and execute our own code.

    **Payload:** `');</script><script>alert('test')</script>`
    - `');`: Closes the current JavaScript string and statement.
    - `</script>`: Closes the website's original script tag.
    - `<script>alert('test')</script>`: Opens a new script tag to execute arbitrary code.
    
    **Result:** An `alert` dialog box pops up, confirming we can execute JavaScript.
    
    ![image](https://github.com/user-attachments/assets/6fa94560-42ff-4ca3-8826-2ff5e82ddeed)

    

3.  **Steal the Cookie (Flag):**
    Based on the challenge's name, we modify the payload to display the page's cookie.

    **Final Payload:** `');</script><script>alert(document.cookie)</script>`

    **Result:** After submitting this payload, the alert dialog will appear, containing the challenge's flag.

    ![image](https://github.com/user-attachments/assets/1e6f5f3c-f5bd-4881-9168-e9feebb11f20)

    **Flag:** coolcookie112

## Impact
- **Severe:** An attacker can inject arbitrary JavaScript to:
    - **Steal Cookies (Session Hijacking):** Take over other users' login sessions.
    - **Perform Actions on Behalf of the User:** Change personal information, send messages, etc.
    - **Redirect Users:** Phish users by redirecting them to a malicious website.

## Remediation
- **Output Encoding:** Always encode all user-supplied data before rendering it back on the page. For data injected into JavaScript, use JS-specific encoding methods to neutralize special characters (e.g., `\'`, `\"`, `\xHH`).
- **Input Validation:** Check and allow only valid characters in the search box.
- **Implement Content Security Policy (CSP):** Use CSP to define trusted script sources, preventing the execution of injected inline scripts.
