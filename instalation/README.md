
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



docker pull mirrorgooglecontainers/node-hello:1.0
docker pull gcr.io/google-samples/node-hello:1.0



This video demonstrate about how to configure kubernetes cluster in redhat7/centOS7 environment. Simple few step to setup and build your kubernetes cluster.

//--registry-mirror=https://registry.docker-cn.com

cat /etc/yum.repos.d/kubernetes.repo 
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0



===============================
1. Install packages on master and minions
yum install kubeadm docker -y

2. Stop firewall/selinux
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config

setenforce 0
systemctl stop firewalld





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
kubeadm init --pod-network-cidr=10.244.0.0/16

8. mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

9. Configure flannel networking for your docker containner.
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml


10.join


kubeadm token create --print-join-command >> worker_init.sh

kubeadm join 10.88.16.68:6443 --token k8cbhw.3o9idkilkqnael5b \
    --discovery-token-ca-cert-hash sha256:312c270ba09bad296cc44380ce49db124a66ddb6db100239841d36a4a4135df0

kubeadm join 10.88.16.69:6443 --token p0mg8d.aaa3vyaafj5ixo3r \
    --discovery-token-ca-cert-hash sha256:27d9782f948f4c37175c192fe5ed73f71d44f37bd83ad80471ae4e074748de9a

kubeadm join --token <token> <master-ip>:<master-port> --discovery-token-ca-cert-hash sha256:<hash>

kubeadm join --token k8cbhw.3o9idkilkqnael5b 10.88.16.68:6443 --discovery-token-unsafe-skip-ca-verification
kubeadm join --token pdpa3x.aft8fdh97igu4cz6 10.88.16.69:6443 --discovery-token-unsafe-skip-ca-verification

kubectl proxy --accept-hosts="^*$"
kubectl proxy --port=8001 --address='10.88.16.68' --accept-hosts="^*$"


kubectl delete deployment kubernetes-dashboard --namespace=kube-system
kubectl delete service kubernetes-dashboard  --namespace=kube-system
kubectl delete role kubernetes-dashboard-minimal --namespace=kube-system
kubectl delete rolebinding kubernetes-dashboard-minimal --namespace=kube-system
kubectl delete sa kubernetes-dashboard --namespace=kube-system
kubectl delete secret kubernetes-dashboard-certs --namespace=kube-system
kubectl delete secret kubernetes-dashboard-key-holder --namespace=kube-system


step 1 install kubectl in your local machine    
step 2 sudo scp root@10.88.16.68:/etc/kubernetes/admin.conf ~/.kube/config   
step 3 kubectl proxy  
step 4 kubectl describe secret dashboard-token-2rtkz


sudo scp root@10.88.16.68:/etc/kubernetes/admin.conf ~/.kube/config



kubectl delete sa dashboard

kubectl create sa dashboard -n default

kubectl delete clusterrolebinding dashboard-admin

kubectl create clusterrolebinding dashboard-admin -n default --clusterrole=cluster-admin --serviceaccount=default:dashboard


kubectl describe sa dashboard
kubectl describe secret dashboard-token-cdrwl

kubectl get secret $(kubectl get serviceaccount dashboard -o jsonpath="{.secrets[0].name}") -o jsonpath="{.data.token}" | base64 --decode



