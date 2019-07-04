FROM debian:buster AS ceph_builder

RUN apt-get update && apt-get install -y build-essential git wget libffi-dev

# 14.2.1 + arm32 fixes
RUN set -eux; mkdir /ceph; cd /ceph; git init; \
    git remote add origin https://github.com/anupcshan/ceph; \
    git fetch origin; \
    git checkout ea262cbc87d80c597dd7eacd6e945f550291bea2; \
    git submodule update --init --recursive

WORKDIR /ceph

RUN apt-get update && ./install-deps.sh
RUN ./do_cmake.sh -DCMAKE_BUILD_TYPE=Release -DBOOST_J=$(nproc) -DCMAKE_INSTALL_PREFIX=/usr
RUN cd build; make -j$(nproc) V=1
