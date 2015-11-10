
Developing a Docker Container
=============================
${image?fileName=docker_logo.png}

Most of the tools that are developed for the Challenge will depend on some external software (R, Python, C++, etc.).
Since these tools are being run in the Cloud and not on your local machine we need some way of downloading these dependencies and setting up the environment so that your tool can run
properly. This is where [Docker](https://www.docker.com/) comes in. Docker allows you to specify all the dependencies and setup required for your tool in a Dockerfile and then it will build the
environment for you, making it much easier to use your tool in the Cloud and also to share your tool with your teammates and the broader community.

Below we have provided a number of resources on Docker, including a more in depth introduction to what Docker is, tutorials on how to build a Dockerfile and an example Dockerfile. If you want more resources check out the [Docker forum](https://forums.docker.com/) and the [Docker documentation](https://docs.docker.com/). For definitions of the Docker terminology look in [2.8 Glossary](https://www.synapse.org/#!Synapse:syn2786217/wiki/232923).

Introduction To Docker
----------------------
${youtube?videoId=YiZkHUbE6N0}


Building a Dockerfile
---------------------
### Videos
A Dockerfile specifies all the information

${youtube?videoId=gG_x28rDxus}

${youtube?videoId=L6bjTlVdc6U}

### Interactive Tutorial
Besides these tutorials you can also find an interactive tutorial for Docker here: [https://www.docker.com/tryit/](https://www.docker.com/tryit/)

### Example Docker Environments

Docker provides a large collection of pre-defined Docker environments that can be found in the [Docker Registry](https://registry.hub.docker.com/). These include full installations of [R](https://registry.hub.docker.com/_/r-base/) and [Python's SciKit-Learn](https://registry.hub.docker.com/u/buildo/docker-python2.7-scikit-learn/). If what you need is missing, you can also join Docker and add your own projects to the registry.

If the environment you need is not available on the registry you can provide a build description as part of your Galaxy tool. For this to work there needs to be a "Dockerfile" which describes all dependencies and environmental setup required to run the tool.  This includes any and all software packages, symlinks, and environmental variables that are needed by the tool itself or needed by its dependencies.  

One of the easiest ways to create your Dockerfile is to download one of the given examples in the [Docker Registry](https://registry.hub.docker.com/) and then make adjustments as needed, similar to the 'fork and commit' strategy in github.

### Written Tutorial

A written step-by-step guide to creating docker files and running them is here:
[https://www.digitalocean.com/community/tutorials/docker-explained-using-dockerfiles-to-automate-building-of-images](https://www.digitalocean.com/community/tutorials/docker-explained-using-dockerfiles-to-automate-building-of-images)

### Reference Manual

The Dockerfile reference manual can be found here: [https://docs.docker.com/reference/builder/](https://docs.docker.com/reference/builder/)

### Example Dockerfile for a Galaxy Tool

Below is the Dockerfile used to setup the PhyloWGS tool which is one of the two example tools we provide for you in Galaxy.

```
FROM ubuntu

RUN apt-get update && apt-get install -y gfortran build-essential make gcc build-essential python python-dev wget libgsl0ldbl gsl-bin libgsl0-dev python-pip git

# blas
ADD blas.sh /tmp/blas.sh
RUN chmod 755 /tmp/blas.sh
RUN /tmp/blas.sh
ENV BLAS /usr/local/lib/libfblas.a

# lapack
ADD lapack.sh /tmp/lapack.sh
RUN chmod 755 /tmp/lapack.sh
RUN /tmp/lapack.sh
ENV LAPACK /usr/local/lib/liblapack.a

RUN pip install numpy scipy
RUN easy_install -U ete2

WORKDIR /opt

RUN git clone https://github.com/morrislab/phylowgs.git

RUN cd phylowgs && g++ -o mh.o  mh.cpp  util.cpp `gsl-config --cflags --libs`
```

The first part of the docker file uses 'apt-get' commands (the Debian package management system) to install zip, wget, samtools and pip. Pip is then used to install numpy and scipy. Finally the source code for the tool is 'git cloned' and build (in /opt, which was defined as the 'WORKDIR').
