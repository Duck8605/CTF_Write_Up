# Easy message

## Description:

There's a simple login page, but the credentials aren't obvious. Sometimes, administrators leave messages or files behind by mistake. If you can get in, there's a message waiting for you.

## Initial Analysis

### Functionality Description
The website consists of only a simple login form. The goal is to find the credentials to access the content within.

### Identifying Anomalies
Inspecting the HTML source code and other information reveals no clues.

### Vulnerability Hypothesis
**Information Disclosure:** Since no technical vulnerabilities are found on the main page, the leading hypothesis is that sensitive information (like credentials or hidden paths) is leaked through configuration or junk files accidentally left by an administrator. One of the most common files to check is `robots.txt`.

## Exploitation Steps

1.  **Check `robots.txt`:** Navigate to the file at `[URL]/robots.txt`.
    
2.  **Discover a Backup File:** The content of `robots.txt` reveals a "Disallowed" path: `/ajax.php~`. The tilde (`~`) at the end is a common indicator of a backup file automatically created by text editors.

    ![image](https://github.com/user-attachments/assets/fdcaf9c2-5ee1-4201-89b5-75fce6d1240a)

4.  **View the Backup File's Content:** Navigate directly to the path `[URL]/ajax.php~`. The content of this file is the PHP source code for the login handler.

    ![image](https://github.com/user-attachments/assets/75f520f2-1c5d-43ad-9689-bf06fd81f48f)


6.  **Decode the Credentials:** The source code contains two Base64-encoded strings.
    - **String:** `Q3liZXItVGFsZW50`
    - **Decode:** Using a Base64 decoder, we get the result `Cyber-Talent`.
    - Therefore, both the username and password are `Cyber-Talent`.

    ![image](https://github.com/user-attachments/assets/0eead2d8-d5a1-47ab-80ca-5c0cfb5c4a72)


7.  **Log In and Find the Flag:**
    - Use the credentials `Cyber-Talent:Cyber-Talent` to log in.
    - After a successful login, the website displays a strange message in the form of dots and dashes. This is the format of **Morse code**.

    ![image](https://github.com/user-attachments/assets/2234da12-2652-4570-9d03-5ca54ad8accf)


8.  **Decode the Morse Code:** Copy the Morse code string and use an online decoder.
    
    ![image](https://github.com/user-attachments/assets/5b2a3747-187c-49e9-b2dc-6d4ef21baa0b)

    - **Flag:** `FLAG(I-KNOW-YOU-AR3-M0RS3)`

## Impact
- **Severe:** Leaving backup files (.bak, ~, .old) on a web server is a critical configuration error, leading to the full exposure of source code and any hardcoded sensitive information within.
- Gaining access to one account can provide a foothold for deeper attacks on the system.

## Remediation
- **Do Not Leave Junk/Backup Files:** Configure the web server (e.g., via `.htaccess` on Apache or `nginx.conf` on Nginx) to block access to files with common backup extensions. Implement a deployment process that cleans up unnecessary files.
- **Manage `robots.txt`:** Do not include sensitive paths in `robots.txt`. This file is only a "request" for search engine bots not to index paths; it is not a security measure.
- **Do Not Store Sensitive Information in Source Code:** Avoid hardcoding credentials. Use environment variables or secret management services instead.
