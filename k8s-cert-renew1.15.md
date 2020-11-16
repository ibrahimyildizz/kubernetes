kubeadm config view > /tmp/kubeadm-config.yaml
cp -rp /etc/kubernetes /etc/kubernetes.backup
cp -r /var/lib/etcd /var/lib/etcd.backup
mkdir /etc/kubernetes/conf-backup
mv /etc/kubernetes/admin.conf /etc/kubernetes/kubelet.conf /etc/kubernetes/scheduler.conf /etc/kubernetes/controller-manager.conf /etc/kubernetes/conf-backup/conf-backup/

kubeadm alpha certs renew all --config=/tmp/kubeadm-config.yaml
kubeadm init phase kubeconfig all --config /tmp/kubeadm-config.yaml

docker ps |grep -E 'k8s_kube-apiserver|k8s_kube-controller-manager|k8s_kube-scheduler|k8s_etcd_etcd' | awk -F ' ' '{print $1}' |xargs docker restart
systemctl restart kubelet

cp -r $HOME/.kube $HOME/kubebackup
rm -rf $HOME/.kube
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

kubectl get pods
