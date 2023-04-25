VERSION 0.6
FROM alpine

ARG GOLINT_VERSION=1.52.2
# renovate: datasource=docker depName=golang
ARG GO_VERSION=1.20
# renovate: datasource=docker depName=hadolint/hadolint versioning=docker
ARG HADOLINT_VERSION=2.12.0-alpine
# renovate: datasource=docker depName=renovate/renovate versioning=docker
ARG RENOVATE_VERSION=35
# renovate: datasource=docker depName=koalaman/shellcheck-alpine versioning=docker
ARG SHELLCHECK_VERSION=v0.9.0

golint:
    ARG GO_VERSION
    FROM golang:$GO_VERSION
    ARG GOLINT_VERSION
    RUN wget -O- -nv https://raw.githubusercontent.com/golangci/golangci-lint/master/install.sh | sh -s v$GOLINT_VERSION
    WORKDIR /build
    COPY . .
    RUN golangci-lint run

hadolint:
    ARG HADOLINT_VERSION
    FROM hadolint/hadolint:$HADOLINT_VERSION
    WORKDIR /images
    COPY images .
    RUN ls
    RUN find . -name "Dockerfile*" -print | xargs -r -n1 hadolint

renovate-validate:
    ARG RENOVATE_VERSION
    FROM renovate/renovate:$RENOVATE_VERSION
    WORKDIR /usr/src/app
    COPY renovate.json .
    RUN renovate-config-validator

shellcheck-lint:
    ARG SHELLCHECK_VERSION
    FROM koalaman/shellcheck-alpine:$SHELLCHECK_VERSION
    WORKDIR /mnt
    COPY . .
    RUN find . -name "*.sh" -print | xargs -r -n1 shellcheck

yamllint:
    FROM cytopia/yamllint
    COPY . .
    # TODO: Find all yaml files or accept directories as input
    RUN yamllint .github/workflows/ overlay/

lint:
  BUILD +golint
  BUILD +hadolint
  BUILD +renovate-validate
  BUILD +shellcheck-lint
  BUILD +yamllint
