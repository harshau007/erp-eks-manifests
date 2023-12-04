# ERP-Cloud on Amazon EKS

## Before Starting Create Necessary Roles
-  [Amazon EKS cluster IAM role](https://docs.aws.amazon.com/eks/latest/userguide/service_IAM_role.html)
- [Amazon EKS node IAM role](https://docs.aws.amazon.com/eks/latest/userguide/create-node-role.html)


## Setting Up ERP

### 1. Create Cluster with created Amazon EKS cluster IAM role

### 2. Add Node Group with created Amazon EKS cluster node IAM role

### 3. Add 3 Nodes in Node Group

### 4. Update kubeconfig
```bash
aws eks update-kubeconfig --name <cluster-name>
```

### 5. Add [Amazon EBS CSI Driver](https://stackoverflow.com/a/73871494) Add-On ( Follow StackOverflow )

### 6. Check ebs-csi-driver is running in kube-system namespace
```bash
kubectl get pods -n kube-system
```

### 7. Apply Configs Sequentially

### 8. Wait for DB to Setup
```bash
watch kubectl get all
```

### 9. Exec into Primary DB ( find it if you can't )
```bash
kubectl exec -it mongodb-0 -- mongosh
```

### 10. Add new user to erp db
```bash
use admin # Change DB to admin

db.auth('<your-username-from-mongodb.yaml>', '<your-password-from-mongodb.yaml>') # auth 

use erp # Change DB to erp

db.createUser({ user: "<new-user>", pwd: "<new-pass>", roles: ["readWrite", "dbAdmin"] }) # Create user in erp db

db.testcol.insertOne({}) # Dummy Insert on erp

exit
```

### 11. Test Login with new user
```bash
kubectl exec -it mongodb-0 -- /bin/bash
```
```bash
mongosh mongodb://<new-user>:<new-pass>@mongodb-0.mongodb-svc.default.svc.cluster.local:27017/erp?readPreference=nearest
```

### 12. If Success, Apply Backend or else find issue 

### Apply Backend (Using kustomize)
#### For Dev
```bash
kubectl apply -k 6-Backend/dev
```
#### For Prod
```bash
kubectl apply -k 6-Backend/prod
```
