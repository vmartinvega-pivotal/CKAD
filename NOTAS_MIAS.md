* Minios (en el pasado se llamaba asi) y nodes es lo mismo (workers), es donde kubernetes lanza los containers (los encapsula en pods)
* master es el responsable de la monitorizacion de los workiers, de balancear la carga, de arrancar aplicaciones en los workers, donde la informacion de configuracion se guarda.
* Componentes
API Server - frontend para todo el mundo para acceso a kubernetes cluster
etcd - keystore , distributed reliable key value store for kubernetes, para almacenar todos los datos para manejar el cluster.
Implementa los locks para que no haya conflictos entre los masters. Almacena la informacion de una manera distribuida entre todos los nodos del cluster.
kubelet - un agente que ejecuta en cada nodo del cluster. Responsable que los containers estan ejecutando como se espera en los nodos.
container runtine - software para ejecutar los contenedores (docker), aunque hay otras opciones tambien.
controller - El cerebro de la orquestacion
scheduller - Responsable de distribuir trabajo o container entre los nodos del cluster, busca contenedores nuevos creados y los asigna  los nodos. 

WORKERS (MINIONS) - Los contenedores son hosted
 * container runtime (docker normalment). Otros estan disponibles como Rocket (rkt) or CRI-O
 * kubectl - habla con el master para proveerle de informacion de health y hacer las operaciones solicitadas por el master

MASTER
 * kube api server (si tiene esto le hace ser master)
 * etcd
 * controller (procesos que monitorizan los objectos de kubernetes y responden accordingly, por ejemplo replication controller(old) replica-set (new))
 * scheduller

 KUBE CONTROL TOOL (kubectl) command line to interact with kubernetes

 PODS.

 Lo normal es tener un contenedor por POD, y solo hay un contenedor de una clase en un POD pero puede haber mas contenedores de otra clase en el POD,como por ejemplo un contenedor que realiza alguna operacion para un contenedor (helper container). Se pueden comunicar el uno con el otro por localhost, ya que comparten la misma red

 Todos los objetos en kubernetes (yaml files) tienen cuatro secciones:
 * apiVersion: (specific to what we are creating)
 * kind: 
 * metadata:
     name:
     labels:
       
 * spec: (specifications)

 ReplicaSet
 * la parte de especificaciones tiene tres partes principales: template (saber que pod hacer), replicas para saber cuantas debe mantener y selector para saber que pods elegir y monitorizar.  
