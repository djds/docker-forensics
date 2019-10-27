# Run FTK Imager in a container

FROM ubuntu:eoan
LABEL maintainer "djds djds@ccs.neu.edu"

ENV DEBIAN_FRONTEND="noninteractive"

RUN mkdir /setup
WORKDIR /setup

RUN apt-get update && apt-get dist-upgrade -y \
    && apt-get install -y \
    wget

RUN wget "https://ad-zip.s3.amazonaws.com/ftkimager.3.1.1_ubuntu64.tar.gz" \
    && tar -xf ftkimager.3.1.1_ubuntu64.tar.gz \
    && chmod +x ftkimager \
    && mv ftkimager /usr/local/bin/

RUN mkdir /data \
    && rm -rf /var/lib/apt/lists/* \
    && rm -rf /setup

WORKDIR /data

ENTRYPOINT ["/usr/local/bin/ftkimager"]
