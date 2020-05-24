# Using CF for k8s on a local development cluster

* [cf for k8s](https://github.com/cloudfoundry/cf-for-k8s)

## Install cf cli

* [cf cli](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

```shell
$ sudo wget -O /etc/yum.repos.d/cloudfoundry-cli.repo https://packages.cloudfoundry.org/fedora/cloudfoundry-cli.repo

$ sudo dnf install cf-cli
```

## Install kapp

* [kapp](https://k14s.io/#install)

```shell
$ sudo dnf install -y perl-Digest-SHA
$ wget -O- https://k14s.io/install.sh | sudo bash
```

## Install a metrics-server into your cluster

```shell
$ kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/download/v0.3.6/components.yaml
```


## Git clone cf-for-k8s repo

```shell
$ git clone https://github.com/cloudfoundry/cf-for-k8s.git
$ cd cf-for-k8s
```

## Install BOSH cli

* [BOSH cli](https://bosh.io/docs/cli-v2-install/)

```shell
$ curl -L https://github.com/cloudfoundry/bosh-cli/releases/download/v6.2.1/bosh-cli-6.2.1-linux-amd64 --output bosh
$ chmod u+x bosh
$ sudo mv bosh /usr/local/bin/
```

## Start the installation

```shell
$ ./hack/generate-values.sh -d system.localhost.localdomain > /tmp/cf-values.yml
$ ytt -f config -f /tmp/cf-values.yml > /tmp/cf-for-k8s-rendered.yml
$ kapp deploy -a cf -f /tmp/cf-for-k8s-rendered.yml -y
```

## Delete the installation

```shell
$ kapp delete -a cf
```