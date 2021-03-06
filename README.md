# kubernetes-ingressify

[![Latest Release](https://img.shields.io/github/release/goeuro/kubernetes-ingressify.svg)](https://github.com/goeuro/kubernetes-ingressify/releases/latest)
[![Build Status](https://img.shields.io/travis/goeuro/kubernetes-ingressify.svg?label=master)](https://travis-ci.org/goeuro/kubernetes-ingressify)
[![Release Status](https://img.shields.io/travis/goeuro/kubernetes-ingressify/v0.0.1-snapshot.svg?label=release)](https://travis-ci.org/goeuro/kubernetes-ingressify/branches)
[![Go Report Card](https://goreportcard.com/badge/github.com/goeuro/kubernetes-ingressify)](https://goreportcard.com/report/github.com/goeuro/kubernetes-ingressify)
[![codecov](https://codecov.io/gh/goeuro/kubernetes-ingressify/branch/master/graph/badge.svg)](https://codecov.io/gh/goeuro/kubernetes-ingressify)

This is a simple binary that watches kubernetes ingress rules, renders your template and calls your script.
You can use this to generate ingress-based configuration for any backend router.

## Why?

There are multiple kubernetes ingress controller implementations.
Unfortunately they control both router implementation (how router is compiled, built, etc), behavior and configuration.
Its not easy to extend them and add custom logic, e.g. adding custom modules or plugins on the router, custom annotations and routing features, overriding templates, etc.
Different organizations have different traffic handling needs, and having a third-party ingress controller own everything is not possible in many scenarios.
But at the same time, the cost of writing your own ingress controller is also quite high.
This is an attempt to decouple router implementation from configuration and allow anyone to easily create their own ingress controllers.

## Roadmap

- [x] Bootstrap
- [ ] Poll ingress rules, templating and implementation
- [ ] Release v0.1
- [ ] Watch endpoint events, templating and implementation
- [ ] Release v0.2
- [ ] Documentation and examples

## Usage

We have a special release called `v0.0.1-snapshot` which always reflects master build.
Please go ahead and grab it: https://github.com/goeuro/kubernetes-ingressify/releases/tag/v0.0.1-snapshot

Create a configuration file:

```
# ingress.cfg
kubeconfig: <path to kubeconfig, leave it empty for in-cluster authentication>
in_template: <path to template, context provided to template will be documented, defaults to ingress.cfg.tpl>
out_file: <path to output file, defaults to ingress.cfg>
interval: <time between executions>, for field format refer to https://golang.org/pkg/time/#ParseDuration 
hooks:
  post-render:
    - script 1
    - script 2
    - ...
    - script n 
```

Run it:

```
kubernetes-ingressify -config ingress.cfg
```

For more usage details, please refer to the [examples](https://github.com/goeuro/kubernetes-ingressify/tree/master/examples) 

## Development

Use docker/docker-compose to develop. You don't need to have golang installed.

* `docker-compose build` Builds image for development
* `docker-compose run --rm default /bin/bash` Gives you a terminal inside the container, from where you can run go commands like:
  * `bin/test.sh` Runs all tests
  * `gofmt -s -w .` Fix code formatting
  * `go run main.go` Compiles and runs main
* Adding dependencies:
  * catch-22: godep has issues, dep is alpha (not all libs support it) and glide is deprecated in favor of dep
  * We use Godep as the least working option, but it means slightly additional effort when adding dependencies
  * SSH into your container (as above) and follow https://github.com/tools/godep#add-a-dependency
