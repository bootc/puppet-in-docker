# Puppet-in-Docker

A series of Dockerfiles, and the associated build toolchain, for building Docker images containing Puppet and related software.

## Experimental

This approach to packaging Puppet software is experimental. The resulting images are not a supported way of running Puppet or Puppet Enterprise and are likely to change quickly based on feedback from users. Please do try them out and let us know what you think.

## Description

You can copy the individual Dockerfiles in this repo and use them locally for your own purposes. They were created following current Docker best practices, and can be good starting points for custom images.

If you do find yourself customizing these images, please open issues describing why and whether your use is something that could be handled in these images.

You can find published versions of these images on [quay.io](https://quay.io/user/bootc):

* [![Docker Repository on Quay](https://quay.io/repository/bootc/facter/status "Docker Repository on Quay")](https://quay.io/repository/bootc/facter): `quay.io/bootc/facter`
* [![Docker Repository on Quay](https://quay.io/repository/bootc/puppet-agent/status "Docker Repository on Quay")](https://quay.io/repository/bootc/puppet-agent): `quay.io/bootc/puppet-agent`
* [![Docker Repository on Quay](https://quay.io/repository/bootc/puppet-agent-alpine/status "Docker Repository on Quay")](https://quay.io/repository/bootc/puppet-agent-alpine): `quay.io/bootc/puppet-agent-alpine`
* [![Docker Repository on Quay](https://quay.io/repository/bootc/puppet-agent-centos/status "Docker Repository on Quay")](https://quay.io/repository/bootc/puppet-agent-centos): `quay.io/bootc/puppet-agent-centos`
* [![Docker Repository on Quay](https://quay.io/repository/bootc/puppet-agent-debian/status "Docker Repository on Quay")](https://quay.io/repository/bootc/puppet-agent-debian): `quay.io/bootc/puppet-agent-debian`
* [![Docker Repository on Quay](https://quay.io/repository/bootc/puppet-agent-ubuntu/status "Docker Repository on Quay")](https://quay.io/repository/bootc/puppet-agent-ubuntu): `quay.io/bootc/puppet-agent-ubuntu`
* [![Docker Repository on Quay](https://quay.io/repository/bootc/puppet-inventory/status "Docker Repository on Quay")](https://quay.io/repository/bootc/puppet-inventory): `quay.io/bootc/puppet-inventory`
* [![Docker Repository on Quay](https://quay.io/repository/bootc/puppetboard/status "Docker Repository on Quay")](https://quay.io/repository/bootc/puppetboard): `quay.io/bootc/puppetboard`
* [![Docker Repository on Quay](https://quay.io/repository/bootc/puppetdb/status "Docker Repository on Quay")](https://quay.io/repository/bootc/puppetdb): `quay.io/bootc/puppetdb`
* [![Docker Repository on Quay](https://quay.io/repository/bootc/puppetdb-postgres/status "Docker Repository on Quay")](https://quay.io/repository/bootc/puppetdb-postgres): `quay.io/bootc/puppetdb-postgres`
* [![Docker Repository on Quay](https://quay.io/repository/bootc/puppetexplorer/status "Docker Repository on Quay")](https://quay.io/repository/bootc/puppetexplorer): `quay.io/bootc/puppetexplorer`
* [![Docker Repository on Quay](https://quay.io/repository/bootc/puppetserver/status "Docker Repository on Quay")](https://quay.io/repository/bootc/puppetserver): `quay.io/bootc/puppetserver`
* [![Docker Repository on Quay](https://quay.io/repository/bootc/puppetserver-standalone/status "Docker Repository on Quay")](https://quay.io/repository/bootc/puppetserver-standalone): `quay.io/bootc/puppetserver-standalone`
* [![Docker Repository on Quay](https://quay.io/repository/bootc/r10k/status "Docker Repository on Quay")](https://quay.io/repository/bootc/r10k): `quay.io/bootc/r10k`

## Image usage

You can use the images for standing up various Puppet applications on Docker. For a complete set of examples see the [Puppet in Docker examples repository](https://github.com/puppetlabs/puppet-in-docker-examples).

As an example, first we'll create a Docker network. For the purposes of this demonstration, we're using the network and service discovery features added in Docker 1.11.

```
docker network create puppet
```

Then we can run a copy of Puppet Server. In the below code, `standalone` means that this image does not automatically connect to PuppetDB. That's fine for this simple demo, but in other cases you might prefer the `puppet/puppetserver` image.

```
docker run --net puppet --name puppet --hostname puppet puppet/puppetserver-standalone
```

This boots the container and, becuase we're running in the foreground, it prints lots of output to the console. After this is running, we can run a Puppet agent in another container.

```
docker run --net puppet puppet/puppet-agent-ubuntu
```

This connects to the Puppet Server, applies the resulting catalog, prints a summary, and then exits. That's not very useful apart from development purposes, but this is just a basic demonstration. See the above examples repository for fuller examples, or
consider:

### Running periodically

The above example runs with the `onetime` flag, which means that Puppet exits after the first run. The container can be run with any arbitrary Puppet commands, such as:

```
docker run --net puppet puppet/puppet-agent-ubuntu agent --verbose --no-daemonize --summarize
```

This container won't exit, and instead applies Puppet every 30 minutes based on the latest content from the Puppet Server.

### Puppet resource

You can also use other Puppet commands, such as `resource`. For instance, the following command lists all of the packages installed on the image.

```
docker run puppet/puppet-agent-ubuntu resource package --param provider
```

To find out about the packages installed on the host, rather than in the container, mount in various folder from the host like so.

```
docker run --privileged -v /tmp:/tmp --net host -v /etc:/etc -v /var:/var -v /usr:/usr -v lib64:/lib64 puppet/puppet-agent-ubuntu resource package
```

The same approach works with the Facter image as well.

```
docker run --privileged -v /tmp:/tmp --net host -v /etc:/etc -v /var:/var -v /usr:/usr -v lib64:/lib64 puppet/facter os
```

### API

The resulting images expose a label-based API for gathering information about the image or for use in further automation. For example:

```
$ docker inspect -f "{{json .Config.Labels }}" puppet/puppet-agent-ubuntu | jq
{
  "com.puppet.build-time": "2016-05-09T12:14:22Z",
  "com.puppet.dockerfile": "/Dockerfile",
  "com.puppet.git.repo": "https://github.com/puppetlabs/dockerfiles",
  "com.puppet.git.sha": "33f00f35d7275a6ca2a538650b9530734aa66929",
  "com.puppet.version": "1.4.1"
}
```

Please suggest other standard fields for inclusion in the API. Over time, a formal specification may be created, along with further tooling, but this is an experimental feature.

## Toolchain

The repository contains a range of tools for managing the set of Dockerfiles and the resulting images. For instance, you can list the images available.

Note that using the toolchain requires a Ruby environment and Bundler. You'll also need a local Docker installation.

```
$ bundle install
...
$ bundle exec rake list
NAME                    | VERSION | FROM                                 | SHA                                      | BUILD                | MAINTAINER
------------------------|---------|--------------------------------------|------------------------------------------|----------------------|-------------------------------------
facter                  | 1.5.0   | puppet/puppet-agent-ubuntu:1.5.0     | 97475979ffe252d33a9df67524b5aa313022cb05 | 2016-05-20T10:01:19Z | Gareth Rushgrove "gareth@puppet.com"
puppet-agent-alpine     | 4.4.2   | alpine:3.3                           | 97475979ffe252d33a9df67524b5aa313022cb05 | 2016-05-20T10:01:19Z | Gareth Rushgrove "gareth@puppet.com"
puppet-agent-ubuntu     | 1.5.0   | ubuntu:16.04                         | 97475979ffe252d33a9df67524b5aa313022cb05 | 2016-05-20T10:01:19Z | Gareth Rushgrove "gareth@puppet.com"
puppetboard             | 0.1.3   | alpine:3.3                           | 97475979ffe252d33a9df67524b5aa313022cb05 | 2016-05-20T10:01:19Z | Gareth Rushgrove "gareth@puppet.com"
puppetdb                | 4.1.0   | ubuntu:16.04                         | 97475979ffe252d33a9df67524b5aa313022cb05 | 2016-05-20T10:01:19Z | Gareth Rushgrove "gareth@puppet.com"
puppetdb-postgres       | 0.1.0   | postgres:9.5.2                       | 97475979ffe252d33a9df67524b5aa313022cb05 | 2016-05-20T10:01:19Z | Gareth Rushgrove "gareth@puppet.com"
puppetexplorer          | 2.0.0   | alpine:3.3                           | 97475979ffe252d33a9df67524b5aa313022cb05 | 2016-05-20T10:01:19Z | Gareth Rushgrove "gareth@puppet.com"
puppetserver            | 2.3.2   | puppet/puppetserver-standalone:2.4.0 | 161bca4fed59997fd19581df38678caeefe813bc | 2016-05-16T08:05:27Z | Gareth Rushgrove "gareth@puppet.com"
puppetserver-standalone | 2.4.0   | ubuntu:16.04                         | 97475979ffe252d33a9df67524b5aa313022cb05 | 2016-05-20T10:01:19Z | Gareth Rushgrove "gareth@puppet.com"
```

### Building images

The following command builds the `puppet-agent-alpine` image. You can find the relevant Dockerfile in the directory of the same name.

```
$ bundle exec rake puppet-agent-alpine:build
```

This is a simple interface to run `docker build` and creates both a latest and a versioned Docker image in your local repository.

### Testing images

The repository provides two types of tests:

1. Validation of the Dockerfile using [Hadolint](https://github.com/lukasmartinelli/hadolint)
2. Acceptance tests of the image using [ServerSpec](http://serverspec.org)

These can be run individually or together. To run them together, use the following command.

```
$ bundle exec rake puppet-agent-alpine:test
```

### Additional commands

The included toolchain allows you to run lint checks, bump version information, run acceptance tests, build, and then publish the resulting Docker images. You can access the toolchain with `rake`.

```
$ bundle exec rake -T
rake all                          # Run all for all images in repository in parallel
rake build                        # Run build for all images in repository in parallel
rake lint                         # Run lint for all images in repository in parallel
rake publish                      # Run publish for all images in repository in parallel
rake puppet-agent-alpine:build    # Build docker image
rake puppet-agent-alpine:lint     # Run Hadolint against the Dockerfile
rake puppet-agent-alpine:publish  # Publish docker image
rake puppet-agent-alpine:rev      # Update Dockerfile label content for new version
rake puppet-agent-alpine:spec     # Run RSpec code examples
rake puppet-agent-ubuntu:build    # Build docker image
rake puppet-agent-ubuntu:lint     # Run Hadolint against the Dockerfile
rake puppet-agent-ubuntu:publish  # Publish docker image
rake puppet-agent-ubuntu:rev      # Update Dockerfile label content for new version
rake puppet-agent-ubuntu:spec     # Run RSpec code examples
...
rake test                         # Run test for all images in repository in parallel
rake rev                          # Run rev for all images in repository in parallel
rake rubocop                      # Run RuboCop
rake rubocop:auto_correct         # Auto-correct RuboCop offenses
rake spec                         # Run spec for all images in repository in parallel
rake test                         # Run test for all images in repository in parallel
```

## Adding additional images

To add additional images to the repository, create a folder in the root of the repository and include in that folder a standard Dockerfile. The above commands should auto-discover the new image. We recommend that you also include a spec folder containing tests verifying the image's behavior. See examples in the other folders for help getting started. Please suggest new images via pull request.

## Maintainers

This repository is maintained by: Chris Boot <bootc@bootc.net> based on work by Gareth Rushgrove <gareth@puppet.com>.

Individual images may have separate maintainers as mentioned in the relevant Dockerfiles.
