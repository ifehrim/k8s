
## https://www.youtube.com/watch?v=w5K_fUcg8qQ

docker pull mirrorgooglecontainers/kubernetes-dashboard-amd64:v1.10.1
docker pull mirrorgooglecontainers/kube-apiserver:v1.14.1
docker pull mirrorgooglecontainers/kube-controller-manager:v1.14.1
docker pull mirrorgooglecontainers/kube-scheduler:v1.14.1
docker pull mirrorgooglecontainers/kube-proxy:v1.14.1
docker pull mirrorgooglecontainers/pause:3.1
docker pull mirrorgooglecontainers/etcd:3.3.10
docker pull coredns/coredns:1.3.1


docker tag docker.io/mirrorgooglecontainers/kubernetes-dashboard-amd64:v1.10.1 k8s.gcr.io/kubernetes-dashboard-amd64:v1.10.1
docker tag docker.io/mirrorgooglecontainers/kube-apiserver:v1.14.1 k8s.gcr.io/kube-apiserver:v1.14.1
docker tag docker.io/mirrorgooglecontainers/kube-controller-manager:v1.14.1 k8s.gcr.io/kube-controller-manager:v1.14.1
docker tag docker.io/mirrorgooglecontainers/kube-scheduler:v1.14.1 k8s.gcr.io/kube-scheduler:v1.14.1
docker tag docker.io/mirrorgooglecontainers/kube-proxy:v1.14.1 k8s.gcr.io/kube-proxy:v1.14.1
docker tag docker.io/mirrorgooglecontainers/pause:3.1 k8s.gcr.io/pause:3.1
docker tag docker.io/mirrorgooglecontainers/etcd:3.3.10 k8s.gcr.io/etcd:3.3.10
docker tag docker.io/coredns/coredns:1.3.1 k8s.gcr.io/coredns:1.3.1


This video demonstrate about how to configure kubernetes cluster in redhat7/centOS7 environment. Simple few step to setup and build your kubernetes cluster.

===============================
1. Install packages on master and minions
yum install kubeadm docker -y

2. Stop firewall/selinux
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config

systemctl stop firewalld


cat /etc/yum.repos.d/kubernetes.repo 
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0


yum install -y docker kubelet kubeadm kubectl --disableexcludes=kubernetes



3. Start docker and kubelet service
for i in kubelet docker; do systemctl start $i; systemctl enable $i;systemctl status $i; done

4. Ensure swap is off and comment in fstab
swapoff -a

5. Enable IP forwarding or iptables. Update below lines in /etc/sysctl.conf

net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1

6. To take effect the new kernel paramater settings:
sysctl -p

7. Set IP range for pods or docker container:
kubeadm init --pod-network-cidr=172.30.0.0/16

8. mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

9. Configure flannel networking for your docker containner.
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml


10.join


kubeadm join --token <token> <master-ip>:<master-port> --discovery-token-ca-cert-hash sha256:<hash>

kubeadm join --token pdpa3x.aft8fdh97igu4cz6 10.88.16.69:6443 --discovery-token-unsafe-skip-ca-verification



