# PowerPlantTwin

Github repo for Docker container for OpenPLC v3 Linux Runtime:

https://github.com/jpaffrath/docker-openplc

- the Dockerfile in this repo has an issue:
- the Dockerfile is based on debian:stretch, and stretch (Debian 9) has been EOL (end of life) for years. Its package repositories were moved to the Debian archive, so apt-get update fails with 404 Not Found.

Recommended fix: Upgrade base image
- switch to newer Debian or Ubuntu base image while installing required Python 2 dependencies

Since I am on MacBook Air (arm64), some images may try to pull amd64 binaries
- Force platform emulation with:
  - `docker buildx build --platform linux/amd64 -t openplc-docker . `
  - `docker run --platform linux/amd64 ...`  

## Content of updated Dockerfile to be used based on chatgpt:
```
FROM debian:buster

USER root
WORKDIR /root/

# Install dependencies
RUN apt-get update && \
    apt-get -y install \
    git python2 python2-dev python-is-python2 \
    python-pip-whl \
    autoconf bison build-essential pkg-config flex automake \
    libtool make sqlite3 cmake sudo && \
    python2 -m pip install --upgrade pip setuptools && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# Install required Python packages for OpenPLC
RUN python2 -m pip install flask flask-login pyserial pymodbus

# Create OpenPLC user with sudo
RUN useradd --create-home --shell /bin/bash openplc && \
    adduser openplc sudo && \
    echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

USER openplc
WORKDIR /home/openplc

# Clone OpenPLC repo
RUN git clone https://github.com/thiagoralves/OpenPLC_v3.git
WORKDIR /home/openplc/OpenPLC_v3

# Run custom installer
RUN sudo ./install.sh custom

# Create scripts folder for user PLC programs
RUN mkdir /home/openplc/scripts

# Copy entrypoint script
COPY run.sh /home/openplc
RUN sudo chmod +x /home/openplc/run.sh

# Expose OpenPLC ports
EXPOSE 502
EXPOSE 8080
EXPOSE 20000
EXPOSE 43628

CMD ["/home/openplc/run.sh"]

```
