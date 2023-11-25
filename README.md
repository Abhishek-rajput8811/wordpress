# wordpress
#wordpress yaml files
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql
  labels:
    app: mysql
spec:
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
          image: mysql:5.7
          env:
            - name: MYSQL_ROOT_PASSWORD
              value: mypassword
            - name: MYSQL_DATABASE
              value: wordpress
            - name: MYSQL_USER
              value: wordpressuser
            - name: MYSQL_PASSWORD
              value: wordpress
          ports:
            - containerPort: 3306
          volumeMounts:
            - name: mysql-persistent-storage
              mountPath: /var/lib/mysql

      volumes:
        - name: mysql-persistent-storage  # Ensure the volume name matches
          persistentVolumeClaim:
            claimName: mysql-pv-claim  # Ensure the claimName matches

----------------------------------------------------------------------------------------------------------------------------------------------------------

#mysql-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: mysql
  labels:
    app: mysql
spec:
  ports:
    - protocol: TCP
      port: 3306
      targetPort: 3306
  selector:
    app: mysql
-------------------------------------------------------------------------------------------------------------------------------

#mysql-persistent-volume.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mysql-persistent-storage
spec:
  storageClassName: manual
  capacity:
    storage: 3Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data/mysql"
----------------------------------------------------------------------------------------------------------

#mysql-persistent-volume-claim.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 3Gi

----------------------------------------------------------------------------------------------------------------
# wordpress-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wordpress
  labels:
    app: wordpress
spec:
  replicas: 1
  selector:
    matchLabels:
      app: wordpress
  template:
    metadata:
      labels:
        app: wordpress
    spec:
      containers:
        - name: wordpress
          image: wordpress:latest
          env:
            - name: WORDPRESS_DB_HOST
              value: mysql
            - name: WORDPRESS_DB_USER
              value: wordpressuser
            - name: WORDPRESS_DB_PASSWORD
              value: wordpress
            - name: WORDPRESS_DB_NAME
              value: wordpress
          ports:
            - containerPort: 80
          volumeMounts:
            - name: wordpress-persistent-storage
              mountPath: /var/www/html

      volumes:
        - name: wordpress-persistent-storage  # Ensure the volume name matches
          persistentVolumeClaim:
            claimName: wordpress-pv-claim  # Ensure the claimName matches
-----------------------------------------------------------------------------------------------------------------------------------------------

#wordpress-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: wordpress
  labels:
    app: wordpress
spec:
  type: NodePort
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30000
  selector:
    app: wordpress
--------------------------------------------------------------------------------------------------------------------------------------------
#wordpress-persistent-volume.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: wordpress-persistent-storage
spec:
  storageClassName: manual
  capacity:
    storage: 3Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data/wordpress"
----------------------------------------------------------------------------------------------------------------------------
#wordpress-persistent-volume-claim.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: wordpress-pv-claim
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 3Gi
