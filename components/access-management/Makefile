REGISTRY_PROJECT ?= kubeflow-dev
GOLANG_VERSION ?= 1.12
GO = GO111MODULE=on go
GCLOUD_PROJECT ?= kubeflow-images-public
IMG ?= gcr.io/$(GCLOUD_PROJECT)/kfam
PROJECT ?= kubeflow-dev
TAG ?= $(eval TAG := $(shell date +v%Y%m%d)-$(shell git describe --tags --always --dirty)-$(shell git diff | shasum -a256 | cut -c -6))$(TAG)

CHANGED_FILES := $(shell git diff-files --relative=components/centraldashboard)

ifeq ($(strip $(CHANGED_FILES)),)
# Changed files is empty; not dirty
# Don't include --dirty because it could be dirty if files outside the ones we care
# about changed.
GIT_VERSION := $(shell git describe --tags --long)
else
GIT_VERSION := $(shell git describe --tags --long)-dirty-$(shell git diff | shasum -a256 | cut -c -6)
endif

test:
	$(GO) test -v ./...

build:
	CGO_ENABLED=0 GOOS=linux GOARCH=amd64 $(GO) build \
	-a -ldflags '-extldflags "-static"' \
	-o bin/access-management \
	.

docker-build:
	docker build \
	--build-arg GOLANG_VERSION=$(GOLANG_VERSION) \
	-t $(IMG):$(TAG) .

build-gcb:
	gcloud --project=$(PROJECT) \
		builds submit \
		--machine-type=n1-highcpu-32 \
		--substitutions=_GIT_VERSION=$(GIT_VERSION),_REGISTRY=$(REGISTRY_PROJECT) \
		--config=cloudbuild.yaml .

docker-push:
	docker push $(IMG):$(TAG)
