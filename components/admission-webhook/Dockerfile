# Build the manager binary
ARG GOLANG_VERSION=1.12
FROM golang:${GOLANG_VERSION} as builder

WORKDIR /go/src/github.com/kubeflow/kubeflow/components/admission-webhook

# Download dependencies first
COPY go.mod .
COPY go.sum .
RUN go mod download

# Copy in code and build
COPY . .
RUN make test
RUN make build
RUN chmod a+rx bin/webhook

# Build
RUN make build

# Copy the controller-manager into a distroless image
FROM gcr.io/distroless/static-debian10:fd0d99e8c54d7d7b2f3dd29f5093d030d192cbbc
WORKDIR /
COPY --from=builder /go/src/github.com/kubeflow/kubeflow/components/admission-webhook/bin/webhook .
ENTRYPOINT ["/webhook"]
