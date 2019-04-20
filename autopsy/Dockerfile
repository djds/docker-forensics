# Run autopsy in a container

FROM ubuntu:cosmic
LABEL maintainer "djds djds@ccs.neu.edu"

ENV DEBIAN_FRONTEND="noninteractive"

RUN apt-get update && apt-get dist-upgrade -y \
    && apt-get install -y \
    wget \
    ca-certificates \
    libgl1-mesa-dri \
    libgl1-mesa-glx \
    testdisk \
    libswt-gtk-4-java \
    libsolr-java \
    libtika-java \
    sqlite3 \
    gcc \
    g++ \
    make \
    libtool \
    openjfx \
    postgresql \
    libafflib-dev \
    libewf-dev \
    libtsk-dev \
    libvhdi-dev \
    libvmdk-dev \
    libboost-dev \
    libsqlite3-dev \
    zlib1g-dev \
    ant \
    zip

# PPA discontinued due to Oracle licensing change:
# https://launchpad.net/~webupd8team/+archive/ubuntu/java
#RUN apt install -y gpg software-properties-common \
#    && add-apt-repository -y ppa:webupd8team/java \
#    && echo debconf shared/accepted-oracle-license-v1-1 select true | debconf-set-selections \
#    && echo debconf shared/accepted-oracle-license-v1-1 seen true | debconf-set-selections \
#    && apt-get install -y oracle-java8-installer

RUN apt-get install -y \
    gpg \
    software-properties-common \
    openjdk-8-jdk

WORKDIR /opt

RUN wget "https://github.com/sleuthkit/autopsy/releases/download/autopsy-4.10.0/autopsy-4.10.0.zip" \
    && wget "https://github.com/sleuthkit/autopsy/releases/download/autopsy-4.10.0/autopsy-4.10.0.zip.asc" \
    && wget "https://github.com/sleuthkit/sleuthkit/releases/download/sleuthkit-4.6.5/sleuthkit-4.6.5.tar.gz" \
    && wget "https://github.com/sleuthkit/sleuthkit/releases/download/sleuthkit-4.6.5/sleuthkit-4.6.5.tar.gz.asc"

RUN gpg --recv-keys "0917A7EE58A9308B13D3963338AD602EC7454C8B" \
    && gpg --verify autopsy-4.10.0.zip.asc \
    && gpg --verify sleuthkit-4.6.5.tar.gz.asc \
    && tar -xf sleuthkit-4.6.5.tar.gz \
    && unzip autopsy-4.10.0.zip

#COPY autopsy-4.10.0 /opt/autopsy-4.10.0
#COPY sleuthkit-4.6.5 /opt/sleuthkit-4.6.5

WORKDIR /opt/sleuthkit-4.6.5 

#RUN export JAVA_HOME="/usr/lib/jvm/java-8-oracle" \
RUN export JAVA_HOME="/usr/lib/jvm/java-8-openjdk-amd64" \
    && ./configure \
    && make \
    && make install

WORKDIR /opt/autopsy-4.10.0

#    && export JAVA_HOME="/usr/lib/jvm/java-8-oracle" \
RUN chmod +x ./unix_setup.sh \
    && export JAVA_HOME="/usr/lib/jvm/java-8-openjdk-amd64" \
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
