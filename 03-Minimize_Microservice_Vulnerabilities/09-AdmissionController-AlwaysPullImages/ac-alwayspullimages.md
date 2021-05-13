
### Pre-Requisite:

Make sure you have a custom image available locally.
```sh
mkdir /root/image-policy
```
```sh
nano Dockerfile
FROM ubuntu:latest
CMD ["sleep","3600"]
```
```sh
docker build -t hello-world:latest .
```
#### Add the Admission Controller:
```sh
nano /etc/kubernetes/manifests/kube-apiserver.yaml
```
Add following within kube-apiserver manifest to enable admission controller
```sh
AlwaysPullImages
--enable-admission-plugins=PodSecurityPolicy,AlwaysPullImages
```
#### Delete Old POD:
```sh
kubectl delete pod hello-world
```
#### POD Manifest:
```sh
nano hello-world-new.yaml
```
```sh
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: hello-world
  name: hello-world-new
spec:
  containers:
  - image: hello-world
    name: hello-world
    imagePullPolicy: IfNotPresent
```
```sh
kubectl apply hello-world.yaml
```
```sh
kubectl get pods hello-world-new -o yaml
```
