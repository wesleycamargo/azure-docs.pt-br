---
title: Exemplo de Script da CLI do Azure - Gerenciar o tráfego de rede | Microsoft Docs
description: Exemplo de Script da CLI do Azure - Gerenciar o tráfego da web com um gateway de aplicativo e um conjunto de escala de máquinas virtuais.
services: application-gateway
documentationcenter: networking
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: b398ff3b5f3e6fd2ea7d6e2544e7399646f4fa5c
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2018
---
# <a name="manage-web-traffic-using-the-azure-cli"></a>Gerenciar o tráfego da web usando a CLI do Azure

Este script cria um gateway de aplicativo que usa um conjunto de dimensionamento de máquinas virtuais para servidores de back-end. O gateway de aplicativo pode ser configurado para gerenciar o tráfego da web. Depois de executar o script, você pode testar o gateway de aplicativo usando seu endereço IP público.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/application-gateway/create-vmss/create-vmss.sh "Create application gateway")]

## <a name="clean-up-deployment"></a>Limpar a implantação 

Execute o comando a seguir para remover o grupo de recursos, gateway de aplicativo e todos os recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroupAG --yes
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para criar a implantação. Cada item em que a tabela contém links para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#az_net) | Cria uma rede virtual. |
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Cria uma rede virtual e uma sub-rede. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/public-ip#az_network_public_ip_create) | Cria o endereço IP público para o gateway do aplicativo na tela de Visão geral. |
| [az network application-gateway create](https://docs.microsoft.com/cli/azure/application-gateway#az_application_gateway_create) | Criar um gateway de aplicativo. |
| [az vmss create](https://docs.microsoft.com/cli/azure/vmss#az_vmss_create) | Cria um conjunto de dimensionamento de máquinas virtuais. |
| [az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip#az_network_public_ip_show) | Pega o endereço de IP público do gateway do aplicativo. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos de script CLI de máquinas virtuais adicionais podem ser encontrados na [documentação da VM do Windows do Azure](../cli-samples.md).
