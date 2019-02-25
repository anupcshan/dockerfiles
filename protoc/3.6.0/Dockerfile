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
ENV PROTOC_VERSION 3.6.0
RUN set -eux; \
    wget -O /tmp/protoc.zip https://github.com/google/protobuf/releases/download/v$PROTOC_VERSION/protobuf-cpp-$PROTOC_VERSION.zip; \
    echo "50873796cabf7e97b7879f78a19c5dc26069136f2df32aeb5ce3bfea987870bb  /tmp/protoc.zip" | sha256sum -c -; \
    unzip -q /tmp/protoc.zip -d /tmp/protoc; \
    rm /tmp/protoc.zip
WORKDIR /tmp/protoc/protobuf-$PROTOC_VERSION
RUN set -eux; autoreconf -f -i -Wall,no-obsolete; ./configure --prefix=/usr --enable-static=no; make -j4; make install DESTDIR=/out


FROM alpine:3.7
RUN apk add --no-cache libstdc++
COPY --from=protoc_builder /out/ /
COPY --from=go_builder /go/bin/protoc-gen-go* /usr/bin/
