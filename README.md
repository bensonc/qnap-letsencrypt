# Let's Encrypt on QNAP
## Install Instructions
### Setup NAS
1. Login to your NAS and make sure the following Apps are installed: 
	  * Git
	  * Python3 (Encountered issues with 2.7.3 w/HTTPS support)
2. Create a folder to store the scripts in /share (Same level as the default Multimedia,Public folders)
3. Copy `init.sh` and `renew_certificate.sh` to this folder. For example, I will use `/share/scripts`
	
### Connect to Terminal
1. run `init.sh`

2. create a Certificate Signing Request(csr):

	**single domain cert:** (replace nas.xxx.de with your domain name)
	```
	openssl req -new -sha256 -key keys/domain.key -subj "/CN=nas.xxx.de" > domain.csr
	```

	**multiple domain cert:** (replace nas.xxx.de and xxx.myqnapcloud.com with your domain name)
	```
	cp /etc/ssl/openssl.cnf openssl-csr-config.cnf
	printf "[SAN]\nsubjectAltName=DNS:nas.xxx.de,DNS:xxx.myqnapcloud.com" >> openssl-csr-config.cnf
	openssl req -new -sha256 -key keys/domain.key -subj "/" -reqexts SAN -config openssl-csr-config.cnf > domain.csr
	``` 
4. `mv /etc/stunnel/stunnel.pem /etc/stunnel/stunnel.pem.orig` (backup)

5. run `renew_certificate.sh`

6. account.key, domain.key and even the csr (according to acme-tiny readme) can be reused, so just create a cronjob to run `renew_certificate.sh` every night (certificate will only be renewed if <30 days left)
    example: (make sure to use crontab -e!)
    ```
    30 3 * * * cd /share/scripts && /share/scripts/renew_certificate.sh &> /share/scripts/renew_certificate.log
    ```
    this should hopefully persist a reboot...
    just to be sure, restart crond: `/etc/init.d/crond.sh restart` 
