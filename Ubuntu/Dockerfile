FROM ubuntu:18.04
MAINTAINER HLXEasy <hlxeasy@gmail.com>

ENV WORK_DIR=/data/work \
    DEBIAN_FRONTEND=noninteractive \
    LC_ALL=en_US.UTF-8

RUN apt-get update -y \
 && apt-get upgrade -y

RUN apt-get install -y --no-install-recommends \
    autoconf \
    automake \
    build-essential \
    ca-certificates \
    g++ \
    git \
    less \
    libboost1.65-all-dev \
    libcap-dev \
    libevent-dev \
    libqt5webchannel5-dev \
    libseccomp-dev \
    libssl-dev \
    libtool \
    libz-dev \
    locales \
    make \
    mc \
    mingw-w64 \
    mingw-w64-x86-64-dev \
    openssh-client \
    pkg-config \
    qt5-default \
    qtbase5-dev \
    qttools5-dev-tools \
    qtwebengine5-dev \
    wget

# Set locale to UTF8
RUN echo "en_US.UTF-8 UTF-8" > /etc/locale.gen \
 && locale-gen en_US.UTF-8 \
 && dpkg-reconfigure locales \
 && /usr/sbin/update-locale LANG=en_US.UTF-8
