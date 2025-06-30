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
     
   (https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/01-Information_Gathering/08-Fingerprint_Web_Application_Framework)
     
3) Identify technologies used

4) Identify user roles (admin, user, ....)

5) Identify application entry points

      - Example shows a GET request that would purchase an item from an online shopping application.

             GET https://x.x.x.x/shoppingApp/buyme.asp?CUSTOMERID=100&ITEM=z101a&PRICE=62.50&IP=x.x.x.x
             Host: x.x.x.x
             Cookie: SESSIONID=Z29vZCBqb2IgcGFkYXdhIG15IHVzZXJuYW1lIGlzIGZvbyBhbmQgcGFzc3dvcmQgaXMgYmFy

      - Result Expected: Here the tester would note all the parameters of the request such as CUSTOMERID, ITEM, PRICE, IP, and the Cookie (which could just be encoded parameters or used for session state).

   (https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/01-Information_Gathering/06-Identify_Application_Entry_Points)

6) Identify client-side code

      (https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/06-Testing_for_Client-side_Resource_Manipulation)

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
     
   (https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/05-Enumerate_Infrastructure_and_Application_Admin_Interfaces)

2) Check for old, backup, and unreferenced files
   - Unreferenced or forgotten files. e.g. viewdoc.old.jsp, myservlets.jar.old, login.asp.old, .....
   - Google Hacking/Docking
   - /robots.txt

   (https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/02-Configuration_and_Deployment_Management_Testing/04-Review_Old_Backup_and_Unreferenced_Files_for_Sensitive_Information)

4) Check HTTP methods supported and Cross Site Tracing (XST)

5) Test file extensions handling

6) Test RIA cross domain policy

7) Test for security HTTP headers (e.g. CSP, X-Frame-Options, HSTS)

8) Test for policies (e.g. Flash, Silverlight, robots)

9) Check for sensitive data in client-side code (e.g. API keys, credentials)
