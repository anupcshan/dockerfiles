FROM golang:1.10-alpine as go_builder
RUN apk add --no-cache git
RUN go get -u -v -ldflags '-w -s' \
        github.com/golang/protobuf/protoc-gen-go \
        github.com/gogo/protobuf/protoc-gen-gofast \
        github.com/gogo/protobuf/protoc-gen-gogo \
        github.com/gogo/protobuf/protoc-gen-gogofast \
        github.com/gogo/protobuf/protoc-gen-gogofaster


FROM alpine:3.7 as protoc_builder
RUN apk add --no-cache build-base wget unzip automake autoconf libtool
ENV PROTOC_VERSION 3.5.1
RUN set -eux; \
    wget -O /tmp/protoc.zip https://github.com/google/protobuf/releases/download/v3.5.1/protobuf-cpp-3.5.1.zip; \
    echo "706ac3cbd4504f34c5b991e1b4567e35d7fa883c417e1644de594a169e193af8  /tmp/protoc.zip" | sha256sum -c -; \
    unzip -q /tmp/protoc.zip -d /tmp/protoc; \
    rm /tmp/protoc.zip
WORKDIR /tmp/protoc/protobuf-3.5.1
RUN set -eux; autoreconf -f -i -Wall,no-obsolete; ./configure --prefix=/usr --enable-static=no; make -j4; make install DESTDIR=/out


FROM alpine:3.7
RUN apk add --no-cache libstdc++
COPY --from=protoc_builder /out/ /
COPY --from=go_builder /go/bin/protoc-gen-go* /usr/bin/
