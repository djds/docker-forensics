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
    zip \
    java-common

# PPA discontinued due to Oracle licensing change:
# https://launchpad.net/~webupd8team/+archive/ubuntu/java
#RUN apt install -y gpg software-properties-common \
#    && add-apt-repository -y ppa:webupd8team/java \
#    && echo debconf shared/accepted-oracle-license-v1-1 select true | debconf-set-selections \
#    && echo debconf shared/accepted-oracle-license-v1-1 seen true | debconf-set-selections \
#    && apt-get install -y oracle-java8-installer

RUN apt-get install -y \
    gpg \
    software-properties-common

WORKDIR /opt

#RUN wget -O "jre-8u211-linux-x64.tar.gz" \
#    "https://javadl.oracle.com/webapps/download/AutoDL?BundleId=238719_478a62b7d4e34b78b671c754eaaf38ab" \
#    && tar -xf "jre-8u211-linux-x64.tar.gz"

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

#RUN wget "https://github.com/sleuthkit/autopsy/releases/download/autopsy-4.10.0/autopsy-4.10.0.zip" \
#    && wget "https://github.com/sleuthkit/autopsy/releases/download/autopsy-4.10.0/autopsy-4.10.0.zip.asc" \
#    && wget "https://github.com/sleuthkit/sleuthkit/releases/download/sleuthkit-4.6.5/sleuthkit-java_4.6.5-1_amd64.deb"

#RUN gpg --recv-keys "0917A7EE58A9308B13D3963338AD602EC7454C8B" \
#    && gpg --verify autopsy-4.10.0.zip.asc \
#    && unzip autopsy-4.10.0.zip \
#    && apt-get install -y ./sleuthkit-java_4.6.5-1_amd64.deb \
#    && apt-get install -y --fix-missing


#RUN wget "https://github.com/sleuthkit/sleuthkit/releases/download/sleuthkit-4.6.5/sleuthkit-4.6.5.tar.gz" \
#    && wget "https://github.com/sleuthkit/sleuthkit/releases/download/sleuthkit-4.6.5/sleuthkit-4.6.5.tar.gz.asc" \
#    && wget "https://github.com/sleuthkit/autopsy/archive/autopsy-4.10.0.tar.gz"

#RUN gpg --recv-keys "0917A7EE58A9308B13D3963338AD602EC7454C8B" \
#    && gpg --verify sleuthkit-4.6.5.tar.gz.asc \
#    && tar -xf sleuthkit-4.6.5.tar.gz \
#    && tar -xf autopsy-4.10.0.tar.gz

#COPY autopsy-4.10.0 /opt/autopsy-4.10.0
#COPY sleuthkit-4.6.5 /opt/sleuthkit-4.6.5

WORKDIR /opt/sleuthkit-4.6.5 

#RUN export JAVA_HOME="/usr/lib/jvm/java-8-oracle" \
#RUN export JAVA_HOME="/usr/lib/jvm/java-8-openjdk-amd64" \
#RUN export JAVA_HOME="/opt/jre1.8.0_211" \
RUN export JAVA_HOME="/usr/lib/jvm/java-1.8.0-amazon-corretto/" \
    && ./configure \
    && make \
    && make install

#WORKDIR /opt/autopsy-autopsy-4.10.0
#RUN export JAVA_HOME="/opt/jre1.8.0_211" \
#    && ant

WORKDIR /opt/autopsy-4.10.0

#    && export JAVA_HOME="/usr/lib/jvm/java-8-oracle" \
#    && export JAVA_HOME="/usr/lib/jvm/java-8-openjdk-amd64" \
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
