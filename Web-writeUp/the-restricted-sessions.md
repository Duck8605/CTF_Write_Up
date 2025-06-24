# Challenge 3: The Restricted Sessions

## Initial Analysis

### Functionality Description
The website displays a "You are not logged-in" message and has no apparent login forms or interactive features.

### Identifying Anomalies
Inspecting the page's source code reveals an interesting JavaScript snippet. This code retrieves the value of the `PHPSESSID` cookie and sends it to a hidden file named `getcurrentuserinfo.php` via a POST request.

```javascript
<script type="text/javascript">
if(document.cookie = ''){
    $.post('getcurrentuserinfo.php',{
        'PHPSESSID':document.cookie.match(/PHPSESSID=([^;]+)/)[1]
    }, function(data){
        cu = data;
    });
}
</script>
```

### Vulnerability Hypothesis
The vulnerability is related to insecure session management and information disclosure. There might be a hidden file or endpoint containing sensitive information that can be exploited to hijack another user's session.

## Exploitation Steps

1.  **Test with a Random Cookie:** Use Burp Suite to intercept a request to the main page and add a `PHPSESSID` cookie with a random value (e.g., `PHPSESSID=test`).

2.  **Discover Information Leak:** The server's response reveals a file path: `data/session_store.txt`.
    

3.  **Access the Leaked File:** Navigate directly to the path `[URL]/data/session_store.txt`. This file contains a list of valid `PHPSESSID` values.
    

4.  **Exploit the `getcurrentuserinfo.php` Endpoint:**
    - Using Burp Suite Repeater, create a `POST` request to `getcurrentuserinfo.php`.
    - In the request body, send one of the valid `PHPSESSID`s found in step 3.
    - **Payload:** `PHPSESSID=iuqwhe23eh23kej2hd2u3h2k23`
    - The response is a JSON object containing user information (`name`, `email`, `session_id`).
    

5.  **Impersonate the User and Get the Flag:**
    - Return to the browser.
    - Using DevTools, set the `PHPSESSID` cookie to the value `iuqwhe23eh23kej2hd2u3h2k23`.
    - Based on the response in step 4, the server might also check another cookie. Try adding a `UserInfo=john` cookie.
    - Reload the page. The website will authenticate you as the logged-in user and display the flag.
    

## Impact
- **Severe:**
    - **Information Disclosure:** The `data/store.txt` file leaks valid session IDs.
    - **Broken Access Control:** The `getcurrentuserinfo.php` endpoint returns user information based solely on a provided `PHPSESSID`.
    - **Session Hijacking:** An attacker can take over the session of any user whose session ID is exposed.

## Remediation
- **Protect Sensitive Files:** Never store files containing sensitive information (like session IDs) in a publicly accessible web directory. Configure file permissions correctly.
- **Enforce Strict Access Control:** The `getcurrentuserinfo.php` endpoint must validate the session of the user making the request on the server, not just trust the data sent in the request body.
- **Do Not Return Sensitive Paths:** Configure the server not to return debugging information or internal paths in HTTP responses.
- **Design Comprehensive Authentication:** The authentication mechanism should rely on a secure server-side session state, not just on the presence of a few client-side cookies.