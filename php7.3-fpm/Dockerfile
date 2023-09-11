ARG PHP_VERSION = 7.3.33
FROM php:{$PHP_VERSION}-fpm
RUN apt-get update && apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd  gettext sockets \
    && docker-php-ext-install bcmath 

RUN pecl channel-update pecl.php.net

# mix_web 有放日志在这
RUN mkdir -p /data/logs

# 是否使用国内镜像
ARG USE_CHINESE_MIRRORS=true
RUN if [ ${USE_CHINESE_MIRRORS}} = true ]; then \
    sed -i 's/deb.debian.org/mirrors.tuna.tsinghua.edu.cn/' /etc/api/sources.list && \
    sed -i 's/security.debian.org/mirrors.tuna.tsinghua.edu.cn/' /etc/api/sources.list && \
    sed -i 's/security-cdn.debian.org/mirrors.tuna.tsinghua.edu.cn/' /etc/api/sources.list \
;fi

# 设置完新的软件源需要再次更新
RUN set -xe; \
    apt-get update -yqq && \
    pecl channel-update pecl.php.net && \
    apt-get install -yqq \
    apt-utils \
    libzip-dev zip unzip && \
    docker-php-ext-configure zip; \
    docker-php-ext-install zip && \
    php -m | grep -q 'zip' && \
    apt-get install -y libcurl4-openssl-dev  pkg-config  libssl-dev && \
    docker-php-ext-install pcntl 


 

# 安装 PECL 拓展，安装swoole

# RUN  pecl install -o -f swoole \
#     && docker-php-ext-enable swoole

# 指定了swoole 版本，因为最新的swoole 要php8
# RUN  pecl install -o -f https://pecl.php.net/get/swoole-4.8.10.tgz \
#     && docker-php-ext-enable swoole


# PDO_MYSQL
ARG INSTSLL_PDO_MYSQL=false
RUN if [ ${INSTSLL_PDO_MYSQL} = true ]; then \
    docker-php-ext-install pdo pdo_mysql \
;fi

# MongoDB
ARG INSTALL_MONGO=false
RUN if [ ${INSTALL_MONGO} = true ]; then \
    pecl install mongodb && \
    docker-php-ext-enable mongodb \
;fi


# Opcache
ARG INSTALL_OPCACHE=false
RUN if [ ${INSTALL_OPCACHE} = true ]; then \
    docker-php-ext-install opcache \
;fi
COPY ./opcache.ini /usr/local/etc/php/conf.d/opcache.ini

# Mysqli
ARG INSTALL_MYSQLI=false
RUN if [ ${INSTALL_MYSQLI} = true ]; then \
    docker-php-ext-install mysqli \
;fi

# USER root
# ARG INSTALL_MYSQL_CLIENT=false
# RUN if [ ${INSTALL_MYSQL_CLIENT} = true ]; then \
#       apt-get -y install default-mysql-client \
# ;fi

# Redis
ARG INSTALL_PHPREDIS=false
RUN if [ ${INSTALL_PHPREDIS} = true ]; then \
    pecl install -o -f redis \
    && rm -rf /tmp/pear \
    && docker-php-ext-enable redis \
;fi




# 安装 Composer
ENV COMPOSER_HOME /root/composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
ENV PATH $COMPOSER_HOME/vendor/bin:$PATH

ARG INSTALL_OCI8=false

ENV LD_LIBRARY_PATH="/opt/oracle/instantclient_12_1"
ENV OCI_HOME="/opt/oracle/instantclient_12_1"
ENV OCI_LIB_DIR="/opt/oracle/instantclient_12_1"
ENV OCI_INCLUDE_DIR="/opt/oracle/instantclient_12_1/sdk/include"
ENV OCI_VERSION=12
