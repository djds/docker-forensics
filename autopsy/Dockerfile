# Run autopsy in a container

FROM ubuntu:cosmic
LABEL maintainer "djds djds@ccs.neu.edu"

ENV DEBIAN_FRONTEND="noninteractive"

RUN apt-get update && apt-get dist-upgrade -y \
    && apt-get install -y \
    ant \
    ca-certificates \
    g++ \
    gcc \
    gpg \
    java-common \
    libafflib-dev \
    libboost-dev \
    libewf-dev \
    libgl1-mesa-dri \
    libgl1-mesa-glx \
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

RUN wget "https://d3pxv6yz143wms.cloudfront.net/8.212.04.1/java-1.8.0-amazon-corretto-jdk_8.212.04-1_amd64.deb" \
    && apt-get install -y ./java-1.8.0-amazon-corretto-jdk_8.212.04-1_amd64.deb \
    && apt-get install -y --fix-missing

RUN wget "https://github.com/sleuthkit/autopsy/releases/download/autopsy-4.10.0/autopsy-4.10.0.zip" \
    && wget "https://github.com/sleuthkit/autopsy/releases/download/autopsy-4.10.0/autopsy-4.10.0.zip.asc" \
    && wget "https://github.com/sleuthkit/sleuthkit/releases/download/sleuthkit-4.6.5/sleuthkit-4.6.5.tar.gz" \
    && wget "https://github.com/sleuthkit/sleuthkit/releases/download/sleuthkit-4.6.5/sleuthkit-4.6.5.tar.gz.asc"

RUN gpg --recv-keys "0917A7EE58A9308B13D3963338AD602EC7454C8B" \
    && gpg --verify autopsy-4.10.0.zip.asc \
    && gpg --verify sleuthkit-4.6.5.tar.gz.asc \
    && tar -xf sleuthkit-4.6.5.tar.gz \
    && unzip autopsy-4.10.0.zip

WORKDIR /opt/sleuthkit-4.6.5 

RUN export JAVA_HOME="/usr/lib/jvm/java-1.8.0-amazon-corretto/" \
    && ./configure \
    && make \
    && make install

WORKDIR /opt/autopsy-4.10.0

RUN chmod +x ./unix_setup.sh \
    && export JAVA_HOME="/usr/lib/jvm/java-1.8.0-amazon-corretto/" \
    && ./unix_setup.sh \
    && apt-get install -y --fix-broken \
    && chmod +x /opt/autopsy-4.10.0/bin/autopsy

ARG GID
ARG ID
ARG AUDIO

RUN groupadd -g $GID autopsy \
    && groupmod -g $AUDIO audio \
    && useradd -m -G audio,video,plugdev -u $ID -g $GID autopsy \
    && mkdir -p /home/autopsy/data \
    && chown -R autopsy:autopsy /home/autopsy \
    && rm -rf /var/lib/apt/lists/* \
    && rm /opt/sleuthkit-4.6.5.tar.gz \
    && rm /opt/autopsy-4.10.0.zip 


WORKDIR /home/autopsy/data

# Run as non privileged user
USER autopsy

ENTRYPOINT [ "/opt/autopsy-4.10.0/bin/autopsy" ]
