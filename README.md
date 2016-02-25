1. run `init.sh`

2. create a csr:

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
    30 3 * * * cd /share/homes/admin/qnap-letsencrypt && /share/homes/admin/qnap-letsencrypt/renew_certificate.sh &> /share/homes/admin/qnap-letsencrypt/renew_certificate.log
    ```
    this should hopefully persist a reboot...
    just to be sure, restart crond: `/etc/init.d/crond.sh restart` 