
---

# [Phishing Email Analysis Lab – Deep Dive with Advanced Techniques](https://www.youtube.com/watch?v=F-Hl_7mabKE)

---

**Credits**

- This project was inspired by:
	- [Empirical Training](https://www.youtube.com/@empiricaltraining)
	- [Intezer](https://www.youtube.com/@Intezer)

---

> ⚠️ **Warning**
> - This lab should be completed within a Virtual Machine for safety reasons. I will be using Parrot Security OS as my VM, and VirtualBox as my hypervisor for this lab.

## Summary

As a SOC Analyst, the ability to dissect and interpret email headers is a critical skill for detecting, tracing, and responding to phishing campaigns. This lab takes a **real-world, malicious phishing email** and breaks it down **line by line**, mapping every SMTP hop, authentication result, metadata flag, and payload behavior. We use a structured and replicable methodology that teaches how to **validate authenticity**, **spot red flags**, and **uncover attacker infrastructure**, even when standard security checks pass.

This guide serves as both a **technical training tool** and a **professional reference**. It teaches analysts how to:

- Reconstruct the full mail path using `Received:` headers
- Evaluate SPF, DKIM, and DMARC at a forensic level
- Interpret Microsoft-specific metadata fields for threat scoring
- Understand cloud relay behavior (Mailgun, Outlook, etc.)
- Extract and analyze encoded HTML payloads for lures and links
- Document findings in a structured and report-ready format

Each section is logically grouped, summarized with purpose, and supported by verification steps so that analysts can **explain not just what is happening, but how they know** it's happening.

Whether you're a junior analyst building your skills or a seasoned responder refining your workflow, this project will enhance your ability to **think like an attacker and investigate like a pro**.

- **Key Skills/Tools:** Email header analysis, SMTP forensics, SPF/DKIM/DMARC, documentation best practices

**Prerequisites**

- Parrot Security OS VM in VirtualBox (snapshot before starting)
- Text editor for documentation (VSCode, Obsidian, or Markdown viewer)
- Optional: Python tools or browser-based header analyzers for cross-validation

---

## Scenario: An Employee Reports a Suspicious Email

### (Step 1) **Initial Review & Setup**:

- You, the SOC analyst, receive a report from an employee about a suspicious email that appears to be a phishing attempt. We'll simulate this by heading to the **[Phishing Pot](https://github.com/rf-peixoto/phishing_pot)** Github. Phishing Pot is a collection of real phishing samples collected via honey pots. The purpose of the repository is to provide a reliable database for researchers and developers of detection solutions. When you arrive at the repository, click the `email` folder.
- ![image](https://github.com/user-attachments/assets/ef8e2feb-cc76-44e3-8cb3-65b770d28cc7)
- Here is where we will download our sample. You can choose any, but for this lab, I'll choose `sample-1003.eml`. Click the download icon to download the raw file. Download a few other samples as well for further analysis!
- ![image](https://github.com/user-attachments/assets/26eb9b85-fba2-4c07-a22c-0222a9e1cbc2)
- Save the file.
- ![image](https://github.com/user-attachments/assets/9586fc3c-2586-48ef-8188-42307036a8e2)
- Now open your terminal and install **Thunderbird** with:

```bash
sudo apt install thunderbird
```

- ![image](https://github.com/user-attachments/assets/ae1cee12-8024-4115-88e1-a83ec3796740)
- Now you should have some example emails in your "Downloads" folder.
- ![image](https://github.com/user-attachments/assets/78423e94-ddfc-4647-8951-ca1d326bb125)

---

### (Step 2) **Body & Content Analysis**:

- Since we already have the `.eml` files, let's see what they used to look like by opening them in **Thunderbird**. Thunderbird is a free and open-source email client developed by Mozilla. It allows users to manage multiple email accounts in one place.
- ![image](https://github.com/user-attachments/assets/44146a32-d7ef-456a-8ced-fecfc6c144b6)
- Looks like `sample-1003.eml` is a cryptocurrency scam targeting Ripple (XRP) users.
- ![image](https://github.com/user-attachments/assets/1f7a5b24-d5bc-43f8-9bec-be44c40e230e)
- This is great for us, because this sample is rich with red flags! Before diving into header forensics, let’s break down this `.eml` **visually and contextually** based on **phishing indicators** seen in the **email body, sender identity, formatting, and linked domains**. Think of this as your **Phase 1 - Pre-Header Triage**: the visual and textual "first impression" stage. Here are some red flags I noticed:

#### 🚩 **Red Flags**:

##### **(1) Sender and Domain Mismatch**

- ![image](https://github.com/user-attachments/assets/e5f4e82a-b0d9-444c-9ee3-770984dd5838)
- **Claimed Identity:** CoinDesk
- **Actual Sender Email:** `coindesk@rpsins.247crisis-resilience.com`
- **Legit Domain?** ❌ _No._
	- CoinDesk’s real domain is `@coindesk.com`
	- `247crisis-resilience.com` is suspicious and **completely unrelated to crypto**
	- `rpsins` is likely trying to appear like an internal subdomain or business unit to obscure legitimacy

> **💡 Threat Actor Tactic:**
> - This is a classic *email spoofing* tactic. Threat actors use a legit-sounding display name and confusing domain structure to bypass quick visual checks.

##### **(2) Subject Line Red Flags**

- ![image](https://github.com/user-attachments/assets/58cfc573-7e50-4c84-abfb-9d3aa06873c3)
- **Subject:** `No time to lose - XRP Token Allocation in progress`
	- Urgency = Phishing Hook
	- Targeting crypto = High-value lure
	- **Mentions XRP**, a popular token often targeted in airdrop scams

> **💡 Threat Actor Tactic:**
> - Urgent subject + familiar cryptocurrency brand to trigger emotional response and time-based action. Creating a sense of **[Urgency](https://www.codecademy.com/learn/cyber-attacks/modules/social-engineering/cheatsheet)** is one of the social engineering principles.

##### **(3) Content Language Red Flags**

- ![image](https://github.com/user-attachments/assets/d0677d2d-7712-4c42-8c26-af04156cc48d)
- Buzzwords: “**Exciting Rewards**”, “**Token Allocation Tool**”, “**Partial Win**”, “**15% Token Boost**”, “**Early Adopters**”
- Over-the-top benefits for little effort
- Grammatically correct, but **unnaturally promotional** for CoinDesk
- **Repetition of keywords** = phishing trigger tactic (SEO-style repetition in scam emails)

> **💡 Threat Actor Tactic:**
> - The attacker knows their audience. They’re leveraging news (court case win), hype (airdrop), and urgency.

##### **(4) Hyperlinks Are Suspicious**

- ![image](https://github.com/user-attachments/assets/41707dc9-3d4b-451a-8902-c93c204d9765)
- ![image](https://github.com/user-attachments/assets/851f28f2-a41a-4cbe-b103-5ce34d2c8af3)
- 🚨 Both links go to: `https://mail119-ripple.net/726f647269676f2d662d7040686f746d61696c2e636f6d?c_id=...`
- Let's break that down:
	- `mail119-ripple.net` = **Not a legitimate Ripple domain**
	- Path appears to be **Base64-encoded string**, likely obfuscating a redirect (we’ll decode this in analysis)
	- Query parameter: `c_id=...` likely used to track click-throughs per email recipient

> **💡 Threat Actor Tactic:**
> - Combining a _typo-squat domain_ (e.g., `ripple.net` ≠ `ripple.com`) with **click-tracking IDs** for mass-campaign monitoring.

##### **(5) Footer & Unsubscribe Links**

- ![image](https://github.com/user-attachments/assets/f019ecb0-4f2c-4881-9439-9a6b8c8f97d3)
- ![image](https://github.com/user-attachments/assets/65526176-4052-4c55-b105-1b87e69fe556)
- Formatting of social links are messy and merged, although this could be an issue with Thunderbird.
- **Physical address is real** (CoinDesk’s actual address), but **copied and pasted** as social proof
- `Unsubscribe` also leads to the same link as the other malicious hyperlinks.

> **💡 Threat Actor Tactic:**
> - Copied-and-pasted legitimate elements from real newsletters to build **credibility**, while maintaining full control over where real links go.

##### Visual Red Flags Summary
We've collected this information and will add it to our **report** at the end of our investigation. Here is what we could see **visually** when first opening the email.

| Indicator                | Finding                                                   |
| ------------------------ | --------------------------------------------------------- |
| **Suspicious Sender**    | `rpsins.247crisis-resilience.com` (unrelated to CoinDesk) |
| **Urgent Subject**       | Triggers emotional urgency: “No time to lose”             |
| **Crypto Lure**          | Exploits popular XRP coin and Ripple case                 |
| **Suspicious Links**     | Obfuscated URL pointing to `mail119-ripple.net`           |
| **Overhyped Promises**   | 15%–30% token rewards, NFT incentives                     |
| **No Real Footer Links** | Copied CoinDesk branding but links lead to malicious site |
| **No Personalization**   | Generic salutation, no recipient targeting                |

#### Further investigation - 🚨 Careful!!!

- If you click the link, Firefox immediately tries to protect you, which makes this this link even more suspicious! If it were legit, we would immediately be sent to the CoinDesk website since it is trusted. This screen may also be showing up because I've hardened Firefox. The warning also says "This website wasn’t found by mozilla.cloudflare-dns.com." which raises further alarms.
- ![image](https://github.com/user-attachments/assets/bacb8862-ccff-47ae-9108-8bf3b1137c3d)
- I've clicked "Always continue for this site" and was brought here:
- ![image](https://github.com/user-attachments/assets/e5680799-1c7f-4737-ae94-cd08fa62200c)
- 🤔 **“We can’t connect to the server at mail119-ripple.net”**... So what does this behavior tell us? This message typically indicates **DNS resolution failure** or **server unavailability**. Here's what it likely means in this phishing context:

| Cause                                  | Meaning in Context                                                                                                                                                   |
| -------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Domain has been taken down**         | The domain `mail119-ripple.net` may have been reported and **takedown requested** by domain registrars, CoinDesk, Ripple, or cybersecurity firms.                    |
| **No DNS A record exists anymore**     | If the phishing site was quickly removed, its DNS records might have been **deleted**, making the domain “unresolvable.”                                             |
| **Temporary command-and-control (C2)** | Attackers sometimes register **“burner domains”** that are only live for a few hours during a campaign. This might have expired or been disabled already.            |
| **My DNS blocked it**                  | A **security-focused DNS provider (like Cloudflare)** may be blocking known malicious domains silently. I made an exception for the website, so this seems unlikely. |
| **Firewall or VPN interference**       | Being behind an outbound firewall, using a VPN, or hardened DNS rules, might block access even if the domain is technically active.                                  |

> ⚠️ **Why This is Still Dangerous**
> - Just because a phishing link **doesn’t resolve anymore**, it doesn't mean:
> 	- It wasn’t **malicious**.
> 	- It didn’t work **earlier** in the campaign.
> 	- It won’t come **back online** with the same infrastructure (common with dynamic DNS).
> - Some attackers rotate DNS or re-use links via **redirectors** like `mail*.ripple.net` pointing to **different payloads over time**.

##### Best Practices for Handling Malicious Links (Especially in a Lab or Investigation)

- ⏪ **Before Clicking (Preferably Don’t Click)**
	- **Use a secure environment**: Always investigate in **isolated VMs** or **sandboxed browsers**.
	- **Use curl/wget with `--resolve` or `--insecure` flags** (no JavaScript execution).
	- **Use passive DNS and URL scan tools**:
	    - [VirusTotal](https://virustotal.com/)
	        - Click `URL`. Paste the malicious URL and click `Search`:
	        - ![image](https://github.com/user-attachments/assets/276c29e4-e94a-480c-be6e-c9e78f69c4d4)
	        - The results only show 1 security vendor, Sophos, to label this URL as spam even though we know it should not be labeled as clean to the others. This is why it is important to use multiple methods of analysis.
	        - ![image](https://github.com/user-attachments/assets/666f7876-9da0-4082-a123-d286da1d2d6f)
	        - In the "DETAILS" tab, we get more info, but not enough to confirm or deny if this URL is involved in malicious activity.
	        - ![image](https://github.com/user-attachments/assets/9b3d42c9-155b-4b29-ab7f-e605aff4252e)
	    - [URLScan.io](https://urlscan.io/)
	        - Paste the URL and click `Public Scan`:
	        - ![image](https://github.com/user-attachments/assets/c2872c4c-7dd7-4af0-9fa9-ea3d3e696379)
	        - The results give us similar information that was given by Firefox earlier:
	        - ![image](https://github.com/user-attachments/assets/8326e241-0c26-4c94-aff5-1abc0c8087d5)
	    - [Any.run](https://any.run/) (requires a business email or special request for personal use.)
	- **Never use a personal browser or primary system**.
	- **Don’t input credentials** or allow downloads.
	- **Use TOR or a proxy-aware VPN** if attribution is a concern.

- ⏩ **After Clicking (If Already Clicked)**
	- **Log DNS and network activity** (Wireshark, Zeek, `tcpdump`, etc.).
	- **Record full HTTP requests/responses** (Burp Suite).
	- **Check for client-side script execution** – use browser dev tools → `Network` tab.
	- **Snapshot the HTML, JS, and redirects** if site resolved.
	- **Run domain through:**
	    - `whois`
	    - `dig +trace mail119-ripple.net`
	    - Certificate Transparency logs (like [crt.sh](https://crt.sh/))
	- **Check local system for changes or cookies** if you clicked directly.
	    - Firefox → `about:preferences#privacy` → Manage Data

- Now that the best practices are set, clean up by removing the exception for the malicious link in Firefox Settings.
- ![image](https://github.com/user-attachments/assets/f5b74d29-28d5-4a2f-8efc-baf6893b4fe1)

> ‼️ **Important Note**
> - I would like to let it be known that I NEVER click links recklessly, and always analyze by hovering my mouse over the link. Clicking the link earlier was a **one time event** for the sake of the lab.

#### Bonus Body & Content Analysis on a Few More Samples

##### 📩 `sample-1.eml` - **Banco Bradesco Livelo Points Phish (Portuguese)**

- ![image](https://github.com/user-attachments/assets/8e7908cc-ff2a-48e8-9b6e-ac2c80ef2b74)
- **Rough email Translation**
	- **Subject**: CLIENT PRIME - BRADESCO LIVELO: Your card has 92,990 Livelo points expiring today!
	- **Body & Content**:

> **Banco do Bradesco (Livelo).**
> 
> You have **Livelo Points** with your **Banco do Bradesco card** available for redemption that **expire TODAY**. Avoid losing these points by redeeming them **right now** using your **Visa Infinite** card balance.
> 
> **Bradesco customers** earn Livelo points every time they use their cards in **debit or credit mode** — it’s fast and easy to accumulate.
> 
> - Exchange your points for airline miles
> - Get up to **35% off** on your card bill
> 
> **92,990**
> 
> **THOUSAND ACCUMULATED POINTS EXPIRE TODAY**
> 
> **`Redeem Now`**
> 
> Redeem them now before they expire! Take advantage and exchange your points for airline miles, up to 35% discounts on your card, or **thousands of prizes** in our catalog.

- **[Banco Bradesco Website](https://banco.bradesco/cartoes/hotsitefidelidade/)** ("Translated") - Banco Bradesco is a major Brazilian bank offering various financial services.
- ![image](https://github.com/user-attachments/assets/edfed715-a4d9-42b1-9edc-db0105859f7c)
- ![image](https://github.com/user-attachments/assets/3380bfe5-3f81-4bb4-b549-13c1ca499855)

**Summary**
- This email falsely claims to be from Banco Bradesco, informing the recipient that their Livelo points are about to expire, urging immediate redemption via a provided link.

| Field                         | Details                                                                              |
| ----------------------------- | ------------------------------------------------------------------------------------ |
| **Sender**                    | BANCO DO BRADESCO LIVELO `<banco.bradesco@atendimento.com.br>`                       |
| **Subject**                   | CLIENTE PRIME - BRADESCO LIVELO: Seu cartão tem 92.990 pontos LIVELO expirando hoje! |
| **Target Emotion**            | Urgency & loss aversion                                                              |
| **Language**                  | Portuguese                                                                           |
| **Phishing Hook**             | Points expiring today – urgent redemption                                            |
| **Phishing Link**             | `https://blog1seguimentmydomaine2bra.me/` (used multiple times)                      |
| **CTA (Call to Action) Text** | “Resgatar Agora”                                                                     |
| **Tactics**                   | Fake point balance, fake urgency, incentive claims (miles, discounts)                |
| **Indicators**                | Typo in domain (`mydomaine2bra.me`), misleading branding, emotional manipulation     |

##### 📩 `sample-10.eml` - **Microsoft Account Login Alert Phish**

- ![image](https://github.com/user-attachments/assets/3504617e-c1d6-4d09-ac92-4bddae7f2c63)
- ![image](https://github.com/user-attachments/assets/ec205c8f-b963-4ae6-845a-5d99c9785aa0)
- **[Microsoft Website](https://www.microsoft.com/en-us/)** - Microsoft Corporation is a global technology company known for its software products and services.
- ![image](https://github.com/user-attachments/assets/5daaaf74-7bd0-4727-8dff-176d5e73d35b)

**Summary**
- Pretending to be from Microsoft, this email alerts the recipient of an unusual sign-in attempt from Russia, prompting them to report the activity via a reply-to Gmail address.

| Field                  | Details                                                                    |
| ---------------------- | -------------------------------------------------------------------------- |
| **Sender**             | `<no-reply@access-accsecurity.com>`                                        |
| **Reply-to**           | `sotrecognizd@gmail.com`                                                   |
| **Subject**            | Microsoft account unusual signin activity                                  |
| **Target Emotion**     | Fear & uncertainty                                                         |
| **Phishing Hook**      | Unusual sign-in from Russia                                                |
| **Phishing Mechanism** | `mailto:` links to attacker-controlled Gmail with prefilled subject/body   |
| **Fake Details**       | IP, country, browser, OS info to create legitimacy                         |
| **Tactics**            | Fake security alert, use of system details to appear real                  |
| **Indicators**         | Gmail reply-to, suspicious domain, `mailto:` trick to bypass link scanning |

##### 📩 `sample-100.eml` - **Dutch Zonnepanelen Scam (Solar Offer)**

- ![image](https://github.com/user-attachments/assets/4c382b26-3f93-4b07-8773-4821fc833354)
- Rough email Translation
	- **Subject**: 🔋 Solar panels at a good price
	- **Body & Content**:

> **Want to receive solar panel quotes with no obligation?**
> 
> Are you also tired of:
> - Electricity and gas bills constantly going up?
> - Having less to spend on fun things because of inflation?
> - Nobody knowing when the price hikes will end?
> 
> 17 million Dutch citizens have the same problem.  
> **Three million consumers** have already bought solar panels.
> 
> **What they know (and the rest of the Netherlands doesn't - yet):**
> 
> - Solar panels are much cheaper than you’d think.
> - Installation happens within **a single day**.
> - It’s worth comparing **multiple providers**.
> - You’ll literally save **hundreds of euros every year** on energy costs.
> 
> `>> Click here for 2 to 3 no-obligation quotes within two business days`
> 
> More and more solar panel companies are struggling to keep up with high demand.  
> So **don’t wait** — **sign up now!**
> 
> `YES, I want solar panel quotes`

- **Website** - None

**Summary**
- This Dutch-language email offers enticing solar panel deals, claiming significant savings and urging recipients to request free quotes through embedded links. While the email doesn't impersonate a specific company, it references the solar panel installation industry in the Netherlands.

|Field|Details|
|---|---|
|**Sender**|Zonnepanelen installateur `<zonnepaneel@appjj.serenitepure.fr>`|
|**Reply-to**|`<news@aichakandisha.com>`|
|**Subject**|🔋 Zonnepanelen voor een goede prijs|
|**Language**|Dutch|
|**Target Emotion**|Cost anxiety, inflation stress|
|**Phishing Hook**|Claim: Free solar quotes + large cost savings|
|**Phishing Links**|`http://go.nltrck.com/?c=495...` (tracking-based)|
|**Tactics**|Fake consumer concern, fake urgency, financial stress exploitation|
|**Indicators**|Mismatched domains, marketing spam tone, URL shorteners/tracking domains|

##### 📩 `sample-1000.eml` - **Binance Leak Compensation Scam**

- ![image](https://github.com/user-attachments/assets/dd5f2eee-90bf-4814-b0a2-ca9d739d25ac)
- **[Binance Website](https://www.binance.us/)** - Binance is a leading global cryptocurrency exchange platform.
- ![image](https://github.com/user-attachments/assets/da0df026-110f-414c-a323-9d5f7e83fdd6)

**Summary**
- Masquerading as a notification from Binance, this email claims the recipient's data has been leaked and offers Bitcoin compensation, directing them to a suspicious link.

|Field|Details|
|---|---|
|**Sender**|[info@libreriacies.es](mailto:info@libreriacies.es)|
|**Subject**|Binance Cybersecurity|
|**Target Emotion**|Fear of data leaks & FOMO for Bitcoin compensation|
|**Phishing Hook**|"120+ leaks found" – Get compensated in BTC|
|**Phishing Link**|`https://axobox.com/vt/wp-track.php`|
|**Fake Details**|Notification ID, official tone, “cybersecurity department” claim|
|**Tactics**|Impersonates Binance + personal alert, targets crypto users|
|**Indicators**|Spanish bookstore domain, grammar errors, fake alert format

---

### (Step 3) **Analyze Email Headers**:

#### What Are Email Headers?

- **Definition**:
	- **Email headers** are metadata blocks attached to every email that track its **origin, transmission path, and technical information** as it travels across mail servers. Think of them as a digital paper trail, or a set of routing and security fingerprints.
- **Example**:
	- If an email = a letter,
		- The **header** = the envelope (with stamps, routing marks, return address)
		- The **body** = the message inside

- ![image](https://github.com/user-attachments/assets/f6040fcf-4ad6-4296-9e26-9cf57614d0b9)
- *Image Courtesy of [Intezer](https://www.youtube.com/watch?v=Xrzsu-FFvu8)*

#### **Why Headers Are Useful**

- 🔍 **Identify sender spoofing or impersonation**
- 📍 **Trace the route** an email took (relay IPs, geolocation)
- 🛡️ **Evaluate authenticity and integrity** using SPF, DKIM, DMARC
- ⚠️ **Detect phishing, spam, or malware campaigns**
- 🧪 **Understand if anti-spam systems flagged or altered the message**

#### List of Header Fields

|**Header Field**|**Purpose / Meaning**|
|---|---|
|`Return-Path:`|Final bounce address; used for delivery status|
|`Delivered-To:`|Recipient’s mailbox (after routing)|
|`Received:`|Chain of servers the email passed through; shows timestamps, IPs, delays|
|`Received-SPF:`|SPF validation result (pass/fail)|
|`Authentication-Results:`|Overall status of SPF, DKIM, DMARC validation|
|`DKIM-Signature:`|Cryptographic signature to verify integrity and origin|
|`ARC-Authentication-Results:`|Results of DKIM/SPF/DMARC in forward or relay chains|
|`From:`|Claimed sender address (can be spoofed)|
|`Reply-To:`|Where replies will go (often abused in phishing)|
|`To:`|Intended recipient(s)|
|`Subject:`|Email subject line|
|`Message-ID:`|Unique identifier for email message|
|`Date:`|Time the email was sent|
|`MIME-Version:`|Type of message format (used for HTML, attachments, etc.)|
|`Content-Type:`|How content is structured (HTML, plain text, multipart, etc.)|
|`X-...:`|Custom headers; often used by mail servers, filters, anti-spam tools|

#### **How to Read & Analyze Headers**

- **Start from the Bottom Up**  
    - Each `Received:` header is added by a server. The _bottom-most_ is the **first hop (origin)**; the _top-most_ is the **final destination**.
- **Check for Anomalies**
    - Sender domain in `From:` doesn't match IP or mail server
    - Hostnames in `Received:` don't match claimed origin
    - IPs from suspicious or foreign geolocations
- **Correlate SPF, DKIM, DMARC**
    - `Authentication-Results:` should show `pass` for at least SPF or DKIM
    - If all three fail, it’s highly suspicious
    - You can also check the **[SPF Record](https://www.spf-record.com/spf-lookup/)**
- **Watch for Sender Spoofing or Reply Diversion**
    - `Reply-To:` differs from `From:` → suspicious
    - `Return-Path:` differs from `From:` → possible forged bounce address
- **Use Lookup Tools**  
    - Paste IPs/domains into:
    - [VirusTotal](https://www.virustotal.com)
    - [MXToolbox](https://mxtoolbox.com)
    - [AbuseIPDB](https://abuseipdb.com)
    - `whois`, `dig`, or `host` in terminal

#### **Core Email Authentication Protocols: SPF, DKIM, DMARC**

| Protocol                                                                 | Purpose                                                            | How It Works                                                                                                    | What to Look For                                                                     |
| ------------------------------------------------------------------------ | ------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| **SPF** (Sender Policy Framework)                                        | Prevents spoofed IPs from sending mail on behalf of a domain       | Publishes a list of **allowed IPs** in DNS; mail servers check if sending IP is listed                          | `SPF=pass` means source IP was authorized by sender domain                           |
| **DKIM** (DomainKeys Identified Mail)                                    | Ensures **email integrity** and that it wasn’t modified in transit | The sender signs parts of the message with a private key; recipients verify with a **DNS-published public key** | `DKIM=pass` confirms the message was **untampered** and from the claimed domain      |
| **DMARC** (Domain-based Message Authentication, Reporting & Conformance) | Unifies SPF and DKIM under a policy to block spoofing              | Domain owner publishes a DNS policy stating what to do if SPF/DKIM fails (e.g., quarantine, reject)             | `DMARC=pass` means **either SPF or DKIM passed** and aligns with the `From:` address |

- **Why These Matter**
	- **SPF** stops simple IP spoofing.
	- **DKIM** ensures the body & headers haven’t been altered.
	- **DMARC** lets domain owners control what happens when both SPF & DKIM fail (e.g., block outright).
	- Together they raise the bar: a phishing email that passes all three is _much_ harder to forge.

#### **Key Fields You Should Focus On During Analysis**

|**Priority**|**Header Field**|**Why It’s Important**|
|---|---|---|
|🔴 High|`Authentication-Results:`|Tells you if SPF, DKIM, DMARC passed or failed|
|🔴 High|`Received:`|Shows mail path and origin IP (check for anomalies)|
|🔴 High|`From:` + `Reply-To:`|Check if the sender is spoofed or redirecting replies|
|🟡 Medium|`Return-Path:`|Often shows the true bounceback origin|
|🟡 Medium|`DKIM-Signature:`|Verify against domain in `From:`|
|🟢 Low|`Message-ID:`|Check domain part to validate origination server|
|🟢 Low|`To:`, `Subject:`|Look for mismatches or generic targets (mass phishing)|

#### 📩 `sample-1003.eml` - CoinDesk Ripple Crypto Scam Email Headers Analysis

- Now that we know what to look out for, let's finally analyze the headers for `sample-1003.eml`. Thunderbird has an option to view headers directly within itself, but we will be using a text editor or IDE of your choice. I'll use `VSCodium`. Right click on `sample-1003.eml` and open with a text editor.
- ![image](https://github.com/user-attachments/assets/7e168ca2-71fa-4f04-9e72-e2f00711bbc6)
- If you are following along in `VSCode` or `VSCodium`, I am using this email syntax highlighting tool, so that it is easier for you to see the fields, and not just a block of text.
- ![image](https://github.com/user-attachments/assets/74c04a39-f25e-4271-8170-1149be676423)
- Check out the Github repository if you want to review the source code for safety reasons.
- ![image](https://github.com/user-attachments/assets/766b44ff-afc0-406d-a2ca-a40e0ea49549)
- Now we get to see our headers:
- ![image](https://github.com/user-attachments/assets/9e47a482-63dd-4e38-8e6f-af5fe5ad125e)
- Let's dissect this in sections:

##### 1️⃣ **`Received:`** - Tracing the Mail Path

- This header is critical for **tracking the route an email took across the internet**, from the sender’s infrastructure to the recipient’s inbox. Each mail server that handled the email **adds a new `Received:` line to the top of the header**, creating a reverse-chronological trace. By analyzing these lines from bottom to top, we can reconstruct the email’s delivery path, identify intermediate relays, and **pinpoint suspicious jumps, unauthorized servers, or inconsistencies** that may signal spoofing or relay abuse.

- In our case, the email shows a **legitimate-looking SMTP flow through Mailgun**, into Microsoft's protection systems, and finally into the target’s Outlook inbox. However, certain domain/IP relationships are **critical to verify**, as attackers often register **lookalike domains** and send via permitted third-party services.

- ![image](https://github.com/user-attachments/assets/e7f32f38-fdad-48a1-b1de-a54f5c45b16a)

###### **Line by Line Inspection**

**Line 5 (Earliest hop - where the message entered the SMTP world):**

- ![image](https://github.com/user-attachments/assets/46e0aff7-5656-41c1-a2fc-0c5b4c800f31)
	- `Received: from <unknown> (<unknown> []) by 60f660e08702 with HTTP id 64c278246f49ebc4e56a44f0; Thu, 27 Jul 2023 13:59:00 GMT`
		- **Interpretation:**
		    - The email was originally **submitted via a web-based HTTP client**, not an email client or relay server.
		    - The sender's original identity is hidden - `<unknown>` for hostname, IP, and envelope sender.
		    - Server `60f660e08702` received it via HTTP, not SMTP.
		- **Importance:**
		    - This points to possible use of a **web form or programmatic API** (e.g., Mailgun’s HTTP API).
		    - Obfuscation of origin (`<unknown>`) is **highly suspicious** for phishing or spoofing attempts. Or it could be hidden thanks to Phishing Pot.

**Line 4**

- ![image](https://github.com/user-attachments/assets/2a862e7b-a000-47f6-a26d-e75ac3229a29)
	- `Received: from m42-9.mailgun.net (69.72.42.9) by AM0EUR02FT043.mail.protection.outlook.com (10.13.54.110) with Microsoft SMTP Server (version=TLS1_2, cipher=TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384) id 15.20.6631.29 via Frontend Transport; Thu, 27 Jul 2023 13:59:00 +0000`
		- **Interpretation:**
		    - This shows the email was relayed **from Mailgun’s server (m42-9.mailgun.net)** using IP `69.72.42.9`.
		    - Delivered to **Microsoft’s Exchange Online Protection (EOP)** node: `AM0EUR02FT043`.
		    - Secure TLS 1.2 was used.
		- **Importance:**
		    - Indicates that **Mailgun was used as the outbound email provider**.
		    - The sender likely **used a Mailgun API or SMTP gateway**.

**Line 3**

- ![image](https://github.com/user-attachments/assets/9c1730b5-940d-4f57-9765-a5689a912c7b)
	- `Received: from AM0EUR02FT043.eop-EUR02.prod.protection.outlook.com (2603:10a6:20b:467:cafe::1) by AS9PR06CA0135.outlook.office365.com (2603:10a6:20b:467::22) with Microsoft SMTP Server (version=TLS1_2, cipher=TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384) id 15.20.6631.29 via Frontend Transport; Thu, 27 Jul 2023 13:59:01 +0000`
		- **Interpretation:**
		    - Microsoft’s EOP server forwarded the message internally to an **Office 365 mailbox server**.
		- **Importance:**
		    - Shows the message **moved deeper into the Microsoft 365 infrastructure**, after passing spam/malware inspection.

**Line 2**

- ![image](https://github.com/user-attachments/assets/4c539375-60e4-43ad-a18b-33e7048fa5f3)
	- `Received: from AS9PR06CA0135.eurprd06.prod.outlook.com (2603:10a6:20b:467::22) by SJ0PR19MB4796.namprd19.prod.outlook.com (2603:10b6:a03:2e0::5) with Microsoft SMTP Server (version=TLS1_2, cipher=TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384) id 15.20.6631.29; Thu, 27 Jul 2023 13:59:02 +0000`
		- **Interpretation:**
		    - Message routed **between two Microsoft regional clusters**, likely **cross-region replication or user-to-user delivery**.
		- **Importance:**
		    - Normal Microsoft internal mail movement. Low forensics value on its own unless headers are manipulated.
`
**Line 1 (Final Hop)**

- ![image](https://github.com/user-attachments/assets/e88f03bd-08e4-4da0-8a02-589efff4e062)
	- `Received: from SJ0PR19MB4796.namprd19.prod.outlook.com (::1) by MN0PR19MB6312.namprd19.prod.outlook.com with HTTPS; Thu, 27 Jul 2023 13:59:04 +0000`
		- **Interpretation:**
		    - Indicates the message was **accessed by the recipient** via Outlook Web Access (OWA) or Microsoft cloud mailbox.
		    - The server `SJ0PR19MB4796` transferred it to `MN0PR19MB6312` using localhost (`::1`) via HTTPS.
		- **Importance:**
		    - Shows the final stage where the email **was rendered or viewed** by the recipient.

###### **Trace Table - Mail Hop Timeline (Earliest to Latest)**

| #   | Hop Type       | Server Name                                 | IP Address              | Notes                                 |
| --- | -------------- | ------------------------------------------- | ----------------------- | ------------------------------------- |
| 1   | Entry Point    | `<unknown>`                                 | `<unknown>`             | Web submission via HTTP               |
| 2   | External Relay | `m42-9.mailgun.net`                         | `69.72.42.9`            | Sent via Mailgun SMTP to Outlook      |
| 3   | EOP Gateway    | `AM0EUR02FT043.mail.protection.outlook.com` | `10.13.54.110`          | Received by Microsoft mail protection |
| 4   | MS365 Internal | `AS9PR06CA0135.outlook.office365.com`       | `2603:10a6:20b:467::22` | Internal routing                      |
| 5   | MS365 Internal | `SJ0PR19MB4796.namprd19.prod.outlook.com`   | `2603:10b6:a03:2e0::5`  | Internal routing                      |
| 6   | Mailbox Access | `MN0PR19MB6312.namprd19.prod.outlook.com`   | `::1`                   | Accessed via HTTPS (Outlook Web)      |

###### **Analysis**

- This section shows the email's path as it travels through various Microsoft mail servers before reaching the recipient. The **originating server is `m42-9.mailgun.net (69.72.42.9)`**, which is a **Mailgun outbound IP** used by marketers or threat actors for mass email distribution. We can also check the IP address on **[AbuseIPDB](https://www.abuseipdb.com)**.
- ![image](https://github.com/user-attachments/assets/2a53eb2b-d391-4c05-9c24-058fb9cc956d)
- All the other `Received:` headers simply document relay hops inside Microsoft’s cloud email infrastructure.

###### **Summary - What We Learn from the `Received:` Section**

- **True Origin Obscured**: Email was submitted via HTTP and not a traditional mail client, suggesting a **programmatic sender**.
- **Mailgun Relay**: It was then transmitted by Mailgun (`69.72.42.9`), a known email-sending service. The domain used SPF, DKIM, and DMARC correctly.
- **Microsoft EOP Chain**: From Mailgun, the message flowed through Microsoft’s **Exchange Online Protection** and internal routing, with **no SPF/DKIM failure**, indicating the infrastructure trusted the email.
- **Spoofing Possibility**: Even though SPF/DKIM passed, this does **not guarantee legitimacy**. The attacker could have fully configured Mailgun for malicious use.

##### 2️⃣ **`Authentication-Results:`** - Email Authentication Evaluation

- This header provides the **final verdict** from the receiving server (here, Microsoft Outlook) on **whether the email passed SPF, DKIM, and DMARC checks**, the trio of modern email authentication protocols. Each mechanism checks different parts of the message (envelope sender, header fields, and cryptographic signatures) to determine if the sender is authorized and the message is unaltered.

- A "pass" for SPF, DKIM, and DMARC can **give false confidence** if the domain itself is malicious but correctly configured, which is a common tactic in phishing campaigns that use **legit infrastructure** to gain trust. In our case, all checks passed, but that’s not the full story. We’ll dig into each mechanism (e.g., `Received-SPF`, `DKIM-Signature`) separately to verify whether the pass results are trustworthy or misleading.

###### **Line by Line Inspection**

- ![image](https://github.com/user-attachments/assets/a06c09a6-bce0-4cbc-9717-e0e2d328dd7b)
- `Authentication-Results: spf=pass (sender IP is 69.72.42.9) smtp.mailfrom=rpsins.247crisis-resilience.com; dkim=pass (signature was verified) header.d=rpsins.247crisis-resilience.com; dmarc=pass action=none header.from=rpsins.247crisis-resilience.com; compauth=pass reason=100`

**Line 1**

- `Authentication-Results: spf=pass (sender IP is 69.72.42.9)`
	- **What it means:**
	    - The **Sender Policy Framework (SPF)** check **passed**.
	    - The sending server’s IP (`69.72.42.9`, which is owned by Mailgun) is **authorized** to send mail **on behalf of** the domain `rpsins.247crisis-resilience.com`.
	- **How we know it’s valid:**
	    - SPF is verified by checking the sending IP against the DNS TXT record of the domain in the `MAIL FROM` (`Return-Path`) field.
	    - This domain allows `69.72.42.9` in its SPF record.
	- **What it implies:**
	    - On its own, a passing SPF **only verifies the sending server is allowed** to send email on behalf of the domain.
	    - **Attackers using Mailgun with a domain they control** (like `rpsins.247crisis-resilience.com`) can still pass SPF.

**Line 2**

- `smtp.mailfrom=rpsins.247crisis-resilience.com;`
	- **What it means:**
	    - The **MAIL FROM** (envelope sender) domain is `rpsins.247crisis-resilience.com`.
	- **How it’s determined:**
	    - This value comes from the SMTP `MAIL FROM:` command, not the visible `From:` header.
	- **Importance:**
	    - Used in SPF checks and bounce message handling. If attackers **own** the domain, they can configure SPF to pass.

**Line 3**

- `dkim=pass (signature was verified) header.d=rpsins.247crisis-resilience.com;`
	- **What it means:**
	    - **DKIM** (DomainKeys Identified Mail) signature was present in the headers and **cryptographically verified**.
	    - The DKIM signature claims the domain `rpsins.247crisis-resilience.com`.
	- **How it’s verified:**
		    - Mailgun signed the message with a private key.
		    - The recipient retrieved the corresponding **public key from the domain’s DNS** and used it to verify the hash.
	- **Implication:**
		    - This proves the message was **not altered in transit** and that **Mailgun is authorized** to send on behalf of the domain.
		    - Again, attackers **can still DKIM-sign with their own domain** if they own it and use Mailgun.

**Line 4**

- `dmarc=pass action=none header.from=rpsins.247crisis-resilience.com;`
	- **What it means:**
	    - **DMARC** (Domain-based Message Authentication, Reporting & Conformance) **passed**.
    - This means both:
        - Either SPF or DKIM passed
        - The `header.from` domain **matches** the domain used in the passing SPF or DKIM result.
	- **What we infer:**
	    - The message is **aligned** per DMARC policy, but:
	        - The DMARC policy (`p=none`) takes **no enforcement action**, which allows **monitoring but not protection**.
	        - This is commonly misused by phishers to **pass DMARC but still spoof**.

**Line 5**

- `compauth=pass reason=100`
	- **What it means:**
	    - **Composite Authentication (compauth)** is Microsoft's internal scoring system for email authentication.
	    - `pass` with `reason=100` indicates **high confidence** that the message is **not spoofed**.
	- **Important caveat:**
	    - Microsoft **relies heavily** on SPF, DKIM, and DMARC.
	    - If all of them pass (as they do here), compauth will also pass **even if it’s a maliciously registered domain**.

###### **Authentication Results Summary Table**

| Protocol | Result             | Domain Used                     | Why It Passed                  | Security Implication                                  |
| -------- | ------------------ | ------------------------------- | ------------------------------ | ----------------------------------------------------- |
| SPF      | pass               | rpsins.247crisis-resilience.com | IP 69.72.42.9 is in SPF record | Valid sender IP, but attacker could own domain        |
| DKIM     | pass               | rpsins.247crisis-resilience.com | Signature matched DNS key      | Content not altered, but domain may be attacker-owned |
| DMARC    | pass (action=none) | rpsins.247crisis-resilience.com | Domain alignment with SPF/DKIM | Passive policy / no protection                        |
| CompAuth | pass (reason=100)  | Internal Microsoft scoring      | All protocols passed           | Can be misled by attacker-controlled infra            |

###### **Summary - What We Learn from `Authentication-Results:`**

- The email **passes all industry-standard authentication checks** (SPF, DKIM, DMARC), and Microsoft’s internal `compauth` logic trusts it.
- However, all these results depend on **attacker-controlled infrastructure** (domain + Mailgun).
- This reinforces the lesson: **Passing authentication doesn’t guarantee the email is safe**, especially in phishing campaigns using bulletproof infrastructure.

##### 3️⃣ **`Received-SPF:`** - Detailed SPF Check

- This field shows the **result of the SPF check** as executed by the recipient's mail server. SPF (Sender Policy Framework) validates that the **IP address used to send the email is authorized by the sender's domain**. This is done by looking up DNS TXT records for the domain and checking if the sending IP is listed.

- In our case, the SPF check **passed**. Microsoft verified that the IP `69.72.42.9` was authorized to send on behalf of `rpsins.247crisis-resilience.com`. However, this doesn’t prove the email is legitimate, it just shows the IP was expected based on SPF records, which **attackers can configure if they own the domain**. This is a prime example of **SPF pass ≠ trustworthy**.

###### **Line by Line Inspection**

- ![image](https://github.com/user-attachments/assets/595c6714-f944-4068-9334-0b201332863b)
- `Received-SPF: Pass (protection.outlook.com: domain of rpsins.247crisis-resilience.com designates 69.72.42.9 as permitted sender) receiver=protection.outlook.com; client-ip=69.72.42.9; helo=m42-9.mailgun.net; pr=C`

**Line 1**

- `Received-SPF: Pass (protection.outlook.com: domain of rpsins.247crisis-resilience.com designates 69.72.42.9 as permitted sender)`
	- **What it means:**
	    - SPF **passed**.
	    - Microsoft’s mail server `protection.outlook.com` **queried the SPF record** of the domain `rpsins.247crisis-resilience.com`.
	    - That domain’s SPF record includes the IP address `69.72.42.9`, which is the IP this email was sent from (via Mailgun).
	- **How this is validated:**
	    - SPF checks are done by querying a DNS TXT record like:
	        - `rpsins.247crisis-resilience.com TXT`
	    - It should return something like:
	        - `v=spf1 include:mailgun.org ~all`
	    - If Mailgun’s servers (including `69.72.42.9`) are listed in the `include:` or `ip4:` directive, SPF passes.
	- **Why attackers can bypass this:**
		- SPF only proves that a domain **authorized the sending IP**, not that the domain itself is legitimate.
		- If an attacker **owns the domain**, they can publish any SPF record they want.

**Line 2**

- `receiver=protection.outlook.com;`
	- **What it means:**
	    - The SPF check was executed by Microsoft’s **email security platform**, `protection.outlook.com`.
	    - This server is responsible for applying security policy and filtering.
	- **Purpose of this field:**
	    - It tells you **which server** performed the SPF validation — useful in multi-hop situations.

**Line 3**

- `client-ip=69.72.42.9;`
	- **What it means:**
	    - This is the **IP address of the sending server** — the one that connected to Microsoft’s mail servers to deliver the email.
	    - `69.72.42.9` belongs to **Mailgun Technologies Inc.**, a known email delivery service.
	- **How this relates to SPF:**
	    - This is the IP that Microsoft checked against the SPF DNS record of `rpsins.247crisis-resilience.com`.
	- **Investigation Tip:**
	    - You can confirm IP ownership using a service like:
	    - `whois 69.72.42.9`
	    - ![image](https://github.com/user-attachments/assets/7c7f67aa-0158-42ff-99eb-50fe4b90ad03)
	    - ![image](https://github.com/user-attachments/assets/35f61120-b1e2-47c6-8cdc-143c4d1ddba8)

**Line 4**

- `helo=m42-9.mailgun.net;`
	- **What it means:**
	    - This is the **HELO/EHLO hostname** presented by the sending server (`69.72.42.9`) during the SMTP handshake.
	    - Mailgun used the hostname `m42-9.mailgun.net`.
	- **Why this matters:**
	    - Some SPF checks validate both `MAIL FROM:` and `HELO:` domains.
	    - If the HELO domain does not match the sending IP’s SPF record, it can trigger a fail, but in this case, it aligns.
	- **Red flag potential:**
	    - If this were something like `gmail.com` but the IP wasn’t owned by Google, it’d be suspicious.
	    - Here, it matches Mailgun’s infrastructure, which is consistent.

**Line 5**

- `pr=C`
	- **What it means:**
	    - This is a proprietary Microsoft **spam filtering flag** (likely short for *probability rating* or *policy reason*).
	    - `C` generally means the message is categorized as **Commercial** (e.g., marketing or newsletter content).
	- **Contextual use:**
	    - Not security-relevant by itself but can affect how Microsoft’s system classifies and prioritizes the message.

###### **`Received-SPF` Analysis Summary Table**

|Field|Value|Meaning|Security Insight|
|---|---|---|---|
|Result|Pass|SPF validation succeeded|The sending IP is listed in the domain's SPF record|
|Receiver|protection.outlook.com|Microsoft performed the SPF check|Identifies which mail gateway validated SPF|
|Client IP|69.72.42.9|IP of the sending server (Mailgun)|Legitimate IP for Mailgun, used to spoof trusted-looking senders|
|HELO|m42-9.mailgun.net|Hostname used during SMTP handshake|Consistent with Mailgun infrastructure|
|pr|C|Microsoft classifies this as Commercial|Suggests email was flagged as marketing, possibly a lure|

###### **Summary - What We Learn from `Received-SPF`**

- SPF **validates that the sending IP (Mailgun)** is authorized by the domain `rpsins.247crisis-resilience.com`.
- This **does NOT prove the email is legitimate**, because the attacker **controls both the domain and the sending server**.
- Mailgun is a common service for both **legitimate and malicious bulk email**.
- The email **passed SPF at Microsoft's filtering gateway**, and it **used a proper Mailgun hostname and IP**, which helps it **evade simple anti-spoofing filters**.

##### 4️⃣ `X-IncomingTopHeaderMarker:` - Internal Header Integrity Metadata

- `X-IncomingTopHeaderMarker` is a **Microsoft-specific diagnostic header** used primarily within Exchange Online Protection (EOP). It provides metadata that helps Microsoft's anti-spam, anti-phishing, and message routing systems track **header integrity**, message size, and the number of headers during transit.

- This header isn’t meant for public trust validation like SPF/DKIM/DMARC, but it's very useful during forensics and threat hunting in **Microsoft 365** environments. If an attacker tries to forge or strip headers to hide the true origin of a message, this header helps identify discrepancies.

###### **Line by Line Inspection**

- ![image](https://github.com/user-attachments/assets/c059030b-4800-46e2-809d-6c5462d326f8)
- `OriginalChecksum:21D3E6243B0C1DE7B24524CEBAF16B81705EAB2550CE9BB9F4CF803CEAEA6917;UpperCasedChecksum:384AE7BEB55816DBADB760F92B3DF6B16766460B0896FED1292FAFF919C4D03A;SizeAsReceived:1187;Count:14`

**Line 1**

- `OriginalChecksum:21D3E6243B0C1DE7B24524CEBAF16B81705EAB2550CE9BB9F4CF803CEAEA6917;`
    - **What it means:**
        - This is a **cryptographic hash (likely SHA-256)** of the raw top email headers _exactly as received_ by Microsoft.
        - It creates a **unique fingerprint** of the header block before any internal processing occurs.
    - **How this is validated:**
        - This hash can be compared to internal recalculations to detect **modifications or corruption** during transit.
        - If the same message had different headers at different points, the checksum would differ.
    - **Why this matters in investigations:**
        - Useful for spotting **header tampering or forgery**, especially in **BEC** or **phishing campaigns**.
        - Security analysts can use this to detect inconsistencies if they have logs or hashes from multiple hops.

**Line 2**

- `UpperCasedChecksum:384AE7BEB55816DBADB760F92B3DF6B16766460B0896FED1292FAFF919C4D03A;`
    - **What it means:**
        - This is a hash of the same headers, but with **field names normalized to uppercase** (e.g., `From:` → `FROM:`).
        - Ensures **capitalization variations** don’t affect header integrity checks.
    - **How this is validated:**
        - Internal systems process headers in a case-insensitive way. This checksum ensures that **case-only changes** won’t trigger false positives.
    - **Why attackers care:**
        - Some email-borne attacks attempt **header spoofing by altering case**, trying to bypass naive parsers. This line helps prevent that.

**Line 3**
- `SizeAsReceived:1187;`
    - **What it means:**
        - The **byte size** of the entire top header section (not the full message) was **1187 bytes** when received.
    - **How this is validated:**
        - Compared internally to typical message size ranges from that source.
        - Can be used in anomaly detection — sudden drops or increases may indicate **header injection or omission**.
    - **Use in forensics:**
        - Useful when analyzing multiple emails from the same sender/domain.
        - Helps **baseline expected message structure** size.

**Line 4**
- `Count:14`
    - **What it means:**
        - This indicates there were **14 total top-level headers** at the time the message was received.
    - **How this is validated:**
        - Counted directly by Microsoft’s SMTP gateway when the message is ingested.
    - **Why it’s important:**
        - Attackers often add or remove headers (like `Reply-To`, `Return-Path`, etc.) to manipulate user trust or hide origin.
        - If an analyst knows that legitimate emails from a vendor typically have 12 headers, and a suspicious one has 18, that’s a **red flag**.


###### **Summary - What we Learned from `X-IncomingTopHeaderMarker`**

- The `X-IncomingTopHeaderMarker` header is a **Microsoft-internal integrity control** that fingerprints the top portion of the email’s headers. It does not influence email delivery or reputation scoring but is **used behind the scenes to detect forgery, injection, and anomalies**.

|Field|Use|
|---|---|
|`OriginalChecksum`|Detects _any modification_ of the original header block|
|`UpperCasedChecksum`|Catches _case-only spoofing_ attempts|
|`SizeAsReceived`|Helps baseline _normal message structure_|
|`Count`|Useful to spot _suspicious additions/removals_ of headers|

- ✅ This email's values are internally consistent and typical. No header tampering is evident.

##### 5️⃣ **`DKIM-Signature:`** - Cryptographic Integrity

- DomainKeys Identified Mail (DKIM) adds a **digital signature** to an email to prove that **specific headers and body content** were **not altered in transit** and that they were **authorized by the domain owner**.

- When validated, it assures the recipient that the message really came from an authorized mail server for the domain listed in the `d=` field and that the email content hasn't been tampered with.

###### **Line by Line Inspection**

- ![image](https://github.com/user-attachments/assets/4deb0a1d-851a-44d9-af6e-bf2a33e539e9)
- `DKIM-Signature: a=rsa-sha256; v=1; c=relaxed/relaxed; d=rpsins.247crisis-resilience.com; q=dns/txt; s=pic; t=1690466340; x=1690473540; h=Content-Type: Content-Transfer-Encoding: Message-Id: To: To: From: From: Subject: Subject: Mime-Version: Date: Sender: Sender; bh=HeWbuCiLBcspIx5xTdDSAIJ0mYN5AtiF1Hnn0JgLAPQ=; b=bxKpNYEanLdW8xlJVTcGBR0ITd2bbND7ZBgdL0xuQHxoRza9WCm86FU+gHOGrOARR1Tb6jpwVVacHPbxme4ZqaP9yMMVm2ofgC4v9jmfdtuaofQnXkc73dVhOjnuJG7uTP7K9urRMZlxzBf/3UYq44m7Ocsno60ptsgKmt+040w=

**Line 1**

- `a=rsa-sha256;`
    - **What it means:**
        - The **algorithm used for signing** is **RSA with SHA-256**, which is the current DKIM standard.
    - **Why it matters:**
        - Older or weaker algorithms (like `rsa-sha1`) are deprecated and vulnerable to collision attacks.
- `v=1;`
    - **What it means:**
        - This indicates **DKIM version 1**, the only standardized version in production use today.
- `c=relaxed/relaxed;`
    - **What it means:**
        - This is the **canonicalization mode** used for:
        - Headers (`relaxed`)
        - Body (`relaxed`)
    - **How `relaxed` mode works:**
        - Ignores insignificant whitespace and line breaks — more tolerant to minor reformatting.
    - **Why it's used:**
        - Helps prevent accidental validation failures due to changes by mail relays (like Gmail adding a blank line).

**Line 2**

- `d=rpsins.247crisis-resilience.com;`
    - **What it means:**
        - The **signing domain** that owns the DKIM public key used to verify this message.
    - **How this is validated:**
        - Recipient performs a **DNS TXT lookup** for: `pic._domainkey.rpsins.247crisis-resilience.com` and retrieves the **public key** used to verify the message's signature.
    - **Why it's significant:**
        - If an attacker owns the domain, they control the key. If they spoof another domain, the DKIM check fails.
- `q=dns/txt;`
	- **What it means:**
		- The **query method** to retrieve the DKIM key. It tells validators to perform a `TXT` DNS lookup (standard practice).
- `s=pic;`
	- **What it means:**
		- The **selector** is `pic`, which specifies which DKIM key to use under the domain.
	- Combined with `d=`, it queries:
		- `pic._domainkey.rpsins.247crisis-resilience.com`
- `t=1690466340;`
	- **What it means:**  
		This is a **Unix timestamp** marking when the DKIM signature was **created**.
	- **Convert to readable time:**  
		`Thu, 27 Jul 2023 13:59:00 UTC`
- `x=1690473540;`
	- **What it means:**  
		This is the **expiration time** of the DKIM signature.
	- **Convert to readable time:**  
		`Thu, 27 Jul 2023 15:59:00 UTC` (2 hours after creation)

**Line 3**

- `h=Content-Type: Content-Transfer-Encoding: Message-Id: To: To: From: From: Subject: Subject: Mime-Version: Date: Sender: Sender;`
	- **What it means:**  
	    This is the list of **headers included in the DKIM hash** — in the order they were signed.
	- **Why headers repeat:**  
	    Some headers may appear more than once (e.g., multiple `From:` fields), and DKIM allows signing them multiple times.
	- **Purpose:**  
	    If **any of these headers are modified**, the DKIM signature becomes invalid. This defends against **header injection/spoofing**.

**Line 4**

- `bh=HeWbuCiLBcspIx5xTdDSAIJ0mYN5AtiF1Hnn0JgLAPQ=;`
	- **What it means:**  
	    This is the **hash of the body** (after canonicalization).
	- **Purpose:**  
	    When a DKIM validator receives the email, it canonicalizes the body and computes its hash. If it doesn’t match `bh=`, it fails.

**Line 5**

- `b=bxKpNYEanLdW8xlJVTcGBR0ITd2bbND7ZBgdL0xuQHxoRza9WCm86FU+gHOGrOARR1Tb6jpwVVacHPbxme4ZqaP9yMMVm2ofgC4v9jmfdtuaofQnXkc73dVhOjnuJG7uTP7K9urRMZlxzBf/3UYq44m7Ocsno60ptsgKmt+040w=`
	- **What it means:**  
	    - This is the actual **DKIM digital signature** — an RSA-signed digest of the specified headers and the body hash.
	- **How it’s validated:**  
	    - The receiving mail server:
	    - Pulls the **public key** from DNS (using `s=pic` and `d=rpsins.247crisis-resilience.com`)
	    - Verifies this signature against the hashed content
	    - If it matches, the DKIM check **passes**

###### **Summary – `DKIM-Signature`**

|Field|Meaning|
|---|---|
|`d=`|Domain signing the message (`rpsins.247crisis-resilience.com`)|
|`s=`|DKIM selector used in the DNS query (`pic`)|
|`bh=`|Hash of the canonicalized body content|
|`b=`|Signature block — RSA-encrypted hash of signed headers + body hash|
|`c=`|Canonicalization (normalization) used: `relaxed/relaxed`|
|`h=`|List of headers protected by the DKIM signature|
|`t=` / `x=`|Time of signing and expiration (2-hour window)|

**DKIM passed**, which tells us:

- The email came from a server **authorized by the signing domain**.
- The **listed headers and body have not been modified** since it left that server.

> ⚠️ Caveat: If the attacker **controls the domain**, they can generate valid DKIM signatures. DKIM alone does **not** prove the domain is trustworthy.

##### 6️⃣ **`X-Mailgun-Sending-Ip / X-Mailgun-Sid:`** (Mailgun Indicators)

- These are **non-standard X-headers** injected by **Mailgun**, a third-party email delivery service, used to track and verify delivery origin and routing. These are often useful in phishing investigations when trying to attribute or trace infrastructure.

###### **Lines by Line Inspection**

- ![image](https://github.com/user-attachments/assets/9d3f6bb0-2486-4138-868f-9d65a8c9cb3f)
- `X-Mailgun-Sending-Ip: 69.72.42.9`
- `X-Mailgun-Sid: WyIxYTg0ZCIsInJvZHJpZ28tZi1wQGhvdG1haWwuY29tIiwiY2I4M2Q2Il0=`

**Line 1**

- `X-Mailgun-Sending-Ip: 69.72.42.9`
    - **What it means:**  
        - The public **IP address** from which Mailgun **sent this email**. It confirms that the outbound SMTP connection originated from `69.72.42.9`.
    - **How to validate:**
        - Perform a reverse DNS (`PTR`) and `WHOIS` lookup:
	        - `dig -x 69.72.42.9` & `whois 69.72.42.9` (we did `whois` earlier)
        - This often maps back to **Mailgun infrastructure** hosted under `mailgun.net`.
        - ![image](https://github.com/user-attachments/assets/6bd40836-ccc3-4aa6-8846-0443be18f7f0)
    - **Use in investigation:**
		- Confirms that **Mailgun infrastructure was used**.
		- Cross-reference with SPF: the IP should be in Mailgun’s `include:` range.
		- This IP was also referenced in:
		    - `Received-SPF`
		    - One of the `Received:` hops

**Line 2**

- `X-Mailgun-Sid: WyIxYTg0ZCIsInJvZHJpZ28tZi1wQGhvdG1haWwuY29tIiwiY2I4M2Q2Il0=`
    - **What it means:**  
        - This is **Mailgun’s internal session ID**, **Base64-encoded**.
    - **What it likely encodes:**  
        - Decoding it reveals:
            - `echo 'WyIxYTg0ZCIsInJvZHJpZ28tZi1wQGhvdG1haWwuY29tIiwiY2I4M2Q2Il0=' | base64 -d`
        - Result:
            - `["1a84d","rodrigo-f-p@hotmail.com","cb83d6"]`
        - ![image](https://github.com/user-attachments/assets/411d73d5-5baa-40f9-9b82-c8e1d4de4b71)
        - **Decoded breakdown:**
	        - `1a84d` - Internal Mailgun customer or message ID
	        - `rodrigo-f-p@hotmail.com` - The **Mailgun account or sender’s registered email**
	        - `cb83d6` - Possibly the Mailgun campaign, domain ID, or batch ID
	- **Use in investigation:**
		- Helps correlate this email with **Mailgun’s logs**, if cooperation is obtained.
		- Links this message to a **sender’s account** or campaign behind the scenes.

###### **Summary - `X-Mailgun-*`**

|Header|Purpose|
|---|---|
|`X-Mailgun-Sending-Ip`|Confirms **which IP** (Mailgun’s server) sent the message — `69.72.42.9`.|
|`X-Mailgun-Sid`|Encoded identifier containing sender info and internal metadata (Base64-decoded shows a Mailgun account email).|

- **Why it matters:**
	- These headers strongly indicate the message **originated via Mailgun’s platform**, and the sender used (or compromised) a Mailgun account to send this.
	- **Investigators can leverage this** to contact Mailgun or correlate with abuse logs.

##### 7️⃣ Email Metadata & Mailgun Headers

- This section provides foundational metadata about the email, who it claims to be from, who it's going to, when it was sent, and its basic characteristics (formatting, encoding, tracking behavior, etc.). These fields are useful for initial triage in phishing investigations because attackers often spoof trusted brands while slipping in small inconsistencies.

###### **Line by Line Inspection**

- ![image](https://github.com/user-attachments/assets/2237fe44-fcfc-4d30-a6ec-5039bfdf7467)

```
Sender: coindesk@rpsins.247crisis-resilience.com
Date: Thu, 27 Jul 2023 13:59:00 +0000
Subject: No time to lose - XRP Token Allocation in progress
From: CoinDesk <coindesk@rpsins.247crisis-resilience.com>
To: phishing@pot
X-Mailgun-Track: false
Message-Id: <20230727135900.d69bd9ffb69e1a18@rpsins.247crisis-resilience.com>
Content-Transfer-Encoding: quoted-printable
Content-Type: text/html; charset="utf-8"
X-IncomingHeaderCount: 14
Return-Path:
 bounce+699d35.cb83d6-phishing@pot=hotmail.com@rpsins.247crisis-resilience.com
```

**Identity & Timestamp Fields**

**Line 1**

- `Sender: coindesk@rpsins.247crisis-resilience.com`
    - **What it means:**
        - This is the **envelope sender** (used by the SMTP server to indicate where the message came from). It differs from the `From:` field in that it’s part of the SMTP protocol, not the message body.
    - **Why it matters:**
        - If this differs significantly from the `From:` address, it could indicate spoofing or redirection behavior.
    - **How to verify:**
        - Compare against the **Return-Path** and **From** fields. All should align in legitimate mail.

**Line 2**

- `Date: Thu, 27 Jul 2023 13:59:00 +0000`
    - **What it means:**
        - This is when the message claims to have been composed and sent.
    - **How to verify:**
        - Compare with timestamps in the `Received:` headers. A forged or future timestamp may indicate manipulation.

**Display Name & Recipient Fields**

**Line 3**

- `Subject: No time to lose - XRP Token Allocation in progress`
    - **What it means:**
        - The subject line is a strong **social engineering** trigger. This one conveys urgency and cryptocurrency-related content.
    - **Why it matters in phishing:**
        - Emotional triggers like urgency or FOMO are red flags in phishing emails.
    - **Triage tip:**
        - Match subject with message body and known campaigns.

**Line 4**

- `From: CoinDesk <coindesk@rpsins.247crisis-resilience.com>`
    - **What it means:**
        - This is the **visible** sender to the user (display name + email).
    - **Why it's suspicious:**
        - CoinDesk would normally send from a `@coindesk.com` domain — here it uses a suspicious domain resembling a business continuity company (`247crisis-resilience.com`).
    - **Verification steps:**
        - Check against CoinDesk’s known sender addresses.
        - Validate `From:` vs `Return-Path`, `Sender`, and `DKIM`.

**Line 5**

- `To: phishing@pot`
    - **What it means:**
        - The intended recipient field. This is truncated and malformed.
    - **Why it’s notable:**
        - It’s unclear what address “@pot” resolves to. Could be a placeholder, testing address, or error in header generation.
    - **Follow-up:**
        - Examine raw SMTP envelope and actual delivery path.

**Tracking, Encoding, and Format Fields**

**Line 6**

- `X-Mailgun-Track: false`
    - **What it means:**
        - Mailgun (the sending service) **disabled open/click tracking** for this message.
    - **Why it’s interesting:**
        - Most marketing or scam emails use tracking — turning it off could mean the sender doesn’t want visibility, or is avoiding detection.

**Line 7**

- `Message-Id: <20230727135900.d69bd9ffb69e1a18@rpsins.247crisis-resilience.com>`
    - **What it means:**
        - This is a **unique identifier** for the email. Often auto-generated by the sending mail server.
    - **Triage use:**
        - Useful for correlation with log entries or identifying duplicates.
            

**Line 8**

- `Content-Transfer-Encoding: quoted-printable`
    - **What it means:**
        - The email body content has been encoded in the **quoted-printable** format, which allows for safe transmission of special characters in plain-text messages.
    - **Why it matters:**
        - Helps preserve HTML structure, but can also obfuscate payloads or phishing links.
    - **Analyst tip:**
        - Decode this format to review the real HTML.
            

**Line 9**

- `Content-Type: text/html; charset="utf-8"`
    - **What it means:**
        - The email body is HTML (not plain text), and it uses UTF-8 character encoding.
    - **Phishing red flag:**
        - HTML allows for obfuscated links, image-based text, and fake form elements.

**Header Integrity Field**

**Line 10**

- `X-IncomingHeaderCount: 14`
    - **What it means:**
        - Microsoft tracks how many headers are included in the incoming message.
    - **Why it matters:**
        - A significantly **low or high count** might hint at header manipulation or incomplete messages.

Bounce and Delivery Tracking Field

**Line 11**

- `Return-Path: bounce+699d35.cb83d6-phishing@pot=hotmail.com@rpsins.247crisis-resilience.com`
    - **What it means:**
        - This is where bounce (undeliverable) messages would go. Mailgun generated it.
    - **Breakdown:**
        - `bounce+699d35.cb83d6-phishing@pot=hotmail.com` is a **VERP (Variable Envelope Return Path)** format used to uniquely track delivery per recipient.
        - `@rpsins.247crisis-resilience.com` is the final domain, showing it’s routed through Mailgun on behalf of the phishing infrastructure.
    - **Red flag:**
        - The presence of `=hotmail.com` hints that the email was originally targeted at a Hotmail user, and has been rerouted or captured via a phishing trap or misconfiguration.

###### **Summary of Email Metadata Section**

|Category|Red Flags / Points of Interest|
|---|---|
|Sender Identity|Suspicious domain masquerading as CoinDesk|
|Timestamp Consistency|Timestamp appears consistent with the `Received:` path|
|Subject Line|FOMO/Urgency and cryptocurrency trigger language|
|HTML Format & Encoding|Use of HTML + quoted-printable enables link hiding and obfuscation|
|Bounce Address (Return-Path)|Generated by Mailgun; domain mismatch and possible phishing routing (`=hotmail.com` pattern)|
|Tracking Disabled|No open/click tracking; could be stealth delivery or OPSEC-aware attacker|

##### 8️⃣ Microsoft Exchange Routing & Anti-Spam Metadata

- This group of headers provides **deep metadata from Microsoft Exchange Online Protection (EOP)** and related anti-spam and routing systems. They are mostly used **internally by Microsoft** to determine message expiration, transport latency, routing diagnostics, and spam filtering results. However, they are useful in forensic analysis to determine **how the message was handled**, **who processed it**, and **what decisions were made about it.**

###### **Line by Line Analysis**

- ![image](https://github.com/user-attachments/assets/2e48f5fb-f0da-4a2c-aaa9-8cc1cd2f67e9)

```
X-MS-Exchange-Organization-ExpirationStartTime: 27 Jul 2023 13:59:01.1549
 (UTC)
X-MS-Exchange-Organization-ExpirationStartTimeReason: OriginalSubmit
X-MS-Exchange-Organization-ExpirationInterval: 1:00:00:00.0000000
X-MS-Exchange-Organization-ExpirationIntervalReason: OriginalSubmit
X-MS-Exchange-Organization-Network-Message-Id:
 ce179516-f234-4e27-e650-08db8ea9a166
X-EOPAttributedMessage: 0
X-EOPTenantAttributedMessage: 84df9e7f-e9f6-40af-b435-aaaaaaaaaaaa:0
X-MS-Exchange-Organization-MessageDirectionality: Incoming
X-MS-PublicTrafficType: Email
X-MS-TrafficTypeDiagnostic:
 AM0EUR02FT043:EE_|SJ0PR19MB4796:EE_|MN0PR19MB6312:EE_
X-MS-Exchange-Organization-AuthSource:
 AM0EUR02FT043.eop-EUR02.prod.protection.outlook.com
X-MS-Exchange-Organization-AuthAs: Anonymous
X-MS-UserLastLogonTime: 7/27/2023 1:52:32 PM
X-MS-Office365-Filtering-Correlation-Id: ce179516-f234-4e27-e650-08db8ea9a166
X-MS-Exchange-EOPDirect: true
X-Sender-IP: 69.72.42.9
X-SID-PRA: COINDESK@RPSINS.247CRISIS-RESILIENCE.COM
X-SID-Result: PASS
X-MS-Exchange-Organization-PCL: 2
X-MS-Exchange-Organization-SCL: 9
X-Microsoft-Antispam: BCL:0;
X-MS-Exchange-CrossTenant-OriginalArrivalTime: 27 Jul 2023 13:59:00.9049
 (UTC)
X-MS-Exchange-CrossTenant-Network-Message-Id: ce179516-f234-4e27-e650-08db8ea9a166
X-MS-Exchange-CrossTenant-Id: 84df9e7f-e9f6-40af-b435-aaaaaaaaaaaa
X-MS-Exchange-CrossTenant-AuthSource:
 AM0EUR02FT043.eop-EUR02.prod.protection.outlook.com
X-MS-Exchange-CrossTenant-AuthAs: Anonymous
X-MS-Exchange-CrossTenant-FromEntityHeader: Internet
X-MS-Exchange-CrossTenant-RMS-PersistedConsumerOrg:
 00000000-0000-0000-0000-000000000000
X-MS-Exchange-Transport-CrossTenantHeadersStamped: SJ0PR19MB4796
X-MS-Exchange-Transport-EndToEndLatency: 00:00:03.4965456
X-MS-Exchange-Processed-By-BccFoldering: 15.20.6631.026
X-Microsoft-Antispam-Mailbox-Delivery:
abwl:0;wl:0;pcwl:0;kl:0;dwl:0;dkl:0;rwl:0;ucf:0;jmr:0;ex:0;auth:1;dest:J;OFR:SpamFilterAuthJ;ENG:(5062000305)(920221119067)(90000117)(920221120067)(90005022)(91005020)(91035115)(9050020)(9100338)(944500132)(2008001134)(2008121020)(4810010)(4910033)(8820095)(9710001)(9610025)(9520007)(10115022)(9320005)(9245025);RF:JunkEmail;
X-Message-Delivery: Vj0xLjE7dXM9MDtsPTA7YT0wO0Q9MjtHRD0yO1NDTD02
```

**Message Expiration Timing (TTL Logic)**

**Line 1**

- `X-MS-Exchange-Organization-ExpirationStartTime: 27 Jul 2023 13:59:01.1549 (UTC)`
    - **What it means:**
        - The time Microsoft starts counting down for **message expiration or auto-deletion**, typically for retention policies or auto-purge.
    - **Why it matters:**
        - Can indicate whether the message was meant to expire or had specific TTL instructions.

**Line 2**

- `X-MS-Exchange-Organization-ExpirationStartTimeReason: OriginalSubmit`
    - **What it means:**
        - The expiration timer started when the message was **initially submitted** — not during forwarding or relay.

**Line 3**

- `X-MS-Exchange-Organization-ExpirationInterval: 1:00:00:00.0000000`
    - **What it means:**
        - The message is set to expire **after 1 day** (1 day, 0 hours, 0 minutes, 0 seconds).
    - **Contextual clue:**
        - Might be related to tenant-level policies (like quarantine retention).
            

**Line 4**

- `X-MS-Exchange-Organization-ExpirationIntervalReason: OriginalSubmit`
    - **What it means:**
        - The 1-day expiration was assigned at **original submission**, not later during processing.

**Routing ID & Tenant Tracking**

**Line 5**

- `X-MS-Exchange-Organization-Network-Message-Id: ce179516-f234-4e27-e650-08db8ea9a166`
    - **What it means:**
        - A **globally unique identifier** Microsoft uses to trace a message through its transport pipeline.

**Line 6**

- `X-EOPAttributedMessage: 0`
    - **What it means:**
        - EOP failed to **attribute** this message to a specific **policy or rule set** (value `0` = unclassified).

**Line 7**

- `X-EOPTenantAttributedMessage: 84df9e7f-e9f6-40af-b435-aaaaaaaaaaaa:0`
    - **What it means:**
        - Shows tenant-specific classification (`:0` = no match), and the tenant GUID for the Office365 instance.


**Message Direction & Traffic Type**

**Line 8**

- `X-MS-Exchange-Organization-MessageDirectionality: Incoming`
    - **What it means:**
        - Message originated **outside the organization** — important distinction for filtering.

**Line 9**

- `X-MS-PublicTrafficType: Email`
    - **What it means:**
        - This is **public-facing email**, not internal or system traffic.

**Line 10**

- `X-MS-TrafficTypeDiagnostic: AM0EUR02FT043:EE_|SJ0PR19MB4796:EE_|MN0PR19MB6312:EE_`
    - **What it means:**
        - An **internal Microsoft traffic path trace** showing which Exchange transport roles touched the message.
    - **Each value** (`X:X`) follows format `Server:Role`, where `EE_` likely represents “Edge” or “Exchange Edge Transport.”

**Auth Source and User Context**

**Line 11**

- `X-MS-Exchange-Organization-AuthSource: AM0EUR02FT043.eop-EUR02.prod.protection.outlook.com`
    - **What it means:**
        - Indicates **where the authentication process began** — this is Microsoft’s EOP gateway.

**Line 12**

- `X-MS-Exchange-Organization-AuthAs: Anonymous`
    - **What it means:**
        - Message was **not authenticated with login credentials** — typical of inbound mail from external sources.

**Line 13**

- `X-MS-UserLastLogonTime: 7/27/2023 1:52:32 PM`
    - **What it means:**
        - Shows the **last time the recipient mailbox logged in**. May aid in correlating account activity.

**Correlation and Routing Confirmation**

**Line 14**

- `X-MS-Office365-Filtering-Correlation-Id: ce179516-f234-4e27-e650-08db8ea9a166`
    - **What it means:**
        - Used to **correlate this message across Office365 logs**, spam filters, and quarantine systems.

**Line 15**

- `X-MS-Exchange-EOPDirect: true`
    - **What it means:**
        - Message was **delivered directly via Microsoft’s EOP**, not through any third-party connectors.
            

**Line 16**

- `X-Sender-IP: 69.72.42.9`
    - **What it means:**
        - Microsoft’s record of the **public IP that actually connected to them** to send this message (Mailgun).

**Sender Identity and Filtering Verdicts**

**Line 17**

- `X-SID-PRA: COINDESK@RPSINS.247CRISIS-RESILIENCE.COM`
    - **What it means:**
        - Microsoft’s **Purported Responsible Address** — the user/domain ultimately considered responsible for sending this message.

**Line 18**

- `X-SID-Result: PASS`
    - **What it means:**
        - The PRA evaluation passed, meaning the sender aligned with Microsoft’s trust checks.

**Line 19**

- `X-MS-Exchange-Organization-PCL: 2`
    - **What it means:**
        - **Phishing Confidence Level** — value of `2` indicates **moderate suspicion**.
    - **Scale:**
        - `-1`: trusted
        - `0`: neutral
        - `1`: low suspicion
        - `2`: moderate suspicion
        - `3`: high suspicion
            

**Line 20**

- `X-MS-Exchange-Organization-SCL: 9`
    - **What it means:**
        - **Spam Confidence Level** — 9 is the **highest score**, strongly indicating **spam or phishing**.
    - **Thresholds:**
        - `0–1`: likely clean
        - `5`: borderline spam
        - `9`: outright spam

**Antispam & Cross-Tenant Security Context**

**Line 21**

- `X-Microsoft-Antispam: BCL:0;`
    - **What it means:**
        - **Bulk Complaint Level (BCL)** of `0` — no history of user complaints about similar messages.

**Line 22**

- `X-MS-Exchange-CrossTenant-OriginalArrivalTime: 27 Jul 2023 13:59:00.9049 (UTC)`
    
    - **What it means:**
        - Exact time the message **entered Microsoft’s infrastructure** from the sending server.

**Line 23**

- `X-MS-Exchange-CrossTenant-Network-Message-Id: ce179516-f234-4e27-e650-08db8ea9a166`
    - **What it means:**
        - Matches the message ID from earlier — shows cross-tenant traceability.

**Line 24**

- `X-MS-Exchange-CrossTenant-Id: 84df9e7f-e9f6-40af-b435-aaaaaaaaaaaa`
    - **What it means:**
        - Identifies the **Microsoft tenant** receiving the message (likely the phishing-report mailbox).

**Line 25**

- `X-MS-Exchange-CrossTenant-AuthSource: AM0EUR02FT043.eop-EUR02.prod.protection.outlook.com`
    - **What it means:**
        - Indicates where cross-tenant auth was evaluated.

**Line 26**

- `X-MS-Exchange-CrossTenant-AuthAs: Anonymous`
    - **What it means:**
        - Message came from an **unauthenticated user** in the external tenant.

**Line 27**

- `X-MS-Exchange-CrossTenant-FromEntityHeader: Internet`
    - **What it means:**
        - Message source is categorized as “Internet,” not internal or partner tenant.

**Line 28**

- `X-MS-Exchange-CrossTenant-RMS-PersistedConsumerOrg: 00000000-0000-0000-0000-000000000000`
    - **What it means:**
        - Rights Management System (RMS) not applied; defaulted to null GUID.

**Message Timing & Filtering Path**

**Line 29**

- `X-MS-Exchange-Transport-CrossTenantHeadersStamped: SJ0PR19MB4796`
    - **What it means:**
        - Final transport server to stamp headers before delivery to recipient mailbox.

**Line 30**

- `X-MS-Exchange-Transport-EndToEndLatency: 00:00:03.4965456`
    - **What it means:**
        - Time between message **entering and exiting** Microsoft’s infrastructure (3.5 seconds total).

**Line 31**

- `X-MS-Exchange-Processed-By-BccFoldering: 15.20.6631.026`
    - **What it means:**
        - Message was **checked for BCC delivery policies** by a specific version of Microsoft’s Exchange software.

**Final Spam Filtering Decision**

**Line 32**

- `X-Microsoft-Antispam-Mailbox-Delivery:`
    - **What it means:**
        - Detailed list of spam filtering engines, rule evaluations, and disposition (`RF:JunkEmail`) meaning it **was routed to the Junk folder**.
    - **Note:**
        - These `ENG:(...)` values map to internal spam rules and thresholds.

**Line 33**

- `X-Message-Delivery: Vj0xLjE7dXM9MDtsPTA7YT0wO0Q9MjtHRD0yO1NDTD02`
    - **What it means:**
        - Encoded Microsoft value showing final delivery routing verdict. Useful mostly to Microsoft engineers.

###### **Section Summary: Exchange Anti-Spam Verdicts & Routing Metadata**

|Function|Key Indicators|
|---|---|
|Message Lifetime|1-day expiration interval; set at original submission|
|Direction & Source|Message marked as **incoming, unauthenticated**, from an **external** source|
|Spam & Phishing Confidence|SCL = 9 (**severe spam**); PCL = 2 (**moderate phishing suspicion**)|
|Tenant Traceability|Unique message ID and cross-tenant tracking via Microsoft GUIDs|
|Anti-Spam Decision|Routed to **Junk Folder** (`RF:JunkEmail`), triggered by Exchange filters|

##### 9️⃣ `X-Microsoft-Antispam-Message-Info:` -  Encrypted Spam Detection Metadata

- The `X-Microsoft-Antispam-Message-Info:` field contains an **obfuscated or Base64-encoded string** generated by Microsoft’s **anti-spam and anti-phishing engines**. This block is **not meant for direct human interpretation** and is typically **used internally by Microsoft engineers** to correlate the message against threat models, filters, and machine learning features.

###### **Explanation**

- ![image](https://github.com/user-attachments/assets/3b0e1677-d7bd-4228-94ac-eabf8b484c6a)
- **Key Characteristics:**
	- Usually begins with the encoding format like `=?utf-8?B?`, which indicates:
	    - `utf-8`: character encoding
	    - `B`: Base64-encoded content
	- May contain:
	    - Unique spam fingerprints
	    - ML rule matches
	    - Correlation hashes across spam campaigns
	    - Heuristic rule outcomes or antispam model decisions
- **Why it matters in forensics:**
	- While **not decodable without Microsoft's internal tools**, the **presence** and **length** of this string can be a strong signal:
	    - A **long string** can indicate **deep analysis and spam/phishing rule engagement**.
	    - If a message ends up in **Junk or Quarantine**, this header helps confirm that antispam logic was applied.
	- The correlation ID and encoded message info may be used by Microsoft support to **trace message classification history**.


##### 🔟 Final Section Summary - **Email Body (HTML Content)**

- This is the **HTML-rendered body of the email**, containing the **visual layout, message content, links, images, and styling**. It is what the recipient actually sees when opening the email in a modern email client like Outlook, Gmail, or Thunderbird.

- This portion is **not part of the headers**!

###### **Explanation**

- ![image](https://github.com/user-attachments/assets/61fdbf01-5614-4d41-9d3e-56fbc2b24b11)

Key Red Flags Found in This HTML Content (What we found in our content and body analysis):

| Type                          | Indicator                                                                         | What It Suggests                                                            |
| ----------------------------- | --------------------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| **Link Mismatch**             | `https://mail119-ripple.net/...` used instead of official `coindesk.com`          | Likely phishing site — mismatch between **display name (CoinDesk)** and URL |
| **Tracking Pixel**            | `<img src="...gif?...">` 1x1 invisible image                                      | Tracking for email open events — common in marketing, but also phishing     |
| **Fake Branding**             | Logo hosted on Sailthru CDN (not Coindesk domain)                                 | May be stolen branding to lend legitimacy                                   |
| **Urgency & Scarcity**        | "No time to lose", "Token Allocation in progress", "30-day campaign", "15% bonus" | Classic phishing tactics to manipulate and rush the recipient               |
| **Reward-based Hook**         | Promises of **XRP rewards**, token boosts, DEX/NFT incentives                     | Common bait in crypto phishing                                              |
| **Unsubscribe URL**           | Unsubscribe link points to `mail119-ripple.net`, not CoinDesk or Sailthru         | Fake unsubscribe pages often collect data or serve malware                  |
| **Display vs. Actual Sender** | Shows `CoinDesk`, but domain is `rpsins.247crisis-resilience.com`                 | Display name spoofing to impersonate trusted entity                         |

- **Why This Matters in Forensics:**
	- **Analyzing the body** reveals the attacker’s **social engineering strategy** and **tactics**.
	- **Links and image sources** are critical IOCs (Indicators of Compromise) for identifying **malicious infrastructure**.
	- Even without a malicious file attachment, this message can **redirect victims to credential phishing**, **wallet drainers**, or **browser exploits**.
	- **Email content inspection** should be paired with:
	    - **Link detonation** in a sandbox.
	    - **Passive DNS** or WHOIS lookups on domains (like `mail119-ripple.net`).
	    - **VirusTotal or URLscan.io** analysis for hosted assets or redirect chains.

---



### (Step 4) **Report and Mitigation**:

   - Here we have prepared a report summarizing the analysis and findings. Still in our simulation, since the email is confirmed as phishing, we'll notify the affected employee and the organization about the threat. To go the extra yard, we can implement necessary measures, such as blocking the sender's domain or IP address and educating employees on recognizing phishing attempts.

- Here is our report:

#### 🧪 Lab Report: Email Header Forensics & Body Analysis – XRP Phishing Email

---

##### 📄 Summary

This report provides a full forensic analysis of a phishing email posing as CoinDesk, offering XRP token allocations. It includes header tracing, authentication inspection, and payload analysis. All metadata fields have been grouped logically and analyzed line-by-line where applicable to understand delivery flow, authentication status, and social engineering techniques.

---

##### 📬 `Received:` – Tracing the Mail Path

**Purpose:** Tracks the email’s delivery route across SMTP servers.

(Full breakdown with line-by-line analysis available in prior documentation.)

**Summary:**

- The message passed through multiple Microsoft Outlook infrastructure nodes.
- Originated from Mailgun (`m42-9.mailgun.net` IP: `69.72.42.9`) and passed SPF.
- Message appears to be delivered from an anonymous source impersonating CoinDesk.

---

##### ✅ `Authentication-Results:` – Email Authentication Evaluation

**Purpose:** Aggregated results from SPF, DKIM, DMARC, and Microsoft’s proprietary `compauth`.

**Summary:**

- SPF, DKIM, and DMARC all **passed**, which usually indicates authenticity.
- However, the domain used (`rpsins.247crisis-resilience.com`) is likely attacker-controlled.
- Microsoft `compauth` score was 100 (maximum pass), despite the malicious nature, likely due to proper SPF/DKIM.

---

##### 🔍 `Received-SPF:` – SPF Check Details

**Purpose:** Validates if the IP address is authorized to send on behalf of the domain.

**Summary:**

- SPF check passed: Mailgun’s IP (`69.72.42.9`) is authorized by the sending domain’s SPF record.
- This result is valid **only** because the attacker controls the sending domain.

---

##### 🧮 `X-IncomingTopHeaderMarker:` – Microsoft Header Integrity Info

**Purpose:** Used internally by Microsoft to verify message integrity and duplication.

**Summary:**

- Shows SHA-256 checksums and message size.
- Not useful for user-level forensics but valuable for internal tracking and deduplication.

---

##### 🔐 `DKIM-Signature:` – Cryptographic Integrity

**Purpose:** Ensures message integrity and authenticates source domain via public key.

**Summary:**

- DKIM signature validates `rpsins.247crisis-resilience.com` as signer.
- Signature was valid, but DKIM can be spoofed if the attacker owns the domain.

---

##### 🛰️ `X-Mailgun-Sending-Ip:` & `X-Mailgun-Sid:` – Mailgun Traceability

**Purpose:** Mailgun-specific headers used for tracking and identifying source mail account.

**Summary:**

- Mailgun assigned this to IP `69.72.42.9`.
- `X-Mailgun-Sid` is a session or ID for internal reference.
- Indicates email was likely sent via a Mailgun API or panel.
    

---

##### ✉️ Metadata Headers – Message Envelope & Composition

**Fields:**

- `Sender:`
- `From:`
- `To:`
- `Date:`
- `Subject:`
- `Return-Path:`
- `Message-Id:`
- `Content-Transfer-Encoding:`
- `Content-Type:`

**Summary:**

- Sender appears as CoinDesk but uses a non-Coindesk domain.
- Recipient field (`To:`) shows an incomplete or placeholder address.
- Content-Type is HTML with `quoted-printable` encoding – standard for styled email.
- Return-Path uses bounce address spoofing (common in Mailgun usage).

---

##### 🧷 `X-MS-Exchange-*` & `X-Microsoft-*` – Microsoft and EOP Processing

**Purpose:** Microsoft-specific metadata that describes message routing, spam scoring, filtering decisions, expiration logic, and cross-tenant flow.

**Summary:**

- Message marked as **highly spammy** with SCL score `9`.
- Filtering engine ran authentication (auth=1), allowed delivery, but flagged as junk.
- Message was received anonymously and flowed through Exchange Online Protection.
- `X-SID-Result: PASS` shows envelope sender alignment.
- Latency (`EndToEndLatency`) within 3.5 seconds – rapid internal handling.

---

##### ⚠️ `X-Microsoft-Antispam-Message-Info:` – Obfuscated Threat Evaluation

**Purpose:** Encoded string representing Microsoft's anti-spam rule hits and ML classifier results.

**Summary:**

- Obfuscated base64 data processed by Exchange spam classifiers.
- Correlates with the SCL and BCL spam scores.
- Not human-readable, but confirms Microsoft's internal engine flagged threat indicators.
    

---

##### 🕸️ Email Body Content (HTML) – Visual Phishing & Payloads

**Purpose:** This is the rendered content users actually see. Vital for assessing social engineering.

**Summary of Body Content:**

- Mimics CoinDesk branding and tone.
- Uses **urgency and rewards** to trick victims into clicking malicious links.
- Links point to `mail119-ripple.net` instead of real CoinDesk domain.
- Tracking pixels (`.gif`) embedded to monitor opens.
- Unsubscribe and CTA links are **malicious** redirectors.
- Multiple spoofing strategies are employed:
    - Fake domain with DKIM/SPF alignment.
    - Realistic design elements (logos, tables, button styling).

---

##### 🧵 Conclusion

Despite passing all standard email authentication checks (SPF, DKIM, DMARC), this email is **fraudulent**. It exploits email marketing infrastructure (Mailgun) to launch a **phishing campaign** impersonating CoinDesk and promoting fake XRP rewards. Forensic examination of both headers and HTML body highlights a **well-crafted social engineering attack** using a fully controlled malicious domain.

**Recommendations:**

- Block the sending IP (`69.72.42.9`) and domain (`rpsins.247crisis-resilience.com`).
- Monitor for indicators related to `mail119-ripple.net`.
- Educate users to verify links and sender identity, even if emails pass technical checks.
- Use advanced tools (sandbox detonation, DNS history, passive WHOIS) for deeper IOCs.

---

### (Step 5) **Summary, Continuous Improvement, and Automation Tips**:

#### 🧠 Summary of Findings, Next Steps, and Automation Opportunities

##### ✅ Summary of What We Learned

This lab provided an in-depth, line-by-line forensic analysis of a suspicious email's full header and HTML body. Key takeaways include:

- **Mail Path Reconstruction (`Received:`)**  
    We mapped the email’s exact journey across multiple mail servers, identifying infrastructure patterns and timeline inconsistencies, which are crucial in detecting spoofing and relay abuse.
- **Authentication Insights (`Authentication-Results:`, `SPF`, `DKIM`, `DMARC`)**  
    Although SPF, DKIM, and DMARC all passed, we learned that **passing these checks does not guarantee legitimacy**. These protocols only verify technical alignment, not trustworthiness.
- **Mailgun Involvement (`X-Mailgun-*`)**  
    The sender used Mailgun’s infrastructure, which is often used legitimately but also abused in phishing campaigns. Indicators like anonymous source (`AuthAs: Anonymous`) and unexpected branding reinforced suspicion.
- **Metadata Indicators (`X-* headers`)**  
    Microsoft-specific headers revealed anti-spam scoring (`SCL: 9`), low bulk complaint likelihood (`BCL: 0`), and message latency. These values provided key context into how the email was processed and flagged internally.
- **HTML Payload Analysis**  
    The email's HTML structure, styling, image loading behavior, and clickable elements were dissected. The inclusion of **unsubscribe links**, **social icons**, and **branding impersonation** (CoinDesk, Ripple) were key phishing lures.

##### 🔄 Steps for Continuous Improvement

To evolve your investigative capabilities over time:

|Area|Action|
|---|---|
|📚 Knowledge|Study **RFC 5321**, **RFC 5322**, **DMARC**, **SPF**, and **DKIM** documentation for deeper technical understanding.|
|🧩 Threat Detection|Build a local or virtual testbed to send known-good and spoofed messages to train your analysis instincts.|
|🔬 Tool Familiarity|Learn to use **header analysis tools** like `Microsoft Message Header Analyzer`, **PhishTool**, and **MSTICPy**.|
|⚠️ Awareness|Keep updated with threat intel feeds (ex: `phish.report`, `Abuse.ch`, `ThreatFox`) to identify patterns seen in the wild.|

##### 🤖 Automation Opportunities

Much of this process can be semi- or fully automated to improve speed and consistency:

|Task|Automation Opportunity|Tool/Approach|
|---|---|---|
|Extract & parse headers|Auto-parse into JSON or YAML|Python (`email.parser`, `re`), `exiftool`|
|Visualize mail flow|Auto-generate email hop maps|Graphviz, Mermaid.js, Python + Matplotlib|
|Validate SPF/DKIM/DMARC|Script DNS lookups and evaluation|`pyspf`, `dmarcian`, custom Bash scripts|
|Detect phishing keywords|NLP & regex scanning in content/body|Python (NLTK, spaCy), YARA rules|
|Alert & log suspicious patterns|Auto-log and notify when red flags occur|ELK Stack + Beats, SIEM integrations|

---
