<p align="center"><a href="../aula02">❮ Aula anterior</a> | <a href="/capítulo03">Próximo capítulo ❯</a></p>
<br/>

# Aula 3 - Recursos do OpenShift
O OpenShift é formado por diversos tipos de recursos, que compõem suas aplicações. A definição dos recursos do OCP são baseadas na sintaxe e no formato usado no Kubernetes, e podem ser definidos usando YAML ou JSON. É possível criar recursos também pela linha de comando ou pela interface web, e a definição, feita em YAML por padrão, fica disponível para alteração. Vamos estudar mais a fundo os recursos detalhados nas aulas anteriores. 

## Pods
Como definimos anteriormente, um pod é a unidade mais básica do Kubernetes e do OpenShift. É uma coleção de containers executando em conjunto, e são cópias da sua aplicação. Quando fazemos escalabilidade horizontal, isto é, aumentamos o número de instâncias de uma aplicação, estamos aumentando o número de pods dela. É possível, também, definir a quantidade de containers por pod, e a cada novo pod criado para sua aplicação, o número de containers vai também sendo incrementado. A definição de um pod pode ser feita por YAML ou por JSON.

Cada pod tem seu próprio endereço IP que servirá para identifica-lo numa rede, e pode expor portas próprias e também expor espaços específicos dentro dos containers que o pod contém. Cada pod tem seu ciclo de vida, que começa com a definição dele, continuada com a execução dele que permanece até que ele seja morto por qualquer razão que seja. A forma como os pods são removidos depende de políticas de implantação definidas pelos desenvolvedores da aplicação.

Pods são imutáveis, e não podem ser alterados enquanto rodam. Caso a definição de pods seja alterada, todos os pods em execução são parados e removidos, e os pods novos terão as configurações novas em execução.

A aplicação criada em um pod é feita a partir de uma imagem, seja ela hospedada no registro interno do OpenShift ou num registro de imagens externo ao cluster. Um exemplo de definição de pod.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: example
  namespace: default
  selfLink: /api/v1/namespaces/default/pods/example
  uid: 5cc30063-0265780783bc
  resourceVersion: '165032'
  creationTimestamp: '2019-02-13T20:31:37Z'
  labels:                  
    app: hello-openshift
  annotations:
    openshift.io/scc: anyuid
spec:
  restartPolicy: Always      
  serviceAccountName: default
  imagePullSecrets:
    - name: default-dockercfg-5zrhb
  priority: 0
  schedulerName: default-scheduler
  terminationGracePeriodSeconds: 30
  nodeName: ip-10-0-140-16.us-east-2.compute.internal
  securityContext:     
    seLinuxOptions:
      level: 's0:c11,c10'
  containers:          
    - resources: {}
      terminationMessagePath: /dev/termination-log
      name: hello-openshift
      securityContext:
        capabilities:
          drop:
            - MKNOD
        procMount: Default
      ports:
        - containerPort: 8080
          protocol: TCP
      imagePullPolicy: Always
      volumeMounts:             
        - name: default-token-wbqsl
          readOnly: true
          mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      terminationMessagePolicy: File
      image: registry.redhat.io/openshift4/ose-ogging-eventrouter:v4.3 
  serviceAccount: default     
  volumes:                    
    - name: default-token-wbqsl
      secret:
        secretName: default-token-wbqsl
        defaultMode: 420
  dnsPolicy: ClusterFirst
status:
  phase: Pending
  conditions:
    - type: Initialized
      status: 'True'
      lastProbeTime: null
      lastTransitionTime: '2019-02-13T20:31:37Z'
    - type: Ready
      status: 'False'
      lastProbeTime: null
      lastTransitionTime: '2019-02-13T20:31:37Z'
      reason: ContainersNotReady
      message: 'containers with unready status: [hello-openshift]'
    - type: ContainersReady
      status: 'False'
      lastProbeTime: null
      lastTransitionTime: '2019-02-13T20:31:37Z'
      reason: ContainersNotReady
      message: 'containers with unready status: [hello-openshift]'
    - type: PodScheduled
      status: 'True'
      lastProbeTime: null
      lastTransitionTime: '2019-02-13T20:31:37Z'
  hostIP: 10.0.140.16
  startTime: '2019-02-13T20:31:37Z'
  containerStatuses:
    - name: hello-openshift
      state:
        waiting:
          reason: ContainerCreating
      lastState: {}
      ready: false
      restartCount: 0
      image: openshift/hello-openshift
      imageID: ''
  qosClass: BestEffort
```

Neste exemplo, temos a definição de um pod com um container chamado hello-openshift. Para que a aplicação seja configurada e rode corretamente, precisaremos definir a imagem base para a criação dela, variáveis de ambiente da aplicação e as portas que deverão ser expostas. Mas além desses dados cruciais, podemos definir volumes compartilhados, metadados (como anotações e tags) e segredos.

## Referências
* [Documentação do OpenShift](https://docs.openshift.com/)

----
<p align="center"><a href="../aula02">❮ Aula anterior</a> | <a href="/capítulo03">Próximo capítulo ❯</a></p>