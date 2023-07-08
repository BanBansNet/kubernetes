# run ddnet servers in kubernetes

ssh into your server and spin up a k3s kubernetes cluster

## Setup k3s

```bash
curl -sfL https://get.k3s.io | sh -
```

- Copy the credentails from ``/etc/rancher/k3s.yaml`` to your machine ``~/.kube/config``
- Replace ``localhost`` in ``~/.kube/config`` with the ip of your server.
- Install ``kubectl`` on your machine
- Verify the cluster is up and running ``kubectl get nodes``

## Run a sample ddnet pod

```bash
kubectl apply -f https://raw.githubusercontent.com/BanBansNet/kubernetes/master/pods/ddnet-no-db.yaml
```

Then connect to your servers ip on port 8304 using a ddnet/teeworlds client.

## Run servers with mysql database

```bash
kubectl apply -f mariadb.yaml
kubectl apply -f ddnet.yaml
```

