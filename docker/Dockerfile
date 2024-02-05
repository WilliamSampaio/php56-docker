FROM php:5.6-apache

RUN sed -i 's/deb.debian.org/archive.debian.org/g' /etc/apt/sources.list
RUN sed -i 's/security.debian.org/archive.debian.org/g' /etc/apt/sources.list
RUN sed -i '/stretch-updates/d' /etc/apt/sources.list

ENV DEBIAN_FRONTEND=noninteractive

EXPOSE 80

# debian packages
RUN apt-get update && apt-get install -y --no-install-recommends \
    curl \
    vim \
    libmcrypt-dev \
    libicu-dev \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libpng-dev \
    libpq-dev \
    libxml2-dev \
    mysql-client \
    unzip \
    git \
    locales \
    tzdata

# locales
RUN printf 'pt_BR.UTF-8 UTF-8\n' >> /etc/locale.gen \
    # && printf 'en_US.UTF-8 UTF-8\n' >> /etc/locale.gen \
    # && printf 'de_DE.UTF-8 UTF-8\n' >> /etc/locale.gen \
    && locale-gen

ENV LANGUAGE=pt_BR.UTF-8
ENV LC_ALL=pt_BR.UTF-8
ENV LC_CTYPE=UTF-8
ENV LANG=pt_BR.UTF-8

# localtime
ENV TZ='America/Manaus'
RUN echo $TZ > /etc/timezone \
    && rm /etc/localtime \
    && ln -snf /usr/share/zoneinfo/$TZ /etc/localtime \
    && dpkg-reconfigure -f noninteractive tzdata

# php modules
RUN docker-php-ext-install -j$(nproc) \
    mysqli \
    pgsql \
    pdo \
    pdo_mysql \
    pdo_pgsql \
    mbstring \
    mysql \
    iconv \
    mcrypt \
    intl \
    opcache \
    xml \
    soap \
    opcache \
    zip \
    exif \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd

# php composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# apache modules
RUN a2enmod rewrite
RUN a2enmod headers

COPY app.conf /etc/apache2/sites-available/app.conf

RUN a2dissite 000-default.conf
RUN a2ensite app.conf

# apache rights
RUN usermod -o -u 1000 www-data

# cleanup
RUN apt-get clean \
    && rm -fr /var/lib/apt/lists/* \
    && rm -fr /tmp/* \
    && rm -fr /var/tmp/*