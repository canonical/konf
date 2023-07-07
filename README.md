# Kubernetes configs for canonical websites

This repository contains the configuration files the Kubernetes deployments for some of [our websites](https://github.com/canonical).

We are using [jinja2](https://jinja.palletsprojects.com/) as a template system for our configurations. All the template values for our projects can be found in the respective project under the `konf/site.yaml` file.

## Structure

We deploy our services into one of two namespaces:

- `staging`: Services for our staging servers (there's usually one per production website)
- `production`: Services for our production servers

### Template system
Using the konf.py script, we generate the following Kubernetes objects:
- service
- deployment
- ingress

E.g.:
``` bash
./konf.py staging ../microcloud.is/konf/site.yaml
```

## Deploying

### To deploy a new service

E.g. to deploy the snapcraft.io service to staging from scratch:

``` bash
# E.g. To deploy the snapcraft.io services to staging
./konf.py staging ../microcloud.is/konf/site.yaml | kubectl apply --filename -
```

E.g. to deploy a specific docker image

``` bash
# E.g. To deploy the snapcraft.io services to staging
./konf.py staging ../microcloud.is/konf/site.yaml --tag a264efb326485 | kubectl apply --filename -
```

### To update an existing service

Or to update an existing snapcraft.io service without changing the deployed image:

``` bash
# E.g. for snapcraft.io
TAG_TO_DEPLOY=$(kubectl get deployment snapcraft-io --namespace staging -o jsonpath="{.spec.template.spec.containers[*].image}" | grep -P -o '(?<=:)[^:]*$')

./konf.py staging ../microcloud.is/konf/site.yaml --tag $TAG_TO_DEPLOY | kubectl apply --filename -
```
