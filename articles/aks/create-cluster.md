---
title: Criar um cluster do Serviço de Kubernetes do Azure (AKS)
description: Crie um cluster do AKS com a CLI ou o Portal do Azure.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 5c133c61c989bf19be3e84287cb76a7d110dccc8
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440466"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster"></a>Criar um cluster do Serviço de Kubernetes do Azure (AKS)

Um cluster do AKS (Serviço de Kubernetes do Azure) pode ser criado com a CLI do Azure ou o Portal do Azure.

## <a name="azure-cli"></a>CLI do Azure

Use o comando [az aks create][az-aks-create] para criar o cluster do AKS.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster
```

As opções a seguir estão disponíveis com o comando `az aks create`. Consulte a [referência da CLI do Azure][az-aks-create] para AKS para obter mais informações sobre cada um desses argumentos.

| Argumento | DESCRIÇÃO | Obrigatório |
|---|---|:---:|
| `--name` `-n` | Nome de recurso do cluster gerenciado. | Sim |
| `--resource-group` `-g` | Nome do grupo de recursos do Serviço de Kubernetes do Azure. | Sim |
| `--admin-username` `-u` | Nome de usuário para a Máquinas Virtuais do Linux.  Padrão: azureuser. | não |
| `--aad-client-app-id` | (VISUALIZAÇÃO) A ID de um aplicativo de cliente do Azure Active Directory do tipo "Nativo". | não |
| `--aad-server-app-id` | (VISUALIZAÇÃO) A ID de um aplicativo de cliente do Azure Active Directory do tipo "Aplicativo web/API". | não |
| `--aad-server-app-secret` | (VISUALIZAÇÃO) O segredo de um aplicativo de servidor do Azure Active Directory. | não |
| `--aad-tenant-id` | (VISUALIZAÇÃO) O ID de um locatário do Azure Active Directory. | não |
| `--admin-username` `-u` | Conta de usuário para criar um nó de VMs para acesso de SSH.  Padrão: azureuser. | não |
| ` --client-secret` | O segredo associado à entidade de serviço. | não |
| `--dns-name-prefix` `-p` | Prefixo DNS para o endereço IP público dos clusters. | não |
| `--dns-service-ip` | Um endereço IP designado ao serviço DNS de Kubernetes. | não |
| `--docker-bridge-address` | Um endereço IP e uma netmask designadas à ponte Docker. | não |
| `--enable-addons` `-a` | Habilitar os addons Kubernetes em uma lista separada por vírgula. | não |
| `--enable-rbac` `-r` | Habilitar Controle de Acesso Baseado em Função Kubernetes. | não |
| `--generate-ssh-keys` | Gere os arquivos de chave pública e privada de SSH, se estiverem ausentes. | não |
| `--kubernetes-version` `-k` | A versão do Kubernetes a ser usada para criar o cluster, por exemplo, '1.7.9' ou '1.9.6'. | não |
| `--locaton` `-l` | Local para o grupo de recursos autocriado | não |
| `--max-pods` `-m` | O número máximo de pods implantados em um nó. | não |
| `--network-plugin` | O plugin de rede Kubernetes para usar. | não |
| `--no-ssh-key` `-x` | Não use ou crie uma chave SSH local. | não |
| `--no-wait` | Não aguarde a conclusão da operação de execução longa. | não |
| `--node-count` `-c` | O número padrão de nós para os pools de nós.  Padrão: 3. | não |
| `--node-osdisk-size` | O tamanho do disco do sistema operacional, em GB, da máquina virtual do pool de nós. | não |
| `--node-vm-size` `-s` | O tamanho da máquina virtual.  Padrão: Standard_D1_v2. | não |
| `--pod-cidr` | Um intervalo IP com notação CIDR do qual designar pod IPs quando kubenet for usado. | não |
| `--service-cidr` | Um intervalo IP com notação CIDR do qual designar serviço cluster IPs. | não |
| `--service-principal` | A entidade de serviço usada para autenticação de cluster. | não |
| `--ssh-key-value` | Caminho do arquivo de chave ou valor do arquivo de chave SSH.  Padrão: ~/.ssh/id_rsa.pub. | não |
| `--tags` | Marcações separadas por espaço no formato 'chave[=valor]'. Use '' para limpar marcações existentes. | não |
| `--vnet-subnet-id` | O ID de uma sub-rede em uma VNet existente na qual implantar o cluster. | não |
| `--workspace-resource-id` | O ID de recurso de um Espaço de Trabalho de Log Analytics existente para usar para dados de monitoramento armazenados. | não |

## <a name="azure-portal"></a>Portal do Azure

Para obter instruções sobre como implantar um cluster do AKS com o Portal do Azure, veja o [Guia de início rápido do Portal do Azure][aks-portal-quickstart] do AKS (Serviço de Kubernetes do Azure).

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[aks-portal-quickstart]: kubernetes-walkthrough-portal.md
