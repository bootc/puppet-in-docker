FROM postgres:9.6.8

LABEL org.label-schema.vendor="Chris Boot" \
      org.label-schema.url="https://github.com/bootc/puppet-in-docker" \
      org.label-schema.name="PostgreSQL instance for PuppetDB" \
      org.label-schema.license="Apache-2.0" \
      org.label-schema.version="9.6.8" \
      org.label-schema.vcs-url="https://github.com/bootc/puppet-in-docker" \
      org.label-schema.vcs-ref="3146a521a42471713cf50426097399cc8a27d0a7" \
      org.label-schema.build-date="2018-03-16T23:16:43Z" \
      org.label-schema.schema-version="1.0" \
      com.puppet.dockerfile="/Dockerfile"

COPY docker-entrypoint-initdb.d /docker-entrypoint-initdb.d

COPY Dockerfile /
