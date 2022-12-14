FROM alpine:latest

LABEL maintainer "https://github.com/blacktop"

LABEL malice.plugin.repository = "https://github.com/malice-plugins/yara.git"
LABEL malice.plugin.category="av"
LABEL malice.plugin.mime="*"
LABEL malice.plugin.docker.engine="*"

ENV YARA 3.11.0
RUN apk add --no-cache tini su-exec
RUN set -x   && echo "===> Add malice user and malware folder..."   && addgroup malice   && adduser -S -G malice malice   && mkdir /malware   && chown -R malice:malice /malware
# Install Yara
RUN apk --update add --no-cache openssl file bison jansson ca-certificates
RUN apk --update add --no-cache -t .build-deps \
  openssl-dev \
  jansson-dev \
  build-base \
  libc-dev \
  file-dev \
  automake \
  autoconf \
  libtool \
  flex \
  git \
  gcc \
  && echo "===> Install Yara from source..." \
  && cd /tmp \
  && git clone --recursive --branch v${YARA} https://github.com/VirusTotal/yara.git \
  && cd /tmp/yara \
  && ./bootstrap.sh \
  && sync \
  && ./configure --enable-magic \
  --enable-cuckoo \
  --enable-dotnet \
  && make \
  && make install \
  && rm -rf /tmp/* \
  && apk del --purge .build-deps

# Install malice plugin
COPY . /go/src/github.com/maliceio/malice-yara
RUN apk --update add --no-cache -t .build-deps \
  openssl-dev \
  jansson-dev \
  build-base \
  mercurial \
  musl-dev \
  openssl \
  bash \
  wget \
  git \
  gcc \
  dep \
  go \
  && echo "===> Building scan Go binary..." \
  && cd /go/src/github.com/maliceio/malice-yara \
  && echo " * copy yara rules" \
  && mkdir /rules \
  && mv rules /rules \
  && export GOPATH=/go \
  && export CGO_CFLAGS="-I/usr/local/include" \
  && export CGO_LDFLAGS="-L/usr/local/lib -lyara" \
  && export PKG_CONFIG_PATH="/usr/local/lib/pkgconfig" \
  && go version \
  && dep ensure \
  && go env -w GO111MODULE=off \
  && CGO_ENABLED=1 go build -ldflags "-s -w -X main.Version=v$(cat VERSION) -X main.BuildTime=$(date -u +%Y%m%d)" -o /bin/scan \
  && rm -rf /go /usr/local/go /usr/lib/go /tmp/* \
  && apk del --purge .build-deps

WORKDIR /malware

ENTRYPOINT ["su-exec", "malice","scan"]
CMD ["--help"]
