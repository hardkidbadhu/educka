
**Install Containerd**
wget https://github.com/containerd/containerd/releases/download/v1.6.2/containerd-1.6.2-linux-amd64.tar.gz
sudo tar Czxvf /usr/local containerd-1.6.2-linux-amd64.tar.gz

wget https://raw.githubusercontent.com/containerd/containerd/main/containerd.service
sudo mv containerd.service /usr/lib/systemd/system/

sudo systemctl daemon-reload
sudo systemctl enable --now containerd

sudo systemctl status containerd

**Install runC**

wget https://github.com/opencontainers/runc/releases/download/v1.1.1/runc.amd64
sudo install -m 755 runc.amd64 /usr/local/sbin/runc

Containerd configuration for Kubernetes

containerd uses a configuration file config.toml for handling its demons. When installing containerd using official binaries, you will not get the configuration file. So, generate the default configuration file using the below commands.

sudo mkdir -p /etc/containerd/
containerd config default | sudo tee /etc/containerd/config.toml


sudo systemctl restart containerd


**Using Docker Repository**

sudo apt update
sudo apt install -y ca-certificates curl gnupg lsb-release

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list

**Install Containerd**

sudo apt update

sudo apt install -y containerd.io


sudo systemctl status containerd


**Containerd configuration for Kubernetes**

cat <<EOF | sudo tee -a /etc/containerd/config.toml
[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc]
[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
SystemdCgroup = true
EOF


Then, enable CRI plugins by commenting out disabled_plugins = ["cri"] in the config.toml file.


sudo sed -i 's/^disabled_plugins \=/\#disabled_plugins \=/g' /etc/containerd/config.toml


sudo systemctl restart containerd


**Install CNI Plugins For Containerd**
For the container to run, you need to install CNI plugins. So, download the latest version of CNI plugins from GitHub and place them in the /opt/cni/bin directory.

sudo mkdir -p /opt/cni/bin/
sudo wget https://github.com/containernetworking/plugins/releases/download/v1.1.1/cni-plugins-linux-amd64-v1.1.1.tgz
sudo tar Cxzvf /opt/cni/bin cni-plugins-linux-amd64-v1.1.1.tgz


sudo systemctl restart containerd


**Install nerdctl (CLI)**

wget https://github.com/containerd/nerdctl/releases/download/v0.19.0/nerdctl-0.19.0-linux-amd64.tar.gz
sudo tar Cxzvf /usr/local/bin nerdctl-0.19.0-linux-amd64.tar.gz


sudo nerdctl run -d -p 80:80 --name=nginx nginx



If you plan to use containerd as the runtime for Kubernetes, configure the systemd cgroup driver for runC.

sudo sed -i 's/SystemdCgroup \= false/SystemdCgroup \= true/g' /etc/containerd/config.toml

Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 172.31.31.14:6443 --token 8hndcs.bsylkq19e7wjjkjo \
	--discovery-token-ca-cert-hash sha256:dbf30050ac2b423462ae3bb3716548306b5bd74dc6bf445841dd813d9d530f68


    Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 172.31.31.14:6443 --token 1gwj7h.w5iapferwjpv7lvo --discovery-token-ca-cert-hash sha256:ed3318379436c6fdcb4e5a7d34616f1d2c17c3ed75bee115a0cc2f491317bcd7 