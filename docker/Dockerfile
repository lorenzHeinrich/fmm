FROM ubuntu:noble AS builder
ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    ca-certificates \
    build-essential \
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

WORKDIR /fmm
COPY . /fmm

RUN mkdir -p build \
    && cd build \
    && cmake .. \
    && make -j"$(nproc)" \
    && make install

RUN py_site="$(python3 -c 'import sysconfig; print(sysconfig.get_path("purelib"))')" \
    && mkdir -p /opt/fmm/bin /opt/fmm/lib /opt/fmm/python \
    && cp -a /usr/local/bin/fmm /usr/local/bin/ubodt_gen /usr/local/bin/stmatch /usr/local/bin/h3mm /opt/fmm/bin/ \
    && cp -a /usr/local/lib/libFMMLIB.so* /opt/fmm/lib/ \
    && cp -a "$py_site/fmm.py" "$py_site"/_fmm*.so /opt/fmm/python/

FROM ubuntu:noble AS runtime
ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    ca-certificates \
    python3 \
    libgdal34 \
    libboost-serialization1.83.0 \
    libgomp1 \
    libbz2-1.0 \
    libexpat1 \
    libstdc++6 \
    && rm -rf /var/lib/apt/lists/*

COPY --from=builder /opt/fmm/bin/ /usr/local/bin/
COPY --from=builder /opt/fmm/lib/ /usr/local/lib/
COPY --from=builder /opt/fmm/python/ /opt/fmm/python/

RUN py_site="$(python3 -c 'import sysconfig; print(sysconfig.get_path("purelib"))')" \
    && mkdir -p "$py_site" \
    && cp -a /opt/fmm/python/. "$py_site"/ \
    && echo "/usr/local/lib" > /etc/ld.so.conf.d/fmm.conf \
    && ldconfig

CMD ["fmm"]
