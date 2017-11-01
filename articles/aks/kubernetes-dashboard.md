---
title: "Gerenciar o cluster do Azure Kubernetes com a interface do usuário da Web | Microsoft Docs"
description: Usar o painel do Kubernetes no AKS
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 764bbb95b661bb750d7802ee5996d8a491be928d
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2017
---
# <a name="kubernetes-dashboard-with-azure-container-service-aks"></a>Painel do Kubernetes com o AKS (Serviço de Contêiner do Azure)

A CLI do Azure pode ser usada para iniciar o Painel do Kubernetes. Este documento explica desde o painel do Kubernetes com a CLI do Azure e também aborda algumas operações básicas do painel. Para obter mais informações sobre o painel do Kubernetes, consulte [Painel de interface do usuário Web do Kubernetes](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/). 

## <a name="before-you-begin"></a>Antes de começar

As etapas detalhadas neste documento pressupõem que você tenha criado um cluster AKS e estabelecido uma conexão kubectl com o cluster. Se você precisar desses itens, veja o [Início rápido do AKS](./kubernetes-walkthrough.md).

Você também precisa da CLI do Azure versão 2.0.20 ou posterior instalada e configurada. Execute az --version para localizar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="start-kubernetes-dashboard"></a>Iniciar o painel do Kubernetes

Use o comando `az aks browse` para iniciar o painel do Kubernetes. Ao executar esse comando, substitua o nome de cluster e do grupo de recursos.

```azurecli
az aks browse --resource-group myResourceGroup --name myK8SCluster
```

Este comando cria um proxy entre o sistema de desenvolvimento e a API do Kubernetes, e abre um navegador da Web para o painel do Kubernetes.

## <a name="run-an-application"></a>Executar um aplicativo

No painel do Kubernetes, clique no botão **Criar** na janela superior direita. Dê o nome `nginx` para a implantação e digite `nginx:latest` para o nome das imagens. Em **Serviço**, selecione **Externo** e insira `80` para a porta e a porta de destino.

Quando estiver pronto, clique em **Implantar** para criar a implantação.

![Caixa de diálogo Criar serviço Kubernetes](./media/container-service-kubernetes-ui/create-deployment.png)

## <a name="view-the-application"></a>Exibir o aplicativo

É possível ver o status do aplicativo no painel do Kubernetes. Quando o aplicativo estiver em execução, cada componente tem uma caixa de seleção verde ao lado.

![Pods Kubernetes](./media/container-service-kubernetes-ui/complete-deployment.png)

Para obter mais informações sobre os pods de aplicativo, clique em **Pods** no menu esquerdo e selecione o pod **NGINX**. Nele você poderá ver informações específicas do pod, como o consumo de recursos.

![Recursos do Kubernetes](./media/container-service-kubernetes-ui/running-pods.png)

Para encontrar o endereço IP do aplicativo, clique em **Serviços** no menu esquerdo e selecione o serviço **NGINX**.

![nginx view](./media/container-service-kubernetes-ui/nginx-service.png)

## <a name="edit-the-application"></a>Editar o aplicativo

Além de criar e exibir os aplicativos, o painel do Kubernetes pode ser usado para editar e atualizar implantações de aplicativo.

Para editar uma implantação, clique em **Implantações** no menu esquerdo e selecione a implantação **NGINX**. Por fim, selecione **Editar** na barra de navegação superior à direita.

![Editar Kubernetes](./media/container-service-kubernetes-ui/view-deployment.png)

Localize o valor `spec.replica`, que deve ser 1 e altere-o para 3. Dessa forma, a contagem de réplica da implantação NGINX é aumentada de 1 para 3.

Selecione **Atualizar** quando estiver pronto.

![Editar Kubernetes](./media/container-service-kubernetes-ui/edit-deployment.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o painel do Kubernetes, consulte a documentação do Kubernetes.

> [!div class="nextstepaction"]
> [Painel de interface do usuário Web do Kubernetes](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)