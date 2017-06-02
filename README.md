# kubernetes-networkpolicy

## goal

Isolate namespaces on network level. The pod's from namespace team0 should not have access to the pods of namespace team1 and vice versa.

## setup 

1. perpare 2 hosts
  1. install ubuntu xenial
  1. install newest docker ce version  https://docs.docker.com/engine/installation/linux/ubuntu/ 
  1. install newest version of kubernetes https://kubernetes.io/docs/setup/independent/install-kubeadm/
1. initalize kubernetes
  1. kubeadm init --pod-network-cidr=10.244.0.0/16
1. set environment
  1. sudo cp /etc/kubernetes/admin.conf $HOME/
  1. sudo chown $(id -u):$(id -g) $HOME/admin.conf
  1. export KUBECONFIG=$HOME/admin.conf
1. setup https://github.com/projectcalico/canal
  1. kubectl apply -f https://raw.githubusercontent.com/projectcalico/canal/master/k8s-install/1.6/rbac.yaml
  1. kubectl apply -f https://raw.githubusercontent.com/projectcalico/canal/master/k8s-install/1.6/canal.yaml