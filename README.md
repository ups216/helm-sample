# Helm Sample Code

Create a AKS cluster

```shell
## Create the resource group
az group create --name MyResourceGroup --location southeastasia

## Create AKS Cluster
## BikeSharingApp need at least 3 nodes to run
az aks create -g MyResourceGroup -n MyAKS --location southeastasia --node-vm-size Standard_DS2_v2 --node-count 1 --disable-rbac --generate-ssh-keys

## Get kubeconfig to local machine in order to use kubectl
az aks get-credentials -g MyResourceGroup -n MyAKS
kubectl get nodes
```

Deploy Tiller

```shell
kubectl -n kube-system create serviceaccount tiller
kubectl create clusterrolebinding tiller --clusterrole cluster-admin --serviceaccount=kube-system:tiller
helm init --service-account tiller

kubectl get pods -n kube-system
NAME                                    READY   STATUS    RESTARTS   AGE
coredns-67fd67489b-2sqzl                1/1     Running   0          9m8s
coredns-67fd67489b-nzvnm                1/1     Running   0          5m19s
coredns-autoscaler-f654c64fd-ts6r8      1/1     Running   0          9m5s
heapster-6d879b9dc8-spmng               2/2     Running   0          5m6s
kube-proxy-2q7vb                        1/1     Running   0          5m53s
kubernetes-dashboard-69bd89c8f7-lr6nq   1/1     Running   1          9m7s
metrics-server-67c75dbf7-qmq4c          1/1     Running   1          9m7s
tiller-deploy-7b659b7fbd-4b2jx          1/1     Running   0          24s
tunnelfront-865c8cff86-nndpg            1/1     Running   0          9m7s
```

Deploy a jenkins chart

```shell
helm fetch stable/jenkins
tar xvf ./jenkins-1.3.3.tagz
kubectl apply -f azure-premium.yaml
kubectl get pvc
helm install --name my-release --set persistence.existingClaim=azure-managed-disk ./jenkins
```

Tire down

```shell
helm delete my-release --purge
az aks delete -g MyResourceGroup -n MyAKS
```