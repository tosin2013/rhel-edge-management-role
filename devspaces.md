# Instructions to develop role via ansible devspaces

## Prerequisites
* OpenShift 4.12+ cluster

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

4. Access Dev Spaces URL and login 
5. Create a new space
![20230821123701](https://i.imgur.com/Y490kDk.png)
![20230821125119](https://i.imgur.com/WuWYqA6.png)
6. Create myplaybook.yml
![20230821125216](https://i.imgur.com/J3EStAt.png)
7. Create vars.yml
![20230821125306](https://i.imgur.com/kQOOWvl.png)