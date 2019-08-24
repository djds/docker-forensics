# Run autopsy in a container

FROM ubuntu:disco
LABEL maintainer "djds djds@ccs.neu.edu"

ENV DEBIAN_FRONTEND="noninteractive"

RUN apt-get update && apt-get dist-upgrade -y \
    && apt-get install -y \
    ant \
    ca-certificates \
    dos2unix \
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

RUN wget "https://d3pxv6yz143wms.cloudfront.net/8.222.10.1/java-1.8.0-amazon-corretto-jdk_8.222.10-1_amd64.deb" \
    && ( [ "$(md5sum ./java-1.8.0-amazon-corretto-jdk_8.222.10-1_amd64.deb | awk '{print $1}')" = "c557ec2400165ebbd665fe4a95658efd" ] || exit 1 ) \
    && apt-get install -y ./java-1.8.0-amazon-corretto-jdk_8.222.10-1_amd64.deb \
    && apt-get install -y --fix-missing

RUN wget "https://github.com/sleuthkit/autopsy/releases/download/autopsy-4.12.0/autopsy-4.12.0.zip" \
    && wget "https://github.com/sleuthkit/autopsy/releases/download/autopsy-4.12.0/autopsy-4.12.0.zip.asc" \
    && wget "https://github.com/sleuthkit/sleuthkit/releases/download/sleuthkit-4.6.7/sleuthkit-4.6.7.tar.gz" \
    && wget "https://github.com/sleuthkit/sleuthkit/releases/download/sleuthkit-4.6.7/sleuthkit-4.6.7.tar.gz.asc"

RUN gpg --recv-keys "0917A7EE58A9308B13D3963338AD602EC7454C8B" \
    && gpg --verify autopsy-4.12.0.zip.asc \
    && gpg --verify sleuthkit-4.6.7.tar.gz.asc \
    && tar -xf sleuthkit-4.6.7.tar.gz \
    && unzip autopsy-4.12.0.zip

ENV JAVA_HOME="/usr/lib/jvm/java-1.8.0-amazon-corretto/" 

WORKDIR /opt/sleuthkit-4.6.7 

RUN ./configure \
    && make \
    && make install

WORKDIR /opt/autopsy-4.12.0

RUN dos2unix ./unix_setup.sh \
    && chmod +x ./unix_setup.sh \
    && ./unix_setup.sh \
    && apt-get install -y --fix-broken \
    && chmod +x /opt/autopsy-4.12.0/bin/autopsy

ARG GID
ARG ID
ARG AUDIO

RUN groupadd -g "${GID}" autopsy \
    && groupmod -g "${AUDIO}" audio \
    && useradd -m -G audio,video,disk -u "${ID}" -g "${GID}" autopsy \
    && mkdir -p /home/autopsy/data \
    && chown -R autopsy:autopsy /home/autopsy \
    && rm -rf /var/lib/apt/lists/* \
    && rm /opt/sleuthkit-4.6.7.tar.gz \
    && rm /opt/autopsy-4.12.0.zip 


WORKDIR /home/autopsy/data

# Run as non privileged user
USER autopsy

ENTRYPOINT [ "/opt/autopsy-4.12.0/bin/autopsy" ]
