# vagrant-rke2-cluster
Configuration and scripts for deploying rke2 in vagrant

## Instruction
`vagrant up` will deploy the following

```
3 Ubuntu 22.04 Linux VMs in libvirt
|-RKE2 Cluster with 1 manager and 2 worker nodes
  |-1 RKE2 server
  |-2 RKE2 nodes. One on each worker node
```

The kubeconfg to connect to the cluster from you dev-machine will be delived to your working folder as rke2.yaml