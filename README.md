Deploying a Node.js Application in Kubernetes

1️⃣ Create a Node.js Application

Create a simple Node.js application:

app.js

const express = require('express')
const app = express()
const port = 3000
app.get('/', (req, res) => {
    res.send('Hello World from ITI 44')
  })
  
  app.listen(port, () => {
    console.log(`Example app listening at http://localhost:${port}`)
  })

package.json
{
  "name": "nodeapp",
  "version": "1.0.0",
  "description": "",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "express": "^4.17.1"  }
}

2️⃣ Dockerize the Application

Create a Dockerfile to containerize the Node.js app.
Dockerfile

FROM node:12
COPY nodeapp /nodeapp
WORKDIR /nodeapp
RUN npm install
CMD ["node", "/nodeapp/app.js"]
Build and Push the Docker Image

Run the following commands:

docker build -t wafaaalaa/k8s-webserver .
docker push wafaaalaa/k8s-webserver

3️⃣ Create Kubernetes Deployment and Service

Now, create Kubernetes YAML manifests for better control.
deployment.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: webserver-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: webserver
  template:
    metadata:
      labels:
        app: webserver
    spec:
      containers:
      - name: webserver
        image: wafaaalaa/k8s-webserver:latest
        ports:
        - containerPort: 3000

service.yaml

apiVersion: v1
kind: Service
metadata:
  name: webserver-service
spec:
  selector:
    app: webserver
  type: NodePort
  ports:
    - protocol: TCP
      port: 3000
      targetPort: 3000
      nodePort: 30007  # This must be in the range 30000-32767

4️⃣ Deploy to Kubernetes

Apply the configuration using kubectl:

kubectl apply -f deployment.yaml
kubectl apply -f service.yaml

Check the status:

kubectl get pods
kubectl get svc

5️⃣ Access the Application

Find your minikube or cluster node IP:

minikube service webserver-service --url

Or manually access:

http://<NODE_IP>:30007

✅ Summary

    Create a Node.js app.
    Dockerize it (Dockerfile).
    Push the image to Docker Hub.
    Deploy the app using Kubernetes Deployment & Service.
    Expose it via NodePort.
