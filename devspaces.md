# Instructions to develop role via ansible devspaces

## Prerequisites
* OpenShift 4.1+ cluster

## Steps

1. Login to OpenShift
```
oc login -u <username> -p <password> <cluster_url>
```

2. Run the script below to install OpenShift Gitops
```
git clone https://github.com/tosin2013/sno-quickstarts.git
cd sno-quickstarts/gitops
./deploy.sh
```

3. Configure Cluster 
```
oc create -f apps/device-edge-demos/cluster-config.yaml
```

![20230821122544](https://i.imgur.com/SALDxq0.png)