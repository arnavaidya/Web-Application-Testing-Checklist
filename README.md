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
