FROM debian:buster AS ceph_builder

RUN apt-get update && apt-get install -y build-essential eatmydata git libffi-dev reprepro wget

# 14.2.1 + arm32 fixes
RUN set -eux; mkdir /ceph; cd /ceph; git init; \
    git remote add origin https://github.com/anupcshan/ceph; \
    eatmydata git fetch origin; \
    eatmydata git checkout ea262cbc87d80c597dd7eacd6e945f550291bea2; \
    eatmydata git submodule update --init --recursive

WORKDIR /ceph

RUN eatmydata apt-get update && eatmydata ./install-deps.sh
RUN NPROC=$(nproc) DEB_CFLAGS_APPEND=-g0 DEB_CXXFLAGS_APPEND=-g0 eatmydata ./make-debs.sh

RUN mkdir -p /tmp/debs && find /tmp/release/Debian/WORKDIR/*.deb | grep -v dbgsym | grep -v ceph-test | grep -v -- -dev_ | xargs -I{} cp -v {} /tmp/debs

FROM debian:buster
COPY --from=ceph_builder /tmp/debs/*.deb /tmp/
RUN apt-get update && apt install --no-install-recommends -y /tmp/*.deb && rm -rf /tmp/*.deb && rm -rf /var/lib/apt/lists/* /var/cache/apt/archives/*
