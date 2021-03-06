FROM php:7.0-alpine

LABEL maintainer="Eduardo Bizarro <eduardo@zendev.com.br>" \
      php="7.0" \
      node="10"

# Set correct environment variables
ENV IMAGE_USER=php
ENV HOME=/home/$IMAGE_USER
ENV COMPOSER_HOME=$HOME/.composer
ENV PATH=$HOME/.yarn/bin:$PATH
ENV GOSS_VERSION="0.3.5"
ENV NODE_VERSION="v10.9.0"
ENV NPM_VERSION=6
ENV YARN_VERSION=latest
ENV PHP_VERSION=7.0

WORKDIR /tmp


# COPY INSTALL SCRIPTS
COPY ./php/scripts/alpine/*.sh /tmp/
COPY --from=composer:1.6 /usr/bin/composer /usr/bin/composer
COPY --from=mhart/alpine-node:10 /usr/bin/node /usr/bin/
COPY --from=mhart/alpine-node:10 /usr/lib/libgcc* /usr/lib/libstdc* /usr/lib/* /usr/lib/

RUN chown -R root:root /usr && chmod +x /tmp/*.sh \
    && adduser -D $IMAGE_USER \
    && mkdir -p /var/www/html \
    && apk add --update --no-cache bash \
    && bash ./packages.sh \
    && bash ./extensions.sh \
    && bash ./nodeyarn.sh \
    && bash ./cleanup.sh \
    && composer global require "hirak/prestissimo:^0.3" \
    && rm -rf ~/.composer/cache/* \
    && chown -R $IMAGE_USER:$IMAGE_USER /var/www $HOME \
    && echo "$IMAGE_USER  ALL = ( ALL ) NOPASSWD: ALL" >> /etc/sudoers \
    && curl -fsSL https://goss.rocks/install | GOSS_VER=v${GOSS_VERSION} sh

USER $IMAGE_USER

WORKDIR /var/www/html

CMD ["php", "-a"]
