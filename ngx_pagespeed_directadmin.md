Setup *ngx_pagespeed* on *Directadmin* 

Preference: [LINK](https://1hosting.com.vn/cai-dat-ngx_pagespeed-cho-nginx-tren-direct-admin "link")

# 1. Download pagespeed to build source
Lastest version: https://www.modpagespeed.com/doc/release_notes

``` bash
NPS_VERSION=1.13.35.2-stable #lastest version
wget https://github.com/pagespeed/ngx_pagespeed/archive/release-${NPS_VERSION}.zip
unzip release-${NPS_VERSION}.zip
cd ngx_pagespeed-release-${NPS_VERSION}/
wget https://dl.google.com/dl/page-speed/psol/${NPS_VERSION}.tar.gz
tar -xzvf ${NPS_VERSION}.tar.gz
```
# 2. Create nginx config
```bash
cd /usr/local/directadmin/custombuild
mkdir -p custom/nginx
cp -fp configure/nginx/configure.nginx custom/nginx/configure.nginx
```
# 3. Add module ngx_pagespeed to config
`vi /usr/local/directadmin/custombuild/custom/nginx/configure.nginx`

```yaml
#!/bin/sh
./configure \
"--add-module=/root/ngx_pagespeed-release-1.13.35.2-stable" \
"--user=nginx" \
"--group=nginx" \
"--prefix=/usr" \
"--sbin-path=/usr/sbin" \
"--conf-path=/etc/nginx/nginx.conf" \
"--pid-path=/var/run/nginx.pid" \
"--http-log-path=/var/log/nginx/access_log" \
"--error-log-path=/var/log/nginx/error_log" \
"--with-ipv6" \
"--without-mail_imap_module" \
"--without-mail_smtp_module" \
"--with-http_ssl_module" \
"--with-http_realip_module" \
"--with-http_stub_status_module" \
"--with-http_gzip_static_module" \
```

# 4. Build nginx config
```bash
./build used_configs
./build nginx
```