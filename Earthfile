VERSION 0.7
FROM golang:1.21
WORKDIR /go-workdir

tools:
    RUN apt-get install -y make

deps:
    COPY go.mod go.sum ./
    RUN go mod download
    # Output these back in case go mod download changes them.
    SAVE ARTIFACT go.mod AS LOCAL go.mod
    SAVE ARTIFACT go.sum AS LOCAL go.sum

build:
    FROM +tools
    FROM +deps
    COPY Makefile go.mod go.sum .
    COPY --dir cmd/ config/ datastore/ messages/ metrics/ protos/ server/ telemetry/ . 
    RUN make build
    SAVE ARTIFACT $GOPATH/bin/fleet-telemetry
    
docker:
    ARG tag='latest'
    COPY +build/fleet-telemetry /go/bin/
    ENTRYPOINT ["/go/bin/fleet-telemetry"]
    SAVE IMAGE fleet-telemetry:$tag