# Phase 4: Juice Shop Attacks — Nmap, SQL Injection & XSS

**Date:** 2026-10-11  
**Attacker:** Kali Linux (192.168.128.2)  
**Target:** Ubuntu Server (192.168.128.4) running OWASP Juice Shop on port 3000  
**Network:** Host Only (sealed — no internet access)

---

## Step 1: Start Juice Shop on Ubuntu

```bash
sudo docker run -d -p 3000:3000 bkimminich/juice-shop
```

- `-d` = detached mode (runs in background)
- `-p 3000:3000` = maps Ubuntu's port 3000 to the container's port 3000
- Verified with `sudo docker ps` — container running, status "Up", ports mapped

<img width="1484" height="812" alt="image" src="https://github.com/user-attachments/assets/43d4ebd5-b6e5-4c3d-b441-5fcc40e7c234" />


---

## Step 2: Verify Access from Kali

Opened Firefox on Kali and navigated to `http://192.168.128.4:3000`

Juice Shop loaded successfully — the fake storefront appeared with products (Apple Juice, Apple Pomace, Banana Juice). This confirmed Kali can reach the vulnerable web app across the Host Only network.

<img width="1292" height="924" alt="image" src="https://github.com/user-attachments/assets/f35ad1b4-ee84-4de2-8133-907a53dbcbef" />


---

## Step 3: Reconnaissance with Nmap

```bash
nmap -sV 192.168.128.4
```

- `nmap` = Network Mapper, a port scanning tool
- `-sV` = service version detection (identifies what software is running on each open port)

### Results:
| Port | State | Service | Version |
|------|-------|---------|---------|
| 22/tcp | open | SSH | OpenSSH 10.2p1 Ubuntu 2ubuntu3.7 |
| 3000/tcp | open | ppp? | (Juice Shop — Nmap couldn't identify it by name) |

- 998 out of 1000 common ports were closed
- Host responded in 0.00021s (very fast because both VMs are on the same physical machine)
- Nmap printed a raw fingerprint of port 3000 showing HTTP headers, OWASP Juice Shop references, and accepted methods (GET, HEAD, PUT, PATCH, POST, DELETE)

**Why this matters:** Before attacking, you scan first. Now I know there are two entry points — SSH (for brute-forcing) and the web app (for injection attacks).

<img width="1292" height="924" alt="image" src="https://github.com/user-attachments/assets/8927e154-42da-459b-8e74-171529900935" />


---

## Step 4: SQL Injection — Admin Login Bypass

**Target:** Juice Shop login page (`http://192.168.128.4:3000/#/login`)

**Payload entered in Email field:**
' OR 1=1 --

**Password field:** `a` (anything works — the password check gets bypassed)

### How it works:

The app builds a database query like:
```sql
SELECT * FROM Users WHERE email = 'user@email.com' AND password = '...'
```

My input broke out of the query:
```sql
SELECT * FROM Users WHERE email = '' OR 1=1--' AND password = '...'
```

- `'` — closes the opening quote, escaping the string
- `OR 1=1` — condition that's always true, so the database returns every user
- `--` — SQL comment, everything after is ignored (including the password check)

**Result:** Logged in as the admin (the first user in the database). Juice Shop displayed a green banner: *"You successfully solved a challenge: Login Admin (Log in with the administrator's user account.)"*

**OWASP Category:** A03:2021 — Injection

<img width="1292" height="924" alt="image" src="https://github.com/user-attachments/assets/133861ad-5416-4b3f-a579-ddc4c3121440" />



---

## Step 5: XSS (Cross-Site Scripting)

**Target:** Juice Shop search bar

**Payload:**
```html
<iframe src="javascript:alert('xss')">
```

### How it works:

The search bar displays user input back on the page without sanitising it. Instead of treating the input as plain text, the browser interpreted the `<iframe>` tag as HTML and **executed the JavaScript code** inside it.

- The browser rendered an `<iframe>` element on the page
- The `javascript:alert('xss')` executed inside that iframe
- A popup dialogue appeared showing "xss" from `192.168.128.4:3000`

**Why this is dangerous:** In a real attack, instead of `alert('xss')`, an attacker could:
- Steal session cookies with `document.cookie`
- Inject a fake login form to capture credentials
- Redirect users to a phishing site

**OWASP Category:** A03:2021 — Injection (XSS is a type of injection)

<img width="1292" height="924" alt="image" src="https://github.com/user-attachments/assets/adb40232-052c-48c1-b9fa-4c318c1be9f3" />


---

## Step 6: Shutdown

```bash
# On Ubuntu:
sudo docker stop $(sudo docker ps -q)    # Stop all running containers
sudo shutdown now                         # Clean shutdown

# On Kali:
sudo shutdown now
```

The `$(sudo docker ps -q)` is command substitution — the inner command returns container IDs, which get passed to `docker stop`.

---

## Key Takeaways

1. **Always scan before attacking** — Nmap revealed two open ports and their services
2. **SQL injection bypasses authentication** by manipulating database queries — caused by unsanitised user input in SQL statements
3. **XSS executes attacker code in the victim's browser** — caused by unsanitised user input rendered as HTML
4. Both vulnerabilities fall under **OWASP Top 10 A03:2021 — Injection**
5. **Defence:** Input validation, parameterised queries (for SQL), output encoding (for XSS)

---

## Safety Confirmation

- Both VMs on Host Only networking (no internet)
- `ping google.com` failed on both VMs (sealed)
- All attacks contained within the isolated lab network
- No real systems were targeted
