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
 * controller
 * scheduller

 KUBE CONTROL TOOL (kubectl) command line to interact with kubernetes