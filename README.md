# 📧 Secure SMTP Server Setup  
*A self-hosted email server with SPF, DKIM, DMARC, and security hardening*  

🚀 **Live Testing:** [MXToolbox Results](https://mxtoolbox.com/SuperTool.aspx) 

## 🛠️ Features & Security Implementations  
✅ **Postfix & Dovecot Setup** – Reliable email sending & receiving  
✅ **SPF, DKIM, DMARC** – Protects against email spoofing & phishing  
✅ **TLS Encryption** – Secures email transmission  
✅ **Fail2Ban & Rate-Limiting** – Prevents brute-force attacks  
✅ **DNS Records Configuration** – Ensures proper email authentication  


##📑Table of Contents
1. [Installation](#instalation)
2. Setup SPF, DKIM & DMARC
3. Securing Your Email Server
4. Testing Your Email Server
5. Additional Security Measures
---

### **⚙️ Installation**  
To set up the SMTP server on your VPS, follow these steps:
1. Step 1:   update the system -
	```sudo apt update && sudo apt upgrade -y```
		
2. Step 2:   Set up a Hostname and DNS Records -
   	a. Set Hostname:
   	```sudo hostnamectl set-hostname mail.yourdomain.com```
	b. Edit /etc/hosts: - 
   	```sudo nano /etc/hosts```
   	ADD
   	```127.0.1.1 mail.yourdomain.com mail```
	c. Configure DNS Records:
		i. A Record: Point mail.yourdomain.com to your VPS IP.
		ii. MX Record: Point @ to mail.yourdomain.com.
		iii. PTR Record: Set this up with your VPS provider to point your IP to mail.yourdomain.com.

4. Step 3:   Install Postfix (SMTP Server) -
	a. Install Postfix:
   	```sudo apt install postfix -y```
   		i. During installation:
			1) Choose Internet site
			2) Set the mail name to your.domain.com
	b. Configure Postfix:
		i. Edit /etc/postfix/main.cf::
		```sudo nano /etc/postfix/main.cf```
		ii. Update the following :
		     ```myhostname = mail.yourdomain.com
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
			smtp_tls_session_cache_database = btree:${data_directory}/smtp_scache```
		iii. Restart Postfix :
		```sudo systemctl restart postfix```
				
6. Step 4:   Install Dovecot (IMAP/POP3 Server) - 
		a. Install Dovecot : 
		```sudo apt install dovecot-core dovecot-imapd dovecot-pop3d -y```
		b. Configuration Dovecot :
			i. Edit the main configuration file :
			```sudo nano /etc/dovecot/dovecot.conf```
			ii. Update the following :
				```protocols = imap pop3
				mail_location = maildir:~/Maildir
				ssl = required
				ssl_cert = </etc/ssl/certs/ssl-cert-snakeoil.pem
				ssl_key = </etc/ssl/private/ssl-cert-snakeoil.key```
		c. Restart Dovecot :
		```sudo systemctl restart dovecot```
			
7. Step 5:   🛡️ Setup SPF, DKIM & DMARC -
		a. SPF (Sender Policy Framework) :
			i. Add a TXT record to you DNS:
			```v=spf1 ip4:(your server IP) -all```
			ii. For eg :   v=spf1 ip4:194.164.149.57 -all
		b. DKIM (DomainKeys Identified Mail) :
			i. Install OpenDKIM:
			```sudo apt install opendkim opendkim-tools -y```
			ii. Configure OpenDKIM:
			```sudo nano /etc/opendkim.conf```
			iii. Update the following:
				```Domain                 yourdomain.com
				KeyFile                  /etc/opendkim/keys/yourdomain.com.private
				Selector                default```
			iv. Generate DKIM keys
				1) But before that create a folder for them -
				```sudo mkdir -p /etc/opendkim/keys/yourdomain.com```
				2) Then change Ownership -
				```sudo chown -R opendkim:opendkim /etc/opendkim```
				3) Now generate the DKIM keys -
				```sudo opendkim-genkey -D /etc/opendkim/keys/yourdomain.com -d yourdomain.com -s default ```
				4) Now change ownership of the key file -
				```sudo chown opendkim:opendkim /etc/opendkim/keys/yourdomain.com/default.private```
				5) In all the above replace yourdomain.com with your actual domain_name.
			v. Add the DKIM TXT record to your DNS:
			```cat /etc/opendkim/keys/yourdomain.com/default.txt```
		c. DMARC (Domain-based Message Authentication, Reporting and Conformance) :
			i. Add a TXT record to your DNS:
			```v=DMARC1; p=none; rua=mailto:admin@yourdomain.com```
				
8. Step 6:   🔒 Securing Your Email Server -
		a. Enable Firewall :
			```sudo ufw allow ssh
			sudo ufw allow 25/tcp      # SMTP
			sudo ufw allow 587/tcp     # Submission
			sudo ufw allow 465/tcp     # SMTPS
			sudo ufw allow 993/tcp     # IMAPS
			sudo ufw allow 995/tcp     # POP3S
			sudo ufw enable```
		b. Install Let's encrypt SSL :
		```sudo apt install certbot -y```
		c. Obtain SSL Certificate :
		```sudo certbot certonly --standalone -d mail.yourdomain.com```
		c. Update Postfix to use the let's Encrypt certificates :
		```sudo nano /etc/postfix/main.cf```
			i. Changes to be made -
				```smtpd_tls_cert_file = /etc/letsencrypt/live/mail.yourdomain.com/fullchain.pem
				smtpd_tls_key_file = /etc/letsencrypt/live/mail.yourdomain.com/privkey.pem```
			ii. Restart Postfix and Dovecot :
			```sudo systemctl restart postfix dovecot```
				
10. Step 7:  🚀 Testing Your Email Server -
		a. Send a Test mail :
		```echo "Subject:SMTP SERVER \n\n SMTP server is setup successfully" | sendmail yourmail@email.com```
		b. Check logs :
		```sudo tail -f /var/log/mail.log```
		c. Verify SPF, DKIM and DMARC :
			i. Use tools like [!MXToolbox](https://mxtoolbox.com/SuperTool.aspx) or [!Mail Tester](https://www.mail-tester.com/)
			
11. Step 8:   ⚠️ Additional Security Measures -
		a. Fail2Ban -
			i. Install and configure Fail2Ban to block brute-force attacks :
				```sudo apt install fail2ban -y
				sudo systemctl enable fail2ban```
		b. Regular Updates -
			i. Keep your server updated :
				```sudo apt update && sudo apt upgrade -y```
