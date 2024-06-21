# MASVS Notes
## Links

> - 🔗 [OWASP MASVS](https://mas.owasp.org/MASVS/)
> - 🔗 [OWASP MASTG](https://mas.owasp.org/MASTG/)

## MASVS v.2.0.0
-	 **MASVS** (**M**obile **A**pplication **S**ecurity **V**erification **S**tandard)

Security Standard - focus on how mobile apps handle, store, protect sensitive information and Standardize the security requirements with a baseline for mobile app security, allowing defense-in-depth measures and protection against client-side threats.

MASVS only covers the security of the client-side mobile app.

-	Mobile Attack Surface
	+	Storage
	+	Cryptography
	+	Authentication & Authorization
	+	Network communication
	+	Platform interaction
	+	Code quality
	+	Resilience against reverse engineering & tampering
	
-	Each control group is labeled with **MASVS-XXXXX** and each individual control with **MASVS-XXXXX-Y**.

-	Security Testing Profiles
	+	before 2023: 3 verification levels (L1, L2, R)
	+	2023: alighend with NIST OSCAL
	
-	Native apps written in **Java**/**Kotlin** for Android or Objective-C/Swift for **iOS**.

-	Apps based on **cross-platform** (Flutter, React Native, Xamarin, Ionic, etc.) and **hybrid** (Cordova, PhoneGap, Framework7, Onsen UI, etc.) frameworks - susceptible to platform-specific vulnerabilities.



## MASVS-STORAGE
**Secure storage of sensitive data on a device (data-at-rest).**

-	PII (Personally identifiable information)
-	Cryptographic material
-	Secrets, Credentials
-	API keys

Any sensitive data intentially stored by the app must be properly protected, regardless of the target (internal storage) location. If possible, use **hardware-backed security** features for apps development.

### MASVS-STORAGE-1
**The app securely stores sensitive data.**

-	Intentional storage location may be:
	+	app's internal (private) storage
	+	public (accessible by user/other apps)
-	Ensure any sensitive data is properly protected

### MASVS-STORAGE-2
**The app prevents leakage of sensitive data.**

-	Unintentional (public) storage location
	+	using certain APIs, backup, logs
-	Ensure prevention of unintentional leaks


## MASVS-CRYPTO
**Cryptographic functionality used to protect sensitive data.**

-	Ensure that the app uses cryptography and keys management (generation, storage, protection, encryption) according to industry best practices
-	Avoid physical access to sensitive data

### MASVS-CRYPTO-1
**The app employs current strong cryptography and uses it according to industry best practices.**

-	External cryptography standards for securing the user's data

### MASVS-CRYPTO-2
**The app performs key management according to industry best practices.**

-	Manage cryptographic keys lifecycle (generation, storage, protection)


## MASVS-AUTH
**Authentication and authorization mechanisms used by the mobile app.**

-	Implement correct user identity validation with Biometrics, Pin, multi-factor authenticatin code generators, to prevent unauthorized access.
-	Remote/local app secure auth


### MASVS-AUTH-1
**The app uses secure authentication and authorization protocols and follows relevant best practices.**

-	Apps must use secure protocols to authenticate to a remote endpoint

### MASVS-AUTH-2
**The app performs local authentication securely according to the platform best practices.**

-	Correctly implement local & remote authentication (biometrics, local PIN code)

### MASVS-AUTH-3
**The app secures sensitive operations with additional authentication.**

-	Securely implement additional authentication methods for sensitive actions inside the app (biometric, pin, MFA, email, deep links, etc)


## MASVS-NETWORK
**Secure network communication between the mobile app and remote endpoints (data-in-transit).**

-	Ensure exchanged data confidentiality and integrity
-	Apps must set up a secure, encrypted network communicatino channel using the TLS protocol.
-	Trust only specific Certificate Authorities

### MASVS-NETWORK-1
**The app secures all network traffic according to the current best practices.**

-	Ensure in-transit Data privacy & integrity, using TLS protocol, in any situation, avoiding security bypass/disabling


### MASVS-NETWORK-2
**The app performs identity pinning for all remote endpoints under the developer's control.**

-	Only very specific CAs are trusted = certificate/public key pinning


## MASVS-PLATFORM
**Secure interaction with the underlying mobile platform and other installed apps.**

-	Mobile platform app's interactions must occur securely, using secure platform-provided IPC mechanisms & WebView configurations
-	IPC (inter-process communication)
-	Prevent sensitive data leakage
-	Secure display sensitive data in app's user interface (passwords, credit card details, one-time pwds notifications)

### MASVS-PLATFORM-1
**The app uses IPC mechanisms securely.**

-	Ensure secure interactions (through IPC mechanisms) between user and installed apps

### MASVS-PLATFORM-2
**The app uses WebViews securely.**

-	Secure WebViews configuration, preventing data leakage & sensitive functionality exposure (e.g. JavaScript to native code)

### MASVS-PLATFORM-3
**The app uses the user interface securely.**

-	Avoid data unintentionally leakage due to platform mechanism (screenshots, UI data displaying)


## MASVS-CODE
**Security best practices for data processing and keeping the app up-to-date.**

-	Data entry points: UI, IPC, Network, File System
-	Treat data as untrusted input, verify and sanitize it before use - prevention of injection attacks (SQL, XSS, insecure deserialization) and bypass of security checks

### MASVS-CODE-1
**The app requires an up-to-date platform version.**

-	Ensure users have the latest security protections (OS patches)

### MASVS-CODE-2
**The app has a mechanism for enforcing app updates.**

-	Force app updates

### MASVS-CODE-3
**The app only uses software components without known vulnerabilities.**

-	Conver "low-hanging fruits" by scanning libraries for known vulnerabilities

### MASVS-CODE-4
**The app validates and sanitizes all untrusted inputs**

-	Data inputs sanitized before usage: UI, IPC, network, file system, etc


## MASVS-RESILIENCE
**Resilience to reverse engineering and tampering attempts.**

-	Defense-in-depth measures (code obfuscation, anti-debugging, anti-tampering, etc)
-	App run on trusted platform, prevent runtime tampering, integrity of app's inteded functionality
-	Quanto l'app è in grado di resistere ad un attacco mirato?
-	**e.g.** Se l'app B2C root check, antiroot, saperlo e bloccarsi

### MASVS-RESILIENCE-1
**The app validates the integrity of the platform.**

-	Validates the OS is not tampered/compromised and its security features can be trusted.

### MASVS-RESILIENCE-2
**The app implements anti-tampering mechanisms.**

-	Ensure app's integrity, preventing original code and resources modifications

### MASVS-RESILIENCE-3
**The app implements anti-static analysis mechanisms.**

-	Impede comprehension of the app's functioning with static analysis.

### MASVS-RESILIENCE-4
**The app implements anti-dynamic analysis techniques.**

-	Prevent dynamic analysis/instrumentation of the app, avoiding code modification at runtime

---

