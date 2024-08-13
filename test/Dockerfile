FROM ctrox/csi-s3:dev-full

LABEL maintainers="Cyrill Troxler <cyrilltroxler@gmail.com>"
LABEL description="csi-s3 testing image"

RUN apt-get update \
  && apt-get install -y \
    git wget make \
  && rm -rf /var/lib/apt/lists/*

# https://golang.org/dl
ARG GO_VERSION=1.22.6

RUN cd /tmp \
  && ARCH=$(uname -m | sed -e 's/aarch64/arm64/' -e 's/x86_64/amd64/') \
  && wget -q https://golang.org/dl/go${GO_VERSION}.linux-${ARCH}.tar.gz \
  && tar -xf go${GO_VERSION}.linux-${ARCH}.tar.gz \
  && mv go /usr/local \
  && rm -rf go*

ENV GOROOT /usr/local/go
ENV GOPATH /go
ENV PATH=$GOPATH/bin:$GOROOT/bin:$PATH

RUN cd /tmp \
  && ARCH=$(uname -m | sed -e 's/aarch64/arm64/' -e 's/x86_64/amd64/') \
  && wget -q https://dl.min.io/server/minio/release/linux-${ARCH}/minio \
  && chmod +x minio \
  && mv minio /usr/local/bin

WORKDIR /app

# prewarm go mod cache
COPY go.mod go.sum ./
RUN  go mod download

ADD test/test.sh /usr/local/bin

ENTRYPOINT ["/usr/local/bin/test.sh"]
