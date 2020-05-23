# Local kubernetes development cluster

## Why?

Every public cloud provider has kubernetes clusters in their offerings. So why should I need a local cluster?  
Yes, you are right. But normally you have to pay for it (at least after the free trial period has ended).  
Hmm, but just in case I need a local kubernetes there are projects like [Minikube](https://kubernetes.io/de/docs/setup/minikube/), [KinD (Kubernetes in Docker)](https://kind.sigs.k8s.io/) or [Minishift](https://www.okd.io/minishift/).  
Yes and these are great projects to get you started with learning and using kubernetes. They come with a ready to use kubernetes installation and your're all set to go.  
For me it was important to learn how to do a basic installation of kubernetes in a somewhat more realistic setup. The aforementioned projects for local kubernetes only include an all in one single node installation. But I wanted to have multi node cluster and with the ressources of todays laptops this should not be a problem. 

This project targets my own needs. I work with [Fedora Linux](https://getfedora.org) on my laptop and so the playbooks are only tested on Fedora 32. If you have the same needs of learning something about a local kubernetes cluster installation on Fedora 32 chances are that you will learn something, too.

## What?

We will do an automatic and repeatable installation of a local multi node kubernetes cluster. 
We will use 

* [Fedora 32 as host operating system](https://getfedora.org)
* [KVM as virtualization solution for the necessary virtual machines](https://www.linux-kvm.org)
* [libvirt as virtualization API](https://libvirt.org/)
* [Virt Manager UI](https://virt-manager.org/)
* [Vagrant to automate the VM setup](https://www.vagrantup.com/)
* [Vagrant libvirt plugin](https://github.com/vagrant-libvirt/vagrant-libvirt)
* [Ansible to automate the kubernetes installation](https://www.ansible.com/)
* [Ubuntu 18.04 as os for the kubernetes nodes](https://app.vagrantup.com/generic/boxes/ubuntu1804)
* [Docker as container engine for kubernetes](https://www.docker.com/)
* [kubeadm to simplify the kubernetes installation](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)
* [Calico as CNI plugin for the pod network](https://www.projectcalico.org/)
* [kubectl to access the kubernetes cluster](https://kubernetes.io/docs/reference/kubectl/overview/)
* [k9s for a simplified access to the cluster](https://github.com/derailed/k9s)

## How?

### Clone this repo

To get a quick start you should clone this repo. 

```shell
[peter@alastor k8s-dev-cluster]$ tree
.
├── LICENSE
├── master-playbook.yml
├── node-playbook.yml
├── README.md
└── Vagrantfile
```

### Change to number of worker nodes

By default you will get a local cluster with one master and two worker nodes. 
You can change the number of worker nodes by changing the value of the variable `N` in Vagrantfile.

```vagrant
...
N = 2
...
```

### Start the installation

After that make sure your current directory is the directory of `Vagrantfile`. 

Then just type at your commandline 

```shell
VAGRANT_DEFAULT_PROVIDER=libvirt vagrant up --no-parallel
```

Now Vagrant will bring up the master vm and will install kubernetes on it. The kubernetes installation is delegated to ansible via this `master-playbook.yml`.

When the installation of the master has finished your directory looks like that

```shell
[peter@alastor k8s-dev-cluster]$ tree
.
├── join
├── kubeconfig
│   └── master
│       └── home
│           └── vagrant
│               └── .kube
│                   └── config
├── LICENSE
├── master-playbook.yml
├── node-playbook.yml
├── README.md
└── Vagrantfile
```

The file `join` contains the command for the worker nodes to join the new cluster.

```shell
[peter@alastor k8s-dev-cluster]$ cat join 
kubeadm join 192.169.50.10:6443 --token j73q4h.7y7kkxnnwxxxxxxx     --discovery-token-ca-cert-hash sha256:ea91bb578b355dbf570c9f375df999fb7ae46c59f96eb206760d17xxxxxxxxxx
```

The file `config` contains all the information for kubectl to access the new cluster. Please copy this file to the `.kube` folder in your home directory. 

```shell
cp kubeconfig/master/home/vagrant/.kube/config ~/.kube/
```

After the master the installation of the worker nodes will be started. When the installation has finished you can access the cluster. 

### Test the cluster

Make sure you have copied the kube config file as describe above. 

```
[peter@alastor k8s-dev-cluster]$ kubectl get cs
NAME                 STATUS    MESSAGE             ERROR
scheduler            Healthy   ok                  
controller-manager   Healthy   ok                  
etcd-0               Healthy   {"health":"true"}
```

You can also start k9s to the see the the status of all resources inside the cluster.

![](.assets/k8s-after-installation.png)

## What next?

## Ressources

* https://github.com/projectcalico/calico/issues/3092
* https://graspingtech.com/create-kubernetes-cluster/
* https://kubernetes.io/docs/tasks/debug-application-cluster/debug-service/#is-the-kube-proxy-working
* https://www.projectcalico.org/
* https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/
* https://computingforgeeks.com/how-to-install-kvm-on-fedora/
* https://github.com/vagrant-libvirt/vagrant-libvirt/blob/master/README.md
* https://github.com/vagrant-libvirt/vagrant-libvirt#management-network
* *https://stackoverflow.com/questions/33864652/how-to-change-vagrant-default-network-range
* http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#/error?namespace=default
* https://www.replex.io/blog/how-to-install-access-and-add-heapster-metrics-to-the-kubernetes-dashboard
* https://github.com/derailed/k9s	