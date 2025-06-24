# Share the Ideas

## Description:
We've created a new platform for security enthusiasts to share their ideas. Register an account, log in, and see what others are posting. Can you share an idea that the system wasn't expecting?

## Initial Analysis

### Functionality Description
The website is a simple forum that requires users to register and log in to post comments.

### Identifying Anomalies
After logging in and submitting a comment, it's clear this function is the main point of interaction with the database. When a special character like a single quote (`'`) is inserted into the comment, the website returns an SQL error message. This confirms the existence of an SQL Injection vulnerability. The error message also suggests the backend might be using **SQLite**.

### Vulnerability Hypothesis
The application is vulnerable to SQL Injection in the comment submission feature. User input is not properly sanitized or escaped before being included in an SQL query.

## Exploitation Steps

1.  **Create an Account and Log In:** Register for an account and log in to gain permission to post comments.

2.  **Confirm the Vulnerability:** Submit a comment containing only a single quote: `'`. The site will return an SQL error, confirming the hypothesis.

3.  **Extract SQLite Version:** Use a payload to retrieve the database version.

    **Payload:** `a' || (select sqlite_version());--`

    **Result:** The website displays the SQLite version (e.g., `a3.8.7.1`).
    ![image](https://github.com/user-attachments/assets/1d94dbac-c181-4322-a9c7-665a30752502)


4.  **List Table Names:** Use a payload to get the schema of tables in the database from the `sqlite_master` table.

    **Payload:** `a' || (select sql from sqlite_master);--`

    **Result:** The website displays the `CREATE TABLE` statement for the `xde43_users` table, revealing its structure and column names.
    ![image](https://github.com/user-attachments/assets/eb113e6f-cf67-46d7-84ec-cdc01fa7bb3c)


5.  **Retrieve Admin's Password:** Based on the table structure, construct a payload to extract the password of the user with the `admin` role.

    **Payload:** `a' || (select password from xde43_users where role="admin");--`

    **Result:** The admin's password (which is the flag) is displayed in the comment.
    ![image](https://github.com/user-attachments/assets/1eb061c5-7f5f-4cd2-ab04-7144f78eca6f)

   **Flag:** 245698

## Impact
- **Severe:** An attacker can read the entire database, including sensitive user information (usernames, emails, passwords).
- It can lead to an admin account takeover and full control of the application.

## Remediation
- **Use Prepared Statements (Parameterized Queries):** This is the most effective way to prevent SQL Injection. User input is always treated as data, never as part of the SQL command.
- **Validate and Sanitize Input:** Filter and clean user-provided data to remove malicious characters.
- **Hide Detailed Error Messages:** Configure the server not to display detailed error messages to end-users, as they can reveal information about the system's architecture.
