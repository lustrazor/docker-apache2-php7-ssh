FROM php:7.4-apache

# Install system dependencies
RUN apt-get update && apt-get install -y \
    zip \
    unzip \
    && rm -rf /var/lib/apt/lists/* \
    # Install and configure PHP extensions
    && docker-php-ext-install pdo_mysql opcache

# Enable Apache modules
RUN a2enmod rewrite headers

# Add security headers
RUN echo "expose_php = Off" > /usr/local/etc/php/conf.d/security.ini \
    && echo "display_errors = Off" >> /usr/local/etc/php/conf.d/security.ini \
    && echo "display_startup_errors = Off" >> /usr/local/etc/php/conf.d/security.ini \
    && echo "allow_url_include = Off" >> /usr/local/etc/php/conf.d/security.ini \
    && echo "session.cookie_httponly = On" >> /usr/local/etc/php/conf.d/security.ini

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

# Configure Apache DocumentRoot
RUN sed -i 's#DocumentRoot /var/www/html#DocumentRoot /var/www/html/public#' /etc/apache2/sites-available/000-default.conf

# Create non-root user
RUN useradd -u 1000 -G www-data webuser

# Set proper permissions
RUN chown -R webuser:www-data /var/www/html \
    && chmod -R 755 /var/www/html

USER webuser 
