# Reflected XSS - DVWA (Low)

**Summary:** The current application directly appends the user's input to the page's HTML to generate a response. This means the user can add their own HTML and JavaScript code to the application input.

**Severity:** Critical - user can extract cookies and log in as other users without needing additional authentication

**Affected component:** The "What's your name?" input box

**Description**: The application greets the user by asking for their name, and responds with a personalised greeting by incorporating their input into the HTML/JavaScript code. As a result, the user can write HTML or JavaScript tags besides their input, and the page will treat it as legitimate HTML/JavaScript code.

**Steps to reproduce**: 
1. Enter `<b>test</b>` into the input box. This confirms that HTML tags can be injected into it, as "test" will appear on the page in bold like the tags indicate the page to do.
2. Enter `<script>alert('XSS')</script>` into the same box. This will cause a page alert that says "XSS", showing that the user can inject their own JavaScript code onto the page.

**Impact**: Because the user can run JavaScript code on the application, they could write a program which steals the user's cookies. Because this payload runs on the real application rather than a fake clone, a link to the site with this added payload can then be spread to other users to steal their cookies, allowing the attacker to log in as other users completely bypassing additional security measures like passwords and MFA.

**Remediation**: Applying output encoding would cause the tags to be displayed as literal text inside the HTML page, stopping code from being injected like this. Input validation could also be implemented as a secondary security measure, and HttpOnly cookies and CSP would allow for extra defense if an XSS attack breaks through the other measures.

**References**:
- CWE-79: Improper Neutralization of Input During Web Page Generation (Cross-site Scripting) — https://cwe.mitre.org/data/definitions/79.html
- OWASP Top 10 (2021) A03: Injection — https://owasp.org/Top10/A03_2021-Injection/
- OWASP Cross Site Scripting Prevention Cheat Sheet — https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html
