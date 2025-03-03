ARG BUILD_FROM=hassioaddons/base:7.0.0
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Setup base
# hadolint ignore=DL3003
RUN \
    apk add --no-cache --virtual .build-dependencies \
        build-base=0.5-r1 \
        git=2.24.1-r0 \
        linux-headers=4.19.36-r0 \
        npm=12.15.0-r1 \
        openzwave-dev=1.6.974-r0 \
        python2-dev~=2.7 \
        yarn=1.19.2-r0 \
    \
    && apk add --no-cache \
        eudev=3.2.9-r1 \
        libusb=1.0.23-r0 \
        lua-resty-http=0.15-r0 \
        nginx-mod-http-lua=1.16.1-r6 \
        nginx=1.16.1-r6 \
        nodejs=12.15.0-r1 \
        openssl=1.1.1d-r3 \
        openzwave=1.6.974-r0 \
    \
    && curl -J -L -o /tmp/openzwave-db.tar.gz \
        "https://github.com/OpenZWave/open-zwave/archive/0d94c9427bbd19e47457578bccc60b16c6679b49.tar.gz" \
    && mkdir /tmp/db \
    && tar zxvf \
        /tmp/openzwave-db.tar.gz \
        --strip 1 -C /tmp/db \
    \
    && mkdir /config/OZW \
    && mv /tmp/db/config /config/OZW \
    \
    && curl -J -L -o /tmp/zwave2mqtt.tar.gz \
        "https://github.com/OpenZWave/Zwave2Mqtt/archive/v2.1.1.tar.gz" \
    && tar zxvf \
        /tmp/zwave2mqtt.tar.gz \
        --strip 1 -C /config \
    \
    && cd /config \
    && npm config set unsafe-perm \
    && npm install \
    && npm run build \
    && npm prune --production \
    \
    && yarn global add modclean \
    && modclean \
        --path /config \
        --no-progress \
        --keep-empty \
        --run \
    && yarn global remove modclean \
    && yarn cache clean \
    \
    && apk del --purge .build-dependencies \
    \
    && rm -f -r \
        /addons/etc/nginx \
        /OZW/openzwave \
        /addons/root/.node-gyp \
        /addons/root/.npm \
        /addons//tmp/.[!.]* \
        /addons//tmp/* \
        /addons//usr/lib/node_modules \
        /addons/usr/local/share/.cache

# Copy root filesystem
COPY rootfs /

# Build arguments
ARG BUILD_ARCH
ARG BUILD_DATE
ARG BUILD_REF
ARG BUILD_VERSION

# Labels
LABEL \
    io.hass.name="CorZ-Wave to MQTT" \
    io.hass.description="Fully configurable Z-Wave to MQTT gateway and control panel." \
    io.hass.arch="${BUILD_ARCH}" \
    io.hass.type="addon" \
    io.hass.version=${BUILD_VERSION} \
    maintainer="Corcent <corapps@corcent.com>" \
    org.label-schema.description="Fully configurable Z-Wave to MQTT gateway and control panel." \
    org.label-schema.build-date=${BUILD_DATE} \
    org.label-schema.name="Z-Wave to MQTT" \
    org.label-schema.schema-version="1.1" \
    org.label-schema.url="" \
    org.label-schema.usage="https://github.com/hassio-addons/addon-zwave2mqtt/tree/master/README.md" \
    org.label-schema.vcs-ref=${BUILD_REF} \
    org.label-schema.vcs-url="https://github.com/hassio-addons/addon-zwave2mqtt" \
    org.label-schema.vendor="Corcent"
