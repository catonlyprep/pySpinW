# Download and install Matlab Compiler Runtime v9.6 (2019a)
#
# This docker file will configure an environment into which the Matlab compiler
# runtime will be installed and in which stand-alone matlab routines (such as
# those created with Matlab's deploytool) can be executed.
#
# See http://www.mathworks.com/products/compiler/mcr/ for more info.

FROM pangeo/pangeo-notebook:latest

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get -q update \
    && apt-get install -q -y --no-install-recommends \
         xorg \
         unzip \
         wget \
         curl \
         bzip2 \
         ca-certificates \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*
    
# Install the MCR dependencies and some things we'll need and download the MCR
# from Mathworks -silently install it
RUN mkdir /mcr-install \
    && mkdir /opt/mcr \
    && cd /mcr-install \
    && wget -q http://ssd.mathworks.com/supportfiles/downloads/R2019a/Release/3/deployment_files/installer/complete/glnxa64/MATLAB_Runtime_R2019a_Update_3_glnxa64.zip \
    && unzip -q MATLAB_Runtime_R2019a_Update_3_glnxa64.zip \
    && rm -f MATLAB_Runtime_R2019a_Update_3_glnxa64.zip \
    && ./install -destinationFolder /opt/mcr -agreeToLicense yes -mode silent \
    && cd / \
    && rm -rf mcr-install

# Move libexpat becuase it is too old
RUN mv /opt/mcr/v96/bin/glnxa64/libexpat.so.1 /opt/mcr/v96/bin/glnxa64/libexpat.so.1.NOFIND

# Setup the graphics
# Replace 1000 with your user / group id
RUN export uid=1000 gid=1000 \
    && mkdir -p /home/developer \
    && echo "developer:x:${uid}:${gid}:Developer,,,:/home/developer:/bin/bash" >> /etc/passwd \
    && echo "developer:x:${uid}:" >> /etc/group \
    && chown ${uid}:${gid} -R /home/developer

# Running as root is bad juju
USER developer
ENV HOME /home/developer
WORKDIR /home/developer

RUN mkdir src \
    && git clone https://github.com/SpinW/pySpinWexamples.git \
    && cd src \
    && git clone https://github.com/SpinW/pySpinW.git \
    && cd pySpinW \
    && pip install -e ./

# Configure environment variables for MCR
ENV LD_LIBRARY_PATH /opt/mcr/v96/runtime/glnxa64:/opt/mcr/v96/bin/glnxa64:/opt/mcr/v96/sys/os/glnxa64

WORKDIR /home/developer/pySpinWexamples