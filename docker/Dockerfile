FROM ubuntu:noble
ARG DEBIAN_FRONTEND=noninteractive

# from https://fmm-wiki.github.io/docs/installation/ubuntu.html
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    ca-certificates \
    build-essential \
    # gpg-agent \
    # software-properties-common \
    # && add-apt-repository -y ppa:ubuntugis/ppa \
    && apt-get update \
    && apt-get install -y --no-install-recommends \
    # git \
    libboost-dev \
    libboost-serialization-dev \
    gdal-bin \
    libgdal-dev \
    make \
    cmake \
    libbz2-dev \
    libexpat1-dev \
    swig \
    python-is-python3 \
    python3-dev \
    && rm -rf /var/lib/apt/lists/*

# RUN git clone https://github.com/cyang-kth/fmm.git \
#     && sed -i 's/OGRSpatialReference \*ogrsr =/const OGRSpatialReference *ogrsr =/' fmm/src/network/network.cpp
#
RUN mkdir -p /fmm
COPY . /fmm
WORKDIR /fmm
RUN rm -rf build
RUN mkdir -p build \
    && cd build \
    && cmake .. \
    && make -j"$(nproc)" \
    && make install

CMD ["bash"]
