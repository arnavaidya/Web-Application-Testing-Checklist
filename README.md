# Web-Application-Testing-Checklist

## Standard Procedure for Web Application Testing:

### 1] Information Gathering:

1) Manually explore the site.
   - Interact with the site as if you are a common user, exploring all possible paths.
     
2) Spider/crawl for missed or hidden content.
   - Use Burp Suite Spider or OWASP ZAP Spider.
     
3) Check the webserver metafiles for information leakage files that expose content, such as robots.txt, sitemap.xml, and .DS_Store.
   - Use dirbuster and/or dirsearch for enumeration.

     dirb http://testphp.vulnweb.com/   #Run with default wordlist
     dirb http://example.com /usr/share/wordlists/dirb/big.txt  #Run with custom wordlist 'big.txt'

4) Check the caches of major search engines for publicly accessible sites.

5) Check for differences in content based on user agent (e.g. mobile sites, accessing as a search engine crawler).

6) Check webpage comments and metadata for information leakage.
