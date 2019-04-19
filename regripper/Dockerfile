# Run regripper in a container

FROM ubuntu:cosmic
LABEL maintainer "djds djds@ccs.neu.edu"

ENV DEBIAN_FRONTEND="noninteractive"

WORKDIR /opt

RUN apt-get update && apt-get dist-upgrade -y \
    && apt-get install -y \
    git \
    libparse-win32registry-perl

RUN git clone "https://github.com/keydet89/RegRipper2.8.git" \
    && cd RegRipper2.8 \
    && ln -s /opt/RegRipper2.8/plugins /etc/perl/plugins \
    && cp /usr/share/perl5/Parse/Win32Registry/WinNT/Key.pm \
        /usr/share/perl5/Parse/Win32Registry/WinNT/Key_old.pm \
    && cp Key.pm /usr/share/perl5/Parse/Win32Registry/WinNT/Key.pm

ARG GID
ARG ID
    
RUN groupadd -g "${GID}" regripper \
    && useradd -m -u "${ID}" -g "${GID}" regripper \
    && mkdir -p /home/regripper/data \
    && chown -R regripper:regripper /home/regripper \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /home/regripper/data

# Run as non privileged user
USER regripper

ENTRYPOINT ["/usr/bin/perl", "/opt/RegRipper2.8/rip.pl"]
