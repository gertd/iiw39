# IIW39

IIW-39 Practical Magic - Policy-as-Code - A hands-on Introduction

https://www.cncf.io/blog/2023/01/19/the-five-laws-of-cloud-native-authorization/

## Prerequisites

Docker is used to host the topaz authorizer and directory process.
* [docker](https://www.docker.com/products/docker-desktop/) 

## Install the Topaz OSS authorization service

* Linux
```shell
brew install aserto-dev/tap/topaz
```

* MacOS
```shell
brew install aserto-dev/tap/topaz
```

* Windows
```
winget install --id Aserto.Topaz
```

## Getting started, the tedious way :)

### Create a configuration
```shell
topaz config new --name iiw39 --resource ghcr.io/aserto-policies/policy-rebac:latest
```

### Set the configuration context
```shell
topaz config use iiw39
```

### Start the authorizer service

```shell
topaz start
```

### Define the ReBAC (Relation Based Access Control) Manifest

The manifest describes the authorization domain model, in terms of objects and relations.

```shell
topaz directory set manifest ./model/manifest.yaml
```

### Load the test data (objects & relations)

```shell
topaz directory import --directory ./data
```

### Lets perform an authorization Check
```shell
curl 'https://localhost:9393/api/v3/directory/check' \
--data-raw '{"subject_type":"user","subject_id":"jerry@the-smiths.com","object_type":"doc","object_id":"rick.inventions","relation":"can_read"}'
```

### Please explain?
```shell
curl 'https://localhost:9393/api/v3/directory/check' \
--data-raw '{"subject_type":"user","subject_id":"beth@the-smiths.com","object_type":"doc","object_id":"morty.journal","relation":"can_read","trace":true}'
```

### Lets test the ReBAC model
```shell
topaz directory test exec ./test/assertions.json
```

### What if the you reference a non-existing type?
```shell
curl 'https://localhost:9393/api/v3/directory/check' \
--data-raw '{"subject_type":"user","subject_id":"jerry@the-smiths.com","object_type":"document","object_id":"morty.journal","relation":"can_read"}'
```

### How about ABAC (Attribute Based Access Control)?
```shell
curl 'https://localhost:8383/api/v2/authz/is' \
--data-raw '{
  "identity_context": {
    "type": "IDENTITY_TYPE_SUB",
    "identity": "rick@the-citadel.com"
  },
  "resource_context": {
    "object_id": "root",
    "object_type": "folder",
    "relation": "owner"
  },
  "policy_context": {
    "decisions": [
      "allowed"
    ],
    "path": "rebac.check"
  }
}'  
```

### Lets test the ABAC checks
```shell
topaz authorizer test exec ./test/decisions.json
```

## That was tedious, there must be a better way?

### Install everything using a template...
```shell
topaz templates install iiw39 ./template.json --force
```

## Lets automate...


