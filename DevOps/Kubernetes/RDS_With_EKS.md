# RDS with EKS

![alt text](image-8.png)

<aside>
💡 create security group for rds by allowing port 3306

</aside>

![alt text](image-9.png)

<aside>
💡 create db subnet group in rds

</aside>

![alt text](image-10.png)

<aside>
💡 create rds database

</aside>


![alt text](image-11.png)

![alt text](image-12.png)

![alt text](image-13.png)

<aside>
💡 Create k8s externelName service manifest and deploy

</aside>

```yaml
#MySQL-externalName-Service.yml
apiVersion: v1
kind: Service
metadata:
  name: mysql
spec:
  type: ExternalName
  externalName: usermgmtdb.cxojydmxwly6.us-east-1.rds.amazonaws.com
```

```yaml
kubectl apply -f kube-manifests/01-MySQL-externalName-Service.yml
```
```