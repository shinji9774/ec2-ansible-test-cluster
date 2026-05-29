.PHONY : help check-connection apt-update update-minecraft

ANSIBLE_LOG_PATH ?= $(PWD)/logs/ansible-$$(date +%Y%m%d-%H%M%S).log

define _printhelp
	@echo '--------------------------------------------------'; \
	echo '  $(1) tools'; \
	echo '--------------------------------------------------'; \
	grep -hE '^[a-zA-Z_-]+:.*?## \[.*$(1).*\] .*$$' $(MAKEFILE_LIST) | sed -E 's/ \[.*$(1).*\]//g' |awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-30s\033[0m: %s\n", $$1, $$2}'
endef

define _run_Playbook
	@mkdir -p $(shell dirname $(ANSIBLE_LOG_PATH)); \
	ANSIBLE_LOG_PATH=$(ANSIBLE_LOG_PATH) \
	ansible-playbook -i $(PWD)/hosts.yml "$(PWD)/$(1)" $(2) $(ANSIBLE_EXTRA_OPTIONS)
endef

define _run_Playbook_from_ecs
	@test -f $(PWD)/tmp/hosts.yml || \
	ansible-playbook -c local "$(PWD)/$(shell dirname $(1))/playbook_prepare_container.yml" $(2) $(ANSIBLE_EXTRA_OPTIONS)
	@ansible-playbook -i $(PWD)/tmp/hosts.yml "$(PWD)/$(1)" $(2) $(ANSIBLE_EXTRA_OPTIONS)
endef

ifeq ($(EXECUTE_FROM_ECS),)
	RUN_PLAYBOOK := _run_Playbook
else
	RUN_PLAYBOOK := _run_Playbook_from_ecs
endif

export ANSIBLE_CONFIG := $(PWD)/ansible.cfg

help: ## [common] show list
	@$(call _printhelp,common)
	@$(call _printhelp,install)
	@$(call _printhelp,development)

install-server: ## [install] install minecraft server from ECS
	@$(call $(RUN_PLAYBOOK),install/playbook_install_server.yml,)

build: ## [development] build docker image
	@docker build --tag ansible-test --target installed .

run-local: build ## [development] run ansible playbook in docker container
	@docker run --rm -it ansible-test sh

syntax-check: ## [development] syntax check all playbooks
	ansible-playbook -i hosts.yml --syntax-check $(shell find . -name 'playbook_*.yml')
