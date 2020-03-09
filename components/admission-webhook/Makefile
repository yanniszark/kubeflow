# Image URL to use all building/pushing image targets
IMG ?= gcr.io/kubeflow-images-public/admission-webhook
TAG ?= $(eval TAG := $(shell date +v%Y%m%d)-$(shell git describe --tags --always --dirty)-$(shell git diff | shasum -a256 | cut -c -6))$(TAG)
GOLANG_VERSION ?= 1.12

test:
	go test -v ./...

build:
	CGO_ENABLED=0 GOOS=linux GOARCH=amd64 $(GO) build \
	-a -ldflags '-extldflags "-static"' \
	-o bin/webhook \
	.

build-gcr:
	docker build -t $(IMG):$(TAG) .
	@echo Built $(IMG):$(TAG)

docker-build:
	docker build \
	--build-arg GOLANG_VERSION=$(GOLANG_VERSION) \
	-t $(IMG):$(TAG) .

docker-push:
	docker push $(IMG):$(TAG)
