<p align="center"><a href="../aula02">Próxima aula ❯</a></p>
<br/>

# Aula 1 - Introdução ao Kubernetes e ao OpenShift
Bem-vindo ao capítulo 2 do curso de OpenShift! Nesta aula, aprenderemos sobre as bases e objetivos do OpenShift. Como funciona sua arquitetura e seus objetivos, e como o Kubernetes foi usado como base para a construção do OpenShift. 

## A era da virtualização
Conforme discutimos no capítulo anterior, nas primeiras eras da computação distribuída, sistemas eram hospedados em grandes data centers mantidos pelas próprias empresas. Isso causava diversos problemas de escalamento, escalonamento, distribuição, comunicação, e, consequentemente, de custos. 

As aplicações eram monolíticas, isto é, eram grandes aplicações, com código complexo, com todas as dependências e módulos instalados no mesmo servidor, que normalmente era uma máquina física. Usando esta arquitetura, quando é necessário escalar mais recursos para uso, além de ser uma tarefa complexa, tudo se tornava muito caro. Várias aplicações eram hospedadas num mesmo servidor, e recursos acabavam não sendo utilizados por todo seu potencial. A otimização era fraca, e o processo caro. A primeira solução para este problema foi quebrar as aplicações em módulos, e rodar partes delas em servidores diferentes. Mas isso ainda mantia o processo mais caro do que o necessário. 

Por conta disso, veio a virtualização. Recursos físicos eram compartilhados em sistemas virtuais, o que criava uma nova camada de segurança, isolando o ambiente de uma aplicação das outras. Isso tornou a otimização de custos e recursos mais efetiva, pois um ambiente virtualizado pode facilmente ter seus recursos escalados caso necessário, com menos subutilização por conta da alocação de recursos onde não existe necessidade. Cada VM roda como um computador completo, com recursos de processamento e memória sendo compartilhados.

Os containers nasceram da ideia das máquinas virtuais, mas com suas diferenças. Não dependem de um hypervisor, e destacam por não serem máquinas completas, apesar de terem recursos físicos compartilhados. São portáveis por não serem ligados à infraestrutura abaixo deles.

## O que é o Kubernetes?
O Kubernetes (conhecido como **kube** ou **k8s**) é uma plataforma de código aberto, originalmente desenvolvida pela Google, e agora mantida pela Cloud-Native Computing Foundation (CNCF), para gerenciamento de workloads e serviços containerizados. Foi transformado em em projeto código aberto pela Google em 2014, depois de 15 anos de desenvolvimento ativo executando códigos em ambiente de produção. O nome vem do grego, que significa "piloto".

O Kubernetes é uma plataforma de orquestração de containers, que automatiza vários dos processos manuais de implantação, compilação, escalamento, escalonamento, containerização e comunicação de aplicações. Serve para criar aplicações em nuvens públicas, privadas e híbridas, o que o tornou importante no mercado de nuvens. É ideal para aplicações distribuídas em microsserviços, pois possui todas as ferramentas e recursos necessários para que essas aplicações funcionem em conjunto. 

No mundo real, implantação de aplicações com containers é uma tarefa trabalhosa. O Kubernetes fornece um serviço PaaS (Plataforma como Serviço) para facilitar a vida dos desenvolvedores nesse (e em muitos outros) quesitos, dando suporte a diversas estratégias de implantação. Assume a responsabilidade do escalamento e tratamento de falhas das aplicações, além de gerenciar todo o ciclo de vida de um sistema, desde a compilação até a implantação. Alguns recursos suportados pelo Kubernetes:

* Descoberta de serviço;
* Implantação canária, blue-green A/B etc;
* Empacotamento automático dos binários da aplicação;
* Reinício de containers após falhas;
* Orquestração de containers entre múltiplos hosts;
* Otimização do uso de hardware;
* Montagem e desmontagem de volumes conforme necessidade;
* Health checks e gerenciamento de saúde da aplicação;
* Telemetria e gerenciamento de métricas;
* Registro de imagens;
* Gerenciamento de segurança.

Enfim, a lista de recursos do Kubernetes é extensa. É uma ferramenta poderosíssima. 

## Como o Kubernetes funciona?

<figure>
  <img src="imagens/arquitetura-kubernetes.svg" alt="Imagem 1.1"/>
  <figcaption>Imagem 1.1 - Arquitetura do Kubernetes (original pela Red Hat, disponível no link de referências)</figcaption>
</figure>

O Kubernetes funciona a partir de um cluster, dividido em nós e no "plano de controle". Cada nó é uma máquina própria, que pode ser física ou virtual, e em cada nó do Kubernetes existem pods sendo executados, e em cada pod existem containers em execução. 

Os nós são responsáveis pela execução das suas aplicações, e são onde os serviços são implantados, dentro de pods. Pods são "caixas", que possuem containers em execução dentro deles, onde a sua aplicação está executando. 

O plano de controle é responsável pela manutenção do estado do cluster, e decidem em qual nós as aplicações serão executadas. É ele quem decide qual dos nós do cluster é a melhor opção para implantar sua aplicação. 

Isso é bom por evitar microgerenciamento de containers, pois o usuário não precisa se preocupar com como a aplicação será implantada e executada. O plano de controle toma conta disso. 

## O que é o OpenShift?
Usando a definição da própria Red Hat, pense no Kubernetes como o motor de um carro. Um motor pode funcionar sozinho, mas se torna apenas uma parte funcional de carro quando é conectado a uma transmissão e às rodas. Para que se torne totalmente funcional num ambiente de produção, é preciso de mais. Redes, segurança, monitoramento, logs, rotas... todo esse set de ferramentas é necessário para que a coisa se torne realmente útil. 

O OpenShift é o Kubernetes, mas para nível corporativo. Inclui todas as tecnologias e ferramentas necessárias que tornam o Kubernetes poderoso, e muito mais. O OpenShift é um componente central da plataforma, além de ser um produto certificado pela própria CNCF. 

Com o OpenShift, você pode criar novas aplicações containerizadas, hospedá-las e implementá-las na nuvem com escalabilidade, controle e orquestração que deixam toda a operação mais poderosa. 

## Como funciona o OpenShift

<figure>
  <img src="imagens/arquitetura-openshift.png" alt="Imagem 1.1"/>
  <figcaption>Imagem 1.2 - Arquitetura do OpenShift 3 (original pela Red Hat, disponível no link de referências)</figcaption>
</figure>

A imagem acima mostra em mais detalhes a arquitetura do OpenShift Container Platform (OCP). Apesar de a imagem descrever o OCP3, a arquitetura do OCP4 segue o mesmo formato, mas não usa o Red Hat Enterprise Linux (RHEL) em sua versão pura e nem o Atomic Host, e sim o Red Hat Enterprise Linux CoreOS (RHCOS), uma versão alterada do CoreOS (também conhecido como Container Linux), uma distribuição do Linux feita especialmente para execução de containers. Além da troca de sistemas operacionais dos nós, o OCP4 trocou o gerenciamento dos containers, que até a versão 3.11 usava o Docker. A partir do OCP 4.1, usa-se o CRI-O, totalmente compatível com OCI, para tal.

Cada nó do OpenShift roda no RHCOS, que usa o kernel do RHEL com SELinux habilitado por padrão para maior segurança. Pelo fato de o OCP rodar por cima do Kubernetes, cada nó tem o kubelet integrado para gerenciamento do nó. Os planos de controle do OCP4 também precisam usar o RHCOS, com o Ignition incluído, que serve para habilitar o gerenciamento das máquinas pelo cluster. 

Essas ferramentas juntas permitem que o OpenShift gerencie o sistema operacional da mesma forma que ele gerenciaria qualquer outra aplciação no cluster.

## Dicionário do OpenShift
O OCP usa ferramentas e recursos parecidos com os do Kubernetes, além dos próprios, tem sua própria forma de gerenciar e executar os recursos.

<figure>
  <img src="imagens/openshift-stack.png" alt="Imagem 1.1"/>
  <figcaption>Imagem 1.3 - A pilha do OCP4 (original por Container Journal, disponível no link de referências)</figcaption>
</figure>

* **RHCOS:** Distribuição Linux otimizada para containers onde o OCP roda.
* **CRI-O:** Uma implementação do Kubernetes Container Runtime Interface (CRI) com compatibilidade com o padrão OCI.
* **Kubernetes:** Gerencia o cluster de hosts que executam os containers, e como se comunicam.
* **Etcd:** Armazenamento chave-valor usado pelo Kubernetes para informações dos recursos do cluster.
* **Nó:** Um servidor que hospeda aplicações num cluster OCP, executado com o RHCOS.
* **Nó mestre:** Nó que gerencia o plano de controle do cluster OCP.
* **Nó de trabalho:** Também chamado de **nó de computação**, executa os workloads do cluster. Pods das aplicações são instalados nestes nós.
* **Recurso:** Qualquer componente gerenciado pelo OCP.
* **Rótulo:** Um par chave-valor designado a recurso do OCP. 
* **Projeto:** Também chamado de **namespace**, é o escopo dos recursos e aplicações. 

Além dos componentes que fazem o cluster funcionar, o OCP tem sua própria terminologia para recursos próprios, usados para compilar, implementar e gerenciar as aplicações e todo o seu ciclo de vida. Cada recurso tem um nome específico, e alguns recursos já serão familiares para pessoas que conhecem o Kubernetes.

* **Pod:** Uma coleção de containers que compartilham recursos. São a unidade básica do OpenShift e do Kubernetes.
* **Serviço (svc):** (service) Combinação de IPs e portas que fornecem acesso a pods. Conectam-se aos pods de forma round-robin.
* **Controle de replicação (rc):** (replication controller) Definição de como pods são replicados (escalados horizontalmente) para nós diferentes. Auxilia na alta disponibilidade das aplicações.
* **Volume persistente (pv):** (persistent volume) Área de armazenamento comum entre pods.
* **Solicitação de volume persistente (pvc):** (persistent volume claim) Pedidos para armazenamento de pods. Um PVC vincula um PV a um pod, montando a unidade no sistema de arquivos dos containers.
* **Mapa de configuração (cm) e Segredo:** (config map ou secret) Sets chave-valor usados por recursos do OCP. Centralizam valores de configuração de vários recursos. 
* **Configuração de implantação (dc):** (deploy config) Definição de como implantações são feitas, com controle de estratégia, quantidade de pods, quantidade de containers nos pods e mais.
* **Configuração de compilação (bc):** (build config) Definição de como a aplicação será compilada, e qual estratégia será usada. Trabalha junto com DCs para fornecer integração contínua.
* **Rota:** (route) Nomes de DNS usados pelo roteador do OCP para fornecer acesso às aplicações gerenciadas pelo cluster.
* **Fluxo de imagem (is):** (image stream) Uma abstração das referências às imagens e tags usadas pelas aplicações na plataform OCP. 

O OCP também fornece a possibilidade de criar e manter redes que permitem que pods e containers se comuniquem, mesmo que tenham sido instalados em nós diferentes. Se comunicam entre si pelos endereços IP fornecidos aos serviços, o que os torna altamente ecaláveis e aumenta a tolerância a falhas. 

O acesso externo aos recursos do OCP é feito através de rotas, que abrem acesso às aplicações e liberam portas específicas para os serviços funcionarem. 


## Referências
* [Documentação do Kubernetes](https://kubernetes.io/docs/)
* [Documentação do OpenShift](https://docs.openshift.com/)
* [etcd](https://etcd.io/)
* [Documentação do etcd](https://etcd.io/docs/v3.4.0/)
* [CRI-O](https://cri-o.io/)
* [What is Kubernetes?](https://www.redhat.com/en/topics/containers/what-is-kubernetes)
* [OpenShift 4.5 Architecture](https://docs.openshift.com/container-platform/4.5/architecture/architecture.html)
* [OpenShift 3.11 Overview](https://docs.openshift.com/container-platform/3.11/architecture/index.html)
* [Red Hat Simplifies Deploying and Managing OpenShift](https://containerjournal.com/topics/container-management/red-hat-simplifies-deploying-and-managing-openshift/)
* [O que é etcd?](https://www.redhat.com/pt-br/topics/containers/what-is-etcd)

----
<p align="center"><a href="../aula02">Próxima aula ❯</a></p>