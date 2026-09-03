# Weak Session ID - DVWA (Low)

**Summary:** The application generates session IDs in a sequential order. As a result, attackers can easily predict the session IDs of users and use them to access the application as those users.

**Severity:** Critical - attackers can bypass login and use the application using another user's account

**Affected component:** The Session ID generator

**Description**: The application generates Session IDs in a sequential order. As a result, attackers can easily predict other session IDs due to their lack of entropy - by generating their own session ID, since any number which comes before that of their own session ID will be valid they can use these predicted session IDs to hijack another user's session on the application.

**Steps to reproduce**: 
1. Generate a Session ID, and use Burp to intercept the request. This will show you the Session ID you have generated (the `dvwaSession` cookie).
2. Generate another Session ID and catch this request. The Session ID will be 1 incremented from the value of the previous Session ID.
3. Generate another Session ID and catch this request. The Session ID will be 1 incremented from the value of the previous Session ID, proving that Session IDs are generated in a sequential order.
4. Setting the cookie to a value lower than the one generated would theoretically allow the user to access the application using a session belonging to another user.

**Impact**: As these tokens are predictable, an attacker can easily hijack other sessions by editing their Session ID within Burp to be one of the existing ones, which they can find out about by simply generating their own Session ID (since all valid Session IDs will have a lower value than the current one).

**Remediation**: To fix this vulnerability, a new Session ID generation system must be implemented which uses a CSPRNG that has enough entropy to make them unpredictable, ideally one that generates Session IDs which are long, use a wide variety of different characters, and are completely random.

**References**:
- CWE-330: Use of Insufficiently Random Values — https://cwe.mitre.org/data/definitions/330.html
- OWASP Top 10 (2021) A07: Identification and Authentication Failures — https://owasp.org/Top10/A07_2021-Identification_and_Authentication_Failures/
- OWASP Session Management Cheat Sheet — https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html
