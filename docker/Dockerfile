FROM ubuntu:24.04

RUN apt update && apt install -y capnproto pkg-config m4 libtool automake autoconf openjdk-17-jdk patchelf python3-venv unzip libssl-dev libgdbm-compat-dev liblzma-dev libbz2-dev cmake zlib1g-dev git python3-pip swig zip libffi-dev wget capnproto libcapnp-dev sudo gcc-arm-none-eabi libeigen3-dev
# /work is bind-mounted from the runner (different uid): let git read it inside the container
RUN git config --global --add safe.directory '*'

COPY capnproto-java /capnproto-java
WORKDIR /capnproto-java
RUN make && make install

RUN python3 -m venv ~/.venv
RUN ~/.venv/bin/pip install buildozer setuptools
# buildozer 1.6.0 pins cython<3, but the scons step cythonizes against NumPy 2 headers, which need Cython 3;
# these are the versions the upstream build used in March 2025.
RUN ~/.venv/bin/pip install 'cython==3.0.12' 'numpy==2.2.3'
RUN ~/.venv/bin/pip install scons pycryptodome pycapnp
RUN ~/.venv/bin/pip install pyyaml certifi hatanaka casadi future-fstrings
