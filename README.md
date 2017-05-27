# Elasticsearch AMI

AMI that should be used to create virtual machines with Elasticsearch
installed.

## Synopsis

This script will create an AMI with Elasticsearch installed and with all of
the required initialization scripts.

The AMI resulting from this script should be the one used to instantiate a
Elasticsearch server (standalone or cluster).

## Getting Started

There are a couple of things needed for the script to work.

### Prerequisites

Packer and AWS Command Line Interface tools need to be installed on your local
computer.
To build a base image you have to know the id of the latest Debian AMI files
for the region where you wish to build the AMI.

#### Packer

Packer installation instructions can be found
[here](https://www.packer.io/docs/installation.html).

#### AWS Command Line Interface

AWS Command Line Interface installation instructions can be found [here](http://docs.aws.amazon.com/cli/latest/userguide/installing.html)

#### Debian AMI's

This AMI will be based on an official Debian AMI. The latest version of that
AMI will be used.

A list of all the Debian AMI id's can be found at the Debian official page:
[Debian official Amazon EC2 Images](https://wiki.debian.org/Cloud/AmazonEC2Image/)

### Usage

In order to create the AMI using this packer template you need to provide a
few options.

```
Usage:
  packer build \
    -var 'aws_access_key=AWS_ACCESS_KEY' \
    -var 'aws_secret_key=<AWS_SECRET_KEY>' \
    -var 'aws_region=<AWS_REGION>' \
    -var 'elasticsearch_version=<ELASTICSEARCH_VERSION>' \
    [-var 'option=value'] \
    elasticsearch.json
```

#### Script Options

- `aws_access_key` - *[required]* The AWS access key.
- `aws_ami_name` - The AMI name (default value: "elasticsearch").
- `aws_ami_name_prefix` - Prefix for the AMI name (default value: "").
- `aws_instance_type` - The instance type to use for the build (default value: "t2.micro").
- `aws_region` - *[required]* The regions were the build will be performed.
- `aws_secret_key` - *[required]* The AWS secret key.
- `java_build_number` - Java build number (default value: "11").
- `java_major_version` - Java major version (default value: "8").
- `java_token` - Java link token (default version: "d54c1d3a095b4ff2b6607d096fa80163").
- `java_update_version` - Java update version (default value: "131").
- `elasticsearch_version` - *[required]* Elasticsearch version.
- `system_locale` - Locale for the system (default value: "en_US").

### Instantiate a Cluster

In order to end up with a functional Elasticsearch Cluster some configurations
have to be performed after instantiating the servers.

To help perform those configurations a small script is included on the AWS
image. The script is called **elasticsearch_config**.

#### Configuration Script

The script can and should be used to set some of the Elasticsearch options as
well as setting the Elasticsearch service to start at boot.

```
Usage: elasticsearch_config [options]
```

##### Options

* `-a <ADDRESS>` - Sets the Elasticsearch node publish address (default value is 'localhost').
* `-c <NAME>` - Sets the Elasticsearch cluster name (default value is 'es-cluster').
* `-D` - Disables the Elasticsearch service from start at boot time.
* `-E` - Enables the Elasticsearch service to start at boot time.
* `-m <MEMORY>` - Sets Elasticsearch heap size. Values should be provided following the same Java heap nomenclature.
* `-n <NAME>` - Sets the Elasticsearch node name (default value is the \`hostname\` value).
* `-p <ENDPOINT>` - Sets a Elasticsearch peer endpoint for discovery purposes (default value is 'localhost').
* `-S` - Starts the Elasticsearch service after performing the required configurations (if any given).
* `-W <SECONDS>` - Waits the specified amount of seconds before starting the Elasticsearch service (default value is '0').

#### Configuring a Elasticsearch node

To prepare an instance to act as a Elasticsearch node the following steps
need to be performed.

Run the configuration tool (*elasticsearch_config*) to configure the instance.

```
elasticsearch_config -a es-node.mydomain.tld -E -S
```

After this steps a Elasticsearch node (for a single node cluster) should be
running and configured to start on server boot.

For a cluster with more than one Elasticsearch node other options have to be
configured on each instance using the same configuration tool
(*elasticsearch_config*).

```
elasticsearch_config -a es-node01.mydomain.tld -c My-Cluster -E -p es-node01.mydomain.tld -p es-node02.mydomain.tld,es-node03.mydomain.tld -S
```

After this steps, the first node of the Elasticsearch cluster (for a three node
cluster) should be running and configured to start on server boot.

More options can be used on the instance configuration, see the
[Configuration Script](#configuration-script) section for more details

## Services

This AMI will have the SSH service running as well as the Elasticsearch
services. The following ports will have to be configured on Security Groups.

| Service                                 | Port   | Protocol |
|-----------------------------------------|:------:|:--------:|
| SSH                                     | 22     |    TCP   |
| Elasticsearch RESTful API               | 9200   |    TCP   |
| Elasticsearch Native Transport Protocol | 9300   |    TCP   |

## Contributing

1. Fork it!
2. Create your feature branch: `git checkout -b my-new-feature`
3. Commit your changes: `git commit -am 'Add some feature'`
4. Push to the branch: `git push origin my-new-feature`
5. Submit a pull request

## Versioning

This project uses [SemVer](http://semver.org/) for versioning. For the versions
available, see the [tags on this repository](https://github.com/fscm/packer-aws-elasticsearch/tags).

## Authors

* **Frederico Martins** - [fscm](https://github.com/fscm)

See also the list of [contributors](https://github.com/fscm/packer-aws-elasticsearch/contributors)
who participated in this project.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE)
file for details
