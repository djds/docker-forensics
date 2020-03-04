FROM ubuntu:focal
LABEL maintainer "djds djds@ccs.neu.edu"

ENV DEBIAN_FRONTEND="noninteractive"

RUN apt-get update && apt-get dist-upgrade -y \
    && apt-get install -y \
    ant \
    ca-certificates \
    curl \
    dos2unix \
    fonts-noto \
    g++ \
    gcc \
    gpg \
    java-common \
    libafflib-dev \
    libboost-dev \
    libewf-dev \
    libgl1-mesa-dri \
    libgl1-mesa-glx \
    libgstreamer1.0 \
    libsolr-java \
    libsqlite3-dev \
    libswt-gtk-4-java \
    libtika-java \
    libtool \
    libtsk-dev \
    libvhdi-dev \
    libvmdk-dev \
    make \
    openjfx \
    postgresql \
    software-properties-common \
    sqlite3 \
    testdisk \
    wget \
    zip \
    zlib1g-dev

WORKDIR /opt

RUN wget "https://corretto.aws/downloads/latest/amazon-corretto-8-x64-linux-jdk.deb" \
    && ( [ "$(md5sum ./amazon-corretto-8-x64-linux-jdk.deb | awk '{print $1}')" = "$(curl https://corretto.aws/downloads/latest_checksum/amazon-corretto-8-x64-linux-jdk.deb)" ] || exit 1 ) \
    && apt-get install -qq ./amazon-corretto-8-x64-linux-jdk.deb \
    && apt-get install -qq --fix-missing

RUN wget "https://github.com/sleuthkit/autopsy/releases/download/autopsy-4.14.0/autopsy-4.14.0.zip" \
    && wget "https://github.com/sleuthkit/autopsy/releases/download/autopsy-4.14.0/autopsy-4.14.0.zip.asc" \
    && wget "https://github.com/sleuthkit/sleuthkit/releases/download/sleuthkit-4.8.0/sleuthkit-4.8.0.tar.gz" \
    && wget "https://github.com/sleuthkit/sleuthkit/releases/download/sleuthkit-4.8.0/sleuthkit-4.8.0.tar.gz.asc"

RUN gpg \
        --keyserver "hkps://keyserver.ubuntu.com" \
        --recv-keys "0917A7EE58A9308B13D3963338AD602EC7454C8B" \
    && gpg --verify autopsy-4.14.0.zip.asc \
    && gpg --verify sleuthkit-4.8.0.tar.gz.asc \
    && tar -xf sleuthkit-4.8.0.tar.gz \
    && unzip autopsy-4.14.0.zip

ENV JAVA_HOME="/usr/lib/jvm/java-8-amazon-corretto/" 

WORKDIR /opt/sleuthkit-4.8.0 

RUN ./configure \
    && make \
    && make install

WORKDIR /opt/autopsy-4.14.0

RUN dos2unix ./unix_setup.sh \
    && chmod +x ./unix_setup.sh \
    && ./unix_setup.sh \
    && apt-get install -qq --fix-broken \
    && chmod +x /opt/autopsy-4.14.0/bin/autopsy

ARG GID
ARG ID
ARG AUDIO

RUN groupadd -g "${GID}" autopsy \
    && groupmod -g "${AUDIO}" audio \
    && useradd -m -G audio,video,disk -u "${ID}" -g "${GID}" autopsy \
    && mkdir -p /home/autopsy/data \
    && chown -R autopsy:autopsy /home/autopsy \
    && rm -rf /var/lib/apt/lists/* \
    && rm /opt/sleuthkit-4.8.0.tar.gz \
    && rm /opt/autopsy-4.14.0.zip 


WORKDIR /home/autopsy/data

# Run as non privileged user
USER autopsy

ENTRYPOINT [ "/opt/autopsy-4.14.0/bin/autopsy" ]
