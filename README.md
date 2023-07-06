# kubernetes
Getting a cluster running

Using containerd, kubeadm and flannel on debian 12

## containerd

[docker docs](https://docs.docker.com/engine/install/debian/)

yea containerd is shipped by docker

```
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install containerd.io
sudo apt-get install containernetworking-plugins # some random thing apt suggested
```

## kubeadm

[kubernetes docs](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)

```
curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-archive-keyring.gpg
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

## start cluster

```
kubeadm init
```

then copy the config file to your machine ~/.kube/config
and install kubectl on your machine

## networking

Run this in the current repo on your authorized machine

```
kubectl apply -f kube-flannel.yml
```

## flannel troubleshooting

For me it didn't pick up the correct ip
So I had to make sure the node uses the same ip as flannel.

```
grep Network kube-flannel.yml # => "Network": "10.244.0.0/16",
```

Has to match

```
kubectl get nodes -o jsonpath='{.items[*].spec.podCIDR}'
```

If it doesnt just patch it

```
kubectl get nodes # to get YOURNODENAME
kubectl patch node YOURNODENAME -p '{"spec":{"podCIDR":"10.244.0.0/16"}}'
```

## general troubleshooting

Check the service logs on the node

```
journalctl -u kubelet.service -f
journalctl -u containerd.service -f
```

## cgroup

```
grep SystemdCgroup /etc/containerd/config.toml
```

should show SystemdCgroup = true

if it does not set it to true. If its not there at all generate a full config with this command

```
containerd config default > /etc/containerd/config.toml
```

And then set SystemdCgroup to true.


