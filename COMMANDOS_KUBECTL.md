kubectl cluster-info
kubectl get nodes

kubectl run nginx --image nginx (crea replica-set, deployment y pod)
kubectl get pods --all-namespaces ( en todos los namespaces)
kubectl get pods -o

* Para crear o ejecutar un fichero yaml en kubernetes se usu el comando apply o create
kubectl apply -f <fichero>
kubectl create -f <fichero>
para reemplazar algo que ya esta creado y modificamos el fichero utilizamos el comando replace
kubectl replace -f <fichero>
kubectl scale (comando para escalar el numero de pods en un replicaset)

kubectl options (para listar todas las opciones que aplican a todos los comandos)
 Entre las mas destacadas:
 --cluster
 --namespace
 --context

 kubectl cluster-info
 kubectl config (set-context use-context ...)

 Para sacar la informacion de los generators:
 kubectl run --help

## Editar pod

A Note on Editing Existing Pods
In any of the practical quizzes if you are asked to edit an existing POD, please note the following:

If you are given a pod definition file, edit that file and use it to create a new pod.

If you are not given a pod definition file, you may extract the definition to a file using the below command:

kubectl get pod <pod-name> -o yaml > pod-definition.yaml

Then edit the file to make the necessary changes, delete and re-create the pod.

Use the kubectl edit pod <pod-name> command to edit pod properties.

* Cuando se crea un ruta para acceder a un servicio la ruta que se crea tiene el siguiente formato:
 - service-name.namespace.service.domain, ejemplo: db-service.development.svc.cluster.local

* En el fichero yaml el namespace se pone en la seccion de metadata, por ejemplo:
 - metadata:
     namespace: development
tambien se puede poner al tiempo de creacion con el tag --namespace=development

* Para modificar el contexto en el que se trabaja y cambiar el namespace:
 - kubectl config set-context $(kubectl config current-context) --namespace=default

 



