FROM alpine:3.7

ENV PUPPET_VERSION="5.5.1" FACTER_VERSION="2.5.1"

LABEL org.label-schema.vendor="Chris Boot" \
      org.label-schema.url="https://github.com/bootc/puppet-in-docker" \
      org.label-schema.name="Puppet Agent (Alpine)" \
      org.label-schema.license="Apache-2.0" \
      org.label-schema.version=$PUPPET_VERSION \
      org.label-schema.vcs-url="https://github.com/bootc/puppet-in-docker" \
      org.label-schema.vcs-ref="HEAD" \
      org.label-schema.build-date="1970-01-01T00:00:00Z" \
      org.label-schema.schema-version="1.0" \
      com.puppet.dockerfile="/Dockerfile"

RUN apk add --update \
      ca-certificates \
      pciutils \
      ruby \
      ruby-irb \
      ruby-rdoc \
      shadow && \
    rm -rf /var/cache/apk/* && \
    gem install puppet:"$PUPPET_VERSION" facter:"$FACTER_VERSION" && \
    /usr/bin/puppet module install puppetlabs-apk

# Workaround for https://tickets.puppetlabs.com/browse/FACT-1351
RUN rm /usr/lib/ruby/gems/2.4.0/gems/facter-"$FACTER_VERSION"/lib/facter/blockdevices.rb

ENTRYPOINT ["/usr/bin/puppet"]
CMD ["agent", "--verbose", "--onetime", "--no-daemonize", "--summarize" ]

COPY Dockerfile /
