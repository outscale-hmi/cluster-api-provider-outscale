
# Prerequisite 
- Install [kubectl][kubectl]

- Outscale account with ak/sk [Outscale Access Key and Secret Key][Outscale Access Key and Secret Key]
- A Kubernetes cluster:
    - You can use a Vm with [kubeadm][kubeadm] or [Minikube][Minikube]. 
    - You can use a container with [kind][kind]. 
    - You can use a rke cluster with [osc-rke][osc-rke].
- Look at cluster-api note ()
# Kubernetes cluster

If you use your own cluster for production (with backup, disaster recovery, ...) and expose it:
```
 export KUBECONFIG=<...>
```

Or you can use kind (only for local dev):
Create kind:
```bash
   kind create cluster
```
Check cluster is ready:
```bash
   kubectl cluster-info
```
# Install clusterctl
You can install clusterctl for linux with:
```bash
  make install-clusterctl
```

## Initialize clusterctl
You can enable clusterResourceSet with 
```bash
export EXP_CLUSTER_RESOURCE_SET=true
```

Please create  $HOME/.cluster-api/clusterctl.yaml:
```
providers:
- name: outscale
  type: InfrastructureProvider
  url: https://github.com/outscale-dev/cluster-api-provider-outscale/releases/v0.1.0/infrastructure-components.yaml
```

You can initialize clusterctl with credential with:
```
export OSC_ACCESS_KEY_ID=<your-access-key>
export OSC_SECRET_KEY=<your-secret-access-key>
make credential
clusterctl init --infrastructure outscale
```


## Launch your stack with clusterctl
You have to set:
```
export OSC_IOPS=<osc-iops>
export OSC_VOLUME_SIZE=<osc-volume-size>
export OSC_VOLUME_TYPE=<osc-volume-type>
export OSC_KEYPAIR_NAME=<osc-keypairname>
export OSC_SUBREGION_NAME=<osc-subregion>
export OSC_VM_TYPE=<osc-vm-type>
export OSC_IMAGE_NAME=<osc-image-name>
```
Then you will generate:
```
clusterctl generate cluster <cluster-name>   --kubernetes-version <kubernetes-version>   --control-plane-machine-count=<control-plane-machine-count>   --worker-machine-count=<worker-machine-count> > getstarted.yaml
```

You can then change getstared as you want base on doc.
Then apply:
```
kubectl apply -f getstarted.yaml
```

### Get Kubeconfig
You can then get the status:
```
root@cidev-admin v1beta1]# kubectl get cluster-api  -A
NAMESPACE   NAME                                                                       CLUSTER       AGE
default     kubeadmconfig.bootstrap.cluster.x-k8s.io/cluster-api-control-plane-lzj65   cluster-api   95m
default     kubeadmconfig.bootstrap.cluster.x-k8s.io/cluster-api-md-0-zgx4w            cluster-api   95m

NAMESPACE   NAME                                                                AGE
default     kubeadmconfigtemplate.bootstrap.cluster.x-k8s.io/cluster-api-md-0   95m

NAMESPACE   NAME                                                      CLUSTER       REPLICAS   READY   AVAILABLE   AGE   VERSION
default     machineset.cluster.x-k8s.io/cluster-api-md-0-7568fb659d   cluster-api   1                              95m   v1.22.11

NAMESPACE   NAME                                                  CLUSTER       REPLICAS   READY   UPDATED   UNAVAILABLE   PHASE       AGE   VERSION
default     machinedeployment.cluster.x-k8s.io/cluster-api-md-0   cluster-api   1                  1         1             ScalingUp   95m   v1.22.11

NAMESPACE   NAME                                                         CLUSTER       NODENAME                                  PROVIDERID         PHASE      AGE   VERSION
default     machine.cluster.x-k8s.io/cluster-api-control-plane-4q2s8     cluster-api   ip-10-0-0-45.eu-west-2.compute.internal   osc://i-3b629324    Running    95m   v1.22.11
default     machine.cluster.x-k8s.io/cluster-api-md-0-7568fb659d-hnkfw   cluster-api   ip-10-0-0-144.eu-west-2.compute.internal   osc://i-add154be   Running    95m   v1.22.11

NAMESPACE   NAME                                   PHASE         AGE   VERSION
default     cluster.cluster.x-k8s.io/cluster-api   Provisioned   95m   

NAMESPACE                           NAME                                                         AGE   TYPE                   PROVIDER      VERSION
capi-kubeadm-bootstrap-system       provider.clusterctl.cluster.x-k8s.io/bootstrap-kubeadm       46h   BootstrapProvider      kubeadm       v1.2.1
capi-kubeadm-control-plane-system   provider.clusterctl.cluster.x-k8s.io/control-plane-kubeadm   46h   ControlPlaneProvider   kubeadm       v1.2.1
capi-system                         provider.clusterctl.cluster.x-k8s.io/cluster-api             46h   CoreProvider           cluster-api   v1.2.1

NAMESPACE   NAME                                                                          CLUSTER       INITIALIZED   API SERVER AVAILABLE   REPLICAS   READY   UPDATED   UNAVAILABLE   AGE   VERSION
default     kubeadmcontrolplane.controlplane.cluster.x-k8s.io/cluster-api-control-plane   cluster-api                                        1                  1         1             95m   v1.22.11

NAMESPACE   NAME                                                                           AGE
default     oscmachinetemplate.infrastructure.cluster.x-k8s.io/cluster-api-control-plane   95m
default     oscmachinetemplate.infrastructure.cluster.x-k8s.io/cluster-api-md-0            95m

```

<!-- References -->
[kubectl]: https://kubernetes.io/docs/tasks/tools/install-kubectl/
[kind]: https://github.com/kubernetes-sigs/kind#installation-and-usage
[kubeadm]: https://kubernetes.io/fr/docs/setup/production-environment/tools/kubeadm/install-kubeadm/
[Outscale Access Key and Secret Key]: https://wiki.outscale.net/display/EN/Creating+an+Access+Key
[osc-rke]: https://github.com/outscale-dev/osc-k8s-rke-cluster
[Minikube]: https://kubernetes.io/docs/tasks/tools/install-minikube/