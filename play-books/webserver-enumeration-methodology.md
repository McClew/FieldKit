---
icon: globe-pointer
layout:
  width: default
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: false
  metadata:
    visible: true
---

# Webserver Enumeration Methodology

## Passive Recon

1. Look at public [dns.md](../field-manual/intelligence/network-enumeration/dns.md "mention") records for domains and subdomains to enumerate.
2. Look at certificates and other public data for domains and subdomains to enumerate.

## Active Recon

1. Add domain(s) to **/etc/hosts** file.
2. Run subdomain / vhost brute force discovery.
3. Look in [#robots.txt](../field-manual/intelligence/web-enumeration/crawling-spidering.md#robots.txt "mention") or sitemaps.xml for hidden endpoints.
4. Use [crawling-spidering.md](../field-manual/intelligence/web-enumeration/crawling-spidering.md "mention") techniques to find more paths.
5. Conduct directory and page brute force discovery.
   * Use tools like [ffuf.md](../toolbox/tooling/web-application-analysis/ffuf.md "mention").
   * Fuzz for extensions.
   * Fuzz for directories and pages - with extensions if found.
6. Look for comments in HTML for sensitive information.
   * Check all crawled and discovered pages.
7. Capture server errors ( e.g. 500, 403 ) that might leak tech stack information.
8. Look for vulnerabilities in web server technologies used.
   * Use Wappalyzer to discover web server technologies in browser.
   * Use BuiltWith to discover web server technologies ( external only ).
   * Use WhatsWeb CLI tool to discover web server technologies.
   * Scan the webserver with [nikto.md](../toolbox/tooling/vulnerability-analysis/nikto.md "mention") to discover web technologies and vulnerabilities.
   * Scan the website with [nmap](../toolbox/tooling/information-gathering/network-enumeration/nmap/ "mention"), using web discovery scripts.
   * Attempt [banner-grabbing.md](../field-manual/intelligence/port-and-service-enumeration/banner-grabbing.md "mention") the webserver using curl.
   * Discover [web-application-firewalls.md](../field-manual/intelligence/web-enumeration/web-application-firewalls.md "mention") ( WAFs ) using [wafw00f.md](../toolbox/tooling/web-application-analysis/wafw00f.md "mention").
9. Look for web service versions on discovered pages. Use enumeration techniques specific to the technology:
   * Look for CNS or app-specific files ( wp-content, .git/, etc. ).
   * [application-enumeration](../field-manual/intelligence/application-enumeration/ "mention")
10. Look for vulnerabilities in discovered web service versions and technologies.
    * Search [metasploit](../toolbox/tooling/exploitation-tools/metasploit/ "mention") for service exploits.
    * Search [ExploitDB](https://www.exploit-db.com/) for service exploits.
11. Look for login pages to test default or weak credentials.
    * Default credentials lists.
    * [brute-force](../field-manual/exploitation/brute-force/ "mention") logins with tools like [burp-suite.md](../toolbox/tooling/web-application-analysis/burp-suite.md "mention").
    * Check for password reset or recovery mechanisms.
    * Review cookies and sessions ( flags like Secure, HttpOnly ).
    * Test for session fixation or missing CSRF protections.
12. Look for input fields to submit data.
13. Test submitting data on _**EVERY user input field**_ and looking at interaction with [burp-suite.md](../toolbox/tooling/web-application-analysis/burp-suite.md "mention").
    * If input appears to be used in a system command, test for [command-injections.md](../field-manual/exploitation/web-attacks/command-injections.md "mention").
    * If a file upload function exists, test for [file-upload.md](../field-manual/exploitation/web-attacks/file-upload.md "mention") vulnerabilities.
    * If the webserver appears to be populating data from a database, test for [sql-injection-sqli.md](../field-manual/exploitation/web-attacks/sql-injection-sqli.md "mention").
    * If URLs use sequential values for data retrieval, test for [insecure-direct-object-references-idor.md](../field-manual/exploitation/web-attacks/insecure-direct-object-references-idor.md "mention") vulnerabilities.
    * If local files are retrieved based on dynamic inputs, test for [file-inclusion.md](../field-manual/exploitation/web-attacks/file-inclusion.md "mention") and path traversal vulnerabilities.
    * Check user input fields for [cross-site-scripting-xss.md](../field-manual/exploitation/web-attacks/cross-site-scripting-xss.md "mention") vulnerabilities.
    * Test HTTP requests for [http-verb-tampering.md](../field-manual/exploitation/web-attacks/http-verb-tampering.md "mention") vulnerabilities to bypass access controls or injection validations.
    * If XML input is accepted, test for [xml-external-entity-xxe-injection.md](../field-manual/exploitation/web-attacks/xml-external-entity-xxe-injection.md "mention").
14. If the webserver has a web socket or API that is reachable, check if requests are injectable with [sqlmap.md](../toolbox/tooling/database-assessment/sqlmap.md "mention").
    1. Websocket / API injection.
