DOCKERHOST = DOCKERHOST
DOCKERORG = DOCKERORG
IMAGENAME = htpasswd-ocp4
TAG = latest
USER=$(shell id -u)
PWD=$(shell pwd)

.PHONY: apb_build
apb_build:
  apb bundle prepare
  docker build -t $(DOCKERHOST)/$(DOCKERORG)/$(IMAGENAME):$(TAG) .
