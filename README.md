mysql-pv.yml




# ================================
# PersistentVolume for MySQL
# ================================
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mysql-pv
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Delete   # auto-cleanup when PVC is deleted
  hostPath:                                # local path on node
    path: /mnt/data/mysql
  storageClassName: mysql-storage-class
---
# ================================
# StorageClass (optional, if not exists)
# ================================
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: mysql-storage-class
provisioner: kubernetes.io/no-provisioner
volumeBindingMode: WaitForFirstConsumer




mysql-statefulset.yml

# MySQL StatefulSet
# ================================
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysql
  namespace: ashapp
spec:
  serviceName: mysql
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:8.1
        ports:
        - containerPort: 3306
          name: mysql
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: MYSQL_ROOT_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: MYSQL_DATABASE
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: MYSQL_PASSWORD
        volumeMounts:
        - name: mysql-storage
          mountPath: /var/lib/mysql
  volumeClaimTemplates:
  - metadata:
      name: mysql-storage
    spec:
      accessModes:
      - ReadWriteOnce
      resources:
        requests:
          storage: 5Gi
      storageClassName: mysql-storage-class
---
# ================================
# MySQL Headless Service
# Needed for StatefulSet
# ================================
apiVersion: v1
kind: Service
metadata:
  name: mysql
  namespace: ashapp
spec:
  ports:
  - port: 3306
    name: mysql
  clusterIP: None
  selector:
    app: mysql



DOCKER_HUB_USERNAME

DOCKER_HUB_ACCESS_TOKEN




kubectl get namespace ashapp -o json > ashapp-latest.json

nano ashapp-latest.json

make like this: -
"spec": {
  "finalizers": []
}

kubectl replace --raw "/api/v1/namespaces/ashapp/finalize" -f ./ashapp-latest.json

kubectl get ns

rm ashapp-latest.json

cd backend

docker build -t ashwanth01/flask-app:latest .

docker push ashwanth01/flask-app:latest


docker build -t ashwanth01/deployer-app:latest -f deployer-dockerfile .

docker push ashwanth01/deployer-app:latest


cd history-services

docker build -t ashwanth01/history-service:latest .

docker push ashwanth01/history-service:latest


cd ../database

docker build -t ashwanth01/flask-monitor:latest .

docker push ashwanth01/flask-monitor:latest


cd ../history-service

docker build -t ashwanth01/history-service:latest .

docker push ashwanth01/history-service:latest



docker build -t ashwanth01/ashapp-backend:latest .

docker run -d --name ashapp-backend \
  -p 5000:5000 \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v $(which docker):/usr/bin/docker \
  -v $(which kubectl):/usr/bin/kubectl \
  -v $HOME/.kube:/home/appuser/.kube \
  -e KUBECONFIG=/home/appuser/.kube/config \
  --user root \
  ashwanth01/ashapp-backend:latest






docker buildx build --platform linux/amd64 -t ashwanth01/history-service:latest .







