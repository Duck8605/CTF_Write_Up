# The Restricted Sessions

##Description:

Access to this area is highly restricted. You are not logged in, and there's no obvious way to do so. Perhaps there's a way to become an authorized user by understanding how sessions are managed.

## Initial Analysis

### Functionality Description
The website displays a "You are not logged-in" message and has no apparent login forms or interactive features.
![image](https://github.com/user-attachments/assets/25c7852f-2dc5-4229-9727-690bb2512213)


### Identifying Anomalies
Inspecting the page's source code reveals an interesting JavaScript snippet. This code retrieves the value of the `PHPSESSID` cookie and sends it to a hidden file named `getcurrentuserinfo.php` via a POST request.

![image](https://github.com/user-attachments/assets/4ffa1bca-bc40-4128-a3b0-aadd83c93d47)

### Vulnerability Hypothesis
The vulnerability is related to insecure session management and information disclosure. There might be a hidden file or endpoint containing sensitive information that can be exploited to hijack another user's session.

## Exploitation Steps

1.  **Test with a Random Cookie:** Use Burp Suite to intercept a request to the main page and add a `PHPSESSID` cookie with a random value (e.g., `PHPSESSID=test`).

2.  **Discover Information Leak:** The server's response reveals a file path: `data/session_store.txt`.
    ![image](https://github.com/user-attachments/assets/b9ff958b-2521-4f2d-8b47-5d3ef2b4a5f8)


3.  **Access the Leaked File:** Navigate directly to the path `[URL]/data/session_store.txt`. This file contains a list of valid `PHPSESSID` values.
    ![image](https://github.com/user-attachments/assets/98b43be7-977d-46c0-a7be-efe15a042cce)

    iuqwhe23eh23kej2hd2u3h2k23
    
    11l3ztdo96ritoitf9fr092ru3
    
    ksjdlaskjd23ljd2lkjdkasdlk


5.  **Exploit the `getcurrentuserinfo.php` Endpoint:**
    - Using Burp Suite Repeater, create a `POST` request to `getcurrentuserinfo.php`.
    - In the request body, send one of the valid `PHPSESSID`s found in step 3.
    - **Payload:** `PHPSESSID=iuqwhe23eh23kej2hd2u3h2k23`
    ![image](https://github.com/user-attachments/assets/e58dbdb3-a881-41ed-82fa-ef76dacab692)

    - The response is a JSON object containing user information (`name`, `email`, `session_id`).
    ![image](https://github.com/user-attachments/assets/eabecbf2-f0be-4fcb-8c16-2b7a07da7609)

    

6.  **Impersonate the User and Get the Flag:**
    - Return to the browser.
    - Set the `PHPSESSID` cookie to the value `iuqwhe23eh23kej2hd2u3h2k23`.
    - Based on the response in step 4, the server might also check another cookie. Try adding a `UserInfo=john` cookie.
    - Reload the page. The website will authenticate you as the logged-in user and display the flag.
    ![image](https://github.com/user-attachments/assets/e63c3d56-881a-4ba9-bbf8-7ba0dd9f6865)

    **Flag:** sessionareawesomebutifitsecure

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
