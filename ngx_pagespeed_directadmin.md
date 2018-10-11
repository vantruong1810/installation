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

# 5. Add pagespeed config into each domains on Directadmin

Go to: `Directadmin>Admin Level>Custom HTTPD Configurations>`


```yaml
pagespeed on;
pagespeed FileCachePath /var/cache/ngx_pagespeed;

# Ensure requests for pagespeed optimized resources go to the pagespeed handler
# and no extraneous headers get set.
location ~ "\.pagespeed\.([a-z]\.)?[a-z]{2}\.[^.]{10}\.[^.]+" {
 add_header "" "";
}
location ~ "^/ngx_pagespeed_static/" { }
location ~ "^/ngx_pagespeed_beacon$" { }
location /ngx_pagespeed_statistics { allow 127.0.0.1; deny all; }
location /ngx_pagespeed_global_statistics { allow 127.0.0.1; deny all; }
location /ngx_pagespeed_message { allow 127.0.0.1; deny all; }

pagespeed MemcachedThreads 1;
pagespeed MemcachedServers "localhost:11211";
pagespeed MemcachedTimeoutUs 100000;

# Defer and minify Javascript
#pagespeed EnableFilters defer_javascript;
pagespeed EnableFilters rewrite_javascript;
pagespeed EnableFilters combine_javascript;
pagespeed EnableFilters canonicalize_javascript_libraries;

# Inline and minimize css
pagespeed EnableFilters rewrite_css;
pagespeed EnableFilters fallback_rewrite_css_urls;
# Loads CSS faster
#pagespeed EnableFilters move_css_above_scripts;
pagespeed EnableFilters move_css_to_head;
# Rewrite, resize and recompress images
#pagespeed EnableFilters rewrite_images;
# pagespeed EnableFilters rewrite_images;
pagespeed DisableFilters rewrite_images;
pagespeed DisableFilters recompress_images;
pagespeed DisableFilters convert_png_to_jpeg;
pagespeed DisableFilters extend_cache_images;
# pagespeed EnableFilters convert_png_to_jpeg;
# pagespeed EnableFilters convert_jpeg_to_webp;
# pagespeed EnableFilters convert_to_webp_lossless;

# remove tags with default attributes
pagespeed EnableFilters elide_attributes;
pagespeed FetchHttps enable,allow_self_signed;
```