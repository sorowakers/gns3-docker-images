# docker image with basic networking tools and web browser

FROM ehlers/ipterm-base

RUN set -ex \
#
# install web tools
#
    && apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get -y --no-install-recommends install \
        firefox-esr lxterminal jwm menu leafpad \
    && rm -rf /var/lib/apt/lists/* \
#
# firefox start script
#
    && /bin/echo -e '\
\x23!/bin/sh\n\
\n\
\x23 use home page on first start\n\
[ -e "$HOME/.mozilla" ] || start_url="about:home"\n\
\n\
\x23 start firefox\n\
firefox $start_url' \
        > /usr/local/bin/start-firefox && chmod +x /usr/local/bin/start-firefox \
#
# customize jwm menu
#
    && /bin/echo -e '\
?package(firefox-esr):\\\n\
 needs="x11"\\\n\
 section="Applications"\\\n\
 title="Mozilla Firefox"\\\n\
 command="start-firefox"' \
        > /etc/menu/firefox \
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
start-firefox &\n\
jwm' \
        > /etc/init.sh && chmod +x /etc/init.sh

VOLUME [ "/root" ]
CMD [ "/etc/init.sh" ]
