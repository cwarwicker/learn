# Open Web Application Security Project (OWASP)

## Top 10

The OWASP Top Ten is a broad consensu of the top 10 most critical security risks to web applications.

### A01:2021 - Broken Access Control
Access control enforces policy such that users cannot act outside their intended permissions.

#### Common vulnerabilities include:
- Violation of Principle of Least Privilege
- Bypassing access controls by modification of data such as URLs
- Elevation of privilege (acting as a user without being logged in as them)
- etc...

#### How to prevent:
- Principle of Least Privilege (Deny by default)
- Validate permissions on each request
- Disable web server directory listing and ensure non-public files are not present in web roots (e.g. .git files)
- Rate limit API tools to minimize automated attacks
- Invalidate stateful sessions after logout. Stateless tokens (e.g. JWT) should be short-lived.

#### More info
- https://owasp.org/Top10/A01_2021-Broken_Access_Control/

### A02:2021 - Cryptographic Failures
Broad term related to failures of lack of crytopgraphy to protect sensitive data.

#### Common vulnerabilities include:
- Sending/storing sensitive data in plain text (e.g. passwords, credit card details, etc...)
- Using weak/old algorithms (e.g. MD5)
- Not validating server certificate/trust chain
- etc...

#### How to prevent:
- Identify which data is sensitive according to privacy laws/business needs
- Don't store sensitive data unnecessarily
- Encrypt all sensitive data at rest
- Encrypt all data in transit
- Use strong, up-to-date algorithms and protocols
- Disable caching responses which contain sensitive data

#### More info
- https://owasp.org/Top10/A02_2021-Cryptographic_Failures/



### A03:2021 - Injection
Injection (SQL) is when SQL queries are run without sanitising the user-supplied data, which can lead to unintended outcomes, or potentially hostile actions by the user.

#### How to prevent:
- When running DB queries, use PDO or ORMs so that all data is passed as parameters safely
- For dynamic queries, escape and sanitise any data before it hits the database
- Use LIMIT and other SQL controls to prevent access to mass records where not required

#### More info
- https://owasp.org/Top10/A03_2021-Injection/


### A04:2021 - Insecure Design
Insecure Design is a broad term for the design flaws, such as lack of security controls. It is different from insecure implementation, as the design itself never accounted for certain security issues.

#### How to prevent:
- Establish and use secure development lifecycle to evaluate design security and privacy controls
- Establsh and use library of secure design patterns
- Use threat modelling
- Integrate security language and controls into user stories
- Integration tests to validate all critical flows are resistent to threats
- etc...

#### More info
- https://owasp.org/Top10/A04_2021-Insecure_Design/

### A05:2021 - Security Misconfiguration
Security Misconfiguration is when a system is configured in such a way that allows potential threats through to the application/database/infrastructure/etc... layers.

#### Common vulnerabilities include:
- Missing or improperly configured permissions
- Unneccessary features enabled or installed (e.g. ports, accounts, etc...)
- Default accounts unchanged/enabled
- Error handling reveals stack traces to users
- Out of date software
- etc...

#### How to prevent:
- Have a repeatable hardening process, common across all production, QA, development environments. Ideally automated
- Minimal platforms. Do not install features, libraries, etc... that are not needed
- Sending security directives to clients, e.g. security headers
- Automated process to verifiy effectiveness of configuraitons and settings in all environments

#### More info
- https://owasp.org/Top10/A05_2021-Security_Misconfiguration/

### A06:2021 - Vulnerable & Outdated Components
Use of out-of-date libraries/components, which may have vulnerabilities which have been patched in newer versions.

#### How to prevent:
- Removed unused dependencies, features, components, files, etc...
- Continuously monitor client and server side components for vulnerabilities and security updates
- Only obtain components from official sources

#### More info
- https://owasp.org/Top10/A06_2021-Vulnerable_and_Outdated_Components/


### A07:2021 - Identification & Authentication Failures
Failure to properly identify and authenticate users to ensure only the correct accounts have access to the correct resources/data.

#### How to prevent:
- Where possible, implement MFA to prevent brute force, stolen credentials, etc...
- Change/remove all default accounts
- Implement password strength checks
- Ensure registration, login, password recovery, etc... have the same message outcomes regardless of state, e.g. don't tell the user that email address doesn't exist
- Limit or delay failed login attempts. Log all failures and alert on detected brute force attacks
- Use session manager with random ID on each login. Invalidate after logout. Don't include in any URLs or source code.

#### More info
- https://owasp.org/Top10/A07_2021-Identification_and_Authentication_Failures/

### A08:2021 - Software & Data Integrity Failures
Code and infrastructure that does not protect against integrity violations. E.g. an application which relies on plugins from untrusted sources, or CDNs.

#### How to prevent:
- Use digital signatures to verify software/data
- Ensure libraries and dependencies are using trusted repositories
- Check dependencies for vulnerabilities
- Ensure there is a review process for code/config which would introduce malicious data
- Ensure CI/CD pipelines have proper segregation, configuration, access controls

#### More info
- https://owasp.org/Top10/A08_2021-Software_and_Data_Integrity_Failures/


### A09:2021 - Security Logging and Monitoring Failures
Lack of, or insufficient, logging and monitoring to detect breaches and other issues.

#### How to prevent:
- Ensure all login, access control, server-side inpuot validation failures are logged, with sufficient user context
- Ensure logs are generated in a format that can be easily consumed
- Ensure log data is encoded to prevent injections or attacks in the logging/monitoring system
- Ensure high-value transactions have audit trails
- Automated alerts for suspicious activities
- Adopt an incident response/recovery plan

#### More info
- https://owasp.org/Top10/A09_2021-Security_Logging_and_Monitoring_Failures/


### A10:2021 - Server-Side Request Forgery (SSRF)
SSRF flaws occur when a web application fetches remote resources without validating user-supplied URLs, allowing users to send requests to unexpected destinations.

#### How to prevent:
- Enforce "deny by default" firewall/network policies
- Sanitise and validate all client-supplied data
- Enforce URL schema with whitelist
- Disable HTTP redirections
- etc...

#### More info
- https://owasp.org/Top10/A10_2021-Server-Side_Request_Forgery_%28SSRF%29/



## Useful Links
- https://owasp.org/
