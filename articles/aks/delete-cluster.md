---
title: "Excluir um cluster do Serviço de Contêiner do Azure (AKS)"
description: Excluir um cluster do AKS com a CLI ou o Portal do Azure.
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/05/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 78056288f45616eda427f8e708efc679f8a5202c
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2018
---
# <a name="delete-an-azure-container-service-aks-cluster"></a>Excluir um cluster do Serviço de Contêiner do Azure (AKS)

Ao excluir um cluster do Serviço de Contêiner do Azure, o grupo de recursos no qual o cluster foi implantado permanece, mas todos os recursos relacionados ao AKS são excluídos. Este documento mostra como excluir um cluster do AKS usando a CLI do Azure e o Portal do Azure. 

Além de excluir o cluster, o grupo de recursos no qual ele foi implantado pode ser excluído, o que também exclui o cluster do AKS.

## <a name="azure-cli"></a>CLI do Azure

Use o comando [az aks delete][az-aks-delete] para excluir o cluster do AKS.

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

As opções a seguir estão disponíveis com o comando `az aks delete`.

| Argumento | DESCRIÇÃO | Obrigatório |
|---|---|:---:|
| `--name` `-n` | Nome de recurso do cluster gerenciado. | Sim |
| `--resource-group` `-g` | Nome do grupo de recursos do Serviço de Contêiner do Azure. | Sim |
| `--no-wait` | Não aguarde a conclusão da operação de execução longa. | não |
| `--yes` `-y` | Não solicite confirmação. | não |

## <a name="azure-portal"></a>Portal do Azure

No Portal do Azure, navegue até o grupo de recursos que contém o recurso do AKS (Serviço de Contêiner do Azure), selecione o recurso e clique em **Excluir**. Será solicitado que você confirme a operação de exclusão.

![Excluir cluster do AKS – Portal](media/container-service-delete-cluster/delete-aks-portal.png)

<!-- LINKS - internal -->
[az-aks-delete]: /cli/azure/aks?view=azure-cli-latest#az_aks_delete