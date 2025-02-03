# Secure-SMTP-Server
Self-hosted SMTP server setup with SPF, DKIM, DMARC &amp; security configurations.

# 📧 Secure SMTP Server Setup  
*A self-hosted email server with SPF, DKIM, DMARC, and security hardening*  

🚀 **Live Testing:** [MXToolbox Results](https://mxtoolbox.com/SuperTool.aspx) 

## 🛠️ Features & Security Implementations  
✅ **Postfix & Dovecot Setup** – Reliable email sending & receiving  
✅ **SPF, DKIM, DMARC** – Protects against email spoofing & phishing  
✅ **TLS Encryption** – Secures email transmission  
✅ **Fail2Ban & Rate-Limiting** – Prevents brute-force attacks  
✅ **DNS Records Configuration** – Ensures proper email authentication  


## Table of Contents
1. [Installation](#installation)
2. [Configuration](#configuration)
3. [DNS Records](#dns-records)
4. [Screenshots](#screenshots)

---

### **Installation**  
To set up the SMTP server on your VPS, follow these steps:

1. Clone this repository:
   ```bash
   git clone https://github.com/your-username/your-repo-name.git
   ```
2. Install the required packages:
   ```
   sudo apt-get update
   sudo apt-get install postfix dovecot-core opendkim opendkim-tools
   ```
3. Copy the configuration files from this repository to their respective locations:
   ```
   sudo cp postfix-main.cf /etc/postfix/main.cf
   sudo cp dovecot.conf /etc/dovecot/dovecot.conf
   sudo cp opendkim.conf /etc/opendkim.conf
   ```
### **Configuration**
Postfix: Edit postfix-main.cf to configure your domain and mail settings.  <br>
Dovecot: Edit dovecot.conf to set up IMAP/POP3 services.  <br>
OpenDKIM: Edit opendkim.conf to configure DKIM signing.

### **DNS Records**
Refer to the [SPF_DKIM_DMARC_Records.txt](./📜%20SPF_DKIM_DMARC_Records.txt) file for the DNS records required for email authentication.

### **Screenshots**
Check out the Screenshots(#Screenshots) folder for visuals of the setup process and final results.
