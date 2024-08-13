FROM golang:1.22-alpine AS gobuild

WORKDIR /build
ADD . /build

RUN go get -d -v ./...
RUN CGO_ENABLED=0 GOOS=linux go build -a -ldflags '-extldflags "-static"' -o ./s3driver ./cmd/s3driver

FROM debian:bookworm-slim

# https://downloads.rclone.org/
ARG RCLONE_VERSION=v1.67.0

LABEL maintainers="Cyrill Troxler <cyrilltroxler@gmail.com>"
LABEL description="csi-s3 slim image"

# s3fs and some other dependencies
RUN apt-get update \
  && apt-get install -y \
    s3fs curl unzip \
  && rm -rf /var/lib/apt/lists/*

# install rclone
RUN cd /tmp \
  && ARCH=$(uname -m | sed -e 's/aarch64/arm64/' -e 's/x86_64/amd64/') \
  && curl -O https://downloads.rclone.org/${RCLONE_VERSION}/rclone-${RCLONE_VERSION}-linux-${ARCH}.zip \
  && unzip /tmp/rclone-${RCLONE_VERSION}-linux-${ARCH}.zip \
  && mv /tmp/rclone-*-linux-*/rclone /usr/bin \
  && rm -r /tmp/rclone*

COPY --from=gobuild /build/s3driver /s3driver

ENTRYPOINT ["/s3driver"]
