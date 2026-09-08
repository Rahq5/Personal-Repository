# Intro to Web Security
here am starting to put some solid screws in the ground for Security in general

- **What is Web Security?**
	Web security refers to the practices and technologies used to protect websites, web applications, and data from cyber threats and unauthorized access.

- **what it does?**
	- protects data during transmission all over the internet 
	- safeguards websites, servers, users and apps
	- prevents data breaches, malware attacks and hacking
	- Essential due to increasing number of attacks

# Major Cyber Threats impacting web security
The [internet](https://www.geeksforgeeks.org/computer-science-fundamentals/what-is-internet-definition-uses-working-advantages-and-disadvantages/) is a powerful tool, but it also opens the door to serious security threats. From ransomware attacks to phishing scams. am showing some types of cyber attacks:
- **Ransomeware** 
- **SQL injection**
- **Phishing**
- **Viruses and worms**
- **SpyWare**
- **Cross-Site Scripting (XSS)**
- **Code Injection**
- **Denial of Service (DOS or DDOS)**

>Note: if you are new to this, better continue reading the rest of the intro, let these topics for later 
### Ransomware
 Ransomware is a type of [malware](https://www.geeksforgeeks.org/ethical-hacking/malware-and-its-types/) that is designed to block user access from own system until a ransom fee is paid to ransomware creator. Ransomware is a lot dangerous than a regular malware and spread through phishing emails having infected attachments.

- **How it Usually works:**
	1. **Infection and Delivery:** The malware enters a device or network, often using phishing emails or other ways like links. 
	  
	2. **Execution and Escalation:** Once inside, the software hides and scans the system. It often deletes local recovery tools like Windows Volume Shadow Copies and target backups so you cannot restore files easily.
	  
	3. **Data Encryption:** The malware locks your data. Modern ransomware uses fast symmetric encryption to scramble files quickly, then secures that symmetric key using asymmetric encryption. This leaves the data completely unreadable without the private key stored on the attacker's server.
	  
	4. **The Ransom Demand**: A ransom note appears on the screen or as a text file. It demands payment—usually in a cryptocurrency like Bitcoin—in exchange for a decryption key, and often includes a countdown timer.

- **Key ideas of Ransomware:**
	- Ransomware is a form of malware designed to block access from system until a ransom fee is paid.
	- The main objective of ransomware is to take money by gaining access.
	- user should restore the PC to gain access.
	- It locks your computer and makes it unusable.
	- It locks your computer and encrypts your personal data for ransom.
	- Crypto, Wanna Cry, Cerber and locker are some of the examples of Ransomwares.
	- It provides profit to the ransomware programmers by getting money from user for unlocking the system.

## SQL Injection

SQL Injection is a type of [attack](https://www.geeksforgeeks.org/computer-networks/what-is-sql-injection/) where an attacker inserts malicious SQL code into an input field (like a login form or search box) to manipulate the backend database. It works because the application blindly trusts user input and pastes it directly into a database query without checking it first.

- How it Usually works:
    1. Vulnerable Input: The attacker finds a form field, URL parameter, or API input that gets inserted directly into a SQL query without proper sanitization.
    2. Crafting the Payload: The attacker types SQL syntax instead of normal data — for example, entering `' OR '1'='1` into a login box, which can trick the query into always returning true.
    3. Query Manipulation: The database executes the attacker's injected code as if it were a legitimate part of the query, bypassing the app's intended logic.
    4. Data Exposure or Damage: Depending on the payload, the attacker can read private data (usernames, passwords), modify records, delete entire tables, or even gain admin access to the server.
- Key ideas of SQL Injection:
    - SQL Injection exploits poor input validation in database-driven applications.
    - The main objective is to trick the database into running commands the developer never intended.
    - Parameterized queries (prepared statements) are the standard defense.
    - It can lead to full database compromise, not just single-record leaks.
    - It's one of the oldest and most common web vulnerabilities (OWASP Top 10 regular).

## Phishing

Phishing is a type of [social engineering attack](https://www.geeksforgeeks.org/ethical-hacking/what-is-phishing/) where an attacker impersonates a trustworthy source (a bank, coworker, or service) to trick a victim into revealing sensitive information or clicking a malicious link. Unlike malware-based attacks, phishing exploits human trust rather than a software flaw.

- How it Usually works:
    1. Bait Creation: The attacker crafts a fake but convincing email, text, or website that mimics a real organization (logo, tone, sender name).
    2. Delivery: The message is sent to a large number of targets, or a specific one in the case of "spear phishing," usually urging urgent action ("your account will be locked").
    3. The Hook: The victim clicks a link leading to a fake login page, or opens an attachment that installs malware.
    4. Credential or Data Theft: Any information entered (passwords, card numbers) goes straight to the attacker, who can then use it for fraud or further attacks.
- Key ideas of Phishing:
    - Phishing relies on deception and urgency rather than technical exploits.
    - The main objective is to steal credentials, money, or install further malware.
    - Spear phishing targets specific individuals; regular phishing is sent in bulk.
    - Checking sender addresses and hovering over links before clicking are basic defenses.
    - It's often the first step in larger attacks, including ransomware infections.

## Viruses and Worms

A virus is a type of [malware](https://www.geeksforgeeks.org/ethical-hacking/malware-and-its-types/) that attaches itself to a legitimate file or program and needs a host action (like opening the file) to spread. A worm is similar but self-replicates and spreads across networks on its own, without needing a user to run anything.

- How it Usually works:
    1. Infection: A virus embeds its code inside a host file (document, executable); a worm instead scans networks for vulnerable machines to jump into directly.
    2. Activation: A virus activates when the infected file is opened or run; a worm activates automatically once it reaches a vulnerable system.
    3. Replication: The virus copies itself into other files on the same machine; the worm copies itself across the network to other machines, often very fast.
    4. Payload Execution: Once active, both can perform damaging actions — corrupting files, stealing data, opening backdoors, or slowing systems down through mass replication.
- Key ideas of Viruses and Worms:
    - Viruses need a host file and user action to spread; worms are self-propagating and need neither.
    - The main objective varies — from data destruction to opening access for future attacks.
    - Worms tend to spread faster and cause wider network damage due to no human trigger needed.
    - Antivirus software and network segmentation are common defenses.
    - Famous worms like Stuxnet and ILOVEYOU show how much damage self-spreading malware can cause.

## Spyware

Spyware is a type of [malware](https://www.geeksforgeeks.org/ethical-hacking/malware-and-its-types/) designed to secretly monitor a user's activity and collect information — like keystrokes, browsing habits, or credentials — without their knowledge or consent. It's usually built to stay hidden for as long as possible rather than cause immediate visible damage.

- How it Usually works:
    1. Silent Installation: Spyware often gets bundled with free software, hidden in email attachments, or installed via a drive-by download from a compromised site.
    2. Establishing Persistence: It hides itself in system processes or startup routines so it keeps running even after a reboot, avoiding detection.
    3. Data Collection: It quietly logs activity — keystrokes (keyloggers), screenshots, browsing history, or webcam/microphone access — depending on its type.
    4. Data Exfiltration: The collected information is sent back to the attacker's server, often used for identity theft, financial fraud, or corporate espionage.
- Key ideas of Spyware:
    - Spyware's main objective is silent, ongoing surveillance rather than immediate damage.
    - Common subtypes include keyloggers, adware trackers, and system monitors.
    - It's often bundled with legitimate-looking free software.
    - Since it hides well, regular malware scans and monitoring outgoing network traffic help detect it.
    - It's frequently the tool used to gather credentials before a larger attack.

## Cross-Site Scripting (XSS)

Cross-Site Scripting is a type of [web vulnerability](https://www.geeksforgeeks.org/ethical-hacking/cross-site-scripting-xss/) where an attacker injects malicious JavaScript into a trusted website, which then runs in other users' browsers when they visit that page. Unlike SQL Injection (which targets the database), XSS targets the victim's browser through a site they already trust.

- How it Usually works:
    1. Finding the Gap: The attacker locates a part of a website that displays user input without properly escaping it (a comment box, search bar, or profile field).
    2. Injecting the Script: The attacker submits malicious JavaScript instead of normal text, for example inside a comment field.
    3. Storage or Reflection: The script is either stored on the server (Stored XSS, shown to every visitor) or reflected back immediately in a crafted link (Reflected XSS, needs the victim to click it).
    4. Execution in Victim's Browser: When a victim loads the page, their browser runs the attacker's script as if it were part of the trusted site — stealing cookies, session tokens, or redirecting them to fake pages.
- Key ideas of Cross-Site Scripting (XSS):
    - XSS exploits trust the user has in a website, not trust in the user's machine.
    - The main objective is usually to steal session cookies or hijack a logged-in session.
    - Stored XSS affects every visitor; Reflected XSS needs a victim to click a specific link.
    - Proper input sanitization and output encoding are the standard defenses.
    - It's one of the most common vulnerabilities in web applications that handle user-generated content.

## Code Injection

Code Injection is a broader type of [attack](https://www.geeksforgeeks.org/ethical-hacking/code-injection-and-mitigation-techniques/) where an attacker feeds malicious code into a vulnerable program, and the program executes it as if it were legitimate code. SQL Injection and XSS are actually specific subtypes of this broader category, but Code Injection can also target the server's operating system, PHP, or other interpreters directly.

- How it Usually works:
    1. Vulnerable Interpreter: The attacker finds a place where the application passes user input directly to a code interpreter (shell commands, PHP `eval()`, template engines) without validation.
    2. Payload Crafting: The attacker writes code in the target language (e.g., a shell command or script snippet) instead of normal input data.
    3. Execution: The interpreter runs the attacker's code with the same privileges as the application itself, not realizing it wasn't part of the intended program logic.
    4. System Compromise: Depending on the interpreter and permissions, this can lead to reading files, running arbitrary commands, or fully taking over the server.
- Key ideas of Code Injection:
    - Code Injection is the parent category; SQL Injection and XSS are specific forms of it.
    - The main objective is to make the target system execute attacker-controlled code.
    - It typically results from unsafely mixing user input with executable code (shell, `eval`, templates).
    - The severity depends on what privileges the vulnerable process runs with.
    - Input validation, sandboxing, and avoiding dynamic code execution are key defenses.

## Denial of Service (DoS or DDoS)

A Denial of Service attack is a type of [attack](https://www.geeksforgeeks.org/computer-networks/denial-of-service-ddos-attack/) that aims to make a system, service, or network unavailable to its legitimate users by overwhelming it with traffic or requests. A Distributed Denial of Service (DDoS) is the same idea but launched from many machines at once — often a botnet — making it much harder to block.

- How it Usually works:
    1. Preparation: In a DDoS, the attacker first builds or rents a botnet — a large group of compromised devices infected with malware and controlled remotely.
    2. Flooding the Target: The attacker commands all these devices (or a single powerful one in a basic DoS) to send massive amounts of traffic or requests to the target simultaneously.
    3. Resource Exhaustion: The target's server, bandwidth, or application resources get overwhelmed trying to process the flood, leaving no capacity left for real users.
    4. Service Disruption: The website or service becomes slow or completely unreachable, sometimes used as a distraction while another attack (like data theft) happens in the background.
- Key ideas of Denial of Service (DoS or DDoS):
    - DoS uses one source; DDoS uses many distributed sources, making it harder to stop by simply blocking one IP.
    - The main objective is disruption and unavailability, not data theft (though it can be a smokescreen for it).
    - Botnets, made of hijacked IoT devices or computers, are commonly used to launch DDoS attacks.
    - Traffic filtering, rate limiting, and services like CDNs/WAFs help mitigate these attacks.
    - Unlike most attacks on this list, DoS/DDoS doesn't need to break into a system — it just needs to overload it.
# Understanding WebSec
Web security is about keeping websites, servers, users, and devices safe from [cyberattacks](https://www.geeksforgeeks.org/ethical-hacking/what-is-a-cyber-attack/) that come through the internet. These attacks can include things like viruses, fake emails (phishing), and other harmful activities that can steal or leak important information

To stay protected, web security uses different tools and methods, such as firewalls, systems that block suspicious activity, filters that block dangerous websites, and antivirus software. It also covers the security of [Web Apps](https://www.geeksforgeeks.org/websites-apps/what-is-web-app/), [APIs](https://www.geeksforgeeks.org/software-testing/what-is-an-api/), and cloud systems to keep everything running safely online.

**Example**: when you transferring data between client and web server, this transmission and data have to be protected

also what can Cyber attacks cause:
- Cyber threats are increasing in complexity and frequency
- Attacks can cause financial, reputational, and legal damage
- Advanced technologies are required for protection
- Strong access controls help prevent unauthorized access
- Secure development practices ensure safer applications
- (from the writer) physical attack is the most strongest type in breaching systems, you talking about a method that succesfully breached a NUCLEAR lab in iran using a small weapon "USB drive"

# Best Practices
these amma call the commandments of WebSec

- ****Keep Software Updated****: Regularly update all software to fix known vulnerabilities and prevent exploits by hackers.
  
- ****Beware of SQL Injection****: Prevent attackers from injecting malicious queries into your [database](https://www.geeksforgeeks.org/dbms/what-is-database/) by using parameterized queries and input validation.
  
- ****Prevent Cross-Site Scripting (XSS)****: Sanitize user input to block scripts that could run in users’ [browsers](https://www.geeksforgeeks.org/blogs/web-browser-a-complete-overview/) and steal sensitive data.
  
- ****Limit Error Messages****: Avoid exposing system details in error messages. Keep messages generic to prevent attackers from gaining insight.
  
- ****Validate User Input****: Perform input validation on both client and server sides to block malformed or malicious data.
  
- ****Use Strong Passwords****: Enforce complex password policies to protect against [brute-force attacks](https://www.geeksforgeeks.org/computer-networks/brute-force-attack/) include uppercase, lowercase, numbers, and symbols.
  
- ****Implement HTTPS****: Secure your website with [HTTPS](https://www.geeksforgeeks.org/computer-networks/https-full-form/) to encrypt data during transmission and prevent interception.
  
- ****Enable Two-Factor Authentication (2FA)****: Add an extra layer of security by requiring a [second form of verification](https://www.geeksforgeeks.org/ethical-hacking/how-does-two-factor-authentication-2fa-work/) beyond a password.
  
- ****Access Control****: Restrict access based on user roles and use the principle of least privilege to minimize risk.
  
- ****Monitor and Log Activity****: Keep logs of access and actions on your site to detect suspicious behavior and audit breaches.
  
- ****Use Modern and Secure Tech Stacks****: Build websites using updated and secure frameworks like the [MEAN stack](https://www.geeksforgeeks.org/javascript/introduction-to-mean-stack/) ([MongoDB](https://www.geeksforgeeks.org/mongodb/mongodb-an-introduction/), [Express.js](https://www.geeksforgeeks.org/node-js/express-js/), [Angular,](https://www.geeksforgeeks.org/angular-js/angularjs/)[Node.js](https://www.geeksforgeeks.org/node-js/nodejs/)) for better performance, scalability, and built-in security features.
