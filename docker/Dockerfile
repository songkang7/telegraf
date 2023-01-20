FROM golang:1.17 as build-env


ADD . /src/github.com/influxdata/telegraf

WORKDIR /src/github.com/influxdata/telegraf

ENV GOPATH /:/src/github.com/influxdata/telegraf/vendor

ENV GOPROXY=https://proxy.golang.com.cn

RUN go install github.com/sparrc/gdm@latest

ENV GO111MODULE=on

RUN make build

FROM registry.aliyuncs.com/acs/alpine:3.3

COPY --from=build-env /usr/local/go/lib/time/zoneinfo.zip /usr/local/go/lib/time/zoneinfo.zip
COPY --from=build-env /src/github.com/influxdata/telegraf/telegraf /
COPY --from=build-env /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/
RUN mkdir /lib64 && ln -s /lib/libc.musl-x86_64.so.1 /lib64/ld-linux-x86-64.so.2
COPY --from=build-env /src/github.com/influxdata/telegraf/docker/telegraf.conf /etc/acs/monitoring.conf

RUN mkdir -p /etc/acs/acs.conf.d && chmod 777 /etc/acs/acs.conf.d

RUN chmod 777 /telegraf

ENTRYPOINT ["/telegraf","-config", "/etc/acs/monitoring.conf", "-config-directory", "/etc/acs/acs.conf.d"]