Useful information for CKAD certificatoin

### Core Info

Export the export **KUBE_EDITOR="nano"** to use nano instead of vim to edit inline.

### Urls
https://medium.com/@iizotov/exam-notes-ckad-c1c4f9fb9e73<br>



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

### ClusterIp Service
Used to group different pods as a single access point (different backends acting as a one)

Pod
spec:
  containers:
    - name: ubuntu
      image: ubuntu
      command: ["command"] --> overrides the ENTRYPOINT in the dockerfile
      args: ["value"] --> overrrides the CMD in the dockerfile

ENTRYPOINT is the command to execute in the docker file
CMD is the default command, if there are entrypoint and CMD the command to execute will be ENTRYPOING CMD
if only CMD will be that the command to execute


#### Load configMap as env variables

```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    imagePullPolicy: IfNotPresent
    name: nginx
    resources: {}
    envFrom: # different than previous one, that was 'env'
    - configMapRef: # different from the previous one, was 'configMapKeyRef'
        name: anotherone # the name of the config map
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

#### Load configMap as volume
```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx2
  name: nginx2
spec:
  containers:
  - image: nginx
    name: nginx2
    volumeMounts:
      - name: config-volume
        mountPath: /etc/lala
    resources: {}
  volumes:
    - name: config-volume
      configMap:
        # Provide the name of the ConfigMap containing the files you want
        # to add to the container
        name: cmvolume
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

configmap

apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    imagePullPolicy: IfNotPresent
    name: nginx
    resources: {}
    env:
    - name: option # name of the env variable
      valueFrom:
        configMapKeyRef:
          name: options # name of config map
          key: var5 # name of the entity in config map
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}

    env:
    - name: option # name of the env variable
      valueFrom:
        configMapKeyRef:
          name: options # name of config map
          key: var5 # name of the entity in config map



    envFrom: # different than previous one, that was 'env'
    - configMapRef: # different from the previous one, was 'configMapKeyRef'
        name: anotherone # the name of the config map

