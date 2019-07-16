# Apache Zookeeper AMI

Packer template to create an AMI with Apache Zookeeper installed.

All required initialization scripts, packages and configurations are included in this script.
This can be used to instantiate a zookeeper server in both standalone or cluster mode.

### Requirements

Install Packer and AWS Command Line Interface tools on your local machine. I am using Mac
so most of the commands and instruction here should work in a Mac.

Building the base image requires the id of the latest Debian AMI files
for the region where you wish to build the AMI.

#### Debian AMI

This AMI will be based on an official Debian AMI. The latest version of that
AMI will be used.

### Running packer

Below options are required to build the image. Check the json file
for other options that can be supplied.

```
Usage:
  packer build \
    -var 'aws_access_key=AWS_ACCESS_KEY' \
    -var 'aws_secret_key=<AWS_SECRET_KEY>' \
    -var 'aws_region=<AWS_REGION>' \
    -var 'zookeeper_version=<ZOOKEEPER_VERSION>' \
    [-var 'option=value'] \
    zookeeper.json
```

#### Script Options

- `aws_access_key` - *[required]* The AWS access key.
- `aws_ami_name` - The AMI name (default value: "zookeeper").
- `aws_ami_name_prefix` - Prefix for the AMI name (default value: "").
- `aws_instance_type` - The instance type to use for the build (default value: "t2.micro").
- `aws_region` - *[required]* The regions were the build will be performed.
- `aws_secret_key` - *[required]* The AWS secret key.
- `system_locale` - Locale for the system (default value: "en_US").
- `zookeeper_version` - *[required]* Zookeeper version.


#### Configuration Script

The script can and should be used to set some of the Zookeeper options as well
as setting the Zookeeper service to start at boot. The same script can be called
by ansible to start and configure the zookeeper

```
Usage: zookeeper_config [options]
```

##### Options

* `-D` - Disables the Zookeeper service from start at boot time.
* `-E` - Enables the Zookeeper service to start at boot time.
* `-i <ID>` - Sets the Zookeeper broker ID (default value is '1').
* `-m <MEMORY>` - Sets Zookeeper maximum heap size. Values should be provided following the same Java heap nomenclature.
* `-n <ID:ADDRESS>` - The ID and Address of a cluster node (e.g.: '1:127.0.0.1'). Should be used to set all the Zookeeper nodes. Several Zookeeper nodes can be set by either using extra `-n` options or if separated with a comma on the same `-n` option.
* `-S` - Starts the Zookeeper service after performing the required configurations (if any given).
* `-W <SECONDS>` - Waits the specified amount of seconds before starting the Zookeeper service (default value is '0').

To cconfigure a multi-node zookeeper

```
zookeeper_config -E -i 1 -n 1:zookeeper01.mydomain.tld -n 2:zookeeper02.mydomain.tld,3:zookeeper03.mydomain.tld -S
```



The script should be run as sudo user or root.

## Required ports

| Service      | Port      | Protocol |
|--------------|:---------:|:--------:|
| SSH          | 22        |    TCP   |
| Zookeeper    | 2181      |    TCP   |
| Zookeeper    | 2888:3888 |    TCP   |
| JMX          | 7199      |    TCP   |

