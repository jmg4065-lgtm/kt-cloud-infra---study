apiVersion: apps/v1
kind: Deployment
metadata:
  name: web
  namespace: team-4c
spec:
  replicas: 1
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
        - name: web
          image: fao0711/php-app:latest
          ports:
            - containerPort: 80

