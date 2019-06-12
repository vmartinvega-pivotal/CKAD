Useful information for CKAD certificatoin

### Core Info

### NodePort Service

* targetPort: port in the POD where the application is listening for connections
* port: Port in the service
* nodePort: port in the node itself (30000 - 32767)

Create a NodePort with kubectl
```
kubectl create service nodeport flask --tcp=81:81 --node-port=30000
kubectl get service flask -o yaml >> flask
# Edit and change selector or any other value (mainly the selector)
kubectl apply -f flask
```

![Nodeport](docs/nodeport.jpg?raw=true "Nodeport")