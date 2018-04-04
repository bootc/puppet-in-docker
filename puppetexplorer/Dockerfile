FROM alpine:3.7

ENV PUPPET_EXPLORER_VERSION="2.0.0"

LABEL org.label-schema.vendor="Chris Boot" \
      org.label-schema.url="https://github.com/bootc/puppet-in-docker" \
      org.label-schema.name="Puppet Explorer" \
      org.label-schema.license="Apache-2.0" \
      org.label-schema.version=$PUPPET_EXPLORER_VERSION \
      org.label-schema.vcs-url="https://github.com/bootc/puppet-in-docker" \
      org.label-schema.vcs-ref="3146a521a42471713cf50426097399cc8a27d0a7" \
      org.label-schema.build-date="2018-03-16T23:16:43Z" \
      org.label-schema.schema-version="1.0" \
      com.puppet.dockerfile="/Dockerfile"

RUN apk add --no-cache --update ca-certificates curl wget && \
    update-ca-certificates && \
    rm -rf /var/cache/apk/*

RUN curl --silent --show-error --fail --location \
      --header "Accept: application/tar+gzip, application/x-gzip, application/octet-stream" -o - \
      "https://caddyserver.com/download/linux/amd64?plugins=http.prometheus,http.realip" \
    | tar --no-same-owner -C /usr/bin/ -xz caddy \
 && chmod 0755 /usr/bin/caddy \
 && /usr/bin/caddy -version

RUN wget https://github.com/spotify/puppetexplorer/releases/download/"$PUPPET_EXPLORER_VERSION"/puppetexplorer-"$PUPPET_EXPLORER_VERSION".tar.gz -O - | tar -xz && \
    ln -s puppetexplorer-"$PUPPET_EXPLORER_VERSION" /puppetexplorer

# This patch fixes https://github.com/spotify/puppetexplorer/issues/56 until a new release of puppetexplorer is made
RUN sed -i -e 's/puppetlabs\.puppetdb\.query\.population/puppetlabs\.puppetdb\.population/g' -e 's/type=default,//g' /puppetexplorer/app.js

COPY Caddyfile /etc/caddy/Caddyfile
COPY config.js /puppetexplorer

EXPOSE 80

WORKDIR /etc/caddy

CMD ["/usr/bin/caddy"]

COPY Dockerfile /
