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

4) Identify user roles (admin, user,....)

5) Identify application entry points

6) Identify client-side code

7) Identify multiple versions/channels (e.g. web, mobile web, mobile app)
