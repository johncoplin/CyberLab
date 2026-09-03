# Brute Force - DVWA (Low)

**Summary:** The application allows users unlimited attempts to enter valid credentials into the log-in system. This combined with the weak password attached to the administrator account makes the application very susceptible to brute force attacks.

**Severity:** High - attackers can easily brute force the system

**Affected component:** The login system

**Description**: The current system has no rate limiter, no CAPTCHA, and doesn't stop the user from guessing passwords after a number of wrong guesses. This makes it very easy to attack the application via brute force.

**Steps to reproduce**: 
1. Attempt to log in to the application using `admin` as the username and a password of your choice. Use Burp to catch the request this generates.
2. In Intruder Mode, find your chosen password after `password=` in the HTML and enter a payload of passwords. Make sure `password` is included in this payload. 
3. When the attack completes, note that all incorrect guesses have a response length between 4861 and 4862 while `password` has a length of 4900 - this shows that `password` is the correct password and that the system can be brute-forced.

**Impact**: Because the system allows for brute-force attacks and the admin password is so easily guessed, attackers can easily gain access to the admin account.

**Remediation**: A rate limiter should be added to the system to stop users from sending in lists of password guesses as a brute-force attack. A CAPTCHA system would also prevent this by ensuring that each guess is being entered by a person in real time. Finally, a system which locks the user out for a given duration after a select number of guesses would greatly improve the security of the application against this kind of attack.

**References**:
- CWE-307: Improper Restriction of Excessive Authentication Attempts — https://cwe.mitre.org/data/definitions/307.html
- OWASP Top 10 (2021) A07: Identification and Authentication Failures — https://owasp.org/Top10/A07_2021-Identification_and_Authentication_Failures/
- OWASP Authentication Cheat Sheet — https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html
