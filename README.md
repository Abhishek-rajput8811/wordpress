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
