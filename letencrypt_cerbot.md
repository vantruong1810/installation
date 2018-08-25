Setup LET’S ENCRYPT CLIENT

Preference: [LINK](https://nodejs.vn/topic/113/t%E1%BB%B1-t%E1%BA%A1o-v%C3%A0-c%C3%A0i-%C4%91%E1%BA%B7t-ssl-mi%E1%BB%85n-ph%C3%AD-cho-nginx-apache-v%E1%BB%9Bi-letsencrypt "link")

# Prerequisites
- NGINX
- GIT
- Python (>= 2.7)
# Installation
## Check Python version
```bash
python -v
```
Or `python2.7 -v` or `python3.5 -v` 
Or check in directory `/usr/local/bin/`

If python version < 2.7 do as below or skip this step:

Eg: Install python 2.7.6
```
yum -y update
yum groupinstall "Development tools"
yum install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel
cd /opt
wget --no-check-certificate https://www.python.org/ftp/python/2.7.6/Python-2.7.6.tar.xz
tar xf Python-2.7.6.tar.xz 
cd Python-2.7.6
./configure --prefix=/usr/local
make && sudo make altinstall
```
## Run Let's Encrypt
### Clone into directory `/opt`
```git
git clone https://github.com/letsencrypt/letsencrypt /opt/letsencrypt
```

### Stop nginx
```bash
sudo service nginx stop 
```
OR `sudo systemctl stop nginx.service`
### Run
```bash
cd /opt/letsencrypt
./letsencrypt-auto certonly --standalone
```

*Input domains which use ssl certificates and Agree all*

### Note: 
SSL files:

`cert.pem`: Domain certificate SSL.

`chain.pem`: Let’s Encrypt certificate.

`fullchain.pem`: Merge cert.pem and chain.pem.

`privkey.pem`: Private key.

SSL path: `/etc/letencrypt/live/domain.com/`

## Renew Let's Encrypt
### Manually
```
cd opt/letsencrypt
./letsencrypt-auto certonly -a webroot --agree-tos --renew-by-default --webroot-path=/usr/share/nginx/html -d example.com -d www.example.com
sudo service nginx reload
```
### Automatically
```
sudo cp /opt/letsencrypt/examples/cli.ini /usr/local/etc/le-renew-webroot.ini
sudo nano /usr/local/etc/le-renew-webroot.ini
```
Comment this row:
`#server = https://acme-staging.api.letsencrypt.org/directory`
UnComment and change these rows:
```
rsa-key-size = 4096
email = contact@nodejs.vn
domains = nodejs.vn, www.nodejs.vn
webroot-path = /usr/share/nginx/html
```
Run this command:
```
cd /opt/letsencrypt
./letsencrypt-auto certonly -a webroot --renew-by-default --config /usr/local/etc/le-renew-webroot.ini
```

[Optional] Maybe run `sudo nginx restart` as well

*SSL will be expired after 3 months*
