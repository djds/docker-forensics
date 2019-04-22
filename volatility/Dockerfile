# Run volatility in a container

FROM ubuntu:disco
LABEL maintainer "djds djds@ccs.neu.edu"

ENV DEBIAN_FRONTEND="noninteractive"

RUN mkdir /setup
WORKDIR /setup

RUN apt-get update && apt-get dist-upgrade -y \
    && apt-get install -y \
    git \
    ipython \
    python \
    python-distorm3 \
    python-openpyxl \
    python-pil \
    python-pip \
    python-pycryptodome \
    python-setuptools \
    python-tz \
    python-ujson \
    python-yara

RUN pip install pycrypto \
    && git clone "https://github.com/volatilityfoundation/volatility" \
    && cd volatility \
    && python setup.py build \
    && python setup.py install

ARG GID
ARG ID
    
RUN groupadd -g "${GID}" volatility \
    && useradd -m -G audio,video,plugdev -u "${ID}" -g "${GID}" volatility \
    && mkdir -p /home/volatility/data \
    && chown -R volatility:volatility /home/volatility \
    && rm -rf /var/lib/apt/lists/* \
    && rm -rf /setup

WORKDIR /home/volatility/data

# Run as non privileged user
USER volatility

ENTRYPOINT ["/usr/local/bin/vol.py"]
