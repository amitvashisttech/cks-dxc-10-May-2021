### Documentation Referred:

https://kubernetes.io/docs/concepts/services-networking/ingress/

#### Step 1: Setup an Ingress Cantroller in non-managed K8s.
```
kubectl apply -f nginx-ingress-controller.yaml
kubectl apply -f echoservice.yml
kubectl get pods  
```

#### Step 2: Apply Cluster wide roles to Ingress 
```
kubectl create clusterrolebinding add-on-cluster-admin   --clusterrole=cluster-admin   --serviceaccount=kube-system:default
kubectl create clusterrolebinding add-on-cluster-admin-1   --clusterrole=cluster-admin   --serviceaccount=default:default
```
#### Step 3: Check the Ingress Service & Verify
```
kubectl expose rc nginx-ingress-controller --type=NodePort
kubectl get svc
curl http://{MASTER_IP_Address}:{INGRESS-HTTP-PUBLIC-PORT}
Sample : curl 172.31.0.100:32513
```

#### Step 4 - Create Self Signed Certificate for Domain:
```sh
mkdir ingress
cd ingress
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout ingress.key -out ingress.crt -subj "/CN=example.internal/O=security"
```
#### Step 5 - Create Kubernetes TLS based secret :
```sh
kubectl create secret tls tls-cert --key ingress.key --cert ingress.crt
kubectl get secret tls-cert -o yaml
```
#### Step 6 - Create Kubernetes Ingress with TLS:
```sh
kubectl get ingress
kubectl delete ingress example-ingress
```
```sh
nano ingress-tls.yaml
```
```sh
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: demo-ingress
  annotations:
     nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  tls:
  - hosts:
      - example.internal
    secretName: tls-cert
  rules:
  - host: example.internal
    http:
      paths:
      - pathType: Prefix
        path: "/"
        backend:
          service:
            name: example-service
            port:
              number: 80
```
```sh
kubectl apply -f ingress-tls.yaml
```

#### Step 4 - Ingress Pod Deployemnt & Expose Example Service:
```sh
kubectl run nginx-pod --image=nginx 
kubectl apply -f example-svc.yaml
```

#### Step 4 - Make a request to Controller:
```sh
curl -kv https://{MASTER_IP_ADDRESS}:{HTTPS_PUBLIC_PORT}
curl -kv https://172.31.0.100:30555 -H 'Host: example.internal'
```
