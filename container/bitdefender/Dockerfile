####################################################
# GOLANG BUILDER
####################################################
FROM golang:1.11 as go_builder

COPY . /go/src/github.com/malice-plugins/bitdefender
WORKDIR /go/src/github.com/malice-plugins/bitdefender
RUN go get -u github.com/golang/dep/cmd/dep && dep ensure
RUN go build -ldflags "-s -w -X main.Version=v$(cat VERSION) -X main.BuildTime=$(date -u +%Y%m%d)" -o /bin/avscan

####################################################
# PLUGIN BUILDER
####################################################
FROM ubuntu:bionic
# FROM debian:jessie


# Create a malice user and group first so the IDs get set the same way, even as
# the rest of this may change over time.
RUN groupadd -r malice \
  && useradd --no-log-init -r -g malice malice \
  && mkdir /malware \
  && chown -R malice:malice /malware

ARG BDKEY
ENV BDVERSION 7.7-1

RUN apt-get update && \
    apt-get install -y software-properties-common && \
    rm -rf /var/lib/apt/lists/*

RUN buildDeps='ca-certificates wget build-essential' \
  && apt-get update -qq \
  && apt-get install -yq $buildDeps psmisc 

RUN wget -q -O "-" http://download.bitdefender.com/repos/deb/bd.key.asc | apt-key add - 
RUN echo "deb [trusted=yes] http://download.bitdefender.com/repos/deb/ bitdefender non-free" >> /etc/apt/sources.list.d/bitdefender.list
# Ensure ca-certificates is installed for elasticsearch to use https
RUN apt-get update -qq && apt-get install -yq --no-install-recommends ca-certificates \
  && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
RUN apt-get update && apt-get install -yq bitdefender-scanner

# Update breaks bdscan for some reason.
ADD http://www.eicar.org/download/eicar.com.txt /malware/EICAR
RUN mkdir -p /opt/malice && echo "accept" | bdscan /malware/EICAR ; exit 0

COPY --from=go_builder /bin/avscan /bin/avscan

WORKDIR /malware
ENTRYPOINT ["/bin/avscan"]
CMD ["--help"]
