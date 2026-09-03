# File Inclusion - DVWA (Low)

**Summary:** The application doesn't check that the user is permitted to access a given directory within the server the application is hosted on when accepting page parameters, allowing users to access files which should be restricted from them.

**Severity:** Critical - attackers can read sensitive files on the application and potentially execute their own code through it.

**Affected component:** The page parameter

**Description**: The user can access different pages on the application by editing the page parameter. As the application has no way of checking whether a given user is allowed to access a given page, this means that attackers can use file traversal techniques to go to the root of the server directory and access all of its information.

**Steps to reproduce**: 
1. Go to one of the three available pages by clicking on their associated link.
2. Insert the payload `../../../../../../etc/passwd` in place of the page parameter in the URL and search again. The application will show the contents of `etc/passwd` in text above the application interface.

**Impact**: Because this allows the user to view files anywhere on the server, attackers can potentially uncover information such as source code and database credentials. It's possible that this vulnerability could even result in unwanted code being executed on the application through methods like log poisoning or session file inclusion.

**Remediation**: Creating an allowlist containing only the files a user should have access to should resolve this problem, as this will stop user input from ever directly forming a path. Additionally, path traversal sequences should be rejected.

**References**:
- CWE-98: Improper Control of Filename for Include/Require Statement in PHP Program ('PHP Remote File Inclusion') — https://cwe.mitre.org/data/definitions/98.html
- CWE-22: Improper Limitation of a Pathname to a Restricted Directory ('Path Traversal') — https://cwe.mitre.org/data/definitions/22.html
- OWASP Top 10 (2021) A01: Broken Access Control — https://owasp.org/Top10/A01_2021-Broken_Access_Control/
- OWASP File Inclusion / Path Traversal guidance (WSTG) — https://owasp.org/www-project-web-security-testing-guide/