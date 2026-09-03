# SQL Injection - DVWA (Medium)

**Summary:** The dropdown user lookup system can be injected with inputs outside of those listed on the dropdown. Because the application handles these values as raw SQL, the user can run any SQL query they want on the database.

**Severity:** High - user can extract all database contents

**Affected component:** The 'id' parameter

**Description**: Because the current system adds the user's input as raw SQL for a parameter, the user can write a valid unquoted numeric value and then append their own SQL queries afterward. As a result, the quote-escaping protection method does not prevent injection.

**Steps to reproduce**: 
1. Use Burp Suite to catch a User ID lookup request.
2. Find the ID parameter and edit it to contain `1'` - this will cause the application to return an SQL error, but given that the error lists a backslash, it shows the input box does have escaping protection measures against quotation marks.
3. Edit the ID parameter so it contains `1%20OR%201=1` - this allows the dropdown and escaping to be bypassed.

**Impact**: The user can run their own SQL queries on the database (e.g. extracting all first and last names at once), which means they could use UNION to extract any data they want from the database.

**Remediation**: This finding shows escaping user input to be an insufficient defence - using parameterised queries would fix this vulnerability, as this way the SQL query would be parsed separately from the data the user enters. This would stop the user from being able to enter their own SQL queries regardless of how they are formatted. The app should also be modified to not show the user all of the details of any errors that occur if an erroneous search is made. Finally, the passwords should be stored using a slow, salted algorithm instead of MD5.

**References**: 
- CWE-89: Improper Neutralization of Special Elements used in an SQL Command — https://cwe.mitre.org/data/definitions/89.html
- OWASP Top 10 (2021) A03: Injection — https://owasp.org/Top10/A03_2021-Injection/
- OWASP SQL Injection Prevention Cheat Sheet — https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html
