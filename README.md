# Kubernetes
Cluster Architecture (1 master , 2 nodes )
Kubernetes components :
kubeadm:tool automates large portion of process to setup cluster .
kubelet:Handles running container on every node (agent).
kubectl:command line tool to interact with the cluster.
Docker Installation :
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"

sudo apt-get update

sudo apt-get install -y docker-ce=18.06.1~ce~3-0~ubuntu

sudo apt-mark hold docker-ce (prevents auto upgrade )
sudo docker version

Kubernetes installation :

curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

cat << EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF

sudo apt-get update

sudo apt-get install -y kubelet=1.12.7-00 kubeadm=1.12.7-00 kubectl=1.12.7-00

sudo apt-mark hold kubelet kubeadm kubectl
kubeadm version

Bootstrapping the cluster :
sudo kubeadm init --pod-network-cidr=10.244.0.0/16 (Intialise the cluster in master)

copy and run output commands :
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

kubectl version

sudo kubeadm join $some_ip:6443 --token $some_token --discovery-token-ca-cert-hash $some_hash

if you want to see token use this command : kubeadm token list 
if you want to see sha number use this command :
openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | \
   openssl dgst -sha256 -hex | sed 's/^.* //'
   
   now combine above two and run this command "sudo kubeadm join $some_ip:6443 --token $some_token --discovery-token-ca-cert-hash $some_hash"
   on master run kubectl get nodes
   
   COnfiguring Networking with flannel :
   
   flannel is network plugin
  run this two commands on every node and master to make cluster more functional 
echo "net.bridge.bridge-nf-call-iptables=1" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p

installation of flannel on master :
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/bc79dd1505b0c8681ece4de4c0d86c5cd2643275/Documentation/kube-flannel.yml

kubectl get pods -n kube-system
sudo systemctl status kubelet



