---
title: "Criar um cluster do AKS (Serviço de Contêiner do Azure)"
description: Crie um cluster do AKS com a CLI ou o Portal do Azure.
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 02/12/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 3e884bc16680d74801911547045deb48246afccd
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2018
---
# <a name="create-an-azure-container-service-aks-cluster"></a>Criar um cluster do AKS (Serviço de Contêiner do Azure)

Um cluster do AKS (Serviço de Contêiner do Azure) pode ser criado com a CLI do Azure ou o Portal do Azure.

## <a name="azure-cli"></a>CLI do Azure

Use o comando [az aks create][az-aks-create] para criar o cluster do AKS.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster
```

As opções a seguir estão disponíveis com o comando `az aks create`.

| Argumento | DESCRIÇÃO | Obrigatório |
|---|---|:---:|
| `--name` `-n` | Nome de recurso do cluster gerenciado. | Sim |
| `--resource-group` `-g` | Nome do grupo de recursos do Serviço de Contêiner do Azure. | Sim |
| `--admin-username` `-u` | Nome de usuário para a Máquinas Virtuais do Linux.  Padrão: azureuser. | não |
| ` --client-secret` | O segredo associado à entidade de serviço. | não |
| `--dns-name-prefix` `-p` | Prefixo DNS para o endereço IP público dos clusters. | não |
| `--generate-ssh-keys` | Gere os arquivos de chave pública e privada de SSH, se estiverem ausentes. | não |
| `--kubernetes-version` `-k` | A versão do Kubernetes a ser usada para criar o cluster, por exemplo, '1.7.9' ou '1.8.2'.  Padrão: 1.7.7. | não |
| `--no-wait` | Não aguarde a conclusão da operação de execução longa. | não |
| `--node-count` `-c` | O número padrão de nós para os pools de nós.  Padrão: 3. | não |
| `--node-osdisk-size` | O tamanho do disco do sistema operacional, em GB, da máquina virtual do pool de nós. | não |
| `--node-vm-size` `-s` | O tamanho da máquina virtual.  Padrão: Standard_D1_v2. | não |
| `--service-principal` | A entidade de serviço usada para autenticação de cluster. | não |
| `--ssh-key-value` | Caminho do arquivo de chave ou valor do arquivo de chave SSH.  Padrão: ~/.ssh/id_rsa.pub. | não |
| `--tags` | Marcações separadas por espaço no formato 'chave[=valor]'. Use '' para limpar marcações existentes. | não |

## <a name="azure-portal"></a>Portal do Azure

Para obter instruções sobre como implantar um cluster do AKS com o Portal do Azure, veja o [Guia de início rápido do Portal do Azure][aks-portal-quickstart] do AKS (Serviço de Contêiner do Azure). 

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az_aks_create
[aks-portal-quickstart]: kubernetes-walkthrough-portal.md