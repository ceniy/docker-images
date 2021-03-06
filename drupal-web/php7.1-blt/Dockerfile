FROM php:7.1

LABEL maintainer="yechaozheng@ciandt.com"

ENV DRUSH_VERSION 8.1.18
ENV DRUSH_LAUNCHER_VERSION 0.6.0
ENV DRUSH_LAUNCHER_FALLBACK "/usr/local/drush/drush"

# Install necessary packages and PHP extensions for both Drupal 7 and 8
# Drupal 7 PHP requirement: https://www.drupal.org/docs/7/system-requirements/drupal-7-php-requirements#extensions
# Drupal 8 PHP requirement: https://www.drupal.org/docs/8/system-requirements/php-requirements
RUN curl -sL https://deb.nodesource.com/setup_6.x | bash - \
  && apt-get update && apt-get install -y --no-install-recommends \
      mysql-client \
      libpng-dev \
      libjpeg-dev \
      libpq-dev \
      libxml2-dev \
      nano \
      libmemcached-dev \
      libbz2-dev \
      git \
      zip \
      unzip \
      nodejs \
      openssh-client \
      python-dev \
  && docker-php-ext-configure gd --with-png-dir=/usr --with-jpeg-dir=/usr \
  && docker-php-ext-install gd pdo pdo_mysql pdo_pgsql zip opcache sockets soap bz2 mysqli \
  # Install Composer
  && curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
  && apt-get clean && rm -rf /var/lib/apt/lists/*

COPY config/php.ini /usr/local/etc/php/conf.d/

# Add default user `docker` and modify user id and group id
RUN groupadd -r -g 1001 docker && useradd --no-log-init -r -u 1000 -g docker docker \
  && echo '#!/bin/bash\nset -e\n\
[[ $(id -u docker) != ${CURRENT_USER_UID:-1000} ]] && usermod -u ${CURRENT_USER_UID:-1000} docker\n\
[[ $(id -g docker) != ${CURRENT_USER_GID:-1001} ]] && groupmod -g ${CURRENT_USER_GID:-1001} docker\n\
chown -R docker:docker /var/www/html' > /start.sh && chmod 755 /start.sh && /start.sh \
  # Install Drush
  && mkdir -p /usr/local/drush \
  && curl -fsSL -o /usr/local/drush/drush "https://github.com/drush-ops/drush/releases/download/$DRUSH_VERSION/drush.phar" \
  && curl -fsSL -o /usr/local/bin/drush https://github.com/drush-ops/drush-launcher/releases/download/${DRUSH_LAUNCHER_VERSION}/drush.phar \    
  && chmod +x /usr/local/drush/drush /usr/local/bin/drush \
  && drush --version
