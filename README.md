# OpenShift Nginx 1.6.2 PHP-FPM 5.4 Cartridge
This cartridge serves static content using nginx web server, passing requests to .php files down to php-fpm.

Place your static files inside www/static dir, commit and push.
Place your php files inside php/ dir, commit and push.

## Usage

```bash
$ rhc app create <appname> https://reflector-getupcloud.getup.io/reflect?github=dottobr83/openshift-nginx1.6.2-php-fpm-fastcgi
$ cd <appname>
$ echo '<?php phpinfo(); ?>' > php/info.php
$ echo 'Hello World' >> www/static/hello.html
$ git add .
$ git commit -m 'Testing'
$ git push

To add mysql cartridge
$ rhc cartridge add mysql-5.5 -a <appname>

To add Wordpress
$ cd <appname>
$ git remote add upstream https://github.com/openshift/wordpress-example
$ git pull upstream master
## 1. fix conflicts in action_hooks/deploy
## 2. edit wp-config.php file (change FORCE_SSL_ADMIN to false)
## 3. add in wp-config.php file
## (before this line) /* That's all, stop editing! Happy blogging. */
## /** Woocommerce plugin recommends to increase WP Memory Limit to 128MB */
## define( 'WP_MEMORY_LIMIT', '128M' );
## (at the end)
## /** path to fastcgi cache directory for Nginx Helper plugin */
## define('RT_WP_NGINX_HELPER_CACHE_PATH','/tmp/nginx/nginx-cache');
## 4. then commit
$ git add -A
$ git commit -am 'install wordpress'
$ git push
## Add 'Nginx Helper' plugin to purge fastcgi cache
```

## User-defined configuration

Place your nginx .conf files inside config/nginx.d/. It will be include()ed from "http" scope.
Place your php-fpm .conf files inside config/php-fpm.d/. It will be include()ed from main php-fpm.conf file.

## Added fastcgi_cache with conditional purging for Wordpress
Sources of info include:<br>
https://rtcamp.com/wordpress-nginx/tutorials/single-site/fastcgi-cache-with-purging/<br>
https://vpsboard.com/topic/108-nginx-wordpress-with-caching/<br>
http://centminmod.com/nginx_configure_wordpress.html#fastcgicache

## nginx is configured with
./configure --prefix=${OPENSHIFT_PHP_DIR}/usr/share/nginx --sbin-path=${OPENSHIFT_PHP_DIR}/usr/sbin/nginx --conf-path=${OPENSHIFT_PHP_DIR}/configuration/etc/nginx.conf --pid-path=${OPENSHIFT_PHP_DIR}/run/nginx.pid --error-log-path=${OPENSHIFT_PHP_LOG_DIR}/nginx_error.log --http-log-path=${OPENSHIFT_PHP_LOG_DIR}/nginx_access.log --http-client-body-temp-path=${TMP}/nginx/client_body --http-proxy-temp-path=${TMP}/nginx/proxy --http-fastcgi-temp-path=${TMP}/nginx/fastcgi --http-uwsgi-temp-path=${TMP}/nginx/uwsgi --http-scgi-temp-path=${TMP}/nginx/scgi --user=${USER} --group=${USER} --with-file-aio --with-ipv6 --with-http_ssl_module --with-http_realip_module --with-http_addition_module --with-http_xslt_module --with-http_image_filter_module --with-http_geoip_module --with-http_sub_module --with-http_dav_module --with-http_flv_module --with-http_mp4_module --with-http_gzip_static_module --with-http_random_index_module --with-http_secure_link_module --with-http_degradation_module --with-http_stub_status_module --with-mail --with-mail_ssl_module --with-debug --add-module=../headers-more-nginx-module-0.25 --add-module=../echo-nginx-module-0.53 --add-module=../ngx_cache_purge-2.1 --with-pcre=../pcre-8.35
