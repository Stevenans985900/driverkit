# driverkit

Status: **Under development**

A command line tool that can be used to build the Falco kernel module and eBPF probe.


## Usage

When you meet `kernelversion` that refers to the version you get executing `uname -v`:

For example, below, the version is the `59` after the hash

```bash
uname -v
#59-Ubuntu SMP Wed Dec 4 10:02:00 UTC 2019
```

When you meet `kernelrelease`, that refers to the kernel release you get executing `uname -r`:

```
uname -r
4.15.0-1057-aws
```

### Against a Kubernetes cluster

```bash
driverkit kubernetes --output-module /tmp/falco.ko --kernelversion=81 --kernelrelease=4.15.0-72-generic --driverversion=dev --target=ubuntu-generic
```

### Against a Docker daemon

```bash
driverkit docker --output-module /tmp/falco.ko --kernelversion=81 --kernelrelease=4.15.0-72-generic --driverversion=dev --target=ubuntu-generic
```


### Build using a configuration file

Create a file named `ubuntu-aws.yaml` containing the following content:

```yaml
kernelrelease: 4.15.0-1057-aws
kernelversion: 59
target: ubuntu-aws
output:
  module: /tmp/falco-ubuntu-aws.ko
  probe: /tmp/falco-ubuntu-aws.o
driverversion: dev
```

Now run driverkit using the configuration file:

```bash
driverkit docker -c ubuntu-aws.yaml
```

## Supported targets

### ubuntu-generic
Example configuration file to build both the Kernel module and eBPF probe for Ubuntu generic.

```yaml
kernelrelease: 4.15.0-72-generic
kernelversion: 81
target: ubuntu-generic
output:
  module: /tmp/falco-ubuntu-generic.ko
  probe: /tmp/falco-ubuntu-generic.o
driverversion: dev
```

### ubuntu-aws

Example configuration file to build both the Kernel module and eBPF probe for Ubuntu AWS.

```yaml
kernelrelease: 4.15.0-1057-aws
kernelversion: 59
target: ubuntu-aws
output:
  module: /tmp/falco-ubuntu-aws.ko
  probe: /tmp/falco-ubuntu-aws.o
driverversion: dev
```

### centos 6

```yaml
kernelrelease: 2.6.32-754.14.2.el6.x86_64
kernelversion: 1
target: centos
output:
  module: /tmp/falco-centos6.ko
driverversion: dev
```

### centos 7

```yaml
kernelrelease: 3.10.0-957.12.2.el7.x86_64
kernelversion: 1
target: centos
output:
  module: /tmp/falco-centos7.ko
driverversion: dev
```

### centos 8

```yaml
kernelrelease: 4.18.0-147.5.1.el8_1.x86_64
kernelversion: 1
target: centos
output:
  module: /tmp/falco-centos8.ko
driverversion: dev
```

### amazonlinux

```yaml
kernelrelease: 4.14.26-46.32.amzn1.x86_64
target: amazonlinux
output:
    module: /tmp/falco_amazonlinux_4.14.26-46.32.amzn1.x86_64.ko
driverversion: be1ea2d9482d0e6e2cb14a0fd7e08cbecf517f94
```

### amazonlinux 2

```yaml
kernelrelease: 4.14.171-136.231.amzn2.x86_64
target: amazonlinux2
output:
    module: /tmp/falco_amazonlinux2_4.14.171-136.231.amzn2.x86_64.ko
    probe: /tmp/falco_amazonlinux2_4.14.171-136.231.amzn2.x86_64.o
driverversion: be1ea2d9482d0e6e2cb14a0fd7e08cbecf517f94
```

### debian

Example configuration file to build both the Kernel module and eBPF probe for Debian.

```yaml
kernelrelease: 4.19.0-6-amd64
kernelversion: 1
output:
  module: /tmp/falco-debian.ko
  probe: /tmp/falco-debian.o
target: debian
driverversion: dev
```

### vanilla

In case of vanilla, you also need to pass the kernel config data in base64 format.

In most systems you can get `kernelconfigdata`  by reading `/proc/config.gz`.

```yaml
kernelrelease: 5.5.2
kernelversion: 1
target: vanilla
output:
  module: /tmp/falco-vanilla.ko
  probe: /tmp/falco-vanilla.o
driverversion: 0de226085cc4603c45ebb6883ca4cacae0bd25b2
```

Now you can add the `kernelconfigdata` to the configuration file, to do so:

```bash
zcat /proc/config.gz| base64 -w0 | awk '{print "kernelconfigdata: " $1;}' >> /tmp/vanilla.yaml
```

The command above assumes that you saved the configuration file at `/tmp/vanilla.yaml`

#### Note

Usually, building for a `vanilla` target requires more time.

So, we suggest to increase the `driverkit` timeout (defaults to `60` seconds):

```bash
driverkit docker -c /tmp/vanilla.yaml --timeout=300
```

## Goals

- [x] Have a package that can build the Falco kernel module in k8s
- [x] Have a package that can build the Falco kernel module in docker
- [x] Have a package that can build the Falco eBPF probe in k8s
- [x] Have a package that can build the Falco eBPF probe in docker
- [x] Support the top distributions in our [Survey](http://bit.ly/driverkit-survey-vote) and the Vanilla Kernel
  - [x] Ubuntu (`ubuntu-aws`, `ubuntu-generic`)
  - [x] CentOS 8
  - [x] CentOS 7
  - [x] CentOS 6
  - [x] AmazonLinux (`amazonlinux`, `amazonlinux2`)
  - [x] Debian
  - [x] Vanilla kernel (`vanilla`)

## Survey

We are conducting a [survey](http://bit.ly/driverkit-survey-vote) to know what is the most interesting set of Operating Systems we must support first in driverkit.

You can find the results of the survey [here](http://bit.ly/driverkit-survey-results)
