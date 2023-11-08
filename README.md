# evaluation-system

## Setup local k8s cluster

1. ### Install VirtualBox

```
sudo apt-get update -y
```
```
sudo apt-get install virtualbox virtualbox-ext-pack
```

2. ### Install kubectl

```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

3. ### Install minikube

```
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

4. ### Start minikube

```
minikube start -n 3 --disk-size=10g \\n--cpus=2 --memory=4g
```
## Setup evaluation system's components

1. ### Create namespace for Jenkins and NFS storage

```
kubectl create namespace jenkins,storage
```

2. ### Create PV and PVC for Jenkins

    1. First, ssh to worker node `minikube-m03`

    ```
    minikube ssh minikube-m03
    ```
    
    > You can also ssh to minikube worker node by its ip and ssh info: `username: docker, password: tcuser`

    2. Create directory for data

    ```
    mkdir -p /data/jenkins-demo-volume
    ```

    > Remember to give right permission to directory so that Jenkins can have enough right to save data to the directory

    Then exit

    ```
    exit
    ```
       
    4. Create pv and pvc based on local path
    ```
    kubectl apply -f pv.yaml
    ```

    ```
    kubectl apply -f pvc.yaml
    ```

2. ### Create Jenkins Deployment and Jenkins Service

```
kubectl apply -f jenkins-depl.yaml
```

4. ### Create Service Account and RoleBinding

```
kubectl apply -f service-account.yaml
```

5. ### Create NFS server

    1. First, create local storage in worker node minikube-m02

    ```
    minikube ssh minikube-m02
    ```

    2. Create directory for saving data

   ```
   mkdir /data/nfs
   ```

   3. Exit ssh

   ```
   exit
   ```

   4. Create NFS server deployment

   ```
   kubectl apply -f nfs-server.yaml
   ```
   
