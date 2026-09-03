# SQL Injection - DVWA (Low)

**Summary:** The current User ID lookup system takes the user's input and puts it into an SQL query. This means users can overload this system to run any SQL query they want on the database, allowing the user to read all the information it holds.

**Severity:** High - user can extract all credentials in database

**Affected component:** The 'id' parameter

**Description**: Because the current system uses concatenation, the database has no way of distinguishing user input from the prewritten query, meaning that anything the user writes is treated like it is part of that query. Therefore, if the user can work out how to break out of the input box they can access more of the database than they should be able to.

**Steps to reproduce**: 
1. Enter `'` at the start of input in the User ID field. This returns an SQL syntax error, confirming it's injectable. Payloads are terminated by ending input with `-- ` or `#` to comment out the closing quote.
2. Enter `ORDER BY` between the first and ending sets of characters with 1 and keep counting up until you reach 3 - this will return an error as there is no third column to sort by, confirming there are only two columns.
3. Enter `UNION SELECT user, password FROM users -- ` - this will list every user's username and hashed password.

**Impact**: The user can run any SQL query they want on the database, allowing them to access sensitive user information like usernames and hashed passwords. As these passwords are stored as MD5 hashes, the user can easily crack the passwords and make them usable.

**Remediation**: Using parameterised queries would fix this vulnerability, as this way the SQL query would be parsed separately from the data the user enters. This means that the user has no easy way of 'escaping' the limited input box like this, as the code cannot reach the database through a string. The app should also be modified to not show the user all of the details of any errors that occur if an erroneous search is made. Finally, the passwords should be stored using a slow, salted algorithm instead of MD5.

**References**: 
- CWE-89: Improper Neutralization of Special Elements used in an SQL Command — https://cwe.mitre.org/data/definitions/89.html
- OWASP Top 10 (2021) A03: Injection — https://owasp.org/Top10/A03_2021-Injection/
- OWASP SQL Injection Prevention Cheat Sheet — https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html
