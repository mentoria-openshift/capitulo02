<p align="center"><a href="../aula01">❮ Aula anterior</a> | <a href="../aula03">Próxima aula ❯</a></p>
<br/>

# Aula 2 - Consoles do OpenShift
Nesta aula, aprenderemos sobre o console do OpenShift, tanto a versão web (GUI) quanto a linha de comando (CLI). A maior parte do curso será feita a partir da linha de comando, mas vamos também apresentar o console gráfico fornecido pela web.

## O console web
O OpenShift fornece uma interface gráfica para gerenciamento do cluster e das suas aplicações. As opções disponíveis são definidas por política de acesso, que são gerenciadas por usuários administradores do cluster. O acesso padrão de um cluster é o de desenvolvedor, no qual acesso total é liberado nos projetos criados pela conta de desenvolvedor. As contas administradores têm acesso a todos os projetos e recursos de todo o cluster. O OpenShift fornece uma série de formas de login, desde OAuth2 integrado com GitLab ou GitHub até usuários fixos criados para autenticação básica através do htpasswd. 

Ao fazer login no console web do OpenShift, o usuário verá a lista de projetos aos quais ele tem acesso. Caso o usuário não tenha processos criados, ele será direcionado ao dashboard no cluster, que mostra detalhes da saúde e dos recursos do cluster. 

```IMAGEM 1.1 - Home page do console web```

Ao selecionar um projeto, você verá os recursos disponíveis nele, gráficos de utilização, detalhes e atividade do projeto.

```IMAGEM 1.2 - Página do projeto```


## Referências
* [Documentação do OpenShift](https://docs.openshift.com/)

----
<p align="center"><a href="../aula01">❮ Aula anterior</a> | <a href="../aula03">Próxima aula ❯</a></p>