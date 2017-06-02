# kubernetes-networkpolicy

## goal

Isolate namespaces on network level. The pod's from namespace team0 should not have access to the pods of namespace team1 and vice versa.

## setup kubernetes

1. perpare 2 hosts
    1. install ubuntu xenial
    1. install newest docker ce version  https://docs.docker.com/engine/installation/linux/ubuntu/ 
    1. install newest version of kubernetes https://kubernetes.io/docs/setup/independent/install-kubeadm/
1. initalize kubernetes
    1. `kubeadm init --pod-network-cidr=10.244.0.0/16` 
1. set environment
    1. `sudo cp /etc/kubernetes/admin.conf $HOME/`
    1. `sudo chown $(id -u):$(id -g) $HOME/admin.conf`
    1. `export KUBECONFIG=$HOME/admin.conf`
1. setup https://github.com/projectcalico/canal
    1. `kubectl apply -f https://raw.githubusercontent.com/projectcalico/canal/master/k8s-install/1.6/rbac.yaml`
    1. `kubectl apply -f https://raw.githubusercontent.com/projectcalico/canal/master/k8s-install/1.6/canal.yaml`
    kubectl annotate ns team0 "net.beta.kubernetes.io/network-policy={\"ingress\": {\"isolation\": \"DefaultDeny\"}}"
## setup resources

1. `kubectl create ns team0`
1. `kubectl create ns team1`
1. `kubectl create -f jenkins-deployment0.yml`
1. `kubectl create -f jenkins-service0.yml`
1. `kubectl create -f jenkins-deployment1.yml`
1. `kubectl create -f jenkins-service1.yml`
 
## setup NetworkPolicy

1. `kubectl annotate ns team0 "net.beta.kubernetes.io/network-policy={\"ingress\": {\"isolation\": \"DefaultDeny\"}}"`
1. `kubectl annotate ns team1 "net.beta.kubernetes.io/network-policy={\"ingress\": {\"isolation\": \"DefaultDeny\"}}"`
    1. at this point the jenkins container of team1 cannot reach the jenkins container of team0 and the services are not accessible over the NodePort
1. `kubectl create -f allow-team0.yml`
    1. at this point the jenkins container of team1 can reach the jenkins container of team0 with the command `wget <internal IP>:8080` this shouldn't be the case, since the from podSelector should match only pod's with the label `team0`
    
## add pod with different label

1. `kubectl run busybox99 --rm -ti --namespace=team0 --labels="team=team99" --image=busybox /bin/sh`
    1. the jenkins of team0 is reachable from this container, which shouldn't be the case.
