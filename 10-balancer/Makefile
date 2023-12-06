#!/usr/bin/make

.DEFAULT_GOAL := help

help: ## Show this help
	@awk 'BEGIN {FS = ":.*?## "} /^[a-zA-Z_-]+:.*?## / {printf "  \033[36m%-15s\033[0m %s\n", $$1, $$2}' $(MAKEFILE_LIST)
	@echo "\n  Allowed for overriding next properties:\n\n\
	    Usage example:\n\
	        make run"

start: ## Start
	docker-compose up -d

stop: ## Down
	docker-compose stop

test: ## Test service
	curl 127.0.0.1:10000/
