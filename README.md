# PowerPlantTwin

Github repo for Docker container for OpenPLC v3 Linux Runtime:

https://github.com/jpaffrath/docker-openplc

- the Dockerfile in this repo has an issue:
- the Dockerfile is based on debian:stretch, and stretch (Debian 9) has been EOL (end of life) for years. Its package repositories were moved to the Debian archive, so apt-get update fails with 404 Not Found.

Recommended fix: Upgrade base image
- switch to newer Debian or Ubuntu base image while installing required Python 2 dependencies


## Content of updated Dockerfile to be used based on chatgpt:
`
...
`
