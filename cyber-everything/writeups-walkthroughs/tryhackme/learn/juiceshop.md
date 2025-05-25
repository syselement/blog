# Juice Shop

![tryhackme.com - © TryHackMe](.gitbook/assets/tryhackme-logo-small.png)

---

## 🌐 Resources 🔗

> - [TryHackMe | OWASP Juice Shop](https://tryhackme.com/r/room/owaspjuiceshop)
> - [OWASP Top 10:2021](https://owasp.org/Top10/)
>   - [OWASP Top Ten 2017](https://owasp.org/www-project-top-ten/2017/)

---

## Enumeration

```bash
accountant@juice-sh.op
admin@juice-sh.op
bender@juice-sh.op
jim@juice-sh.op
morty@juice-sh.op
mc.safesearch@juice-sh.op
stan@juice-sh.op
uvogin@juice-sh.op

# Registered users
admin@juice-sh.op
jim@juice-sh.op
bender@juice-sh.op
bjoern.kimminich@gmail.com
ciso@juice-sh.op
support@juice-sh.op
morty@juice-sh.op
mc.safesearch@juice-sh.op
J12934@juice-sh.op
wurstbrot@juice-sh.op
amy@juice-sh.op
bjoern@juice-sh.op
bjoern@owasp.org
accountant@juice-sh.op
uvogin@juice-sh.op
demo
```

```bash
0 st4rs f0r 7h3 h0rr1bl3 s3cur17y
K33p5 y0ur ju1cy 5plu773r 70 y0ur53lf!
y0ur f1r3wall needs m0r3 musc13
```

---


## [SQL Injection](https://owasp.org/Top10/A03_2021-Injection/) - Login

- Admin - request:

`{"email":"' or 1=1--","password":"aa"}`

- bender@juice-sh.op - request:

`{"email":"bender@juice-sh.op'--","password":"a"}`

---

## [Broken Authentication](https://owasp.org/Top10/A07_2021-Identification_and_Authentication_Failures/) - Brute force

- Login request to Intruder (Burp Suite) - brute force password with `/usr/share/wordlists/SecLists/Passwords/Common-Credentials/best1050.txt`

```bash
{"email":"admin@juice-sh.op","password":"admin123"}
```

---

## [Sensitive Data Exposure](https://owasp.org/Top10/A02_2021-Cryptographic_Failures/)

- FTP public at `/ftp`
- Find `mc.safesearch@juice-sh.op` password
  - listen - https://www.youtube.com/watch?v=v59CX2DiX0Y

```bash
mc.safesearch@juice-sh.op
Mr. N00dles
```

### Poison Null Byte

**URL Encoding and Null Bytes:**

- In URLs, the null byte (`\x00`) is encoded as `%00`.
- Some web servers and applications improperly decode `%00`, interpreting it as a null byte, which can terminate strings prematurely.

**Mismatch in Validation vs. Execution:**

- During **validation**, the application might check the entire string.
- During **execution**, if the backend uses C-style string handling, the string stops at the null byte.
- This mismatch can cause security checks to be bypassed.

```bash
%00
%2500

http://10.10.212.2/ftp/package.json.bak%2500.md
```

The `%2500` encoding is an extra layer of obfuscation:

- `%25` is the literal encoding of `%`, so `%2500` decodes to `%00`.
- This may be necessary when certain servers or filters block `%00` directly, but fail to handle double-encoded payloads like `%2500`.

---

## [Broken Access Control](https://owasp.org/Top10/A01_2021-Broken_Access_Control/)

Go to http://10.10.212.2/main-es2015.js

- F12 Dev Tools - Sources - `main-es2015.js`
  - Search for `path: "administration"`
  - go to http://10.10.212.2/#/administration



To view another user's shopping basket Go to http://10.10.212.2/#/basket

- change to `GET /rest/basket/2`



Go to http://machine_ip/#/administration

- Remove 5 stars reviews

---

## [Cross-Site Scripting (XSS)](https://owasp.org/www-project-top-ten/2017/A7_2017-Cross-Site_Scripting_(XSS).html)

### DOM XSS (Special)

- **Document Object Model-based Cross-site Scripting**

Input the following in the search bar

- XFS (Cross-Frame Scripting)

```bash
# e.g. 1
<iframe src="javascript:alert(`xss`)">

# e.g. 2
 <iframe width="100%" height="166" scrolling="no" frameborder="no" allow="autoplay" src="https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/tracks/771984076&color=%23ff5500&auto_play=true&hide_related=false&show_comments=true&show_user=true&show_reposts=false&show_teaser=true"></iframe>
```

### Persistent (Server-side)

With admin, go to http://10.10.212.2/#/privacy-security/last-login-ip

- turn Burp interceptor on, and logout
- for the `GET /rest/saveLoginIp` request, got to Headers tab and add a new header

```bash
Name:
True-Client-IP
Value:
<iframe src="javascript:alert(`xss`)">
```

- Forward the request
- Upon signing back into the admin account and revisiting the "Last Login IP" page, the XSS alert will trigger!
- The **True-Client-IP** header functions similarly to the **X-Forwarded-For** header, as both inform the server or proxy about the client's IP address. However, due to the lack of proper sanitization in the header, we can exploit it to execute an XSS attack

### Reflected (Client-side)

With admin, go to http://10.10.212.2/#/order-history

- click truck icon - `http://10.10.212.2/#/track-result?id=5267-8f615dcb73f36818`
- Use the following iframe XSS in the place of the `5267-8f615dcb73f36818`

```bash
<iframe src="javascript:alert(`xss`)">
```

- Submit the URL, refresh the page, and an alert displaying "XSS" will appear!
- The server uses a lookup table or database (depending on its configuration) to manage each tracking ID. Since the `id` parameter is not sanitized before being processed by the server, this allows us to execute an XSS attack

---

Solve other chalenges by checking http://10.10.212.2/#/score-board

---

## Metrics challenge

http://10.10.212.2/metrics

---

