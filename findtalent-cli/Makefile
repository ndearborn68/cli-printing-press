.PHONY: build test lint install clean

BIN_EXT := $(if $(filter windows,$(shell go env GOOS)),.exe,)

build:
	go build -trimpath -o bin/findtalent-pp-cli$(BIN_EXT) ./cmd/findtalent-pp-cli

test:
	go test ./...

lint:
	golangci-lint run

install:
	go install ./cmd/findtalent-pp-cli

clean:
	rm -rf bin/

build-mcp:
	go build -trimpath -o bin/findtalent-pp-mcp$(BIN_EXT) ./cmd/findtalent-pp-mcp

install-mcp:
	go install ./cmd/findtalent-pp-mcp

build-all: build build-mcp
