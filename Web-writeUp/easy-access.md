# Easy access

## Description:

Getting into this system should be easy, right? It's just a login form. Prove you have 'Easy access' by getting past it without knowing the real password.

## Initial Analysis

### Functionality Description
The website has a single login form with two input fields: Username and Password.

### Identifying Anomalies
As this is a classic login form with no other information available, the primary hypothesis is that the website is vulnerable to SQL Injection.

### Vulnerability Hypothesis
The data entered in the username and password fields is not properly handled and is inserted directly into an SQL query on the server-side, leading to an SQL Injection vulnerability.

## Exploitation Steps

1.  **Test for the Vulnerability (Error-based):**
    To confirm the hypothesis, we inject a special SQL character, the single quote (`'`), into one of the input fields. This character will break the SQL query's syntax if it is not properly escaped.
    - **Username:** `'`
    - **Password:** (leave blank or enter anything)
    - After clicking the login button, the website returns an SQL error message containing the keyword "MariaDB." This confirms the existence of the SQL Injection vulnerability 100% and reveals the database management system in use.


2.  **Construct a Payload to Bypass Login:**
    The goal is to construct an SQL statement that always evaluates to `true`, allowing the system to grant access without a valid password. A classic payload for this scenario is `' OR 1=1 --`.
    - **Payload Analysis:**
        - Assume the SQL query on the server looks like this: `SELECT * FROM users WHERE username = '[username]' AND password = '[password]'`
        - When we inject our payload into the username field, the query becomes: `SELECT * FROM users WHERE username = '' OR 1=1 --' AND password = ''`
        - The `OR 1=1` clause is always `true`.
        - The `--` (followed by a space) is the comment character in SQL, which will disable the rest of the query (`AND password = ''`).
        - As a result, the `WHERE` condition is always true, and the system will return the first user record in the table (usually the admin) and grant access.

    ![image](https://github.com/user-attachments/assets/a0e4c8a6-16a3-4c13-9ca5-05644f4d0cb3)

3.  **Log In and Get the Flag:**
    - **Username:** `' OR 1=1 -- ` (note the space at the end)
    - **Password:** (leave blank or enter anything)
    - After clicking login, the system will authenticate successfully and redirect to a page containing the flag.

    ![image](https://github.com/user-attachments/assets/d5cff33c-697a-4983-89e2-164d6552ed69)

    **Flag:** flag{!njection_3v3ry_wh3r3}


## Impact
- **Critical:** An SQL Injection vulnerability in a login form allows an attacker to completely bypass the authentication mechanism.
- An attacker can log in to any account in the system, including the administrator's, without knowing the password.
- Depending on the SQL query, an attacker could exploit it further to extract the entire database (dump the database).

## Remediation
- **Use Prepared Statements (Parameterized Queries):** This is the most effective and recommended method to prevent SQL Injection. With this approach, user input is always treated as raw data and is never executed as part of the SQL command.
- **Use an ORM (Object-Relational Mapping):** Modern ORM libraries (like Eloquent in Laravel or Doctrine in Symfony) have built-in mechanisms that use prepared statements, helping developers avoid common pitfalls.
- **Input Sanitization/Validation:** While not the primary defense, validating input (e.g., ensuring a username contains only alphanumeric characters) can help reduce the attack surface.
- **Hide Detailed Error Messages:** Configure the server not to display detailed database error messages to end-users, as they provide valuable information to attackers.
