# Security tips

1. Verify all the inputs of the system syntactically and semantically. Treat all data sources as untrusted, unless there is a clear and compelling reason to trust. The input validation should be done as early as possible.
    * Syntactic validation: the structure of the input is as expected. For example if the format of the input date is YYYY-MM-DD, the application should enforce this format by explicit checks, and discard any other input formats.
    * Semantic validation: the meaning of the input is as expected. For example the end date must be after of the start date.
2. Use prepared statements with parameterized queries to access the database. Since the untrusted user input may contain raw SQL statements, concatenating string queries based on the user input may alter the logic of the application, which can result in SQL injection attack, and compromise the whole database/system.
3. Use output encoding and escaping. Encoding is basically translating characters into another set of equivalent characters that is no longer dangerous, for example translating <  into \&lt; . Escaping is adding a character before a certain character or string to aid the interpret to understand it correctly. For example adding \  before double quote. Lack of proper output encoding and escaping can result in XSS (Cross Site Scripting) attack. The output should be encoded to the correct context on its way out by the correct encoder. For example URI encoder for URIs and text encoder for texts.
4. Configure XML parsers to explicitly ignore DTDs (Document Type Definitions). XML is able to load external entities such as images, documents and etc. If the parser is weakly configured, this may result in XEE (XML External Entity) attack. This attack may lead to disclosure of confidential data, denial of service and etc. The XML parser should be configured to explicitly ignore all the DTDs.
5. Avoid deserializing objects via the built-in Java mechanism if you can. Serialization is the process of turning some object into a binary data format that can be restored later. Deserialization is the reverse of that process, taking data structured from some format, and rebuilding it into an object. Malformed or unexpected data could be used to execute arbitrary code, when deserialized. Instead of using the built-in Java mechanism for serialization, use the alternatives, such as JSON.
6. Protect the application against CSRF attack. Cross-Site Request Forgery (CSRF) is an attack that forces a user to execute unwanted actions on a web application in which they’re currently authenticated. CSRF inherits the identity and privileges of the victim to perform an undesired function on the victim’s behalf. The frameworks that are used for web applications (e.g. Spring) have this mechanism enabled by default. The application designer would need to for example use a secret cookie to ensure that the request is not forged by a third-party actor.
7. Authenticate anyone who can access the application. Authentication is the process of validating the claimed identity. In other words, authentication just focuses on who the user is. Different types of authentication:
    * Basic: send the credentials with every request. Simple, no logout mechanism.
    * Form-based: login once via a form and establish a session. Client side simplicity,logout mechanism, session established and stored on server's memory.
    * Token-based:
        * Bearer: server creates a token in string form and sends it to the client. With every request client sends the token back, server looks it up in a DB and proceeds if the token matches.
        * JWT: server creates JWT with a secret and sends the JWT to the client. The client stores the JWT and includes it in the header with every request. If the JWT is tampered, it becomes invalid because of the JWT hashing. The server would then validate the JWT with every request from the client and sends response.
    * External-based:
        * OAuth: a higher-trust service authorizes a lower-trust service to login without disclosing the credentials.
        * OpenID Connect: adds a layer on top of OAuth to provide consistency and additional security.
8. Authorize users with the least needed privilege. In simple words, users should be able to access the least possible resources that they need. Different types of authorization:
    * Simple: upon successful authentication, users are allowed to access everything on the system.
    * Role-based: upon successful authentication, users will be able to access only the resources that are defined based on their role.
9. Use TLS for secure data transfer. Without TLS everyone within the path between client and server could see the contents of your packets. Note that TLS is not the same as SSL. Secure Socket Layer (SSL) was the original protocol, but after a few releases, it was changed to TLS. The recommended version to use is TLS 1.2.
10. Log the system activity. Without logging the system has no visibility and thus debugging and diagnostic will be compromised. Any sensitive information needs to be excluded from logging: application source code, credentials, database connection settings and etc. The events that should be logged:
    * Input validation failures e.g. protocol violations, unacceptable encodings,
    invalid parameter names and values
    * Output validation failures e.g. database record set mismatch, invalid data encoding
    * Authentication successes and failures
    * Authorization (access control) failures
    * Sequencing failure
    * Excessive use
    * Data changes
    * Other non-sensitive information that could be useful for debugging and diagnostic.
11. Avoid storing the passwords in plain text. Passwords should never be stored in reversible formats and the chosen method for storing them should make it as hard as possible to perform brute force attack. Hashing the password and storing them with MD5, SHA1, SHA256 is not secure enough. Hash calculation for these algorithms takes relatively short amount of time, thus opening up the window for brute force vulnerability. Password should be created with random salt based on password-based key derivation algorithms (e.g. Argon2).
12. Fail securely. In general, the security mechanism should be designed in a way so that a failure will follow the same execution path as disallowing the operation. For example, security methods like isAuthorized() , isAuthenticated() , and validate()  should all return false if there is an exception during processing.
13. "Shift left" the security to ensure it is a built-in feature throughout the entire development process. Security cannot be achieved if it becomes a concern after completion of application development. Security should be a concern from the very beginning of the system design.