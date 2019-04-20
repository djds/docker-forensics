# Run FTK Imager in a container

FROM ubuntu:cosmic
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

ARG GID
ARG ID
    
RUN groupadd -g "${GID}" ftk \
    && useradd -m -G audio,video,plugdev -u "${ID}" -g "${GID}" ftk \
    && mkdir -p /home/ftk/data \
    && chown -R ftk:ftk /home/ftk \
    && rm -rf /var/lib/apt/lists/* \
    && rm -rf /setup

WORKDIR /home/ftk/data

# Run as non privileged user
USER ftk

ENTRYPOINT ["/usr/local/bin/ftkimager"]
