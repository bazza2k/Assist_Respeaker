ARG BUILD_FROM
FROM ${BUILD_FROM}

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

WORKDIR /usr/src
ARG WYOMING_SATELLITE_VERSION
ENV PIP_BREAK_SYSTEM_PACKAGES=1

RUN \
    apt-get update \
    && apt-get install -y --no-install-recommends \
        netcat-traditional \
        libasound2-plugins \
        alsa-utils \
        python3 \
        python3-pip \
		git \
        dkms \
       i2c-tools \
       libasound2-plugins \
    && git clone "https://github.com/respeaker/seeed-voicecard" \
    && cd seeed-voicecard \
    && chmod a+x ./install.sh \
    && sh ./install.sh \
    && rm -rf .git \
	&& cd .. \
    \
    && pip3 install --no-cache-dir -U \
        setuptools \
        wheel \
    && pip3 install --no-cache-dir \
        "wyoming_satellite[webrtc]==${WYOMING_SATELLITE_VERSION}" \
    \
    && apt-get clean \
	&& apt-get purge -y --auto-remove git
    && rm -rf /var/lib/apt/lists/*

COPY sounds/ ./sounds/

WORKDIR /
COPY rootfs /

HEALTHCHECK --start-period=10m \
    CMD echo '{ "type": "describe" }' \
        | nc -w 1 localhost 10700 \
        | grep -q "assist" \
        || exit 1
