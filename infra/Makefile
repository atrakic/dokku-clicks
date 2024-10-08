SHELL := /bin/bash

vmName?=dokku
adminUsername?=$(vmName)
rgName?=rg-vm-$(vmName)
dnsNameForPublicIP?=$(vmName)-$(shell whoami)
location?=northeurope
sshKeyData?=$(shell cat $$HOME/.ssh/id_rsa.pub)

.PHONY: all status outputs test clean

all:
	az account show
	az group create --name $(rgName) --location $(location)
	az deployment group create \
		--resource-group $(rgName) \
		--template-file main.bicep \
		--parameters adminUsername=$(adminUsername) \
		--parameters sshKeyData="$(sshKeyData)" \
		--parameters dnsNameForPublicIP=$(dnsNameForPublicIP) \
		--parameters location=$(location)

status:
	az resource list --resource-group $(rgName) --location $(location) --output table

outputs:
	az deployment group list --resource-group $(rgName) --query "[].properties.outputs"

test:
	curl -sL $(shell az deployment group show --name "main" --resource-group $(rgName) --query properties.outputs.fqdn.value -o tsv)

healthz:
	curl -sL $(shell az deployment group show --name "main" --resource-group $(rgName) --query properties.outputs.fqdn.value -o tsv)/healthz

clean:
	az group delete --name $(rgName)
