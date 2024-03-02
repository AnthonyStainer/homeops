Live ISO download Ubuntu Server 22.04.4
Written to USB stick in DD mode with Rufus. GPT - Doesn't seem to detect it when booting UEFI with MBR
Minimal install
10.1.1.200/24 - DHCP reservation
LVM, defaults, internal 512GB SSD
Attached to ubuntu pro account ubuntu@xerxes.ai
Enabled unattended updates
Installed grafana agent
```
curl -sfL https://get.k3s.io | sh -
```

```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

https://github.com/argoproj/argo-cd/releases/download/v2.9.7/argocd-linux-amd64

```bash
mkdir -p ~/.kube
sudo k3s kubectl config view --raw | tee ~/.kube/config
chmod 600 ~/.kube/config
```

Updated the argo deployment to add --insecure

```
apiVersion: traefik.containo.us/v1alpha1
kind: IngressRoute
metadata:
  name: argocd-server
  namespace: argocd
spec:
  entryPoints:
    - websecure
  routes:
    - kind: Rule
      match: Host(`10.1.1.200`)
      priority: 10
      services:
        - name: argocd-server
          port: 80
    - kind: Rule
      match: Host(`10.1.1.200`) && Headers(`Content-Type`, `application/grpc`)
      priority: 11
      services:
        - name: argocd-server
          port: 80
          scheme: h2c
  tls:
    certResolver: default
```


https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
anthony@spirit:~$ cloudflared tunnel login
Please open the following URL and log in with your Cloudflare account:

https://dash.cloudflare.com/argotunnel?aud=&callback=https%3A%2F%2Flogin.cloudflareaccess.org%2FQdK8J-T7LTLuhcmvfnRG1HSZ1yTf70YJiSBhvDI4By8%3D

anthony@spirit:~$ cloudflared tunnel login
Please open the following URL and log in with your Cloudflare account:

https://dash.cloudflare.com/argotunnel?aud=&callback=https%3A%2F%2Flogin.cloudflareaccess.org%2FQdK8J-T7LTLuhcmvfnRG1HSZ1yTf70YJiSBhvDI4By8%3D

Leave cloudflared running to download the cert automatically.
You have successfully logged in.
If you wish to copy your credentials to a server, they have been saved to:
/home/anthony/.cloudflared/cert.pem
anthony@spirit:~$ cloudflared tunnel create homeops
Tunnel credentials written to /home/anthony/.cloudflared/d2c78ac0-9671-48c2-bc69-a5cf35660308.json. cloudflared chose this file based on where your origin certificate was found. Keep this file secret. To revoke these credentials, delete the tunnel.

Created tunnel homeops with id d2c78ac0-9671-48c2-bc69-a5cf35660308
anthony@spirit:~$ kubectl create secret generic tunnel-credentials \
--from-file=credentials.json=/home/anthony/.cloudflared/d2c78ac0-9671-48c2-bc69-a5cf35660308.json
secret/tunnel-credentials created
anthony@spirit:~$

