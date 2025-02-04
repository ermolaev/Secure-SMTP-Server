
 1. Step 1:   update the system -
		a. sudo apt update && sudo apt upgrade -y
		
	2. Step 2:   Set up a Hostname and DNS Records -
		a. sudo hostnamectl set-hostname mail.yourdomain.com
		b. Update the following file - 
			i. sudo nano /etc/hosts
			ii. ADD --> 127.0.1.1 mail.yourdomain.com mail
		c. Configure DNS Records:
			i. A Record: Point mail.yourdomain.com to your VPS IP.
			ii. MX Record: Point @ to mail.yourdomain.com.
			iii. PTR Record (Reverse DNS): Set this up with your VPS provider to point your IP to mail.yourdomain.com
			
	3. Step 3:   Install Postfix (SMTP Server) -
		a. Install Postfix:
			i. sudo apt install postfix -y
			ii. During installation:
				1) Choose Internet site
				2) Set the mail name to your.domain.com
		b. Configure Postfix:
			i. Edit the main configuration file :
				1) sudo nano /etc/postfix/main.cf
			ii. Update the following :
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
			iii. Restart Postfix :
				1) sudo systemctl restart postfix
				
	4. Step 4:   Install Dovecot (IMAP/POP3 Server) - 
		a. Install Dovecot : 
			i. sudo apt install dovecot-core dovecot-imapd dovecot-pop3d -y
		b. Configuration Dovecot :
			i. Edit the main configuration file :
				1) sudo nano /etc/dovecot/dovecot.conf
			ii. Update the following :
				protocols = imap pop3
				mail_location = maildir:~/Maildir
				ssl = required
				ssl_cert = </etc/ssl/certs/ssl-cert-snakeoil.pem
				ssl_key = </etc/ssl/private/ssl-cert-snakeoil.key
		c. Restart Dovecot :
			i. sudo systemctl restart dovecot
			
	5. Step 5:   Setup SPF, DKIM and DMARC -
		a. SPF (Sender Policy Framework) :
			i. Add a TXT record to you DNS:
				1) v=spf1 ip4:(your server IP) -all
				2) For eg :   v=spf1 ip4:194.164.149.57 -all
		b. DKIM (DomainKeys Identified Mail) :
			i. Install OpenDKIM:
				1) sudo apt install opendkim opendkim-tools -y
			ii. Configure OpenDKIM:
				1) sudo nano /etc/opendkim.conf
			iii. Update the following:
				Domain                 yourdomain.com
				KeyFile                  /etc/opendkim/keys/yourdomain.com.private
				Selector                default
			iv. Generate DKIM keys
				1) But before that create a folder for them -
					a) sudo mkdir -p /etc/opendkim/keys/yourdomain.com
				2) Then change Ownership -
					a) sudo chown -R opendkim:opendkim /etc/opendkim
				3) Now generate the DKIM keys -
					a) sudo opendkim-genkey -D /etc/opendkim/keys/yourdomain.com -d yourdomain.com -s default 
				4) Now change ownership of the key file -
					a) sudo chown opendkim:opendkim /etc/opendkim/keys/yourdomain.com/default.private
				5) In all the above replace yourdomain.com with your actual domain_name.
			v. Add the DKIM TXT record to your DNS:
				1) cat /etc/opendkim/keys/yourdomain.com/default.txt
		c. DMARC (Domain-based Message Authentication, Reporting and Conformance) :
			i. Add a TXT record to your DNS:
				1) v=DMARC1; p=none; rua=mailto:admin@yourdomain.com
				
	6. Step 6:   Secure Your Email Server -
		a. Enable Firewall :
			i. sudo ufw allow ssh
			ii. sudo ufw allow 25/tcp       # SMTP
			iii. sudo ufw allow 587/tcp     # Submission
			iv. sudo ufw allow 465/tcp     # SMTPS
			v. sudo ufw allow 993/tcp     # IMAPS
			vi. sudo ufw allow 995/tcp     # POP3S
			vii. sudo ufw enable
		b. Install Let's encrypt SSL :
			i. sudo apt install certbot -y
			ii. sudo certbot certonly --standalone -d mail.yourdomain.com
		c. Update Postfix to use the let's Encrypt certificates :
			i. sudo nano /etc/postfix/main.cf
			ii. Updates to make -
				smtpd_tls_cert_file = /etc/letsencrypt/live/mail.yourdomain.com/fullchain.pem
				smtpd_tls_key_file = /etc/letsencrypt/live/mail.yourdomain.com/privkey.pem
			iii. Restart Postfix and Dovecot :
				1) sudo systemctl restart postfix dovecot
				
	7. Step 7:   Test you Email Server -
		a. Send a Test mail :
			i. echo "Subject:SMTP SERVER \n\n SMTP server is setup successfully" | sendmail yourmail@email.com
		b. Check logs :
			i. sudo tail -f /var/log/mail.log
		c. Verify SPF, DKIM and DMARC :
			i. Use tools like MXToolbox or Mail Tester
			
	8. Step 8:   Additional Security measures -
		a. Fail2Ban -
			i. Install and configure Fail2Ban to block brute-force attacks :
				1) sudo apt install fail2ban -y
				2) sudo systemctl enable fail2ban
		b. Regular Updates -
			i. Keep your server updated :
				1) sudo apt update && sudo apt upgrade -y
