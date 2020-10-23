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
* Redes e comunicação;
* Registro de imagens;
* Gerenciamento de segurança.

Enfim, a lista de recursos do Kubernetes é extensa. É uma ferramenta poderosíssima. 

## Como o Kubernetes funciona?

## O que é o OpenShift?

## Arquitetura do OpenShift

## Referências
* [Documentação do Kubernetes](https://kubernetes.io/docs/)
* [Documentação do OpenShift](https://docs.openshift.com/)

----
<p align="center"><a href="../aula02">Próxima aula ❯</a></p>