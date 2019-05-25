<!--ts-->
   * [wordpress-ultimate-setup](#wordpress-ultimate-setup)
   * [Installation](#installation)
      * [Open LiteSpeed](#open-litespeed)
         * [Quick Commands](#quick-commands)
      * [Percona DB](#percona-db)
      * [XDebug](#xdebug)
   * [Notes](#notes)
      * [Caching](#caching)

<!-- Added by: jtrask, at: Wed  8 May 2019 12:33:15 PDT -->

<!--te-->
# Installation
## Open LiteSpeed
1. Download the repository enable script from Litespeed
### Quick Commands
```
wget -O - http://rpms.litespeedtech.com/debian/enable_lst_debain_repo.sh | bash
apt install openlitespeed lsphp72 lsphp72-curl lsphp72-imap lsphp72-mysql lsphp72-intl lsphp72-pgsql lsphp72-sqlite3 lsphp72-tidy lsphp72-snmp
```
## LiteSpeed Paid
### LSPHP
```
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 011AA62DEDA1F085
wget -O - http://rpms.litespeedtech.com/debian/enable_lst_debain_repo.sh | bash
apt-get install lsphp73 lsphp73 lsphp73-opcache lsphp73-mysql lsphp73-memcached lsphp73-curl
```
## LetsEncrypt SSL
### Quick Install
```
add-apt-repository ppa:certbot/certbot
apt-get install certbot
```
## Vanish
https://www.linode.com/docs/websites/varnish/use-varnish-and-nginx-to-serve-wordpress-over-ssl-and-http-on-debian-8/
## Percona DB

## Redis

1. Resources
    - https://github.com/pressjitsu/pj-object-cache-red
    - https://pressjitsu.com/blog/redis-object-cache-wordpress/
2. Monitoring
    - redis-cli monitor
    - https://www.serverdensity.com/pricing/
    - https://github.com/junegunn/redis-stat

## XDebug
This works for not only OpenLitespeed but also LiteSpeed, and is for Ubuntu 18

1. Install appropriate packages
```apt-get install lsphp7.0-dev```
2. Download xdebug source and extract
```wget https://xdebug.org/files/xdebug-2.7.2.tgz
tar -zxvf xdebug-2.7.2.tgz
``
3. Build xdebug module
```cd xdebug-2.7.2
/usr/local/lsws/lsphp72/bin/phpize
./configure --with-php-config=/usr/local/lsws/lsphp72/bin/php-config
make
```
4. Copy xdebug module to lsphp72 modules directory
```cp modules/xdebug.so /usr/local/lsws/lsphp72/lib/php/20170718```
5. Create mods-available/xdebug.ini file
```joe /usr/local/lsws/lsphp72/etc/php/7.2/mods-available/xdebug.ini```
Add the following lines to enable error tracing

```
[xdebug]
zend_extension = xdebug.so
xdebug.max_nesting_level=1000
xdebug.show_mem_delta=1
xdebug.collect_params=4
xdebug.dump_globals=on
xdebug.collect_vars=on
xdebug.show_local_vars=on
xdebug.show_error_trace=on
```
## New Relic for LiteSpeed/OpenLitespeed
###
You'll need to install the Ubuntu newrelic repo and the newrelic-php5 package. Don't worry, all of the compiled modules are available for every PHP version.
```
echo 'deb http://apt.newrelic.com/debian/ newrelic non-free' | sudo tee /etc/apt/sources.list.d/newrelic.list
wget -O- https://download.newrelic.com/548C16BF.gpg | sudo apt-key add -
apt-get update
apt-get install newrelic-php5
```
The newrelic-php5 package will detect your litespeed configuration and typically drop a newrelic.ini file within your Litespeed $SERVER_ROOT/mods-available folder.

New Relic won't know which newrelic-*.so file to use, so you'll have to define "extension=" within the newrelic.ini file. To find out what PHP API version you have, run phpinfo(); and look for PHP API. In this case we have PHP 7.3 of which PHP API is 20180731 so we'll use the following
```
extension = "/usr/lib/newrelic-php5/agent/x64/newrelic-20180731.so"
```
### Multiple Sites with New Relic
However, this will mean that all of your sites will use the same appname, which isn't great. So you should configure independant PHP External Apps for each site. And then set the following variable under "Environment":
```
PHP_INI_SCAN_DIR=$VH_ROOT/mods-available/
```
Then copy all of your $SERVER_ROOT/mods-available files to $VH_ROOT/mods-available and then update $VH_ROOT/mods-available/newrelic.ini app name. This is a means of having per site/user PHP configuration.

##Content Delivery Network (CDN)
- https://www.keycdn.com/pricing
- https://stackpath.com/
    - Ensure "CORS Header Support" is not checked off or you'll have multiple access-control-allow-origin
## Wordpress
### Transients
- https://pressjitsu.com/blog/optimizing-wp-options-for-speed/
- https://github.com/pressjitsu/wp-transients-cleaner/blob/master/transient-cleaner.php

### Disable Cron
- https://pressjitsu.com/blog/wordpress-cron-cli/

# Notes
## Load Testing
https://locust.io/

## Google PageSpeed Module
https://www.wpoven.com/tutorial/how-to-setup-modpagespeed-with-nginx-varnish-and-php-fpm/
https://www.litespeedtech.com/support/wiki/doku.php/litespeed_wiki:config:enable_pagespeed#set_server_level_pagespeed_file_path
```
# currently standby, for testing.
pagespeed standby;
pagespeed ModifyCachingHeaders on;
pagespeed FileCachePath /tmp/lshttpd/pagespeed;
pagespeed EnableFilters rewrite_css,combine_css,trim_urls;
pagespeed DisableFilters sprite_images,convert_jpeg_to_webp,convert_to_webp_lossless;
```
## PHP
### OPCode Caching

## Caching
### Litespeed
1. Litespeed
-https://www.litespeedtech.com/support/wiki/doku.php/litespeed_wiki:cache:drop_query_string
-https://www.litespeedtech.com/support/wiki/doku.php/litespeed_wiki:cache:advanced?redirect=1
-https://www.litespeedtech.com/support/wiki/doku.php/litespeed_wiki:cache:avoid-network-bottleneck-even-cache-enabled
-https://www.litespeedtech.com/support/wiki/doku.php/litespeed_wiki:cache:common_installation:advanced
-https://www.litespeedtech.com/support/wiki/doku.php/litespeed_wiki:cache
-https://www.litespeedtech.com/support/wiki/doku.php/litespeed_wiki:php:improve-performance
### LSCMD
Install isntructions https://www.litespeedtech.com/support/wiki/doku.php/litespeed_wiki:lsmcd:installation
#### Quick Install
```
apt-get install git build-essential zlib1g-dev libexpat1-dev openssl libssl-dev libsasl2-dev libpcre3-dev -y
git clone https://github.com/litespeedtech/lsmcd.git
cd lsmcd
./fixtimestamp.sh
./configure CFLAGS=" -O3" CXXFLAGS=" -O3"
make
make install
chown -R username /usr/local/lsmcd
systemctl start lsmcd
systemctl stop lsmcd
systemctl enable lsmcd
systemctl disable lsmcd
```
## Wordpress Constants
CONCATENATE_SCRIPTS	undefined
COMPRESS_SCRIPTS	undefined
COMPRESS_CSS	undefined
WP_LOCAL_DEV	undefined

### CONCATENATE_SCRIPTS
Not used due to a DoS attack? https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-6389
#### Mitigation 
1. Apache/Litespeed - https://www.netsted.co.za/a-simple-solution-to-wordpress-dos-vulnerability-cve-2018-6389/
```
RewriteCond %{HTTP_REFERER} !yourdomain\.co\.za [NC]
RewriteCond %{THE_REQUEST} \.php[\ /?].*HTTP/ [NC]
RewriteRule ^wp-admin/load-scripts\.php$ – [R=403,L]
```
2. NGiNX - https://bjornjohansen.no/load-scripts-php
```
limit_req_zone $binary_remote_addr zone=php:10m rate=1r/s;

server {
  […]
    location ~ .php$ {
      limit_req zone=php burst=10 nodelay;
      […]
    }
}
```

# Interesting Reads
- https://cachewall.com/
- https://wordpress.org/plugins/pj-page-cache-red/
- https://nginxconfig.io/?https&wordpress&file_structure=modularized
- https://www.modpagespeed.com/doc/configuration
- https://www.modpagespeed.com/doc/configuration


