FROM php:7.4-fpm-alpine

# Install system dependencies
RUN apk add --no-cache \
    zip \
    unzip \
    libxml2 \
    # Add build dependencies for PHP extensions
    && apk add --no-cache --virtual .build-deps \
        $PHPIZE_DEPS \
    # Install and configure PHP extensions
    && docker-php-ext-install pdo_mysql opcache \
    # Remove build dependencies
    && apk del .build-deps

# Add security headers
RUN echo "expose_php = Off" > /usr/local/etc/php/conf.d/security.ini \
    && echo "display_errors = Off" >> /usr/local/etc/php/conf.d/security.ini \
    && echo "display_startup_errors = Off" >> /usr/local/etc/php/conf.d/security.ini \
    && echo "allow_url_include = Off" >> /usr/local/etc/php/conf.d/security.ini \
    && echo "allow_url_fopen = Off" >> /usr/local/etc/php/conf.d/security.ini \
    && echo "session.cookie_httponly = On" >> /usr/local/etc/php/conf.d/security.ini \
    && echo "disable_functions = curl_exec,curl_multi_exec,curl_init,curl_setopt,curl_setopt_array" >> /usr/local/etc/php/conf.d/security.ini

# Set recommended PHP.ini settings for PHP 7.4
RUN { \
    echo 'opcache.memory_consumption=128'; \
    echo 'opcache.interned_strings_buffer=8'; \
    echo 'opcache.max_accelerated_files=4000'; \
    echo 'opcache.revalidate_freq=2'; \
    echo 'opcache.fast_shutdown=1'; \
    echo 'opcache.enable_cli=1'; \
    echo 'opcache.enable_file_override=0'; \
    echo 'opcache.validate_timestamps=1'; \
    echo 'opcache.save_comments=1'; \
} > /usr/local/etc/php/conf.d/opcache-recommended.ini

# Create non-root user
RUN adduser -u 1000 -D -G www-data webuser
USER webuser 