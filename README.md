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
