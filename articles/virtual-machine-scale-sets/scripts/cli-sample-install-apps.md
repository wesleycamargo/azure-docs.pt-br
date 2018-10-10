---
title: Exemplos da CLI do Azure – instalar aplicativos | Microsoft Docs
description: Exemplos de CLI do Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 631ac058549e09209b5a717354bc9b35cc707916
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46952810"
---
# <a name="install-applications-into-a-virtual-machine-scale-set-with-the-azure-cli"></a>Instalar aplicativos em um conjunto de dimensionamento de máquinas virtuais com a CLI do Azure
Esse script cria um conjunto de dimensionamento de máquinas virtuais executando o Ubuntu e usa a Extensão de Script Personalizado para instalar um aplicativo Web básico. Depois de executar o script, é possível acessar o aplicativo Web por meio de navegador da Web.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/install-apps/install-apps.sh "Install apps into a scale set")]

## <a name="clean-up-deployment"></a>Limpar a implantação
Execute o comando a seguir para remover o grupo de recursos, o conjunto de dimensionamento e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script
Este script usa os comandos a seguir para criar um grupo de recursos, um conjunto de dimensionamento de máquinas virtuais e todos os recursos relacionados. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/ad/group#az_ad_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az vmss create](/cli/azure/vmss#az_vmss_create) | Cria um conjunto de dimensionamento de máquinas virtuais e a conecta à rede virtual, à sub-rede e ao grupo de segurança de rede. Um balanceador de carga também é criado para distribuir o tráfego para as diversas instâncias de VM. Esse comando também especifica a imagem da VM a ser usada e as credenciais administrativas.  |
| [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set) | Instala a Extensão de Script Personalizado do Azure para executar um script que prepara os discos de dados em cada instância de VM. |
| [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) | Cria uma regra do balanceador de carga para distribuir o tráfego na porta TCP 80 para as instâncias de VM no conjunto de dimensionamento. |
| [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) | Obtém informações sobre o endereço IP público atribuído usado pelo balanceador de carga. |
| [az group delete](/cli/azure/ad/group#delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Outros exemplos de script da CLI do Azure de conjunto de dimensionamento de máquinas virtuais podem ser encontrados na [documentação do conjunto de dimensionamento de máquinas virtuais do Azure](../cli-samples.md).
