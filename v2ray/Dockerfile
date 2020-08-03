FROM caoyufei/debian

ADD config.json /etc/v2ray.json

RUN apt-get update -y \
    && apt-get --no-install-recommends install -y wget unzip \
    && wget --no-check-certificate -O /tmp/v2ray/v2ray.zip https://github.com/v2ray/v2ray-core/releases/download/v4.26.0/v2ray-linux-64.zip \
    && unzip /tmp/v2ray/v2ray.zip -d /tmp/v2ray \
    && install -m 755 /tmp/v2ray/v2ray /usr/local/bin/v2ray \
    && install -m 755 /tmp/v2ray/v2ctl /usr/local/bin/v2ctl \
    && apt purge --auto-remove wget unzip

CMD ["v2ray -config /etc/v2ray.json"]