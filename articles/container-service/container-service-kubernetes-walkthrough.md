---
title: "Implantar cluster do Serviço de Contêiner do Azure com Kubernetes| Microsoft Docs"
description: "Implantar um cluster do Serviço de Contêiner do Azure com Kubernetes"
services: container-service
documentationcenter: 
author: anhowe
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2016
ms.author: anhowe
translationtype: Human Translation
ms.sourcegitcommit: 0989c85e4b7c73a8b7b335254e4af90ef34ddc01
ms.openlocfilehash: 8c10da9d2363f0938d821b59cc4fd58b53f9ed17


---

# <a name="microsoft-azure-container-service-engine---kubernetes-walkthrough"></a>Mecanismo do Serviço de Contêiner do Microsoft Azure – Passo a passo do Kubernetes

## <a name="deployment"></a>Implantação

Aqui estão as etapas para implantar um cluster Kubernetes simples:

1. [gerar sua chave ssh](https://github.com/Azure/azure-quickstart-templates/blob/master/101-acs-dcos/docs/SSHKeyManagement.md#ssh-key-generation)
3. [gerar a entidade de serviço](https://github.com/Azure/acs-engine/blob/master/docs/serviceprincipal.md)
4. Clique no [Botão Implantar no Azure no LEIAME](https://github.com/Azure/azure-quickstart-templates/blob/master/101-acs-kubernetes/README.md) e preencha os campos

## <a name="walkthrough"></a>Passo a passo

Depois que o cluster Kubernetes for criado, você terá um grupo de recursos com:

1. 1 mestre acessível por SSH na porta 22 ou kubectl na porta 443

2. um conjunto de nós em um conjunto de disponibilidade.  Os nós podem ser acessados por meio de um mestre.  Consulte [encaminhamento do agente](https://github.com/Azure/azure-quickstart-templates/blob/master/101-acs-dcos/docs/SSHKeyManagement.md#key-management-and-agent-forwarding-with-windows-pageant) para obter um exemplo de como fazer isso.

A imagem a seguir mostra a arquitetura de um cluster do serviço de contêiner com 1 mestre e 2 agentes:

![Imagem do cluster Kubernetes no Azure](media/container-service-kubernetes-walkthrough/kubernetes.png)

Na imagem acima, você pode ver as seguintes partes:

1. **Componentes Mestres** – O mestre executa o agendador Kubernetes, o servidor de api e o gerenciador do controlador.  A porta 443 é exposta para o gerenciamento remoto com a cli do kubectl.
2. **Nós** – os nós Kubernetes são executados em um conjunto de disponibilidade.  Os balanceadores de carga do Azure são adicionados dinamicamente ao cluster dependendo dos serviços expostos. 
3. **Componentes Comuns** – Todas as máquinas virtuais executam um kubelet, Docker e um Proxy.
4. **Rede** – Todas as máquinas virtuais são atribuídas a um endereço ip na rede 10.240.0.0/16.  Cada VM é atribuída uma sub-rede /24 para seu pod CIDR habilitando IP por pod.  O proxy em execução em cada VM implementa a rede de serviço 10.0.0.0/16.

Todas as VMs estão na mesma VNET privada e são totalmente acessíveis entre si.

## <a name="create-your-first-kubernetes-service"></a>Criar seu Primeiro Serviço Kubernetes

Depois de concluir este passo a passo, você saberá como:
 * acessar o cluster Kubernetes via SSH,
 * implantar um aplicativo Docker simples e expor ao mundo,
 * o local do arquivo de configuração Kube e como acessar o cluster Kubernetes remotamente,
 * usar `kubectl exec` para executar comandos em um contêiner, 
 * e, finalmente, acessar o painel do Kubernetes.

1. Depois de implantar o modelo com êxito, anote os mestres FQDNs (Nome de Domínio Totalmente Qualificado).
   1. Se usar o Powershell ou CLI, o parâmetro de saída estará na seção OutputsString chamada 'masterFQDN'
   2. Se usar o Portal, navegue até a folha de Visão Geral do recurso do ContainerService para copiar o “FQDN Mestre”:
     
   ![Imagem do dimensionamento do docker](media/container-service-kubernetes-walkthrough/portal-kubernetes-outputs.png)

2. SSH para o FQDN mestre obtido na etapa 1.

3. Explorar os nós e pods em execução:
  1. para ver uma lista dos nós de tipo `kubectl get nodes`.  Se desejar detalhes completos dos nós, adicione `-o yaml` para se tornar `kubectl get nodes -o yaml`.
  2. para ver uma lista de pods do tipo `kubectl get pods --all-namespaces`.

4. Inicie sua primeira imagem do Docker ao digitar `kubectl run nginx --image nginx`.  Isso iniciará o contêiner do Docker nginx em um pod em um dos nós.

5. Tipo `kubectl get pods -o yaml` para ver os detalhes completos da implantação do nginx. Você pode ver o IP do host e o podIP.  O pod IP é atribuído do pod CIDR no host.  Execute o curl no pod ip para ver a saída de nginx, por exemplo. `curl 10.244.1.4`

  ![Imagem do curl para podIP](media/container-service-kubernetes-walkthrough/kubernetes-nginx1.png)

6. A próxima etapa é expor a implantação de nginx como um serviço Kubernetes em uma rede de serviço privados 10.0.0.0/16:
  1. expor o serviço com o comando `kubectl expose deployment nginx --port=80`.
  2. obter o IP do serviço `kubectl get service`
  3. executar o curl no IP, por exemplo. `curl 10.0.105.199`

  ![Imagem do curl para o IP de serviço](media/container-service-kubernetes-walkthrough/kubernetes-nginx2.png)

7. A etapa final é expor o serviço para o mundo.  Isso é feito ao alterar o tipo de serviço de `ClusterIP` para `LoadBalancer`:
  1. editar o serviço: `kubectl edit svc/nginx`
  2. alterar `type` de `ClusterIP` para `LoadBalancer` e salvá-lo.  Agora, isso levará o Kubernetes a criar um Azure Load Balancer com um IP público.
  3. a alteração levará cerca de 2 a 3 minutos.  Para observar o serviço alterar de "pendente" para um tipo de ip externo `watch 'kubectl get svc'`

  ![Imagem da ação de assistir a transição de pendente para IP externo](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

  4. Depois que visualizar o IP externo, você poderá navegar até ele no seu navegador:

  ![Imagem de navegação para nginx](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  

8. A próxima etapa neste passo a passo é mostrar como gerenciar remotamente o cluster Kubernetes.  Baixe primeiro o Kubectl no seu computador e coloque-o em seu caminho:
  * [Kubectl do Windows](https://storage.googleapis.com/kubernetes-release/release/v1.4.5/bin/windows/amd64/kubectl.exe)
  * [Kubectl do OSX](https://storage.googleapis.com/kubernetes-release/release/v1.4.5/bin/darwin/amd64/kubectl)
  * [Linux](https://storage.googleapis.com/kubernetes-release/release/v1.4.5/bin/linux/amd64/kubectl)

9. O mestre de Kubernetes contém o arquivo de configuração kube para acesso remoto sob o diretório base ~/.kube/config.  Baixe esse arquivo em seu computador, defina a variável de ambiente KUBECONFIG e execute kubectl para verificar se que você pode se conectar ao cluster:
  * Windows para usar pscp de [putty](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).  Certifique-se de que seu certificado esteja exposto por meio do [pageant](https://github.com/Azure/acs-engine/blob/master/docs/ssh.md#key-management-and-agent-forwarding-with-windows-pageant):
  ```
  # MASTERFQDN is obtained in step1
  pscp azureuser@MASTERFQDN:.kube/config .
  SET KUBECONFIG=%CD%\config
  kubectl get nodes
  ```
  * OS X ou Linux:
  ```
  # MASTERFQDN is obtained in step1
  scp azureuser@MASTERFQDN:.kube/config .
  export KUBECONFIG=`pwd`/config
  kubectl get nodes
  ```
10. A próxima etapa é mostrar como executar comandos remotamente em um contêiner do Docker remoto:
  1. Execute `kubectl get pods` para mostrar o nome do seu pod nginx
  2. usando seu nome pod, é possível executar um comando remoto no seu pod.  ex. `kubectl exec nginx-701339712-retbj date`
  3. Tente executar uma sessão remota de bash. ex. `kubectl exec nginx-701339712-retbj -it bash`.  A captura de tela a seguir mostra esses comandos:

  ![Imagem do curl para podIP](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)

11. A etapa final deste tutorial é lhe mostrar o painel:
  1. execute `kubectl proxy` para se conectar diretamente ao proxy
  2. em seu navegador, navegue até o [painel](http://127.0.0.1:8001/api/v1/proxy/namespaces/kube-system/services/kubernetes-dashboard/#/workload?namespace=_all)
  3. procure e explore seus pods e serviços.
  ![Imagem do painel de Kubernetes](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

# <a name="learning-more"></a>Para saber mais

Aqui estão links recomendados para saber mais sobre Kubernetes:

1. [Documentação de Kubernetes do Azure](https://azure.microsoft.com/en-us/documentation/services/container-service/)

## <a name="kubernetes-community-documentation"></a>Documentação de Comunidade do Kubernetes

1. [Kubernetes Bootcamp](https://kubernetesbootcamp.github.io/kubernetes-bootcamp/index.html) – Mostra como implantar, dimensionar, atualizar e depurar aplicativos em contêineres.
2. [Guia do Usuário do Kubernetes](http://kubernetes.io/docs/user-guide/) – Fornece informações sobre como executar programas em um cluster Kubernetes existente.
3. [Exemplos de Kubernetes](https://github.com/kubernetes/kubernetes/tree/master/examples) – Fornecem vários exemplos de como executar aplicativos reais com Kubernetes.



<!--HONumber=Nov16_HO3-->


