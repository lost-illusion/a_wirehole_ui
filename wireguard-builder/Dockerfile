FROM golang:1.20 as awg
COPY ./amneziawg-go /awg
WORKDIR /awg
RUN go mod download && \
    go mod verify && \
    go build -ldflags '-linkmode external -extldflags "-fno-PIC -static"' -v -o /usr/bin

FROM alpine:3.19

COPY ./amneziawg-tools /awg-tools
WORKDIR /awg-tools

RUN apk --no-cache add iproute2 iptables bash && \
    apk add --no-cache --virtual=build-dependencies \
    build-base \
    elfutils-dev \
    git \
    linux-headers && \
    cd src && \
    make -j$(nproc) && \
    make install && \
    chmod +x /usr/bin/awg /usr/bin/awg-quick && \
    ln -s /usr/bin/awg /usr/bin/wg && \
    ln -s /usr/bin/awg-quick /usr/bin/wg-quick && \
    apk del --no-network build-dependencies && \
    rm -rf \
    /tmp/*
COPY --from=awg /usr/bin/amneziawg-go /usr/bin/amneziawg-go
