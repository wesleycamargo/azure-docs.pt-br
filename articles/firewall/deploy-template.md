---
title: Implantar Firewall do Azure usando um modelo
description: Implantar Firewall do Azure usando um modelo
services: firewall
author: vhorne
manager: jpconnock
ms.service: firewall
ms.topic: article
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: d32e6e29c287d140c28206743e36dc025b26158b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991327"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Implantar Firewall do Azure usando um modelo

Este modelo cria um firewall e um ambiente de rede de teste. A rede tem uma VNet, com três sub-redes: *AzureFirewallSubnet*, *ServersSubnet* e um *JumpboxSubnet*. O ServersSubnet e o JumpboxSubnet têm, cada um, um Windows Server de dois núcleos neles.

O firewall está na AzureFirewallSubnet e é configurado com uma Coleção de regra de aplicativo com uma única regra que permite o acesso ao www.microsoft.com.

Uma rota definida pelo usuário é criada que aponta o tráfego de rede do ServersSubnet até o firewall, em que as regras de firewall são aplicadas.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="template-location"></a>Local do modelo

O modelo está localizado em:

[https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox)

Leia a introdução e, quando estiver pronto para implantar, clique em **Implantar no Azure**.

## <a name="clean-up-resources"></a>Limpar recursos

Primeiro, explore os recursos que foram criados com o firewall e, quando não forem mais necessários, é possível usar o comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o grupo de recursos, firewall e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```
## <a name="next-steps"></a>Próximas etapas

Em seguida, é possível monitorar os logs do Firewall do Azure:

- [Tutorial: Monitorar os logs do Firewall do Azure](./tutorial-diagnostics.md)

