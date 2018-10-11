Setup *ngx_pagespeed* on *Directadmin* 

Preference: [LINK](https://1hosting.com.vn/cai-dat-ngx_pagespeed-cho-nginx-tren-direct-admin "link")

# 1. Download pagespeed to build source
Lastest version: https://www.modpagespeed.com/doc/release_notes

``` bash
NPS_VERSION=1.13.35.2-stable #lastest version
wget https://github.com/apache/incubator-pagespeed-ngx/archive/v${NPS_VERSION}.zip
unzip v${NPS_VERSION}.zip
nps_dir=$(find . -name "*pagespeed-ngx-${NPS_VERSION}" -type d)
cd "$nps_dir"
# or cd incubator-pagespeed-ngx-${NPS_VERSION}/
# NPS_RELEASE_NUMBER=${NPS_VERSION/beta/}
NPS_RELEASE_NUMBER=${NPS_VERSION/stable/}
psol_url=https://dl.google.com/dl/page-speed/psol/${NPS_RELEASE_NUMBER}.tar.gz
[ -e scripts/format_binary_url.sh ] && psol_url=$(scripts/format_binary_url.sh PSOL_BINARY_URL)
wget ${psol_url}
tar -xzvf $(basename ${psol_url})  # extracts to psol/
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
"--add-module=/root/incubator-pagespeed-ngx-1.13.35.2-stable" \
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
cd /usr/local/directadmin/custombuild
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

NOTE: 
We can copy module `ngx_pagespeed.so` to `/usr/share/nginx/modules` add this line to the top of our main nginx configuation (Eg: /etc/nginx/nginx.conf):
```
load_module "modules/ngx_pagespeed.so";
```