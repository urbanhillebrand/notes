# kubernetes

## kcl1 Testinstallation mit k3s

**Vorarbeiten auf Proxy außerhalb von Kubernetes (nginx)**

```
stream {
        upstream kcl1_servers {
                server 10.2.0.122:6443;
                server 10.2.0.123:6443;
        }

        server {
                listen 6443;
                proxy_pass kcl1_servers;
        }
}
```

Auf dem ersten API-Server:
```
export K3S_DATASTORE_ENDPOINT='postgres://kcl1:kf2wCvDvyaPh7V@srv.urban.intra:5432/kcl1?sslmode=disable'
curl -sfL https://get.k3s.io | sh -s - server --node-taint CriticalAddonsOnly=true:NoExecute --tls-san 10.2.0.102 --tls-san srv.urban.intra
```

**🔥 CAUTION**\
TODO: SSL auf postgresql aktivieren, `?sslmode=disable` weglassen!

Danach Token ermitteln: `sudo cat /var/lib/rancher/k3s/server/node-token`

**Auf den weiteren API-Servern:**

```
xport K3S_DATASTORE_ENDPOINT='postgres://kcl1:kf2wCvDvyaPh7V@srv.urban.intra:5432/kcl1?sslmode=disable'
curl -sfL https://get.k3s.io | sh -s - server --node-taint CriticalAddonsOnly=true:NoExecute --tls-san 10.2.0.102 --tls-san srv.urban.intra --token <TOKENVONOBEN>
```

Test: `sudo k3s kubectl get node`

**Agents:**

```
curl -sfL https://get.k3s.io | K3S_URL=https://10.2.0.102:6443 K3S_TOKEN=<TOKENVONOBEN> sh -
```

**`kubectl` auf Admin-Rechner**

* `kubectl` installieren
* `/etc/rancher/k3s/k3s.yaml` von einem **Server** nach `~/.kube/config` kopieren, und unter `server` die IP auf die Cluster-IP von oben (nginx!) korrigieren
* danach geht `kubectl get nodes` am Client

**Dashboard installieren**

-> s. https://docs.technotim.live/posts/k3s-ha-install/

### Testdeployment

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysite
  labels:
    app: mysite
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysite
  template:
    metadata:
      labels:
        app : mysite
    spec:
      containers:
        - name : mysite
          image: nginx
          ports:
            - containerPort: 80
```

* Deployment mit `kubectl apply -f testdeploy.yml`
* Check mit `kubectl get pods`, oder im Dashboard unter `pods`

**Test-HTTP-Request**

```
kubectl exec -it mysite-<nummer> -- sh
```

**Scaling ändern**

```
kubectl scale --replicas=10 deploy/mysite
```

**Sonstiges**

```
kubectl get deploy mysite
kubectl delete deploy mysite
```
