REPO=malice-plugins/fprot
ORG=malice
NAME=fprot
CATEGORY=av
VERSION?=$(shell cat VERSION)

MALWARE=tests/malware
NOT_MALWARE=tests/not.malware

all: build size tag test_all

.PHONY: build
build:
	docker build -t $(ORG)/$(NAME):$(VERSION) .

.PHONY: size
size:
	sed -i.bu 's/docker%20image-.*-blue/docker%20image-$(shell docker images --format "{{.Size}}" $(ORG)/$(NAME):$(VERSION)| cut -d' ' -f1)-blue/' README.md

.PHONY: tag
tag:
	docker tag $(ORG)/$(NAME):$(VERSION) $(ORG)/$(NAME):latest

.PHONY: tags
tags:
	docker images --format "table {{.Repository}}\t{{.Tag}}\t{{.Size}}" $(ORG)/$(NAME)

.PHONY: ssh
ssh:
	@docker run --init -it --rm -v $(PWD):/malware --entrypoint=bash $(ORG)/$(NAME):$(VERSION)

.PHONY: tar
tar:
	docker save $(ORG)/$(NAME):$(VERSION) -o $(NAME).tar

gotest:
	go get
	go test -v

avtest:
	@echo "===> F-PROT Version"
	@docker run --init --rm --entrypoint=bash $(ORG)/$(NAME):$(VERSION) -c "/usr/local/bin/fpscan --version" > tests/av_version.out
	@echo "===> F-PROT EICAR Test"
	@docker run --init --rm --entrypoint=bash $(ORG)/$(NAME):$(VERSION) -c "/usr/local/bin/fpscan -r EICAR" > tests/av_scan.out || true

update:
	@docker run  --rm $(ORG)/$(NAME):$(VERSION) update

.PHONY: start_elasticsearch
start_elasticsearch:
ifeq ("$(shell docker inspect -f {{.State.Running}} elasticsearch)", "true")
	@echo "===> elasticsearch already running.  Stopping now..."
	@docker rm -f elasticsearch || true
endif
	@echo "===> Starting elasticsearch"
	@docker run --init -d --name elasticsearch malice/elasticsearch:6.6; sleep 20

.PHONY: malware
malware:
ifeq (,$(wildcard $(MALWARE)))
	wget https://github.com/maliceio/malice-av/raw/master/samples/befb88b89c2eb401900a68e9f5b78764203f2b48264fcc3f7121bf04a57fd408 -O $(MALWARE)
	cd tests; echo "TEST" > not.malware
endif

.PHONY: test_all
test_all: test test_elastic test_markdown test_web

.PHONY: test
test: malware
	@echo "===> ${NAME} --help"
	docker run --init --rm $(ORG)/$(NAME):$(VERSION) --help
	docker run --init --rm -v $(PWD):/malware $(ORG)/$(NAME):$(VERSION) -V $(MALWARE) | jq . > docs/results.json
	cat docs/results.json | jq .

.PHONY: test_elastic
test_elastic: start_elasticsearch malware
	@echo "===> ${NAME} test_elastic found"
	docker run --rm --link elasticsearch -e MALICE_ELASTICSEARCH_URL=http://elasticsearch:9200 -v $(PWD):/malware $(ORG)/$(NAME):$(VERSION) -V $(MALWARE)
	@echo "===> ${NAME} test_elastic NOT found"
	docker run --rm --link elasticsearch -e MALICE_ELASTICSEARCH_URL=http://elasticsearch:9200 -v $(PWD):/malware $(ORG)/$(NAME):$(VERSION) -V $(NOT_MALWARE)
	@docker run --rm --link elasticsearch --entrypoint=sh blacktop/httpie -c "http elasticsearch:9200/malice/_search | jq ." > docs/elastic.json

.PHONY: test_markdown
test_markdown: test_elastic
	@echo "===> ${NAME} test_markdown"
	# http localhost:9200/malice/_search query:=@docs/query.json | jq . > docs/elastic.json
	cat docs/elastic.json | jq -r '.hits.hits[] ._source.plugins.${CATEGORY}.${NAME}.markdown' > docs/SAMPLE.md

.PHONY: test_web
test_web: malware stop
	@echo "===> Starting web service"
	@docker run -d --name $(NAME) -p 3993:3993 $(ORG)/$(NAME):$(VERSION) web
	http -f localhost:3993/scan malware@$(MALWARE)
	@echo "===> Stopping web service"
	@docker logs $(NAME)
	@docker rm -f $(NAME)

.PHONY: stop
stop: ## Kill running docker containers
	@docker rm -f $(NAME) || true

MALICE_SCANID ?= ""

.PHONY: test_malice
test_malice:
	@echo "===> $(ORG)/$(NAME):$(VERSION) testing with running malice elasticsearch DB (update existing sample)"
	docker run --rm -e MALICE_SCANID=$(MALICE_SCANID) -e MALICE_ELASTICSEARCH=elasticsearch --link malice-elastic:elasticsearch -v $(PWD):/malware $(ORG)/$(NAME):$(VERSION) -V -t $(MALWARE)

.PHONY: circle
circle: ci-size
	@sed -i.bu 's/docker%20image-.*-blue/docker%20image-$(shell cat .circleci/size)-blue/' README.md
	@echo "===> Image size is: $(shell cat .circleci/size)"

ci-build:
	@echo "===> Getting CircleCI build number"
	@http https://circleci.com/api/v1.1/project/github/${REPO} | jq '.[0].build_num' > .circleci/build_num

ci-size: ci-build
	@echo "===> Getting artifact sizes from CircleCI"
	@cd .circleci; rm size nsrl bloom || true
	@http https://circleci.com/api/v1.1/project/github/${REPO}/$(shell cat .circleci/build_num)/artifacts${CIRCLE_TOKEN} | jq -r ".[] | .url" | xargs wget -q -P .circleci

clean:
	docker-clean stop
	docker image rm $(ORG)/$(NAME):$(VERSION)
	docker image rm $(ORG)/$(NAME):latest
	rm $(MALWARE)

# Absolutely awesome: http://marmelab.com/blog/2016/02/29/auto-documented-makefile.html
help:
	@grep -E '^[a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-30s\033[0m %s\n", $$1, $$2}'

.DEFAULT_GOAL := all