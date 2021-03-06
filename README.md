# senzing-package

## Overview

The [senzing-package.py](senzing-package.py) python script
copies `data/` and `g2/` directories to new locations.
The `senzing/senzing-package` docker image is a wrapper for use in docker formations (e.g. docker-compose, kubernetes).
Baked into `senzing/senzing-package` is the installed `senzingdata-v1` and `senzingapi` packages.

The dockerized version, `store/senzing/senzing-package`, is at
[hub.docker.com/_/senzing-package](https://hub.docker.com/_/senzing-package).
For more information, scroll down to [Accept docker image](#accept-docker-image).

To see all of the subcommands, run:

```console
$ ./senzing-package.py --help
usage: senzing-package.py [-h]
                          {install,package-version,sleep,version,docker-acceptance-test}
                          ...

Install Senzing packages. For more information, see
https://github.com/Senzing/senzing-package

positional arguments:
  {install,package-version,sleep,version,docker-acceptance-test}
                        Subcommands (SENZING_SUBCOMMAND):
    install             Copy source data and g2 directories to a target.
    package-version     Display Senzing Engine version inside docker image.
    sleep               Do nothing but sleep. For Docker testing.
    version             Print version of program.
    docker-acceptance-test
                        For Docker acceptance testing.

optional arguments:
  -h, --help            show this help message and exit
```

To see the options for a subcommand, run commands like:

```console
./senzing-package.py install --help
```

### Related artifacts

1. [DockerHub](https://hub.docker.com/_/senzing-package)
1. [Helm Chart](https://github.com/Senzing/charts/tree/master/charts/senzing-package)

### Contents

1. [Expectations](#expectations)
    1. [Space](#space)
    1. [Time](#time)
    1. [Background knowledge](#background-knowledge)
1. [Demonstrate using Docker](#demonstrate-using-docker)
    1. [Accept docker image](#accept-docker-image)
    1. [Configuration](#configuration)
    1. [Docker user](#docker-user)
    1. [Run docker container](#run-docker-container)
1. [Develop](#develop)
    1. [Prerequisite software](#prerequisite-software)
    1. [Clone repository](#clone-repository)
    1. [Build docker image for development](#build-docker-image-for-development)
1. [Examples](#examples)
1. [Errors](#errors)
1. [References](#references)

### Legend

1. :thinking: - A "thinker" icon means that a little extra thinking may be required.
   Perhaps you'll need to make some choices.
   Perhaps it's an optional step.
1. :pencil2: - A "pencil" icon means that the instructions may need modification before performing.
1. :warning: - A "warning" icon means that something tricky is happening, so pay attention.

## Expectations

### Space

This repository and demonstration require 6 GB free disk space.

### Time

Budget 40 minutes to get the demonstration up-and-running, depending on CPU and network speeds.

### Background knowledge

This repository assumes a working knowledge of:

1. [Docker](https://github.com/Senzing/knowledge-base/blob/master/WHATIS/docker.md)

## Demonstrate using Docker

### Accept docker image

The use of the [store/senzing/senzing-package](https://hub.docker.com/_/senzing-package)
docker image requires acceptance of an End User License agreement (EULA).
To accept the license:

1. Visit [hub.docker.com/_/senzing-package](https://hub.docker.com/_/senzing-package).
1. Login to DockerHub.
1. On [hub.docker.com/_/senzing-package](https://hub.docker.com/_/senzing-package), click "Proceed to Checkout" button.
1. Check boxes for agreeing and acknowledging
1. Click "Get Content" button.
1. Verify image can be pulled from "Docker Store".  Example:

   ```console
   sudo docker pull store/senzing/senzing-package:0.0.1
   ```

### Configuration

Configuration values specified by environment variable or command line parameter.

- **[SENZING_ACCEPT_EULA](https://github.com/Senzing/knowledge-base/blob/master/lists/environment-variables.md#senzing_accept_eula)**
- **[SENZING_DATA_DIR](https://github.com/Senzing/knowledge-base/blob/master/lists/environment-variables.md#senzing_data_dir)**
- **[SENZING_DEBUG](https://github.com/Senzing/knowledge-base/blob/master/lists/environment-variables.md#senzing_debug)**
- **[SENZING_G2_DIR](https://github.com/Senzing/knowledge-base/blob/master/lists/environment-variables.md#senzing_g2_dir)**
- **[SENZING_LOG_LEVEL](https://github.com/Senzing/knowledge-base/blob/master/lists/environment-variables.md#senzing_log_level)**
- **[SENZING_NETWORK](https://github.com/Senzing/knowledge-base/blob/master/lists/environment-variables.md#senzing_network)**
- **[SENZING_RUNAS_USER](https://github.com/Senzing/knowledge-base/blob/master/lists/environment-variables.md#senzing_runas_user)**
- **[SENZING_SLEEP_TIME](https://github.com/Senzing/knowledge-base/blob/master/lists/environment-variables.md#senzing_sleep_time)**
- **[SENZING_SUBCOMMAND](https://github.com/Senzing/knowledge-base/blob/master/lists/environment-variables.md#senzing_subcommand)**

1. To determine which configuration parameters are use for each `<subcommand>`, run:

    ```console
    ./senzing-package.py <subcommand> --help
    ```

1. :pencil2: Set environment variables.
   Example:

    ```console
    export SENZING_SUBCOMMAND=install
    export SENZING_DATA_DIR=~/my-senzing/data
    export SENZING_G2_DIR=~/my-senzing/g2
    ```

1. Create directories.
   Example:

    ```console
    mkdir -p ${SENZING_DATA_DIR}
    mkdir -p ${SENZING_G2_DIR}
    ```

### Docker user

:thinking: **Optional:**  The docker container runs as "USER root" by default.
Use if a different userid (UID) is required.

1. :pencil2: Manually identify user.
   Example:

    ```console
    export SENZING_RUNAS_USER="1001"
    export SENZING_RUNAS_GROUP="1001"
    ```

   Another option, use current user.
   Example:

    ```console
    export SENZING_RUNAS_USER=$(id -u)
    export SENZING_RUNAS_GROUP=$(id -g)
    ```

1. Construct parameter for `docker run`.
   Example:

    ```console
    export SENZING_RUNAS_USER_PARAMETER="--user ${SENZING_RUNAS_USER}:${SENZING_RUNAS_GROUP}"
    ```

### Run docker container

1. Run docker container.
   Example:

    ```console
    sudo docker run \
      ${SENZING_RUNAS_USER_PARAMETER} \
      --env SENZING_SUBCOMMAND="${SENZING_SUBCOMMAND}" \
      --rm \
      --volume ${SENZING_DATA_DIR}:/opt/senzing/data \
      --volume ${SENZING_G2_DIR}:/opt/senzing/g2 \
      senzing/senzing-package
    ```

## Develop

### Prerequisite software

The following software programs need to be installed:

1. [git](https://github.com/Senzing/knowledge-base/blob/master/HOWTO/install-git.md)
1. [make](https://github.com/Senzing/knowledge-base/blob/master/HOWTO/install-make.md)
1. [docker](https://github.com/Senzing/knowledge-base/blob/master/HOWTO/install-docker.md)

### Clone repository

For more information on environment variables,
see [Environment Variables](https://github.com/Senzing/knowledge-base/blob/master/lists/environment-variables.md).

1. Set these environment variable values:

    ```console
    export GIT_ACCOUNT=senzing
    export GIT_REPOSITORY=senzing-package
    export GIT_ACCOUNT_DIR=~/${GIT_ACCOUNT}.git
    export GIT_REPOSITORY_DIR="${GIT_ACCOUNT_DIR}/${GIT_REPOSITORY}"
    ```

1. Follow steps in [clone-repository](https://github.com/Senzing/knowledge-base/blob/master/HOWTO/clone-repository.md) to install the Git repository.

### Build docker image for development

To use any of the following options to build the Docker image which embeds Senzing code,
you must agree to the Senzing End User License Agreement (EULA).

:warning: This step is intentionally tricky and not simply copy/paste.
This ensures that you make a conscious effort to accept the EULA.
See
[SENZING_ACCEPT_EULA](https://github.com/Senzing/knowledge-base/blob/master/lists/environment-variables.md#senzing_accept_eula)
for the correct value.
Replace the double-quote character in the example with the correct value.
The use of the double-quote character is intentional to prevent simple copy/paste.
Example:

```console
export SENZING_ACCEPT_EULA="
```

1. **Option #1:** Using `docker` command and GitHub.

    ```console
    sudo docker build \
      --env SENZING_ACCEPT_EULA=${SENZING_ACCEPT_EULA} \
      --tag senzing/senzing-package \
      https://github.com/senzing/senzing-package.git
    ```

1. **Option #2:** Using `docker` command and local repository.

    ```console
    cd ${GIT_REPOSITORY_DIR}
    sudo docker build \
      --build-arg SENZING_ACCEPT_EULA=${SENZING_ACCEPT_EULA} \
      --tag senzing/senzing-package \
      .
    ```

1. **Option #3:** Using `make` command.

    ```console
    cd ${GIT_REPOSITORY_DIR}
    sudo \
      SENZING_ACCEPT_EULA=${SENZING_ACCEPT_EULA}
      make docker-build
    ```

    Note: `sudo make docker-build-development-cache` can be used to create cached docker layers.

## Examples

## Errors

1. See [docs/errors.md](docs/errors.md).

## References
