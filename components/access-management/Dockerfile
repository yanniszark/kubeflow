ARG GOLANG_VERSION=1.12.4
FROM golang:${GOLANG_VERSION} as builder

WORKDIR /workspace

# Download dependencies first
COPY go.mod .
COPY go.sum .
RUN go mod download

# Copy in code and build
COPY . .
RUN make test
RUN make build
RUN chmod a+rx bin/access-management

# Use distroless as minimal base image to package the manager binary
# Refer to https://github.com/GoogleContainerTools/distroless for more details
FROM gcr.io/distroless/base:latest as serve
WORKDIR /
COPY third_party third_party
COPY --from=builder /workspace/bin/access-management .
COPY --from=builder /go/pkg/mod/github.com/hashicorp third_party/library/

EXPOSE 8081

CMD ["/access-management"]
