COMMANDS IN DOCKER
CMD, esta sentencia define el comando a ejecutar (con parametros y todo) cuando el contenedor starts
ENTRYPOINT, es el ejecutable que se ejecutara, y todo lo que se ponga en el command line al ejecutar el contenedor
(docker run ubuntu ...) se añadira al entrypoint.

Es decir, si tenemos CMD todo lo que se ponga en el command line al invocar el contenedor reemplazara lo que tenemos en CMD, en cambio con ENTRYPOINT lo que se ponga en el command line se añadir a la sentencia del entrypoint.

Si ENTRYPOINT and CMD son especificados en la imagen, al arrancar si no se añade nada en el command line , el CMD es añadido al ENTRYPOINT al ejecutar.

* En el caso de kubernetes, para pasar argumentos al contenedor a la hora de ejecutar, ira en el parametro **args** debajo del tag image:, este parametro es un array, por ejemplo ["10"]

* En el caso de que queramos sobreescribir el entrypoint entero y poner otro comando, pondremos el argumento **command** (en el mundo de docker lo hacíamos con --entrypoint)

**command** overwrites **ENTRYPOINT**
**args** overwrites **CMD**

* Para especificar un variable de entorno 


### ConfigMaps In PODs

https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/

kubectl create configmap --from-literal= --from-literal=

#### Use ConfigMap-defined environment variables in Pod commands
```
spec:
  containers:
    - name: test-container
      image: k8s.gcr.io/busybox
      command: [ "/bin/sh", "-c", "env" ]
      env:
        # Define the environment variable
        - name: SPECIAL_LEVEL_KEY
          valueFrom:
            configMapKeyRef:
              # The ConfigMap containing the value you want to assign to SPECIAL_LEVEL_KEY
              name: special-config
              # Specify the key associated with the value
              key: special.how
```
#### Configure all key-value pairs in a ConfigMap as container environment variables
```
spec:
  containers:
    - name: test-container
      image: k8s.gcr.io/busybox
      command: [ "/bin/sh", "-c", "env" ]
      envFrom:
      - configMapRef:
          name: special-config
```

#### Add ConfigMap data to a Volume
```
spec:
  containers:
    - name: test-container
      image: k8s.gcr.io/busybox
      command: [ "/bin/sh", "-c", "ls /etc/config/" ]
      volumeMounts:
      - name: config-volume
        mountPath: /etc/config
  volumes:
    - name: config-volume
      configMap:
        # Provide the name of the ConfigMap containing the files you want
        # to add to the container
        name: special-config
```

### Secrets
A quick note about Secrets!
Remember that secrets encode data in base64 format. Anyone with the base64 encoded secret can easily decode it. As such the secrets can be considered as not very safe.

The concept of safety of the Secrets is a bit confusing in Kubernetes. The kubernetes documentation page and a lot of blogs out there refer to secrets as a "safer option" to store sensitive data. They are safer than storing in plain text as they reduce the risk of accidentally exposing passwords and other sensitive data. In my opinion it's not the secret itself that is safe, it is the practices around it. 

Secrets are not encrypted, so it is not safer in that sense. However, some best practices around using secrets make it safer. As in best practices like:

Not checking-in secret object definition files to source code repositories.

Enabling Encryption at Rest for Secrets so they are stored encrypted in ETCD. 



Also the way kubernetes handles secrets. Such as:

A secret is only sent to a node if a pod on that node requires it.

Kubelet stores the secret into a tmpfs so that the secret is not written to disk storage.

Once the Pod that depends on the secret is deleted, kubelet will delete its local copy of the secret data as well.

Read about the protections and risks of using secrets here



Having said that, there are other better ways of handling sensitive data like passwords in Kubernetes, such as using tools like Helm Secrets, HashiCorp Vault. I hope to make a lecture on these in the future.

### Security

* Containers are not totally insolated from the host, they share the same kernel
* El usuario root no tiene todas las capabilities y solo tiene un subconjunto de ellas (/usr/include/linux/capability.h), para sobreescribir este comportamiento se puede hacer lo siguiente cuando se ejecuta el container
  - Añadir capability --cap-add MAC_ADMI
  - Quitar capability --cap-drop KILL

  o podemos definir con que usuario ejecutar --user

  Los contextos de seguridad se pueden añadir a nivel de contenedor en el POD o a nivel ve POD, la diferencia es que a nivel de POD afectará a todos los contenedores en el POD.

  Para añadir security context a nivel de POD habrá que hacerlo dentro de spec al nivel de conatiners:
  ```
  spec:
    securityContext:
      runAsUser: 1000
      runAsGroup: 3000
      fsGroup: 2000
      capabilities:
        add: ["NET_ADMIN", "SYS_TIME"]
  ```
  or
  ```
  spec:
    containers:
      - name: sec-ctx-4
        image: gcr.io/google-samples/node-hello:1.0
        securityContext:
          capabilities:
            add: ["NET_ADMIN", "SYS_TIME"]
  ```

  ### Service Accounts
  hay dos tipos de cuentas en kubernetes
  * user account  (usadas por humanos) - por ejemplo un administrador para hacer tareas de administracion en kubernetes o un desarrollador.
  * service account (usadas por maquinas), por ejemplo jenkins usa a service account to desplegar en kubernetes
  Cuando se crea (kubectl create serviceaccount test), create el objeto y un token que lo mete dentro de un secret, este token es el que se utiliza en una llamada externa como token bearer para interactuar. Ademas luego se le puede añadir permisos a esta cuenta.

  Cada namespace tiene su default service account, que es create automaticamente.

  Este es montado automaticamente como un volumen cada vez que se crea un pod en (/var/run/secrets/kubernetes.io/serviceaccount) si no se ha especificado ninguna otra. Tambien se puede decir que no monte la de por defecto con 
  **automountServiceAccountToken: false**

```
  spec:
    containers:
      - name: sec-ctx-4
        image: gcr.io/google-samples/node-hello:1.0
        securityContext:
          capabilities:
            add: ["NET_ADMIN", "SYS_TIME"]
      serviceAccount: service-account-test
  ```

### Resources
Se consume memoria, disco y cpu, es el scheduller el que decide a que nodo va a ir el POD basandose en los recursos que tiene el nodo.

Resource requests for a container dentro de un Pod: 0.5 CPU, 256MB Memory (es lo minimo).
Limit resources para un container dentro de un Pod, es decir las requests y los limites se especifican a nivel de container.
```
spec:
  containers:
  - name: memory-demo-ctr
    image: polinux/stress
    resources:
      limits:
        memory: "200Mi"
        cpu: "1"
      requests:
        memory: "100Mi"
        cpu: 750m
    command: ["stress"]
    args: ["--vm", "1", "--vm-bytes", "150M", "--vm-hang", "1"]
```
Un pod no puede consumir mas cpu de la que tiene configura en el limite, no ocurre lo mismo con la memoria, que si que puede consumir mas, pero si constantemente consume mas, el pod de terminara automaticamente.

### Tains and Tolerations
Tienen que ver con que pods pueden ejecutar en que nodos.

**taints** are set **on nodes**
**tolerations** are set **on pods**

kubectl taint nodes node-name kay=vaulue:taint-effect
El taint effect significa que les pasa a los PODs que no toleran este taint.
3 taint effects
* NoSchedule - the pods will not be scheduled on the node 
* PreferNoSchedule - el systema intentara evitar poner el POD en el node, pero no es garantizado
* NoExecute - New PODs will not be scheduled en el nodo y los PODs existentes en el nodo seran sacados (evicted) si no toleran el taint.
```
kubectl taint nodes node1 app=blue:NoSchedule
```
Tolerations in pods
```
spec:
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: IfNotPresent
  tolerations:
  - key: "key"
    operator: "Exists"
    effect: "NoSchedule"
```
```
tolerations:
- key: "key"
  operator: "Equal"
  value: "value"
  effect: "NoSchedule"
```
```
tolerations:
- key: "key"
  operator: "Exists"
  effect: "NoSchedule"
```

**LAS TOLERATIONS VAN ENTRE DOBES COMILLAS**

Los taints and tolerations no hacen que un pod ejecute en un node, si no que previenen que ciertos pods que no cumplen la tolerancia al taint sean ejecutados en ese nodo, pero si tienen la tolerancia pueden estar en otro nodo, no el que tiene el taint. !!!! OJO !!!!

Para lo contrario, es decir, que un POD ejecute en un nodo esta el **affinity**

Finalmente cabe destacar que los master nodes al tiempo de instalacion y configuracion se les pone un taint automaticamente pare prevenir que los PODs sean alojados alli.

### Node Selectors
Indica en que nodos se puede ejecutar un POD. Para ello primero hay que labelar los nodos
```
kubectl label nodes <node-name> <label-key>=<label-value>
```
```
kubectl label nodes <node-name> <label-key>=<label-value>
```
Y posteriormente se tiene que poner el nodeSelector al POD
```
spec:
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: IfNotPresent
  nodeSelector:
    disktype: ssd
```
¡¡ El nodeSelector tiene limitaciones, porque por ejemplo no podemos poner en cualquier node que no sea SMALL !!, para eso esta el Node Affinity 

### Node Affinity
