<!--ts-->
   * [Ultimate Wordpress Setup](#ultimate-wordpress-setup)
   * [LiteSpeed](#litespeed)
      * [Open LiteSpeed](#open-litespeed)
         * [Quick Install Commands](#quick-install-commands)
      * [LiteSpeed Paid](#litespeed-paid)
         * [Quick Install Commands](#quick-install-commands-1)
         * [LSPHP](#lsphp)
      * [LetsEncrypt SSL](#letsencrypt-ssl)
         * [Quick Install](#quick-install)
      * [Vanish](#vanish)
      * [Percona DB](#percona-db)
      * [Redis](#redis)
      * [XDebug](#xdebug)
      * [New Relic for LiteSpeed/OpenLitespeed](#new-relic-for-litespeedopenlitespeed)
         * [Multiple Sites with New Relic](#multiple-sites-with-new-relic)
         * [New Relic Daemon Setup](#new-relic-daemon-setup)
      * [GIT Tracking](#git-tracking)
         * [Remove Sensitive Authentication from GIT Tracking](#remove-sensitive-authentication-from-git-tracking)
         * [Wordpress .gitignore](#wordpress-gitignore)
      * [Content Delivery Network (CDN)](#content-delivery-network-cdn)
      * [Wordpress Tweaks](#wordpress-tweaks)
         * [Transients](#transients)
         * [Disable Cron](#disable-cron)
         * [System Fonts versus Web Fonts](#system-fonts-versus-web-fonts)
         * [Move to GeneratePress Theme](#move-to-generatepress-theme)
         * [DNS Pre-Fetching](#dns-pre-fetching)
         * [Force SSL for Wordpress Admin](#force-ssl-for-wordpress-admin)
         * [Force SSL for all content (Don't use a Plugin](#force-ssl-for-all-content-dont-use-a-plugin)
      * [Load Testing](#load-testing)
      * [Google PageSpeed Module](#google-pagespeed-module)
      * [PHP](#php)
         * [OPCode Caching](#opcode-caching)
      * [Caching](#caching)
         * [Litespeed](#litespeed-1)
         * [LSCMD](#lscmd)
            * [Quick Install](#quick-install-1)
      * [Wordpress Constants](#wordpress-constants)
         * [CONCATENATE_SCRIPTS](#concatenate_scripts)
            * [Mitigation](#mitigation)
   * [Interesting Reads](#interesting-reads)

<!-- Added by: jtrask, at: Fri 24 May 2019 15:08:36 PDT -->

<!--te-->
# Ultimate Wordpress Setup
I've created this guide based on my own experiences and research. If you have any questions, please email me directly or create an issue.
# LiteSpeed
I've chosen LiteSpeed due to two simple facts. It has a memory based caching system, and provides the fastest PHP implementation to date.
## Open LiteSpeed
I haven't had a chance to document this process.....
### Quick Install Commands
```
wget -O - http://rpms.litespeedtech.com/debian/enable_lst_debain_repo.sh | bash
apt install openlitespeed lsphp72 lsphp72-curl lsphp72-imap lsphp72-mysql lsphp72-intl lsphp72-pgsql lsphp72-sqlite3 lsphp72-tidy lsphp72-snmp
```
## LiteSpeed Paid
You can get a free license from LiteSpeed for a single site and CPU. It includes the LiteSpeed Cache plugin for Wordpress.
### Quick Install Commands
You'll get an email after payment with a single line command to install LiteSpeed Web Server
```
bash <( curl https://get.litespeed.sh ) (litespeedlicensekey)
```
### LSPHP
LSPHP isn't provided by default so you'll need to install that as well.
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

### New Relic Daemon Setup
By default, new relic will spawn the newrelic-daemon process under the PHP user. You can change this by doing the following.
```
cp /etc/newrelic/newrelic.cfg.template /etc/newrelic/newrelic.cfg
systemctl enable newrelic-daemon
systemctl start newrelic-daemon
```
## GIT Tracking
You can skip this if you don't care about changes you're making to your site.

We can use GIT to track some of the changes made to a sites wp-config.php or .htaccess incase you nuke the site.

### Remove Sensitive Authentication from GIT Tracking
 I like tracking wp-config.php, but it contains sensitive authentication informationr. You'll need to do a little chopping up of the wp-config.php and split out the sensitive information into another file and remove it from wp-config.php

I suggest that you copy your wp-config.php outside of your document root and then remove everything except for the WordPress Database settings, WordPress keys and the WordPress table prefix. Here's an example

```
<?php
define('DB_NAME', 'Your_DB'); // name of database
define('DB_USER', 'DB_User'); // MySQL user
define('DB_PASSWORD', 'DB_pass'); // and password
define('DB_HOST', 'localhost'); // MySQL host
 
define('AUTH_KEY',         'Your_key_here');
define('SECURE_AUTH_KEY',  'Your_key_here');
define('LOGGED_IN_KEY',    'Your_key_here');
define('NONCE_KEY',        'Your_key_here');
define('AUTH_SALT',        'Your_key_here');
define('SECURE_AUTH_SALT', 'Your_key_here');
define('LOGGED_IN_SALT',   'Your_key_here');
define('NONCE_SALT',       'Your_key_here');
 
$table_prefix  = 'wp_'; // only numbers, letters and underscore
?>
```
You'll then need to remove the above CONSTANTS from your wp-config.php within your document root and add the following line.
```
include("/home/user/wp-config.php")
```
The above line requires your home directory path, which you can find pretty easily.

### Wordpress .gitignore
Create a file named .gitignore and add the following.
```
# Ignore Everything
/*

# Let's track some things.
!.htaccess
!wp-config.php
```
This is good to start, eventually we will want to track more.
## Content Delivery Network (CDN)
- https://www.keycdn.com/pricing
- https://stackpath.com/
    - Ensure "CORS Header Support" is not checked off or you'll have multiple access-control-allow-origin
## Wordpress Tweaks
### Transients
- https://pressjitsu.com/blog/optimizing-wp-options-for-speed/
- https://github.com/pressjitsu/wp-transients-cleaner/blob/master/transient-cleaner.php
### Disable Cron
- https://pressjitsu.com/blog/wordpress-cron-cli/
### System Fonts versus Web Fonts
- https://perfmatters.io/wordpress-performance-optimization/
- https://woorkup.com/system-font/
### Move to GeneratePress Theme
- https://generatepress.com
### DNS Pre-Fetching
- https://perfmatters.io/docs/dns-prefetching/
I'll be including this in a plugin shortly.
### Force SSL for Wordpress Admin
Add the following to wp-config.php
```define('FORCE_SSL_ADMIN', true);```
### Force SSL for all content (Don't use a Plugin)
```
<IfModule mod_rewrite.c>
    RewriteEngine On
    RewriteCond %{SERVER_PORT} !^443$
    RewriteRule (.*) https://%{HTTP_HOST}%{REQUEST_URI} [R=301,L]
    RewriteBase /
    RewriteRule ^index\.php$ - [L]
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteRule . /index.php [L]
</IfModule>
```
### Secure Wordpress Passwords
- https://roots.io/improving-wordpress-password-security/

## Image Optimization
- https://piio.co/
- https://imagekit.io/
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
- WordPress Visual Code Extensions - https://visualstudiomagazine.com/articles/2018/01/24/wordpress-extensions.aspx
- https://cachewall.com/
- https://wordpress.org/plugins/pj-page-cache-red/
- https://nginxconfig.io/?https&wordpress&file_structure=modularized
- https://www.modpagespeed.com/doc/configuration
- https://www.modpagespeed.com/doc/configuration
- https://github.com/phpmetrics/PhpMetrics
- https://wpackagist.org/
- https://roots.io/trellis/
