ARG PLATFORM=$TARGETPLATFORM
FROM nvidia/cuda:12.2.0-devel-ubuntu22.04 as builder
ARG PLATFORM
RUN apt-get update && apt-get install -y --no-install-recommends \
        curl \
        git \
        ca-certificates \
        build-essential \
        libhdf5-dev \
        libssl-dev \
        libzstd-dev \
        libsz2 \
        libomp-dev \
        cmake \
        autoconf \
        automake \
        python3 \
        libnuma-dev \
        liblapack-dev       
RUN if [ $PLATFORM = linux/arm64 ]; then ln -s /usr/lib/aarch64-linux-gnu/lapack/liblapack.a /usr/local/cuda/lib64/liblapack_static.a ; ln -s /usr/lib/aarch64-linux-gnu /usr/lib64 ; else ln -s /usr/lib/x86_64-linux-gnu/libcufft_static_nocallback.a  /usr/lib64/libcufft_static_nocallback.a ; fi
RUN mkdir -p /tmp/libnumafiles
RUN if [ $PLATFORM = linux/arm64 ]; then cp /usr/lib/aarch64-linux-gnu/libnuma* /tmp/libnumafiles ; else cp /usr/lib/x86_64-linux-gnu/libnuma* /tmp/libnumafiles ; fi

RUN git clone https://github.com/nanoporetech/dorado.git
WORKDIR /dorado
#version 0.4.0 
#RUN git checkout 7fefd5d949d32decc3b347778afa80c90b9c6417
#version 0.5.0 
#RUN git checkout 0d932c0539a8d81fedb5c98931475e69dd97df93
#version 0.5.1
#RUN git checkout a7fb3e3d4afa7a11cb52422e7eecb1a2cdb7860f
#version 0.5.3
RUN git checkout d9af343c0097e0e60503231e036d69e6eda2f19a
RUN cmake -S . -B cmake-build
RUN cmake --build cmake-build --config Release
RUN cp cmake-build/bin/dorado /usr/local/bin/dorado
RUN cp cmake-build/lib/* /usr/local/lib/
COPY arm64/libcupti* /armLibs/
RUN if [ $PLATFORM = linux/arm64 ]; then cp /armLibs/libcupti* /usr/local/lib/ && \
       cp /usr/lib/aarch64-linux-gnu/libgfortran.so.5 /usr/local/lib/ && \
       cp /usr/lib/aarch64-linux-gnu/libnuma* /usr/local/lib/ && \
       cp /usr/lib/aarch64-linux-gnu/libgomp* /usr/local/lib/; else \
        cp /usr/lib/x86_64-linux-gnu/libsz* /usr/local/lib/ && \
        cp /usr/lib/x86_64-linux-gnu/libaec* /usr/local/lib/ && \
       cp /usr/lib/x86_64-linux-gnu/libiomp5.so /usr/local/lib/; fi 

FROM nvidia/cuda:12.2.0-runtime-ubuntu22.04
COPY --from=builder /usr/local/bin /usr/local/bin
RUN if [ -d /usr/lib/aarch64-linux-gnu ] ; then ln -s /usr/lib/aarch64-linux-gnu /usr/lib64 ; fi
ENV LD_LIBRARY_PATH="/usr/local/lib:$LD_LIBRARY_PATH"
COPY --from=builder /usr/local/lib /usr/local/lib
COPY --from=builder /dorado/cmake-build/libdorado_torch_lib.so /usr/local/lib/libdorado_torch_lib.so
COPY --from=builder /dorado/dorado/3rdparty/hdf5*/hdf5*/lib/* /usr/local/lib/

