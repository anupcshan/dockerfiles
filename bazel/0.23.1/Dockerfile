FROM openjdk:8-jdk-alpine AS bazel_builder

RUN apk add --no-cache build-base python zip wget bash \
    linux-headers # To pull in linux/futex.h

ENV BAZEL_VERSION 0.23.1

RUN set -eux; \
    wget https://github.com/bazelbuild/bazel/releases/download/$BAZEL_VERSION/bazel-$BAZEL_VERSION-dist.zip; \
    mkdir /bazel; \
    unzip -q bazel-$BAZEL_VERSION-dist.zip -d /bazel; \
    rm -f bazel-$BAZEL_VERSION-dist.zip
ENV JAVA_VERSION 1.8

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
    export EXTRA_BAZEL_ARGS; \
    bash -x compile.sh

FROM openjdk:8-jdk-alpine
RUN apk add --no-cache build-base python zip bash
COPY --from=bazel_builder /bazel/output/bazel /usr/bin/bazel
