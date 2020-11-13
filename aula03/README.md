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

## Referências
* [Documentação do OpenShift](https://docs.openshift.com/)
* [Documentação de Pods](https://docs.openshift.com/container-platform/4.5/nodes/pods/nodes-pods-using.html)
* [Monitoramento de serviços](https://docs.openshift.com/container-platform/4.5/monitoring/monitoring-your-own-services.html)

----
<p align="center"><a href="../aula02">❮ Aula anterior</a> | <a href="/capítulo03">Próximo capítulo ❯</a></p>