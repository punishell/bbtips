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
```

# Tools
```
Use exiftool to extract metadata from documents, it might reveal vulnerable htmltopdf generators
Use cloud_enum to find open google buckets or azure accounts 
Use Grep to extract endpoints with grep: grep -o -E '(https?://)?/?[{}a-z0-9A-Z_\.-]{2,}/[{}/a-z0-9A-Z_\.-]+'
```

# Payloads
```
Inject payloads in parameter names, ?<script>alerty</script>=true
Use youtube(olx, etc...) videos with xss in names.
Use round brackets to inject payload into valid e-mail address.
X-Forwarded-For: ${payload}
Use longstring parameters for stacktrace.
WAFBYPASS ?page=";confirm`1`//   Rightwards -> 302; ?pag%65=";confirm`1`//   Rightwards -> 200 + XSS!
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
Use blinf xss as password.
```






