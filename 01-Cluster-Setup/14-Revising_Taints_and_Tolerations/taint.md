#### Remove Taint:
```sh
kubectl taint nodes master node-role.kubernetes.io/master-
```
#### Verification:
```sh
kubectl run nginx-pod --image=nginx
```
