FROM debian:buster

RUN apt-get update && apt-get install -y build-essential curl devscripts git wget cmake default-jre dh-python doxygen fontforge-nox fonts-dejavu-core fonts-droid-fallback  gawk gperf groovy librsvg2-bin libasound2-dev libass-dev libavahi-client-dev libavahi-common-dev libavcodec-dev libavfilter-dev  libavformat-dev  libavutil-dev  libbluetooth-dev libbluray-dev  libcap-dev libcdio-dev libcec-dev  libp8-platform-dev libcommons-lang-java libcurl4-gnutls-dev libcwiid-dev libdbus-1-dev libcrossguid-dev libdrm-dev libegl1-mesa-dev libenca-dev libfmt-dev libfontconfig-dev libfreetype6-dev libfribidi-dev libfstrcmp-dev libgbm-dev libgcrypt-dev libgif-dev libgl1-mesa-dev libglew-dev libglu1-mesa-dev libgnutls28-dev libgpg-error-dev libgtest-dev libinput-dev libiso9660-dev libjpeg-dev liblcms2-dev liblirc-dev liblzo2-dev libmicrohttpd-dev default-libmysqlclient-dev libnfs-dev libpcre3-dev libplist-dev libpng-dev libpostproc-dev  libpulse-dev libsmbclient-dev libsqlite3-dev libswscale-dev  libtag1-dev  libtinyxml-dev libudev-dev libva-dev libvdpau-dev libxkbcommon-dev libxrandr-dev libxslt1-dev libxt-dev python-dev rapidjson-dev swig uuid-dev valgrind wayland-protocols zip zlib1g-dev

RUN mkdir -p /src/kodi && \
    curl -s https://salsa.debian.org/multimedia-team/kodi/-/archive/30ac3a541b445ce8c4eb8121aceae72b076550f3/kodi-30ac3a541b445ce8c4eb8121aceae72b076550f3.tar.gz | \
    tar -x -z --strip-components=1 -C /src/kodi

RUN mkdir -p /src/kodi/webinterface-default && \
    curl -sL http://deb.debian.org/debian/pool/main/k/kodi/kodi_17.1+dfsg1.orig-webinterface-default.tar.xz | \
    tar -xJ --strip-components=1 -C /src/kodi/webinterface-default

RUN mkdir -p /src/kodi/libdvdnav-6-0-0 && \
    curl -sL https://download.videolan.org/pub/videolan/libdvdnav/6.0.0/libdvdnav-6.0.0.tar.bz2 | \
    tar -xj --strip-components=1 -C /src/kodi/libdvdnav-6-0-0

RUN mkdir -p /src/kodi/libdvdread-6-0-1 && \
    curl -sL https://download.videolan.org/pub/videolan/libdvdread/6.0.1/libdvdread-6.0.1.tar.bz2 | \
    tar -xj --strip-components=1 -C /src/kodi/libdvdread-6-0-1

RUN cd /tmp && \
    wget http://www.deb-multimedia.org/pool/main/f/flatbuffers-dmo/libflatbuffers-dev_1.11.0-dmo1_amd64.deb \
         http://www.deb-multimedia.org/pool/main/f/flatbuffers-dmo/flatbuffers-compiler_1.11.0-dmo1_amd64.deb \
         http://www.deb-multimedia.org/pool/main/f/flatbuffers-dmo/libflatbuffers1_1.11.0-dmo1_amd64.deb && \
    dpkg -i *_amd64.deb

WORKDIR /src/kodi

RUN nice debuild -uc -us -b
