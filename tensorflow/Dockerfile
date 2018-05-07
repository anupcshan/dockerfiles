FROM anupcshan/bazel:0.13.0 as tflow_build

# linux-headers - pull in linux/futex.h
# patch         - replace busybox version with one that supports -d
RUN apk add --no-cache build-base wget unzip automake autoconf libtool curl zlib-dev \
    libjpeg-turbo-dev libexecinfo-dev patch linux-headers
ENV TENSORFLOW_VERSION 1.8.0
RUN wget https://github.com/tensorflow/tensorflow/archive/v$TENSORFLOW_VERSION.zip && \
    unzip -q v$TENSORFLOW_VERSION.zip -d tensorflow && rm -f v$TENSORFLOW_VERSION.zip
WORKDIR tensorflow/tensorflow-$TENSORFLOW_VERSION

# To work around musl issues
RUN sed -i '452 s/^\(.*\)$/linkopts = ["-lexecinfo"],\1/' tensorflow/BUILD

ARG remote_http_cache
RUN set -eux; \
    # Verbose logging. Most of the later compile/link steps use a lot of memory (~2GB each). \
    # Set ram_utilization_factor to 1/4 of default to reduce chance of OOM. \
    BAZEL_ARGS="-c opt --noexperimental_ui --show_progress_rate_limit=-1 --show_timestamps --curses=no --ram_utilization_factor=16 --spawn_strategy=sandboxed --genrule_strategy=sandboxed"; \
    remote_http_cache=${remote_http_cache:-}; \
    if [ ! -z "$remote_http_cache" ]; then BAZEL_ARGS="$BAZEL_ARGS --experimental_remote_spawn_cache --remote_http_cache=$remote_http_cache"; fi; \
    export BAZEL_ARGS; \
    bazel build $BAZEL_ARGS //tensorflow:libtensorflow.so

RUN mkdir /out
# TODO(anupcshan): Ideally, we'll script the list of libraries to be copied over. For now, this is fine.
RUN cp bazel-bin/tensorflow/libtensorflow.so \
       bazel-bin/tensorflow/libtensorflow_framework.so \
       /usr/lib/libstdc++.so.6 \
       /usr/lib/libgcc_s.so.1 \
       /usr/lib/libexecinfo.so.1 \
       /out

# RUN curl -o /tmp/inception.zip https://storage.googleapis.com/download.tensorflow.org/models/inception5h.zip \
#     && unzip /tmp/inception.zip -d /tmp/inception \
#     && rm /tmp/inception.zip

FROM alpine:3.7
COPY --from=tflow_build /out /usr/local/lib
