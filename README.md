# Secure-SMTP-Server
Self-hosted SMTP server setup with SPF, DKIM, DMARC &amp; security configurations.

# 📧 Secure SMTP Server Setup  
*A self-hosted email server with SPF, DKIM, DMARC, and security hardening*  

🚀 **Live Testing:** [MXToolbox Results](#) 

📸 **Screenshots:**  
![Postfix Configuration](#)  
![Email Headers with DKIM Pass](#)  

---

## 🛠️ Features & Security Implementations  
✅ **Postfix & Dovecot Setup** – Reliable email sending & receiving  
✅ **SPF, DKIM, DMARC** – Protects against email spoofing & phishing  
✅ **TLS Encryption** – Secures email transmission  
✅ **Fail2Ban & Rate-Limiting** – Prevents brute-force attacks  
✅ **DNS Records Configuration** – Ensures proper email authentication  

---

## 📂 Folder Structure  
📂 Secure-SMTP-Server
│── 📄 README.md  # Project Documentation
│── 📜 postfix-main.cf  # Postfix Configuration
│── 📜 dovecot.conf  # Dovecot Configuration
│── 📜 opendkim.conf  # DKIM Configuration
│── 📜 SPF_DKIM_DMARC_Records.txt  # DNS Records
│── 📸 Screenshots/  # Configuration & Testing Screenshots


---

## 🚀 Step-by-Step Installation Guide  

### **Step 1: Install Required Packages**  
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install postfix dovecot-core dovecot-imapd opendkim opendkim-tools -y

