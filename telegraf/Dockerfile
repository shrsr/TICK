FROM golang:alpine

RUN apk add curl git make
RUN curl https://raw.githubusercontent.com/golang/dep/master/install.sh | sh
RUN mkdir -p $(go env GOPATH)/src/github.com/influxdata/telegraf && \
    cd $(go env GOPATH)/src/github.com/influxdata/telegraf && \
    git clone https://wwwin-github.cisco.com/spa-ie/telegraf -b telemetry-dc2020 . && \
    make && make static && mv telegraf $(go env GOPATH)/bin && \
    cd $(go env GOPATH) && rm -rf $(go env GOPATH)/src/github.com/influxdata/telegraf

CMD ["telegraf"]