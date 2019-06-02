FROM debian:stretch AS bazel_builder

RUN apt-get update && apt-get install -y build-essential openjdk-8-jdk python zip unzip wget

ENV BAZEL_VERSION 0.26.0

RUN set -eux; \
    wget https://github.com/bazelbuild/bazel/releases/download/$BAZEL_VERSION/bazel-$BAZEL_VERSION-dist.zip; \
    mkdir /bazel; \
    unzip -q bazel-$BAZEL_VERSION-dist.zip -d /bazel; \
    rm -f bazel-$BAZEL_VERSION-dist.zip

WORKDIR /bazel
ARG remote_http_cache

RUN set -eux; \
    # Make sure we don't run out of memory compiling. Xmx defaults to ~25% available memory which might not be sufficient on low memory systems (like arm64). \
    # If the build container has less than ~4GB total memory, explicitly set Xmx to 1GB. \
    test $(grep MemTotal /proc/meminfo | awk '{print $2}') -gt 4000000 || export BAZEL_JAVAC_OPTS="-J-Xmx1g -J-Xms200m"; \
    # Verbose logging \
    EXTRA_BAZEL_ARGS="--host_javabase=@local_jdk//:jdk --noexperimental_ui --show_progress_rate_limit=-1 --show_timestamps --curses=no --jobs=$(nproc)"; \
    remote_http_cache=${remote_http_cache:-}; \
    if [ ! -z "$remote_http_cache" ]; then EXTRA_BAZEL_ARGS="$EXTRA_BAZEL_ARGS --experimental_remote_spawn_cache --remote_http_cache=$remote_http_cache"; fi; \
    export SOURCE_DATE_EPOCH=1500000000; \
    export EXTRA_BAZEL_ARGS; \
    bash -x compile.sh

FROM debian:stretch
RUN apt-get update && \
    apt-get install -y eatmydata && \
    eatmydata apt-get install -y pkg-config zip g++ zlib1g-dev unzip python openjdk-8-jdk patch && \
    apt-get autoremove --purge -y eatmydata && \
    rm -rf /var/lib/apt/lists /var/cache/apt/*
COPY --from=bazel_builder /bazel/output/bazel /usr/bin/bazel
RUN bazel version
