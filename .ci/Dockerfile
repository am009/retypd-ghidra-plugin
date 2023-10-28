FROM ubuntu:20.04 as base

ARG UBUNTU_MIRROR=mirrors.ustc.edu.cn

# install required build/test tools
RUN if [[ ! -z "$UBUNTU_MIRROR" ]] ; then sed -i "s/archive.ubuntu.com/$UBUNTU_MIRROR/g" /etc/apt/sources.list \
 && sed -i "s/security.ubuntu.com/$UBUNTU_MIRROR/g" /etc/apt/sources.list ; fi ; \
 apt-get -y update \
    && DEBIAN_FRONTEND=noninteractive apt-get -y install \
        python3-pip \
        build-essential \
        cmake \
        curl \
        openjdk-17-jdk \
        openjdk-17-jre \
        gcc-arm-linux-gnueabi \
        g++-arm-linux-gnueabi \
        gcc-arm-linux-gnueabihf \
        g++-arm-linux-gnueabihf \
        git \
        libc6-dev-armel-cross \
        libcairo2 \
        python3-venv \
        python3-pip \
        python3-setuptools \
        python3-wheel \
        unzip \
        wget \
        zip \
    && apt-get clean \
    && python3 -m pip install --upgrade \
        pip \
        setuptools

COPY . /ghidra_retypd
WORKDIR /ghidra_retypd

ENV PATH="/ghidra_retypd/util/:/ghidra_retypd/third-party/gradle/bin:${PATH}"
# get ghidra
RUN .ci/get_ghidra.sh

# Ghidra environment variables
ENV GHIDRA_HOME=/ghidra_retypd/third-party/ghidra/
ENV GHIDRA_INSTALL_DIR=/ghidra_retypd/third-party/ghidra/
ENV CLASSPATH=.:$GHIDRA_HOME/support/ghidra.jar

# Install the extension
RUN make && make install

FROM base as interactive

RUN python3 -m venv venv
RUN . ./venv/bin/activate

# Install retypd into the virtual environment
RUN git clone https://github.com/GrammaTech/retypd.git /retypd/
WORKDIR /retypd
RUN python3 -m pip install .

# Install ghidra_retypd into the virutal environment
WORKDIR /ghidra_retypd
RUN python3 -m pip install .
