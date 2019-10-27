# Run regripper in a container

FROM ubuntu:eoan
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

WORKDIR /opt/RegRipper2.8

# Change #! and plugin locations for Linux
RUN sed -i 's/#! c:\\perl\\bin\\perl.exe/#!\/usr\/bin\/perl/g' rip.pl \
    && sed -i 's/^#push(@INC,$str);/push(@INC,$str);/g' rip.pl \
    && sed -i '/($^O eq "MSWin32") ? ($plugindir = $str."plugins\/")/d' rip.pl \
    && sed -i '/: ($plugindir = File::Spec->catfile("plugins"));/d' rip.pl \
    && sed -i 's/^#my $plugindir = $str."plugins\/";/my $plugindir = $str."\/opt\/RegRipper2.8\/plugins\/";/g' rip.pl \
    && sed -i 's/^#my $plugindir = File::Spec->catfile("plugins");/my $plugindir = File::Spec->catfile("\/opt\/RegRipper2.8\/plugins");/g' rip.pl \
    && sed -i 's/^#print "Plugins Dir = ".$plugindir."\\n";/print "Plugins Dir = ".$plugindir."\\n";/g' rip.pl \
    && chmod +x rip.pl 

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

ENTRYPOINT ["/opt/RegRipper2.8/rip.pl"]
