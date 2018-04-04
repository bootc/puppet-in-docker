FROM alpine:3.7

ENV PUPPET_BOARD_VERSION="0.3.0" \
    GUNICORN_VERSION="19.7.1" \
    PUPPETBOARD_PORT="8000" \
    PUPPETBOARD_SETTINGS="docker_settings.py"

LABEL org.label-schema.vendor="Chris Boot" \
      org.label-schema.url="https://github.com/bootc/puppet-in-docker" \
      org.label-schema.name="Puppetboard" \
      org.label-schema.license="Apache-2.0" \
      org.label-schema.version=$PUPPET_BOARD_VERSION \
      org.label-schema.vcs-url="https://github.com/bootc/puppet-in-docker" \
      org.label-schema.vcs-ref="96c93246299b2dc6f192e335eb5eba65defc8bb7" \
      org.label-schema.build-date="2018-04-04T08:38:31Z" \
      org.label-schema.schema-version="1.0" \
      com.puppet.dockerfile="/Dockerfile"

# pip symlink is required for serverspec tests
RUN apk add --no-cache --update curl python3 && \
    ln -s pip3 /usr/bin/pip && \
    rm -rf /var/cache/apk/*

RUN pip install puppetboard=="$PUPPET_BOARD_VERSION" gunicorn=="$GUNICORN_VERSION"

EXPOSE 8000

WORKDIR /var/www/puppetboard

CMD /usr/bin/gunicorn -b 0.0.0.0:${PUPPETBOARD_PORT} --access-logfile=/dev/stdout puppetboard.app:app

# Health check
HEALTHCHECK --interval=10s --timeout=10s --retries=90 CMD \
  curl --fail -X GET localhost:${PUPPETBOARD_PORT} \
  |  grep -q 'Live from PuppetDB' \
  || exit 1

COPY Dockerfile /
