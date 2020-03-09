![Legend](http://www.desigifs.com/sites/default/files/2013/BalaKrj2.gif)

# Legend
> The legendary tool to build and manage grafana dashboards for your applications

Build and publish Grafana dashboards for your services with prefilled metrics and alerts
for your services

## Table of contents
* [Features](#features)
* [Getting started](#getting-started)
  * [Pre-requisites](#pre-requisites)
  * [Using Legend](using-legend)
* [Contribution](#contribution)
* [Legend internals](#legend-internals)

## Features

* Build dashboards for your services with prefilled metrics 
* Customizable alerts and panels
* Automatic setup of basic alerts with priority and service mapping
* Beautiful outlay of the dashabord to enable uniformity

## Getting started

This section describes on how to get started with using Legend.

### Pre-requisites

* For each component of your service, there has to be a respective metric files in 
`legend/metrics_library` which containes the metrics to be plotted for that component. If you are adding a new component(and a new metric library file) please refer to [contributing-to-metric-library](docs/contributing-to-metric-library.md)

* The metrics collection has to be enabled for each component of the service. Please refere to the [confluence doc](https://grofers.atlassian.net/wiki/spaces/IN/pages/1491140622/Exposing+metrics+on+services) to how to enable metrics for the components

### Using Legend

You can use `legend` in one of the two ways: 
  * [Use legend from kubernetes (CRD)](#use-legend-from-kubernetes-(crd))
  * [Legend CLI](#legend-cli)

You need to create an input file describing the components of your service [writing-input-file](docs/writing-input-file.md)

#### Use legend from kubernetes (CRD)

Legend runs a custom resource of kind `GrafanaDashboard` in both production and stage kubernetes 
clusters. To use legend, create a spec file in the following format 

```yaml
apiVersion: grofers.io/v1
kind: GrafanaDashboard
metadata:
  name: # Name of the object internally
  labels:
    app: # Add name for reference
spec: # Spec of legend. The same format in which it was mentioned earlier
```

Set the appropriate context. 
> The prod-sgp context creates the dashboard in production grafana (grafana.grofers.com) and the stage
> context create the dashboard in stage grafana (grafa-stage.grofer.io). It is recommended to try and
> test your dashboards in stage before applying in production

To create/update/delete the dashboard, run:

```shell
kubectl apply -f <input-file.yaml>
kubectl delete -f <input-file.yaml>
```

#### Use Legend CLI

Legend can also be installed as a CLI and used to create dashboards. 
> At the current stage, Legeng can only create dashboards but not delete them becuase it doesn not 
> record the state of dashboards it created anywhere

*Installation*
Legend requires python3. It is recommended to install legend in a virtual env
```shell
brew install jsonnet
mkvirtualenv -p `which python3` legend
pip install -e https://github.com/grofers/legend
```

*Configuration*
`LEGEND_HOME` represents the home directory of Legend, by default it is the home directory of the user.
You can override by setting the `LEGEND_HOME` env variable while running legend

Legend needs a configuration file to talk to Grafana, by default it searches for it in `LEGEND_HOME/.legend/legend.cfg`, you can over-ride this with passing `-c` option while runing the commands.

The format for legend.cfg:
> This is pre-requisite to run legend as a CLI. Contact infra team to get the creds

```shell
[grafana]
api_key = # Grafana key with write permission if you are using Legend to create a dashboard, if not read permissions to get the alerting id 
protocol = [https|http] # ex: https 
host = # Grafana host url (ex: grafana-stage.grofer.io)
```

*Running legend*
```shell
legend [OPTIONS] COMMAND [ARGS]
```

## Contribution
You can conribute to legend in two ways:

*Developing/improving legend's functionality*
* You can pick up the existing issues in the github repo of legend and work on the fixes
* Follow the guide [developing on legend](docs/developing-on-legend.md)

*Improve the metrics legend creates for a service*
* This is one of the biggest offerings of Legend - pre configured metrics for a wide variety of
components.
* If you are contibuting to the existing metrics or writing new ones please follow the giude [contributing-to-metric-library](docs/contributing-to-metric-library.md)
* Run tests locally using `tests.sh` (you'll need to setup local config file)
* Raise a PR 


## Legend internals

[Legend internals](docs/legend-internals.md)