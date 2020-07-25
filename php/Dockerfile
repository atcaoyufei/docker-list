FROM caoyufei/debian

ENV  PHP_VERSION=php-7.2.31 PHPRedis_Ver=redis-5.2.2
ADD conf /tmp/conf

ENV PHPIZE_DEPS autoconf dpkg-dev file g++ gcc libc-dev make pkg-config re2c

RUN apt-get update -y \
    && apt-get install -y --no-install-recommends $PHPIZE_DEPS wget curl ca-certificates libxml2-dev libcurl4-openssl-dev libjpeg-dev libpng-dev libfreetype6-dev openssl libssl-dev \
    && CONFIG="\
    --prefix=/usr/local/php --with-config-file-path=/usr/local/php/etc --with-config-file-scan-dir=/usr/local/php/conf.d \
    --enable-fpm --with-fpm-user=www --with-fpm-group=www --enable-mysqlnd --with-mysqli=mysqlnd --with-pdo-mysql=mysqlnd \
    --with-iconv-dir --with-freetype-dir --with-jpeg-dir --with-png-dir --with-zlib --with-libxml-dir=/usr --enable-xml \
    --disable-rpath --enable-bcmath --enable-shmop --enable-sysvsem --enable-inline-optimization --with-curl --enable-mbregex \
    --enable-mbstring --enable-intl --enable-pcntl --enable-ftp --with-gd --with-openssl --with-mhash --enable-pcntl \
    --enable-sockets --with-xmlrpc --enable-zip --enable-soap --with-gettext --enable-opcache --enable-fileinfo \
    " \
    && groupadd www \
    && useradd -s /sbin/nologin -g www www \
    && cd /tmp \
    && wget -c --progress=bar:force --prefer-family=IPv4 --no-check-certificate http://soft.vpser.net/web/php/${PHP_VERSION}.tar.bz2 \
    && tar xjf ${PHP_VERSION}.tar.bz2 \
    && cd /tmp/${PHP_VERSION} \
    && ./configure $CONFIG \
    && make -j$(getconf _NPROCESSORS_ONLN) \
    && make install \
    && ln -sf /usr/local/php/bin/php /usr/bin/php \
    && ln -sf /usr/local/php/bin/phpize /usr/bin/phpize \
    && ln -sf /usr/local/php/bin/pear /usr/bin/pear \
    && ln -sf /usr/local/php/bin/pecl /usr/bin/pecl \
    && ln -sf /usr/local/php/sbin/php-fpm /usr/bin/php-fpm \
    && cp php.ini-production /usr/local/php/etc/php.ini \
    && mkdir -p /usr/local/php/conf.d \
    && mkdir -p /data/wwwroot/ \
    && mkdir -p /data/wwwlogs \
    && chown -R www:www /data/wwwroot \
    && sed -i 's/post_max_size =.*/post_max_size = 50M/g' /usr/local/php/etc/php.ini \
    && sed -i 's/upload_max_filesize =.*/upload_max_filesize = 50M/g' /usr/local/php/etc/php.ini \
    && sed -i 's/;date.timezone =.*/date.timezone = PRC/g' /usr/local/php/etc/php.ini \
    && sed -i 's/;error_log = php_errors.log/error_log = \/data\/wwwlogs\/php_errors.log/g' /usr/local/php/etc/php.ini \
    && sed -i 's/short_open_tag =.*/short_open_tag = On/g' /usr/local/php/etc/php.ini \
    && sed -i 's/;cgi.fix_pathinfo=.*/cgi.fix_pathinfo=0/g' /usr/local/php/etc/php.ini \
    && sed -i 's/max_execution_time =.*/max_execution_time = 300/g' /usr/local/php/etc/php.ini \
    && sed -i 's/disable_functions =.*/disable_functions = passthru,system,chroot,chgrp,chown,shell_exec,popen,ini_alter,ini_restore,dl,openlog,syslog,readlink,popepassthru,stream_socket_server/g' /usr/local/php/etc/php.ini \
    && pear config-set php_ini /usr/local/php/etc/php.ini \
    && pecl config-set php_ini /usr/local/php/etc/php.ini \
    && pecl update-channels \
    && cp /tmp/conf/php-fpm.conf /usr/local/php/etc/php-fpm.conf \
    && rm ~/.pearrc \
    && rm -f /usr/local/php/conf.d/* \
    && cp /tmp/conf/004-opcache.ini /usr/local/php/conf.d/004-opcache.ini \
    && cp sapi/fpm/init.d.php-fpm /etc/init.d/php-fpm \
    && chmod +x /etc/init.d/php-fpm \
    && cd /tmp \
    && wget -c --progress=bar:force --prefer-family=IPv4 --no-check-certificate http://pecl.php.net/get/${PHPRedis_Ver}.tgz \
    && tar zxf ${PHPRedis_Ver}.tgz \
    && cd /tmp/${PHPRedis_Ver} \
    && /usr/local/php/bin/phpize \
    && ./configure --with-php-config=/usr/local/php/bin/php-config \
    && make \
    && make install \
    && cp /tmp/conf/007-redis.ini /usr/local/php/conf.d/007-redis.ini \
    && install -m 755 /tmp/conf/fpm-configure /usr/local/bin/fpm-configure \
    && wget --prefer-family=IPv4 --no-check-certificate -T 120 -t3 http://soft.vpser.net/web/php/composer/composer.phar -O /usr/local/bin/composer \
    && chmod +x /usr/local/bin/composer \
    && composer global require 'fxp/composer-asset-plugin' \
    && apt-get purge --auto-remove autoconf dpkg-dev g++ gcc libc-dev make pkg-config re2c -y \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

STOPSIGNAL SIGQUIT
WORKDIR /data/wwwroot

CMD ["php-fpm", "-F", "--fpm-config", "/usr/local/php/etc/php-fpm.conf"]