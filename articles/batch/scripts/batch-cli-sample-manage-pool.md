---
title: "Amostra de Script da CLI do Azure – Gerenciando Pools no Lote | Microsoft Docs"
description: "Amostra de Script da CLI do Azure – Gerenciando Pools no Lote"
services: batch
documentationcenter: 
author: annatisch
manager: daryls
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/20/2017
ms.author: antisch
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 7cec2a8c66868c601c38dc2dea61dda38d3cd3b2
ms.lasthandoff: 03/24/2017

---

# <a name="managing-azure-batch-pools-with-azure-cli"></a>Gerenciando pools do Lote do Azure com CLI do Azure

Esses scripts demonstram algumas das ferramentas disponíveis na CLI do Azure para criar e gerenciar pools de nós de computação no serviço de Lote do Azure.

Executar esses scripts pressupõe que uma conta do Lote já foi preparada e um aplicativo configurado. Para obter mais informações, consulte os [scripts de exemplo](../batch-cli-samples.md) que abrangem cada um desses tópicos.

> [!NOTE]
> Os comandos neste exemplo criam máquinas virtuais do Azure. VMs em execução acumularão encargos para sua conta. Para minimizar esses encargos, exclua as VMs assim que terminar a execução da amostra. Consulte [Limpar pools](#clean-up-pools).

Se necessário, instale a CLI do Azure usando as instruções encontradas no [guia de instalação da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) e, em seguida, execute `az login` para fazer logon no Azure.

Pools de Lote podem ser configurados de duas maneiras, com uma configuração de Serviços de Nuvem (somente Windows) ou com uma configuração de Máquina Virtual (Windows e Linux).

## <a name="pool-with-cloud-service-configuration-sample-script"></a>Pool com o script de exemplo de configuração de serviço de nuvem

[!code-azurecli[principal](../../../cli_scripts/batch/manage-pool/manage-pool-windows.sh "Gerenciar Pools de Serviços de Nuvem")]

## <a name="pool-with-virtual-machine-configuration-sample-script"></a>Pool com o script de exemplo de configuração de máquina virtual

[!code-azurecli[principal](../../../cli_scripts/batch/manage-pool/manage-pool-linux.sh "Gerenciar Pools de Máquina Virtual")]

## <a name="clean-up-pools"></a>Limpar pools

Depois de executar o script de exemplo acima, execute o comando a seguir para excluir os pools.
```azurecli
az batch pool delete --pool-id mypool-windows
az batch pool delete --pool-id mypool-linux
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para criar e manipular pools do Lote.
Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#login) | Autentique em uma conta do Lote.  |
| [az batch application summary list](https://docs.microsoft.com/cli/azure/batch/application/summary#list) | Liste os aplicativos disponíveis na conta do Lote.  |
| [az batch pool create](https://docs.microsoft.com/cli/azure/batch/pool#create) | Crie um pool de VMs.  |
| [az batch pool set](https://docs.microsoft.com/cli/azure/batch/pool#set) | Atualize as propriedades de um pool.  |
| [az batch pool node-agent-skus list](https://docs.microsoft.com/cli/azure/batch/pool/node-agent-skus#list) | Liste as informações de imagem e SKUs do agente de nó disponíveis.  |
| [az batch pool resize](https://docs.microsoft.com/cli/azure/batch/pool#resize) | Redimensione o número de VMs em execução no pool especificado.  |
| [az batch pool show](https://docs.microsoft.com/cli/azure/batch/pool#show) | Exiba as propriedades de um pool.  |
| [az batch pool delete](https://docs.microsoft.com/cli/azure/batch/pool#delete) | Exclua o pool especificado.  |
| [az batch pool autoscale enable](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#enable) | Habilite o dimensionamento automático em um pool e aplique uma fórmula.  |
| [az batch pool autoscale disable](https://docs.microsoft.com/cli/azure/batch/pool/autoscale#disable) | Desabilite o dimensionamento automático em um pool.  |
| [az batch node list](https://docs.microsoft.com/cli/azure/batch/node#list) | Liste todos os nós de computação no pool especificado.  |
| [az batch node reboot](https://docs.microsoft.com/cli/azure/batch/node#reboot) | Reinicie o nó de computação especificado.  |
| [az batch node delete](https://docs.microsoft.com/cli/azure/batch/node#delete) | Exclua os nós listados do pool especificado.  |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

As amostras de script da CLI do Lote adicionais podem ser encontrados na [documentação da CLI do Lote do Azure](../batch-cli-samples.md).


