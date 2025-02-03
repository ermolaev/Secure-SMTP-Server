## SPF Record
First you will need to go to your DNS management there click on add new record then -   <br>
type field --> TXT ,  name field --> Doamin name or @ ,  value field --> v=spf1 ip6:[your IPv6] -all
![SPF record](https://github.com/0-Anonymous/Secure-SMTP-Server/blob/5e4986ad648992f7d41aa74c9b9c0bc74385bc1a/SPF%20record.png)

## DKIM Record
The same for dkim record click on add new record -   <br>
type --> TXT, name --> default._domainkey.domain_name  or  default._domainkey.@   and  value --> "v=DKIM1; h=sha256; k=rsa; p=[here you public key]"
![DKIM record](https://github.com/0-Anonymous/Secure-SMTP-Server/blob/8605e4b2a530beef906a6ff92e016f92dd8bdaa9/DKIM%20Record.png)

## DMRAC Record
Create one more record - <br>
type -> TXT  name -> _dmarc.domain_name  or  _dmarc.@  and  value -> v=DMARC1; p=quarantine; rua=mailto:dmarc-reports@Domain_name
![DMARC record](https://github.com/0-Anonymous/Secure-SMTP-Server/blob/8605e4b2a530beef906a6ff92e016f92dd8bdaa9/DMARC%20Record.png)
