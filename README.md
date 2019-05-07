# wordpress-ultimate-setup

# Installation
## XDebug
This works for not only OpenLitespeed but also LiteSpeed, and is for Ubuntu 18

1. Install appropriate packages

```apt-get install lsphp7.0-dev```

2. Download xdebug source and extract

```wget https://xdebug.org/files/xdebug-2.7.2.tgz
tar -zxvf xdebug-2.7.2.tgz
```

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
xdebug.show_error_trace
```
