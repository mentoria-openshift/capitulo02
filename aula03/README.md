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
      image: registry.redhat.io/openshift4/ose-ogging-eventrouter:v4.3 # 6
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

São defiinidas `labels`, armazenadas em chave-valor, que servem como metadados para identificar o seu pod. 

Políticas de reinício (definidas na chave `restartPolicy`) também são definidas na configuração do pod, e servem para definir como o OCP responde aos containers do pod definido quando ele morre. O container pode ser reiniciado caso tenha sido removido do pod com sucesso usando a estratégia `Always`. Pode ser reiniciado quando ocorre um problema com a estratégia `OnFailure`. Ou, caso não seja requerido que os containers do pod reiniciem em caso nenhum, pode-se usar a estratégia `Never`.

Um contexto de segurança (chave `securityContext`) também é definido caso os containers do pod precisem executar com permissões especiais. Por padrão, aplicações não executam como root e sim com um usuário aleatório gerado na criação do pod, mas isso pode ser alterado usando políticas de segurança, o que permite que aplicações sejam executadas com qualquer usuário definido nela, inclusive root. 

É possível definir quantos e quais containers comporão o pod criado. A chave `containers` é um array que recebe um ou mais containers para aquele pod, onde são definidas coisas como variáveis de ambiente daquele container, a imagem usada para criá-lo, portas utilizadas e volumes compartilhados.

Para que volumes compartilhados possam ser usados nos pods e em seus containers, eles precisam ser definidos da declaração do pod. Uma área de armazenamento efêmera é criada para que os containers compartilhem-na.

## Serviços (svc)
Serviços servem como um load balancer interno no Kubernetes. São a ponte entre pods e clientes que conectam-se aos pods. Sets de endereços de redes e portas permitem que os pods sejam acessíveis consistentemente. Por padrão, IPs internos relaciondos ao cluster do OCP são criados para os serviços, permitindo que eles se conversem entre si. Para que o serviço seja acessado externamente, IPs externos e IPs de ingresso externos ao cluster são necessários para liberar este acesso. Um exemplo de definição de serviço:

```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: prometheus-example-app
  name: prometheus-example-app
  namespace: ns1
spec:
  ports:
  - port: 8080
    protocol: TCP
    targetPort: 8080
    name: web
  selector:
    app: prometheus-example-app
  type: ClusterIP
```

## Controle de replicação (rc)
O Controle de Replicação certifica que um número específico de réplicas de um pod está executando em dado momento. Em outras palavras, o RC certifica que todos os pods em execução são idênticos, a quantidade de pods disponíveis bate com a definida e que eles estejam sempre disponíveis para acesso.

Caso existam muitos pods, o controle de replicação mata os pods sobressalentes e mantém o número de pods em execução sempre o mesmo. Da mesma forma, caso o número de pods em execução seja menor que o número definido pelo desenvolvedor, o RC cria novos pods e os coloca em execução. É semelhante a um supervisor de processos, mas ao invés de supervisionar processos específicos em um único nó, o RC supervisiona diversos pods em diversos nós.

```yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    app: nginx
  template:
    metadata:
      name: nginx
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
```

O controle de replicação é definido para uma aplicação específica, e define o número de replicas de pod em execução, com seus próprios metadados. 

## Volumes persistentes (pv) e Solicitações de volumes persistentes (pvc)
Volumes persistentes (PV) são áreas comuns de armazenamento que são usadas para compartilhar arquivos entre containers e pods. É um dos recursos herdados do Kubernetes usados no OpenShift, e é uma forma de evitar o uso de armazenamento efêmero para que não haja perda de dados. 

Volumes persistentes são definidos por uma API, e representam um espaço para armazenamento no cluster, provisionado pelo administrador do cluster ou dinamicamente pelo objeto StorageClass. São volumes que são inseridos em pods, e que tem seu próprio ciclo de vida, independente de quaisquer pods que os usem.

Para que os volumes sejam usados, é necessário que haja uma solicitação de volume persistente (PVC) vinculada a ele. PVCs são específicos de projetos, e são criados e usados pelos próprios desenvolvedores como uma forma de usar um PV definido. PVs não são específicos de projetos, apesar de os PVCs serem, e, por isso, é possível criar um PVC em qualquer projeto do cluster, vinculado a qualquer PV existente no cluster. 

A relação entre PV e PVC é de 1 para 1. Uma vez que um PV foi vinculado a um PVC, ele não poderá ser vinculado a outro. Isso vincula o PV existente a um único projeto.

Um PVC libera uma quantidade específica de espaço de armazenamento, e tem o modo de acesso especificado. O PVC é automaticamente vinculado a um PV que bate com a descrição dele, e, caso o PV não existe, um é criado automaticamente. Exemplo de definição de PV e PVC:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: block-pv
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  volumeMode: Block 
  persistentVolumeReclaimPolicy: Retain
  fc:
    targetWWNs: ["50060e801049cfd1"]
    lun: 0
    readOnly: false

apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: block-pvc
spec:
  accessModes:
    - ReadWriteOnce
  volumeMode: Block 
  resources:
    requests:
      storage: 10Gi
```

## Mapa de configuração (cm) e segredos (secret)
Aplicações normalmente precisam de parâmetros de configuração, que são feitos por arquivos, argumentos de comandos e variáveis de ambiente. No OCP, essas informações são mantidas fora da imagem para manter as aplicações em container portáveis. 

É aí que os mapas de configuração entram. Eles mantêm a aplicação sem conhecimento da plataforma do OpenShift, e serve para manter informações guardadas num formato chave-valor para que elas sejam inseridas em recursos e aplicações do OpenShift. É possível armazenar grandes blocos de informação num mapa de configuração, como um objeto JSON completo. Mapas de configuração existem dentro de um projeto específico onde foram criados, e podem ser usados somente por recursos daquele projeto. 

Semelhante aos mapas de configuração, o OpenShift fornece segredos. Funcionam da mesma forma e servem também para armazenar informações que serão inseridas em recursos e aplicações, mas especificamente para informações sensíveis como senhas, chaves e credenciais. 

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: special-config 
  namespace: default 
data:
  special.how: very 
  special.type: charm

apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque 
data:
  username: dXNlci1uYW1l
  password: cGFzc3dvcmQ=
```

## Configuração de implantação (dc)
Implantações (deployment) e configurações de implantação (DC) são recursos do OpenShift que fornecem dois métodos similares para gerenciar aplicações. Esses recursos descrevem o estado desejado de um componente da aplicação, como um pod. DCs envolvem um ou mais controles de replicação, e definem a forma como a aplicação será implantada no ambiente. Por isso, também incluem sets de réplicas dos pods.

Usar configurações de implantação nas suas aplicações torna tudo mais flexível, pois é possível alterar a imagem usada numa implantação de uma aplicação já existe, alterar informações específicas dos pods da aplicação como variáveis de ambientes e mapas de configuração, além de portas. Até o OCP 4.4, aplicações eram criadas com uma configuração de implantação por padrão, mas a partir da versão 4.5 do OCP, novas aplicações são criadas somente com implantações, e é necessário fornecer um flag de criação de um DC para que ele seja criado.

Ao criar uma configuração de implantação, um controle de replicação é criado para representar seu pod. Caso a configuração seja alterada, um novo controle de replicação é criado com a última imagem do pod, e o processo de implantação se inicia para escalar o novo controle de replicação no lugar do antigo.

```yaml
apiVersion: v1
kind: DeploymentConfig
metadata:
  name: frontend
spec:
  replicas: 5
  selector:
    name: frontend
  template:
    metadata:
      labels:
        name: frontend
    spec:
      containers:
        - name: helloworld
          image: openshift/origin-ruby-sample
  triggers:
  - type: ConfigChange 
  - imageChangeParams:
      automatic: true
      containerNames:
      - helloworld
      from:
        kind: ImageStreamTag
        name: hello-openshift:latest
    type: ImageChange  
  strategy:
    type: Rolling

apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-openshift
spec:
  replicas: 1
  selector:
    matchLabels:
      app: hello-openshift
  template:
    metadata:
      labels:
        app: hello-openshift
    spec:
      containers:
      - name: hello-openshift
        image: openshift/hello-openshift:latest
        ports:
        - containerPort: 80
```

## Referências
* [Documentação do OpenShift](https://docs.openshift.com/)
* [Documentação do Kubernetes](https://kubernetes.io/docs)
* [Uso de Pods](https://docs.openshift.com/container-platform/4.5/nodes/pods/nodes-pods-using.html)
* [Monitoramento de serviços](https://docs.openshift.com/container-platform/4.5/monitoring/monitoring-your-own-services.html)
* [Controle de replicação](https://kubernetes.io/docs/concepts/workloads/controllers/replicationcontroller/)
* [Armazenamento persistente](https://docs.openshift.com/container-platform/4.5/storage/understanding-persistent-storage.html)
* [Mapas de configuração](https://docs.openshift.com/container-platform/4.5/builds/builds-configmaps.html)
* [Configurações de implantação](https://docs.openshift.com/container-platform/4.5/applications/deployments/what-deployments-are.html)
* [Estratégias de implantação](https://docs.openshift.com/container-platform/4.5/applications/deployments/deployment-strategies.html)

----
<p align="center"><a href="../aula02">❮ Aula anterior</a> | <a href="/capítulo03">Próximo capítulo ❯</a></p>