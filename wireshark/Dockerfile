# Run wireshark in a container

FROM fedora:rawhide
LABEL maintainer "djds djds@ccs.neu.edu"

RUN dnf upgrade -y \
    && dnf install -y \
    wireshark \
    google-noto-sans-fonts \
    google-noto-serif-fonts \
    mesa-dri-drivers mesa-libGL
    
ARG GID
ARG ID
ARG AUDIO
    
RUN groupdel dialout \
    && groupmod -g "${AUDIO}" audio \
    && useradd -m -g wireshark -G audio,video -u "${ID}" wireshark \
    && mkdir -p /home/wireshark/data \
    && chown -R wireshark:wireshark /home/wireshark \
    && rm -rf /var/cache/dnf/*

WORKDIR /home/wireshark/data

# Run as non privileged user
USER wireshark

ENTRYPOINT ["/usr/bin/wireshark"]
