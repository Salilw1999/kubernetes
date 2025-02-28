# Setup Instructions for kind and kubectl with Docker

## Step 1: Create and Execute the Shell Script

Create a shell script with the following code to install `kind` and `kubectl`:

```bash
#!/bin/bash

[ $(uname -m) = x86_64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.25.0/kind-linux-amd64
chmod +x ./kind
sudo cp ./kind /usr/local/bin/kind

VERSION="v1.30.0"
URL="https://dl.k8s.io/release/${VERSION}/bin/linux/amd64/kubectl"
INSTALL_DIR="/usr/local/bin"

curl -LO "$URL"
chmod +x kubectl
sudo mv kubectl $INSTALL_DIR/
kubectl version --client

rm -f kubectl
rm -rf kind

sudo apt-get update -y
sudo apt-get install docker.io -y

echo "kind & kubectl installation complete."
echo "docker is also installed."
```

Save the script and run it:
```bash
chmod +x <script_name>.sh
./<script_name>.sh
```

---

## Step 2: Verify Installation

Check the installed versions to confirm the tools are correctly installed:
```bash
docker -v
kubectl version
kind version
```

---

## Step 3: Grant Docker Permissions to User

Allow the current user to run Docker without `sudo`:
```bash
sudo usermod -aG docker $USER && newgrp docker
```

---

## Step 4: Create Configuration File

Create a `config.yml` file with the following content:

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4

nodes:
- role: control-plane
  image: kindest/node:v1.31.2
- role: worker
  image: kindest/node:v1.31.2
- role: worker
  image: kindest/node:v1.31.2
- role: worker
  image: kindest/node:v1.31.2
  extraPortMappings:
  - containerPort: 80
    hostPort: 80
    protocol: TCP
  - containerPort: 443
    hostPort: 443
    protocol: TCP
```

Save this file as `config.yml`.

---

## Step 5: Create kind Cluster with Worker Nodes

Create a cluster using the configuration file:
```bash
kind create cluster --name my-cluster --config=config.yml
