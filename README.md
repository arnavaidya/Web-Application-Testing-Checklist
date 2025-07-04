# Web-Application-Testing-Checklist

## Standard Procedure for Web Application Testing:

### 1] Information Gathering:

#### Rendered Site Review

1) Manually explore the site.
   - Interact with the site as if you are a common user, exploring all possible paths.
     
2) Spider/crawl for missed or hidden content.
   - Use Burp Suite Spider or OWASP ZAP Spider.
     
3) Check the webserver metafiles for information leakage files that expose content, such as robots.txt, sitemap.xml, and .DS_Store.
   - Use dirbuster and/or dirsearch for enumeration.

      - dirb (Kali tool)
        
                 dirb http://testphp.vulnweb.com/   #Run with default wordlist
                 dirb http://example.com /usr/share/wordlists/dirb/big.txt  #Run with custom wordlist 'big.txt'

      - dirsearch (Kali tool)
        
                 dirsearch -u http://testphp.vulnweb.com

4) Check the caches of major search engines for publicly accessible sites.
   - The Internet Archive's 'Wayback Machine' archives websites over time. It allows you to view snapshots of a website from various points in the past, offering a rich source of historical information.
   - Google Dorking - try leveraging the “filetype” and “site” operators to find things like “/sitemap.xml” or “/robots.txt” on websites (https://www.exploit-db.com/google-hacking-database).

5) Check for differences in content based on user agent (e.g. mobile sites, accessing as a search engine crawler).

6) Check webpage comments and metadata for information leakage.

#### Development Review

1) Check the web application framework
   - whatweb (Kali tool)
     
                 whatweb https://www.example.com       #Basic scan
                 whatweb -v https://www.example.com    #Verbose
     
   - Wappalyzer (Online tool)
   - BuiltWith (Online tool)

2) Perform web application fingerprinting

   - There are several common locations to consider in order to identify frameworks or components:

      - HTTP headers: 'X-Powered-By' field in the HTTP response header
      - Cookies: Framework-specific cookies. e.g. Cookie: CAKEPHP = rm72......
      - HTML source code: Certain framework-specific paths can be found, i.e. links to framework-specific CSS or JS folders
      - Specific files and folders: dirbusting with Burp, checking robots.txt
      - File extensions: URLs may include file extensions like .php, .aspx, .jsp
      - Error messages: e.g. syntax error, unexpected end of file in /home/johnsmith/example.com/........
     
   OWASP documentation - [Web Application Fingerprinting guide](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/01-Information_Gathering/08-Fingerprint_Web_Application_Framework)
     
3) Identify technologies used

4) Identify user roles (admin, user, ....)

5) Identify application entry points

      - Example shows a GET request that would purchase an item from an online shopping application.

             GET https://x.x.x.x/shoppingApp/buyme.asp?CUSTOMERID=100&ITEM=z101a&PRICE=62.50&IP=x.x.x.x
             Host: x.x.x.x
             Cookie: SESSIONID=Z29vZCBqb2IgcGFkYXdhIG15IHVzZXJuYW1lIGlzIGZvbyBhbmQgcGFzc3dvcmQgaXMgYmFy

      - Result Expected: Here the tester would note all the parameters of the request such as CUSTOMERID, ITEM, PRICE, IP, and the Cookie (which could just be encoded parameters or used for session state).

   OWASP documentation - [Application entry points identification guide](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/01-Information_Gathering/06-Identify_Application_Entry_Points)

6) Identify client-side code

   OWASP documentation - [Client-side code guide](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/06-Testing_for_Client-side_Resource_Manipulation)

7) Identify multiple versions/channels (e.g. web, mobile web, mobile app)

#### Hosting and Platform Review

1) Identify web services
   - Discover APIs, REST/SOAP endpoints, or other web-accessible services.
      - Burp Suite (Proxy, Logger, Repeater, Extensions)
      - OWASP ZAP
      - Postman / curl (for manual requests)
      - JavaScript source inspection (DevTools → Sources tab)

2) Identify co-hosted and related applications
   - Discover other web apps/services hosted on the same server or network.
     - nmap -sV, whatweb to detect services on neighboring IPs.
     - Amass or Sublist3r to enumerate related subdomains.

3) Identify all hostnames and ports
   - Enumerate all domain names (subdomains) and ports (services) used by the app.
      - Amass
      - Sublist3r
      - Assetfinder
      - nmap, masscan
   
              #Run subdomain enumeration
              amass enum -d example.com
              #OR
              sublist3r -d example.com

              #Scan for open ports on discovered hostnames
              nmap -Pn -p- -T4 sub.example.com

5) Identify third-party hosted content
   - Detect scripts, media, or services hosted on external domains. e.g. cdn.jsdelivr.net, fonts.googleapis.com, api.stripe.com, etc.
      - BuiltWith, Wappalyzer
      - Burp Suite / ZAP (Inspect requests)
      - DevTools → Network tab

### 2] Configuration Management:

1) Check for commonly used application and administrative URLs
   - Examples include /admin, /login, /console, /manager, /administrator, /dashboard, and /backend
     
   OWASP documentation - [Common administrative URLs guide](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/05-Enumerate_Infrastructure_and_Application_Admin_Interfaces)

2) Check for old, backup, and unreferenced files
   - Unreferenced or forgotten files. e.g. viewdoc.old.jsp, myservlets.jar.old, login.asp.old, .....
   - Google Hacking/Docking
   - /robots.txt

   OWASP documentation - [Old, unreferenced files guide](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/04-Review_Old_Backup_and_Unreferenced_Files_for_Sensitive_Information)

4) Check HTTP methods supported and Cross Site Tracing (XST)
   - Approaches:

           #Approach 1:
           #Get supported HTTP methods           
           $ nc www.victim.com 80
            OPTIONS / HTTP/1.1
            Host: www.victim.com

            HTTP/1.1 200 OK
            Server: Microsoft-IIS/5.0
            Date: Tue, 31 Oct 2006 08:00:29 GMT
            Connection: close
            Allow: GET, HEAD, POST, TRACE, OPTIONS
            Content-Length: 0

                                OR

           #Approach 2:
           nmap -p 443 --script http-methods www.victim.com

   OWASP documentation - [HTTP methods guide](https://owasp.org/www-project-web-security-testing-guide/v41/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/06-Test_HTTP_Methods#:~:text=Test%20XST%20Potential%20*%20Leveraging%20another%20server%2Dside,that%20the%20attacker%20is%20trying%20to%20steal.)

5) Test file extensions handling
   - .asa, .inc, .config are files commonly containing sensitive information.
   - Tools like Nessus, Nikto check for the existence of well-known web directories.
   - wget
   - curl

   OWASP documentation - [File Extension testing guide](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/03-Test_File_Extensions_Handling_for_Sensitive_Information)

6) Test RIA cross domain policy
   - Example, if the application’s URL is `http://www.owasp.org`, try to download the files `http://www.owasp.org/crossdomain.xml` and `http://www.owasp.org/clientaccesspolicy.xml`.
     
   OWASP documentation - [RIA cross-domain policy guide](https://owasp.org/www-project-web-security-testing-guide/v41/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/08-Test_RIA_Cross_Domain_Policy)

7) Test for security HTTP headers (e.g. CSP, X-Frame-Options, HSTS)

   - wapiti (Kali tool)
   - https://securityheaders.com/ (Online tool)

   OWASP Cheatsheet - [HTTP Headers cheatsheet](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Headers_Cheat_Sheet.html)
   
   OWASP documentation - [HTTP Headers testing guide](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/14-Test_Other_HTTP_Security_Header_Misconfigurations)

9) Test for policies (e.g. Flash, Silverlight, robots)

10) Check for sensitive data in client-side code (e.g. API keys, credentials)

      - Sample finding: 

              <script type="application/json">
                ...
                {"GOOGLE_MAP_API_KEY":"AIzaSyDUEBnKgwiqMNpDplT6ozE4Z0XxuAbqDi4", "RECAPTCHA_KEY":"6LcPscEUiAAAAHOwwM3fGvIx9rsPYUq62uRhGjJ0"}
                ...
              </script>

     OWASP documentation - [Leaked API keys guide](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/01-Information_Gathering/05-Review_Web_Page_Content_for_Information_Leakage)

### 3] Secure Transmission:

#### Protocols and Encryption

1) Check SSL version, algorithms, and key length

   - Use nmap --script command for ssl scans.
  
           nmap --script ssl-cert example.com         #Gives issuer, key type, key length, algo, validity,.....
           nmap --script ssl-enum-ciphers example.com   #Gives protocol supported, cipher suites, cipher strength,..... 

2) Check for digital certificate validity (duration, signature, and CN)

         openssl s_client -connect <target>:443 -servername <target> | openssl x509 -noout -text

3) Check that credentials are only delivered over HTTPS

   - Check thst the login page only loads over 'https' and not 'http'.

4) Check that the login form is delivered over HTTPS

5) Check that session tokens are only delivered over HTTPS

6) Check if HTTP Strict Transport Security (HSTS) in use

7) Test ability to forge requests

8) Test web messaging (HTML5)

9) Check CORS implementation (HTML5)

#### Web Services and REST

1) Test for web service issues

2) Test REST

### 4] Authentication:

#### Application Password Functionality

1) Test password quality rules

2) Test remember me functionality

3) Test password reset and/or recovery

4) Test password change process

5) Test CAPTCHA

6) Test multi-factor authentication

7) Test for logout functionality presence

8) Test for default logins

9) Test for out-of-channel notification of account lockouts and successful password changes

10) Test for consistent authentication across applications with shared authentication schema/SSO and alternative channels

11) Test for weak security question/answer

#### Additional Authentication Functionality

1) Test for user enumeration

2) Test for authentication bypass

3) Test for brute force protection

4) Test for credentials transported over an encrypted channel

5) Test for cache management on HTTP (eg Pragma, Expires, Max-age)

6) Test for user-accessible authentication history

### 5] Authorization

1) Test for path traversal

2) Test for vertical access control problems (a.k.a. privilege escalation)

3) Test for horizontal access control problems (between two users at the same privilege level)

4) Test for missing authorization

5) Test for insecure direct object references

### 6] Session Management

1) Establish how session management is handled in the application (e.g., tokens in cookies, token in URL)

2) Check session tokens for cookie flags (HttpOnly and Secure)

3) Check session cookie scope (Path and Domain)

4) Check session cookie duration (Expires and Max-Age)

5) Check session termination after a maximum lifetime

6) Check session termination after a relative timeout

7) Check session termination after logout

8) Test to see if users can have multiple simultaneous sessions

9) Test session cookies for randomness

10) Confirm that new session tokens are issued on login, role change, and logout

11) Test for consistent session management across applications with shared session management

12) Test for session puzzling

13) Test for CSRF and clickjacking

### 7] Data Validation Testing

#### Injection

1) Test for HTML Injection  

2) Test for SQL Injection  

3) Test for LDAP Injection  

4) Test for ORM Injection  

5) Test for XML Injection  

6) Test for XXE Injection  

7) Test for SSI Injection  

8) Test for XPath Injection  

9) Test for XQuery Injection  

10) Test for IMAP/SMTP Injection  

11) Test for Code Injection  

12) Test for Expression Language Injection  

13) Test for Command Injection  

14) Test for NoSQL Injection

#### Other

1) Test for Reflected Cross Site Scripting  

2) Test for Race Condition  

3) Test for Stored Cross Site Scripting  

4) Test for DOM Based Cross Site Scripting  

5) Test for Cross Site Flashing  

6) Test for Overflow (Stack, Heap, and Integer)  

7) Test for Format String  

8) Test for Incubated Vulnerabilities  

9) Test for HTTP Splitting/Smuggling  

10) Test for HTTP Verb Tampering  

11) Test for Open Redirection  

12) Test for Local File Inclusion  

13) Test for Remote File Inclusion  

14) Compare Client-side and Server-side Validation Rules  

15) Test for HTTP Parameter Pollution  

16) Test for Auto-binding  

17) Test for Mass Assignment  

18) Test for NULL/Invalid Session Cookie  

19) Test for Integrity of Data  

20) Test for the Circumvention of Workflows  

21) Test Defenses Against Application Misuse  

22) Test That a Function or Feature Cannot Be Used Outside Of Limits  

23) Test for Process Timing  

24) Test for Web Storage SQL Injection (HTML5)  

25) Check Offline Web Application  

### 8] Error Handling

1) Analysis of Error Codes

2) Analysis of Stack Traces

### 9] Cryptography

1) Check if data which should be encrypted is not

2) Check for wrong algorithms usage depending on context

3) Check for weak algorithms usage

4) Check for proper use of salting

5) Check for randomness functions

### 10] Denial of Service

1) Test for anti-automation

2) Test for account lockout

3) Test for HTTP protocol DoS

4) Test for SQL wildcard DoS

### 11] Specific Risky Functionality

#### File Uploads

1) Test that acceptable file types are whitelisted and non-whitelisted types are rejected  

2) Test that file size limits, upload frequency, and total file counts are defined and are enforced  

3) Test that file contents match the defined file type  

4) Test that all file uploads have anti-virus scanning in place  

5) Test upload of malicious files  

6) Test that unsafe filenames are sanitized  

7) Test that uploaded files are not directly accessible within the web root  

8) Test that uploaded files are not served on the same hostname/port  

9) Test that files and other media are integrated with the authentication and authorization schemas  

#### Payments

1) Test for Known Vulnerabilities and Configuration Issues on Web Server and Web Application  

2) Test for Default or Guessable Password  

3) Test for Injection Vulnerabilities  

4) Test for Buffer Overflows  

5) Test for Insecure Cryptographic Storage  

6) Test for Insufficient Transport Layer Protection  

7) Test for Improper Error Handling  

8) Test for Server Side JavaScript Injection  

9) Test for All Vulnerabilities with a CVSS v2 Score > 4.0  

10) Test for Authentication and Authorization Issues  

11) Test for CSRF
