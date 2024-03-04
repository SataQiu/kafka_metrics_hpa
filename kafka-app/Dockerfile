FROM golang:alpine as build-stage

#Install dev tools
RUN apk add --update --no-cache alpine-sdk bash ca-certificates \
      libressl tar git openssh openssl  openssl-dev \
      yajl-dev zlib-dev cyrus-sasl-dev build-base coreutils

# Build librdkafka
RUN cd /root && \
    git clone https://github.com/edenhill/librdkafka.git && \
    cd /root/librdkafka && \
    ./configure && \
    make -j "$(getconf _NPROCESSORS_ONLN)" && \
    make install

RUN mkdir /lib64 && ln -s /lib/libc.musl-x86_64.so.1 /lib64/ld-linux-x86-64.so.2

WORKDIR /

COPY consumer.go .
COPY producer.go .
COPY go.mod .
COPY go.sum .

RUN go mod download

RUN go build -tags musl -o consumer consumer.go
RUN go build -tags musl -o producer producer.go
