SHELL := bash

DOCKER_DEST ?= $(DOCKER_IMAGE_REPO):$(VERSION_VALUE)
QUAY ?= quay.io
QUAY_IMAGE ?= $(QUAY)/$(DOCKER_IMAGE_REPO)

BRANCH = $(shell echo "$(REPO_BRANCH)" | sed 's/\//_/')

DOCKER ?= docker

.PHONY: docker-build
docker-build:
ifeq ($(BUNDLE_GEMS__CONTRIBSYS__COM),)
	$(DOCKER) build -t $(DOCKER_DEST) .
else
	$(DOCKER) build --build-arg bundle_gems__contribsys__com=$(BUNDLE_GEMS__CONTRIBSYS__COM) -t $(DOCKER_DEST) .
endif

.PHONY: docker-login
docker-login:
	$(DOCKER) login -u=$(QUAY_ROBOT_HANDLE) -p=$(QUAY_ROBOT_TOKEN) $(QUAY)

.PHONY: docker-push-latest-master
docker-push-latest-master:
	$(DOCKER) tag $(DOCKER_DEST) $(QUAY_IMAGE):$(VERSION_VALUE)
	$(DOCKER) push $(QUAY_IMAGE):$(VERSION_VALUE)
	$(DOCKER) tag $(DOCKER_DEST) $(QUAY_IMAGE):latest
	$(DOCKER) push $(QUAY_IMAGE):latest

.PHONY: docker-push-branch
docker-push-branch:
	$(DOCKER) tag $(DOCKER_DEST) $(QUAY_IMAGE):$(VERSION_VALUE)-$(BRANCH)
	$(DOCKER) push $(QUAY_IMAGE):$(VERSION_VALUE)-$(BRANCH)

.PHONY: ship
ship: docker-build docker-login

ifeq ($(BRANCH),master)
ifeq ($(PULL_REQUEST),false)
ship: docker-push-latest-master
endif
else
ship: docker-push-branch
endif
