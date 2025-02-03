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
2. [DNS Records](#dns-records)
3. [Configuration](#configuration)
4. [Screenshots](#screenshots)

---

### **Installation**  
To set up the SMTP server on your VPS, follow these steps:

1. Clone this repository:
   ```
   git clone https://github.com/0-Anonymous/Secure-SMTP-Server.git
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

### **DNS Records**
Refer to the [SPF_DKIM_DMARC_Records.md](SPF_DKIM_DMARC_Records.md) file for the DNS records required for email authentication.
The above is how a SPF record looks like

   
### **Configuration**
1.   Postfix: Edit postfix-main.cf to configure your domain and mail settings.
   ```
     nano /etc/postfix/main.cf
   ```
  <br>
2.   Now check the details that are in this file matches the following -

      # Postfix Main Configuration File
      
      # Hostname and Domain
      myhostname = mail.example.com
      mydomain = example.com
      
      # Network Settings
      inet_interfaces = all
      inet_protocols = ipv4
      
      # Mailbox Settings
      home_mailbox = Maildir/
      
      # TLS Settings
      smtpd_tls_cert_file=/etc/letsencrypt/live/Domain_name/fullchain.pem
      smtpd_tls_key_file=/etc/letsencrypt/live/Domain_name/privkey.pem
      smtpd_use_tls = yes 
   
3.   Dovecot: Edit dovecot.conf to set up IMAP/POP3 services.
   ```
      # ========================
      # Custom Dovecot Configurations
      # ========================
      
      # Enable IMAP protocol
      protocols = imap pop3 lmtp
      
      # Set the mail location (modify based on your setup)
      mail_location = maildir:/var/mail/vhosts/
      
      # Auth Mechanisms (for secure authentication)
      auth_mechanisms = plain login
      
      # SSL/TLS Configuration (Enable SSL if required)
      ssl = yes
      ssl_cert = </etc/letsencrypt/live/yourdomain.com/fullchain.pem
      ssl_key = </etc/letsencrypt/live/yourdomain.com/privkey.pem

 ```
4.  OpenDKIM: Edit opendkim.conf to configure DKIM signing.
   ```
      # ================================
      # OpenDKIM Configuration
      # ================================
      
      # Define the domain that will be signing emails
      Domain                 yourdomain.com
      Selector              mail
      
      # Path to the private key for signing emails
      KeyFile               /etc/opendkim/keys/yourdomain.com/mail.private
      
      # Enable logging to monitor OpenDKIM activity
      LogWhy                yes
      Syslog                yes
      SyslogSuccess         yes
      
      # Set mode to sign and verify emails
      Mode                  sv
      
      # Socket for communication with mail server (Postfix, Exim, etc.)
      Socket                inet:8891@localhost
      
      # Set up signing and key tables
      SigningTable          refile:/etc/opendkim/signing.table
      KeyTable              refile:/etc/opendkim/key.table
  ```

   Replace your domain with actual domain
   Ensure that the required files exists if not create them - <br>
      - /etc/opendkim/trusted.hosts <br>
      - /etc/opendkim/signing.table <br>
      - /etc/opendkim/key.table <br>

5.   Get an SSL certificate :
   ```
   certbot certonly --standalone -d adi.r-webtech.com
   systemctl restart postfix
   ```
6.   Open firewall ports :
   ```
   ufw allow 25/tcp  # SMTP
	ufw allow 587/tcp # Secure SMTP
	ufw allow 993/tcp # Secure IMAP
	ufw allow 995/tcp # Secure POP3
   ufw reload
   ```
7.   Verify SMTP Server is Working :
   ```
   ss -tuln | grep :25
   ss -tuln | grep :587
   ```
8.   Test Sending Emails -
   ```
   echo "Subject:this is my Test email  \n\n This is the body and this SMTP server  setup is successfully" | sendmail youremail@gmail.com
   ```
9.   Check Postfix Logs -
   ```
   tail -f /var/log/mail.log
   ```
10.   Test with telnet or netcat
   ```
    telnet adi.r-webtech.com 25
    nc -zv adi.r-webtech.com 25
   ```

### **Screenshots**
Check out the [Screenshots](Screenshots) folder for visuals of the setup process and final results.
