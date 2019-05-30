# Ultimate WordPress Deployment Guide
This guide provides the fastest WordPress instance available for use on a full operating system, it's based on Ubuntu 18. You can use some of the information within this guide for managed WordPress Hosting. However, you should contact your provider before proceeding with any changes contained in this guide.

I've created this guide based on my own experiences and research. If you have any questions, please email me directly or create a github issue.
I'm also on Gitter [![Gitter](https://badges.gitter.im/jordantrizz/community.svg)](https://gitter.im/jordantrizz/community?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge) if I don't respond, then email me and I'll hop on.

## Sponsors
This guide is sponsored by the following organizations.

- This guide is sponsored by [LMT Solutions](https://lmt.ca)

## Cautionary Disclaimer
<aside class="warning">
This is an on-going document, there are incomplete sections. Use at your own risk.
</aside>
- This guide is a work in progress.
- There are no guarantees.
- I'm not responsible for data loss or service interruptions.
- This is an unbiased guide, it's for pure speed and functionality.

# Table of Contents

<!--ts-->
   * [Ultimate WordPress Deployment Guide](README.md#ultimate-wordpress-deployment-guide)
      * [Sponsors](README.md#sponsors)
      * [Cautionary Disclaimer](README.md#cautionary-disclaimer)
   * [Table of Contents](README.md#table-of-contents)
   * [Infrastructure Technology](README.md#infrastructure-technology)
      * [LiteSpeed](README.md#litespeed)
         * [Open LiteSpeed](README.md#open-litespeed)
         * [LiteSpeed Paid](README.md#litespeed-paid)
            * [LSPHP](README.md#lsphp)
      * [LetsEncrypt SSL](README.md#letsencrypt-ssl)
         * [Quick Install](README.md#quick-install)
      * [Vanish](README.md#vanish)
      * [Percona DB](README.md#percona-db)
      * [Redis](README.md#redis)
      * [XDebug](README.md#xdebug)
      * [New Relic for LiteSpeed/OpenLitespeed](README.md#new-relic-for-litespeedopenlitespeed)
         * [Multiple Sites with New Relic](README.md#multiple-sites-with-new-relic)
         * [New Relic Daemon Setup](README.md#new-relic-daemon-setup)
      * [GIT Tracking](README.md#git-tracking)
         * [Remove Sensitive Authentication from GIT Tracking](README.md#remove-sensitive-authentication-from-git-tracking)
         * [WordPress .gitignore](README.md#wordpress-gitignore)
      * [Content Delivery Network (CDN)](README.md#content-delivery-network-cdn)
         * [Solutions](README.md#solutions)
         * [CloudFlare](README.md#cloudflare)
            * [Default CloudFlare Rules for Wordpress](README.md#default-cloudflare-rules-for-wordpress)
      * [WordPress Tweaks](README.md#wordpress-tweaks)
         * [Transients](README.md#transients)
         * [Disable Cron](README.md#disable-cron)
         * [System Fonts versus Web Fonts](README.md#system-fonts-versus-web-fonts)
         * [Move to GeneratePress Theme](README.md#move-to-generatepress-theme)
         * [DNS Pre-Fetching](README.md#dns-pre-fetching)
         * [Force SSL for WordPress Admin](README.md#force-ssl-for-wordpress-admin)
         * [Force SSL for all content (Don't use a Plugin)](README.md#force-ssl-for-all-content-dont-use-a-plugin)
         * [Secure WordPress Passwords](README.md#secure-wordpress-passwords)
      * [Image Optimization](README.md#image-optimization)
      * [Load Testing](README.md#load-testing)
      * [Google PageSpeed Module](README.md#google-pagespeed-module)
      * [PHP](README.md#php)
         * [OPCode Caching](README.md#opcode-caching)
      * [Caching](README.md#caching)
         * [Litespeed](README.md#litespeed-1)
         * [LSCMD](README.md#lscmd)
            * [Quick Install](README.md#quick-install-1)
      * [WordPress Constants](README.md#wordpress-constants)
         * [CONCATENATE_SCRIPTS](README.md#concatenate_scripts)
            * [Mitigation](README.md#mitigation)
   * [Interesting Reads and Sources](README.md#interesting-reads-and-sources)

<!-- Added by: jtrask, at: Wed 29 May 2019 12:53:31 PDT -->

<!--te-->

# Infrastructure Technology
When deciding on infrastructure to run WordPress on, I wanted to make sure functionality came first and speed was second. Here is a breakdown of all the required pieces to run a WordPress instance.

| Level | Technology | Reason |
| --- | --- | --- |
OS | Ubuntu 18 | explanation later.
DNS | CloudFlare | Free and easy to use.
Web Server | LiteSpeed | Currently provides the most functionality HTTP2/QUIC, etc.
SSL | LetsEncrypt | Simple and Easy. Open to alternatives.
PHP | LSAPI | LiteSpeed Server Application Programming Interface, Currently the fastest implementation of PHP.
Front-End Cache | LiteSpeed | Still need to test Varnish.
WP Caching Plugin | LiteSpeed | Provided for free and works with LiteSpeed's Front End cache. Also has all required caching options as WP Rocket, W3TC, etc.
CDN | StackPath | Currently provides the easiest means to deploy. LiteSpeeds Caching plugin provides the means to deploy this CDN.

DB | Percona | XtraDB seems to be far supierior than MariaDB, even though its available within MariaDB. I'm biased here.
Monitoring | New Relic | Free and simple.

# Operating System
Need to flesh this out with Operating System specifics.
# DNS
Need to flesh this out with DNS options.
## CloudFlare
# Web Server
## NGiNX
I think NGiNX has it's place, but unfortunately the paid version is insanely expensive, and is missing a big feature of being able to flush the entire fastcgi cache.

## LiteSpeed
I've chosen LiteSpeed due to two simple facts. It has a memory based caching system, and provides the fastest PHP implementation to date.
### Open LiteSpeed
This needs more documentation.
```
wget -O - http://rpms.litespeedtech.com/debian/enable_lst_debain_repo.sh | bash
apt install openlitespeed lsphp72 lsphp72-curl lsphp72-imap lsphp72-mysql lsphp72-intl lsphp72-pgsql lsphp72-sqlite3 lsphp72-tidy lsphp72-snmp
```
### LiteSpeed Paid
You can get a free license from LiteSpeed for a single domain, 1CPU and 2GB of memory. If you go over any of the resources, LiteSpeed won't start. The LiteSpeed Cache plugin for WordPress is included with the free license.

Once you've ordered a licenses, install instructions will following via email with a one-liner command to install using your new license key.
```
bash <( curl https://get.litespeed.sh ) (litespeedlicensekey)
```
# SSL
Only provided Lets Encrypt
## LetsEncrypt SSL
LetsEncrypt is easy and works well.
### Quick Install
This will generate your certificate, you'll need to configure it in LiteSpeed. I also suggest you setup a cron for automatic renewals.
```
add-apt-repository ppa:certbot/certbot
apt-get install certbot
certbot certonly --webroot -w /home/domain/public_html -d domain.com -d www.domain.com
```

#PHP
Lots of folks enjoy PHP-FPM, however LSAPI seems to be a faster alternative.
## LSPHP
LSPHP isn't provided by default so you'll need to install that as well.
```
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 011AA62DEDA1F085
wget -O - http://rpms.litespeedtech.com/debian/enable_lst_debain_repo.sh | bash
apt-get install lsphp73 lsphp73 lsphp73-opcache lsphp73-mysql lsphp73-memcached lsphp73-curl
```
# Front-End Cache
## LiteSpeed
Native to the LiteSpeed web server, there's also a WordPress plugin to control the cache. Provides ESI also.
## Varnish
Need's more documentation, if we go with LiteSpeed we can use this guide to proxy SSL requests to Varnish.

However further tests need to be completed to see the speed differences between LiteSpeed Cache and Varnish.

- https://www.linode.com/docs/websites/varnish/use-varnish-and-nginx-to-serve-WordPress-over-ssl-and-http-on-debian-8/
## Percona DB

## Redis

1. Resources
    - https://github.com/pressjitsu/pj-object-cache-red
    - https://pressjitsu.com/blog/redis-object-cache-WordPress/
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

### WordPress .gitignore
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
### Solutions
- https://www.keycdn.com/pricing
- https://stackpath.com/
    - Ensure "CORS Header Support" is not checked off or you'll have multiple access-control-allow-origin, only do this if you're having issues.
### CloudFlare
<aside class="warning">
Don't enable the same configuration option on CloudFlare as you have set on your Web Server or WordPress caching plugin as they will conflict.
</aside>
CloudFlare isn't really a CDN, it does more and includes WAF, AnyCast DNS, and Caching. If you do pay for any level of CloudFlare, take a look at this article for what options you should be setting.

https://onlinemediamasters.com/cloudflare-settings-for-wordpress/

#### Default CloudFlare Rules for Wordpress
Theses are the default rules you should have.
1. `\*.domain.com/*`
   - Cache Level: Cache Everything
2. `\*.domain.com/wp-content/uploads*`
   - Browser Cache TTL: 4 days
   - Cache Level: Bypass
   - Edge Cache TTL: a month
3. `\*.domain.com/wp-admin*`
   - Browser Integrity Check: On
   - Security Level: High
   - Cache Level: Bypass
   -   Disable Performance

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
# WordPress
<!-- Wordpress Start -->
## WP-CLI
### Useful packages
- wp package install wp-cli/doctor-command --allow-root
## WordPress Constants
To be placed into `wp-config.php` and defined as `define('WP_POST_REVISIONS',5);`

| Constant | Description  |
|---|---|
| CONCATENATE_SCRIPTS | Will essentially concatenate all scripts, <br> Not used due to a DoS attack? https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-6389 |
|  COMPRESS_SCRIPTS||
|  COMPRESS_CSS||
| WP_LOCAL_DEV|
| WP_POST_REVISIONS|Will setup the number of post revisions available. |
| CONCATENATE_SCRIPTS||
| COMPRESS_SCRIPTS||
| COMPRESS_CSS||
| WP_LOCAL_DEV||

### CONCATENATE_SCRIPTS
Not used due to a DoS attack? https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2018-6389
#### Mitigation 
1. Apache/Litespeed - https://www.netsted.co.za/a-simple-solution-to-WordPress-dos-vulnerability-cve-2018-6389/
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

## WordPress Tweaks
### Transients
- https://pressjitsu.com/blog/optimizing-wp-options-for-speed/
- https://github.com/pressjitsu/wp-transients-cleaner/blob/master/transient-cleaner.php
### Disable Cron
- https://pressjitsu.com/blog/WordPress-cron-cli/
### System Fonts versus Web Fonts
- https://perfmatters.io/WordPress-performance-optimization/
- https://woorkup.com/system-font/
### Move to GeneratePress Theme
- https://generatepress.com
### DNS Pre-Fetching
- https://perfmatters.io/docs/dns-prefetching/
I'll be including this in a plugin shortly.
### Force SSL for WordPress Admin
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
### Secure WordPress Passwords
- https://roots.io/improving-WordPress-password-security/

<!-- Wordpress End -->
# Interesting Reads and Sources
HeRe are all the websites I've found myself explaining some of the information above.
- WordPress Visual Code Extensions - https://visualstudiomagazine.com/articles/2018/01/24/WordPress-extensions.aspx
- https://cachewall.com/
- https://WordPress.org/plugins/pj-page-cache-red/
- https://nginxconfig.io/?https&WordPress&file_structure=modularized
- https://www.modpagespeed.com/doc/configuration
- https://www.modpagespeed.com/doc/configuration
- https://github.com/phpmetrics/PhpMetrics
- https://wpackagist.org/
- https://roots.io/trellis/
