FROM quay.io/bootc/puppet-agent-ubuntu:5.5.2

ENV R10K_VERSION="2.6.3"

LABEL org.label-schema.vendor="Chris Boot" \
      org.label-schema.url="https://github.com/bootc/puppet-in-docker" \
      org.label-schema.name="r10k" \
      org.label-schema.license="Apache-2.0" \
      org.label-schema.version=$R10K_VERSION \
      org.label-schema.vcs-url="https://github.com/bootc/puppet-in-docker" \
      org.label-schema.vcs-ref="HEAD" \
      org.label-schema.build-date="1970-01-01T00:00:00Z" \
      org.label-schema.schema-version="1.0" \
      com.puppet.dockerfile="/Dockerfile"

RUN apt-get update && \
    apt-get install --no-install-recommends -y git && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

RUN /opt/puppetlabs/puppet/bin/gem install r10k:"$R10K_VERSION"

ENTRYPOINT ["/opt/puppetlabs/puppet/bin/r10k"]
CMD ["help"]

COPY Dockerfile /
