# 📧 Secure SMTP Server Setup  
*A self-hosted email server with SPF, DKIM, DMARC, and security hardening*  

🚀 **Live Testing:** [MXToolbox Results](https://mxtoolbox.com/SuperTool.aspx) 

## 🛠️ Features & Security Implementations  
✅ **Postfix & Dovecot Setup** – Reliable email sending & receiving  
✅ **SPF, DKIM, DMARC** – Protects against email spoofing & phishing  
✅ **TLS Encryption** – Secures email transmission  
✅ **Fail2Ban & Rate-Limiting** – Prevents brute-force attacks  
✅ **DNS Records Configuration** – Ensures proper email authentication  

## **📑Table of Contents**
1. [⚙️ Installation](#installation)
2. [Setup SPF, DKIM & DMARC](#setup-spf-dkim--dmarc)
3. [Securing Your Email Server](#securing-your-email-server)
4. [Testing Your Email Server](#testing-your-email-server)
5. [Additional Security Measures](#additional-security-measures)



---

### **⚙️ Installation**  
To set up the SMTP server on your VPS, follow these steps:

1. **Step 1:** Update the system -
    ```bash
    sudo apt update && sudo apt upgrade -y
    ```

2. **Step 2:** Set up a Hostname and DNS Records -
    - **Set Hostname:**
        ```bash
        sudo hostnamectl set-hostname mail.yourdomain.com
        ```
    - **Edit `/etc/hosts`:** 
        ```bash
        sudo nano /etc/hosts
        ```
        Add:
        ```text
        127.0.1.1 mail.yourdomain.com mail
        ```
    - **Configure DNS Records:**
        - **A Record:** Point mail.yourdomain.com to your VPS IP.
        - **MX Record:** Point `@` to mail.yourdomain.com.
        - **PTR Record:** Set this up with your VPS provider to point your IP to mail.yourdomain.com.

3. **Step 3:** Install Postfix (SMTP Server) -
    - Install Postfix:
        ```bash
        sudo apt install postfix -y
        ```
        During installation:
        1. Choose *Internet site*
        2. Set the mail name to `your.domain.com`
    - Configure Postfix:
        Edit `/etc/postfix/main.cf`:
        ```bash
        sudo nano /etc/postfix/main.cf
        ```
        Update the following:
        ```text
        myhostname = mail.yourdomain.com
        mydomain = yourdomain.com
        myorigin = $mydomain
        inet_interfaces = all
        mydestination = $myhostname, $mydomain, localhost.$mydomain, localhost
        mynetworks = 127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128
        home_mailbox = Maildir/
        smtpd_tls_cert_file = /etc/ssl/certs/ssl-cert-snakeoil.pem
        smtpd_tls_key_file = /etc/ssl/private/ssl-cert-snakeoil.key
        smtpd_use_tls = yes
        smtpd_tls_session_cache_database = btree:${data_directory}/smtpd_scache
        smtp_tls_session_cache_database = btree:${data_directory}/smtp_scache
        ```
    - Restart Postfix:
        ```bash
        sudo systemctl restart postfix
        ```

4. **Step 4:** Install Dovecot (IMAP/POP3 Server) -
    - Install Dovecot:
        ```bash
        sudo apt install dovecot-core dovecot-imapd dovecot-pop3d -y
        ```
    - Configure Dovecot:
        Edit `/etc/dovecot/dovecot.conf`:
        ```bash
        sudo nano /etc/dovecot/dovecot.conf
        ```
        Update the following:
        ```text
        protocols = imap pop3
        mail_location = maildir:~/Maildir
        ssl = required
        ssl_cert = </etc/ssl/certs/ssl-cert-snakeoil.pem
        ssl_key = </etc/ssl/private/ssl-cert-snakeoil.key
        ```
    - Restart Dovecot:
        ```bash
        sudo systemctl restart dovecot
        ```

---

### **🛡️ Setup SPF, DKIM & DMARC**

1. **SPF (Sender Policy Framework):**
    - Add a TXT record to your DNS:
        ```text
        v=spf1 ip4:(your server IP) -all
        ```
    - Example:
        ```text
        v=spf1 ip4:194.164.149.57 -all
        ```

2. **DKIM (DomainKeys Identified Mail):**
    - Install OpenDKIM:
        ```bash
        sudo apt install opendkim opendkim-tools -y
        ```
    - Configure OpenDKIM:
        ```bash
        sudo nano /etc/opendkim.conf
        ```
        Update the following:
        ```text
        Domain                 yourdomain.com
        KeyFile                 /etc/opendkim/keys/yourdomain.com.private
        Selector                default
        ```
    - Generate DKIM keys:
        - Create a folder for them:
            ```bash
            sudo mkdir -p /etc/opendkim/keys/yourdomain.com
            ```
        - Change Ownership:
            ```bash
            sudo chown -R opendkim:opendkim /etc/opendkim
            ```
        - Generate the DKIM keys:
            ```bash
            sudo opendkim-genkey -D /etc/opendkim/keys/yourdomain.com -d yourdomain.com -s default
            ```
        - Change ownership of the key file:
            ```bash
            sudo chown opendkim:opendkim /etc/opendkim/keys/yourdomain.com/default.private
            ```
    - Add the DKIM TXT record to your DNS:
        ```bash
        cat /etc/opendkim/keys/yourdomain.com/default.txt
        ```

3. **DMARC (Domain-based Message Authentication, Reporting and Conformance):**
    - Add a TXT record to your DNS:
        ```text
        v=DMARC1; p=none; rua=mailto:admin@yourdomain.com
        ```

---

### **🔒 Securing Your Email Server**

1. **Enable Firewall:**
    ```bash
    sudo ufw allow ssh
    sudo ufw allow 25/tcp      # SMTP
    sudo ufw allow 587/tcp     # Submission
    sudo ufw allow 465/tcp     # SMTPS
    sudo ufw allow 993/tcp     # IMAPS
    sudo ufw allow 995/tcp     # POP3S
    sudo ufw enable
    ```

2. **Install Let's Encrypt SSL:**
    ```bash
    sudo apt install certbot -y
    ```

3. **Obtain SSL Certificate:**
    ```bash
    sudo certbot certonly --standalone -d mail.yourdomain.com
    ```

4. **Update Postfix to use the Let's Encrypt certificates:**
    Edit `/etc/postfix/main.cf`:
    ```bash
    sudo nano /etc/postfix/main.cf
    ```
    Changes to be made:
    ```text
    smtpd_tls_cert_file = /etc/letsencrypt/live/mail.yourdomain.com/fullchain.pem
    smtpd_tls_key_file = /etc/letsencrypt/live/mail.yourdomain.com/privkey.pem
    ```
    Restart Postfix and Dovecot:
    ```bash
    sudo systemctl restart postfix dovecot
    ```

---

### **🚀 Testing Your Email Server**

1. **Send a Test mail:**
    ```bash
    echo "Subject:SMTP SERVER \n\n SMTP server is setup successfully" | sendmail yourmail@email.com
    ```

2. **Check logs:**
    ```bash
    sudo tail -f /var/log/mail.log
    ```

3. **Verify SPF, DKIM and DMARC:**
    - Use tools like [MXToolbox](https://mxtoolbox.com/SuperTool.aspx) or [Mail Tester](https://www.mail-tester.com/)

---

### **⚠️ Additional Security Measures**

1. **Fail2Ban:**
    - Install and configure Fail2Ban to block brute-force attacks:
        ```bash
        sudo apt install fail2ban -y
        sudo systemctl enable fail2ban
        ```

2. **Regular Updates:**
    - Keep your server updated:
        ```bash
        sudo apt update && sudo apt upgrade -y
        ```

---

Enjoy your self-hosted SMTP server! 📨
