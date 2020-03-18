FROM ubuntu:16.04
MAINTAINER Overhang.io <contact@overhang.io>

############ common to lms & cms

# Install system requirements
RUN apt update && \
    # Global requirements
    apt install -y language-pack-en git python-virtualenv build-essential software-properties-common curl git-core libxml2-dev libxslt1-dev python-virtualenv libmysqlclient-dev python-apt python-dev libxmlsec1-dev libfreetype6-dev swig gcc g++ \
    # openedx requirements
    gettext gfortran graphviz graphviz-dev libffi-dev libfreetype6-dev libgeos-dev libjpeg8-dev liblapack-dev libpng12-dev libsqlite3-dev libxml2-dev libxmlsec1-dev libxslt1-dev lynx nodejs npm ntp pkg-config \
    && rm -rf /var/lib/apt/lists/*

# Dockerize will be useful to wait for mysql DB availability
ARG DOCKERIZE_VERSION=v0.6.1
RUN curl -L -o /tmp/dockerize.tar.gz https://github.com/jwilder/dockerize/releases/download/$DOCKERIZE_VERSION/dockerize-linux-amd64-$DOCKERIZE_VERSION.tar.gz \
    && tar -C /usr/local/bin -xzvf /tmp/dockerize.tar.gz \
    && rm /tmp/dockerize.tar.gz

# Checkout edx-platform code
ARG EDX_PLATFORM_REPOSITORY=https://github.com/edx/edx-platform.git
ARG EDX_PLATFORM_VERSION=open-release/ironwood.2
RUN mkdir -p /openedx/edx-platform && \
    --no-cache git clone $EDX_PLATFORM_REPOSITORY --branch $EDX_PLATFORM_VERSION --depth 1 /openedx/edx-platform
WORKDIR /openedx/edx-platform

# Apply patches
# Certificates XSS vulnerability https://github.com/edx/edx-platform/pull/20904
RUN curl https://github.com/edx/edx-platform/commit/b33db2c548a1a530510d785f7659c78783a187fa.patch | git apply -
# CustomTagModule mako template injection https://groups.google.com/forum/#!topic/openedx-ops/aVHomKimstU
RUN curl https://github.com/edx/edx-platform/commit/f9689aadb0f8a41570a4bb76654f980b4e31ad96.patch | git apply -

# Download extra locales to /openedx/locale
# TODO upgrade this to ironwood
RUN cd /tmp \
    && curl -L -o openedx-i18n.tar.gz https://github.com/openedx/openedx-i18n/archive/hawthorn.tar.gz \
    && tar xzf /tmp/openedx-i18n.tar.gz \
    && mv opened