# docker image with snmp and basic networking tools

FROM ehlers/ipterm-base

RUN set -ex \
#
# install snmp tools
#
    && sed -i -e '/^deb / s/$/ non-free/' /etc/apt/sources.list \
    && apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get -y --no-install-recommends install \
        snmp snmp-mibs-downloader libqtgui4 lxterminal jwm menu leafpad \
    && rm -rf /var/lib/apt/lists/* \
    && sed -i -e 's/^mibs/#mibs/' /etc/snmp/snmp.conf \
    && curl -s -S -L -o qtmib.deb https://sourceforge.net/projects/qtmib/files/qtmib.deb/qtmib_1.1_1_amd64.deb/download \
    && dpkg -i qtmib.deb \
    && rm qtmib.deb \
#
# customize jwm menu
#
    && /bin/echo -e '\
?package(qtmib):\\\n\
 needs="x11"\\\n\
 section="Applications"\\\n\
 title="SNMP MIB browser"\\\n\
 command="qtmib"' \
        > /etc/menu/qtmib \
    && echo "postrun="\""sed -i '/^    </ d' debian-menu"\" >> /etc/menu-methods/jwm \
    && sed -i 's/\(Desktops width\)="[0-9]*"/\1="2"/' /etc/jwm/system.jwmrc \
    && sed -i 's/xterm/x-terminal-emulator/g' /etc/jwm/system.jwmrc \
    && update-menus \
#
# set lxterminal defaults
#
    && mkdir -p /root/.config/lxterminal \
    && /bin/echo -e '\
[general]\n\
scrollback=1000\n\
fgcolor=#ffffff' \
        > /root/.config/lxterminal/lxterminal.conf \
#
# startup script
#
    && /bin/echo -e '\
\x23!/bin/sh\n\
[ $$ -eq 1 ] && exec dumb-init -- "$0" "$@"\n\
\n\
\x23 workaround: sometimes the first start of an X application fails\n\
lxterminal -e true 2> /dev/null\n\
\n\
cd\n\
export SHELL=/bin/bash\n\
qtmib &\n\
jwm' \
        > /etc/init.sh && chmod +x /etc/init.sh

VOLUME [ "/root" ]
CMD [ "/etc/init.sh" ]
