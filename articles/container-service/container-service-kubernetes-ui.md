---
title: "Usando a interface do usuário Web Kubernetes no Serviço de Contêiner do Azure | Microsoft Docs"
description: "Usando a interface do usuário Web Kubernetes no Serviço de Contêiner do Azure"
services: container-service
documentationcenter: 
author: bburns
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2016
ms.author: bburns
translationtype: Human Translation
ms.sourcegitcommit: bcc2d3468c8a560105aa2c2feb0d969ec3cccdcb
ms.openlocfilehash: 9ae6e606bf215d9f53f725899b8ac7c466b781f1


---

# <a name="microsoft-azure-container-service-engine---using-the-kubernetes-web-ui"></a>Mecanismo de Serviço de Contêiner do Microsoft Azure - usando a interface do usuário da Web do Kubernetes

## <a name="prerequisites"></a>Pré-requisitos
Este passo a passo presume que você tenha [criado um cluster Kubernetes usando o Serviço de contêiner do Azure](container-service-kubernetes-walkthrough.md).

> [!NOTE]
> O suporte a Kubernetes no Serviço de Contêiner do Azure está atualmente em visualização.
>

Isso também pressupõe que você tenha as ferramentas cli e kubectl do Azure instaladas.

Você pode testar se tem a ferramenta `az` instalada executando:

```console
$ az --version
```

Se você não tem a ferramenta `az` instalada, há instruções [aqui](https://github.com/azure/azure-cli#installation).

Você pode testar se tem a ferramenta `kubectl` instalada executando:

```console
$ kubectl version
```

Se não tem `kubectl` instalado, você pode executar:

```console
$ az acs kubernetes install-cli
```

## <a name="overview"></a>Visão geral

### <a name="connect-to-the-web-ui"></a>Conecte-se à interface do usuário da Web
Você pode iniciar a interface do usuário Web Kubernetes executando:

```console
$ az acs kubernetes browse
```

Isso deve abrir um navegador da Web configurado para se comunicar com um proxy seguro, conectando a máquina local à interface do usuário Web Kubernetes.

### <a name="create-and-expose-a-service"></a>Criar e expor um serviço
Na interface do usuário da Web de Kubernetes, você verá um botão 'Criar' na janela superior direita.

![Criar interface do usuário Kubernetes](media/k8s/create.png)

Isso deve abrir uma caixa de diálogo em que você pode começar a criar o aplicativo.
Atribua o nome `hello-nginx`. Use o [`nginx` contêiner de Docker](https://hub.docker.com/_/nginx/) e implante três réplicas do serviço Web.

![Caixa de diálogo Criar Pod Kubernetes](media/k8s/nginx.png)

Depois, crie um Serviço Kubernetes 'Externo' para balancear a carga do tráfego para as três réplicas.  Selecione 'Externo' e insira a porta 80.

![Caixa de diálogo Criar serviço Kubernetes](media/k8s/service.png)

Por fim, pressione o botão 'Implantar' para implantar esses serviços e contêineres.

![Implantar Kubernetes](media/k8s/deploy.png)

### <a name="view-your-containers"></a>Exibir seus contêineres
Depois que você pressionar 'Implantar', a interface do usuário mostrará uma exibição do serviço que ela implanta:

![Status do Kubernetes](media/k8s/status.png)

Você pode ver o status de cada objeto Kubernetes no círculo no lado esquerdo da interface do usuário. Se for um círculo parcialmente cheio, o objeto ainda está sendo implantado. Quando um objeto é totalmente implantado, ele exibe uma marca de seleção verde:

![Kubernetes implantado](media/k8s/deployed.png)

Quando tudo estiver em execução, você poderá clicar em um dos Pods para ver detalhes sobre o serviço Web em execução

![Pods Kubernetes](media/k8s/pods.png)

No modo de exibição específico de Pod, você pode ver informações sobre os contêineres de Pod, bem como os recursos de CPU e memória usados por esses contêineres:

![Recursos do Kubernetes](media/k8s/resources.png)

Se não encontrar os recursos, você precisará aguardar alguns minutos para propagar os dados de monitoramento.

Você também pode clicar no link 'Logs' para ver os logs do contêiner:

![Logs do Kubernetes](media/k8s/logs.png)

### <a name="viewing-your-service"></a>Exibindo o serviço
Além de executar seus contêineres, a interface do usuário Kubernetes criou um `Service` externo que provisiona um balanceador de carga para trazer o tráfego para os contêineres no cluster.

Você pode clicar em 'Serviços' no painel de navegação à esquerda para exibir todos os serviços (no momento, deve existir apenas um)

![Serviços Kubernetes](media/k8s/service-deployed.png)

Nesse modo, você deve ver um endereço IP externo que foi alocado para o serviço.
Se clicar no endereço IP, você deverá ver o contêiner nginx em execução por trás do balanceador de carga.

![nginx view](media/k8s/nginx-page.png)

### <a name="resizing-your-service"></a>Redimensionar o serviço
Além de exibir os objetos na interface do usuário, você também pode editar e atualizar os objetos da API Kubernetes.

Primeiro, vá para o `Deployment` para seu serviço, clicando em 'Implantações' no painel de navegação à esquerda.

Quando você estiver nesse modo de exibição, clique em ReplicaSet e clique no botão 'Editar' na barra de navegação superior:

![Editar Kubernetes](media/k8s/edit.png)

Edite o campo `spec.replicas` para ser '2' e pressione 'Atualizar'.

Isso fará com que o número de réplicas diminua para dois, excluindo um de seus Pods.

 




<!--HONumber=Dec16_HO3-->


