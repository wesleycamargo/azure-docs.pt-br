---
title: "Introdução à criação de balanceador de carga para a Internet no modo clássico usando o PowerShell | Microsoft Docs"
description: "Saiba como criar um balanceador de carga para a Internet no modo clássico usando o PowerShell"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
tags: azure-service-management
ms.assetid: 73e8bfa4-8086-4ef0-9e35-9e00b24be319
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: c40545833da86426d3e71955b8eb8627db3c1e4b
ms.openlocfilehash: 3855b32c8ff13f73cf91ea8973ed40a34a062224

---

# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-powershell"></a>Introdução à criação de um balanceador de carga para a Internet (clássico) no PowerShell

> [!div class="op_single_selector"]
> * [Portal clássico do Azure](../load-balancer/load-balancer-get-started-internet-classic-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [CLI do Azure](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Serviços de Nuvem do Azure](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Antes de trabalhar com os recursos do Azure, é importante entender que, no momento, o Azure apresenta dois modelos de implantação: Azure Resource Manager e clássico. Verifique se você entendeu [os modelos e as ferramentas de implantação](../azure-classic-rm.md) antes de trabalhar com qualquer recurso do Azure. Você pode exibir a documentação para ferramentas diferentes clicando nas guias na parte superior deste artigo. Este artigo aborda o modelo de implantação clássico. Também é possível [Saber como criar um balanceador de carga para a Internet usando o Gerenciador de Recursos do Azure](load-balancer-get-started-internet-arm-ps.md).

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="set-up-load-balancer-using-powershell"></a>Configurar o balanceador de carga usando o PowerShell

Para configurar um balanceador de carga usando o powershell, siga as etapas abaixo:

1. Se você nunca usou o Azure PowerShell, consulte [Como Instalar e Configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) e siga as instruções até o fim para entrar no Azure e selecionar sua assinatura.
2. Depois de criar uma máquina virtual, você pode usar os cmdlets do PowerShell para adicionar um balanceador de carga a uma máquina virtual no mesmo serviço de nuvem.

No exemplo a seguir, você adicionará um conjunto de balanceadores de carga chamado "webfarm" ao serviço de nuvem "mytestcloud" (ou myctestcloud.cloudapp.net), adicionando os pontos de extremidade para o balanceador de carga às máquinas virtuais chamadas "web1" e "web2". O balanceador de carga recebe tráfego de rede na porta 80 e faz o balanceamento de carga entre as máquinas virtuais definidas pelo ponto de extremidade local (nesse caso, na porta 80) usando TCP.

### <a name="step-1"></a>Etapa 1

Criar um ponto de extremidade com balanceamento de carga para a primeira VM "web1"

```powershell
Get-AzureVM -ServiceName "mytestcloud" -Name "web1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM
```

### <a name="step-2"></a>Etapa 2

Criar outro ponto de extremidade para a segunda VM "web2" usando o mesmo nome do conjunto de balanceadores de carga

```powershell
Get-AzureVM -ServiceName "mytestcloud" -Name "web2" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM
```

## <a name="remove-a-virtual-machine-from-a-load-balancer"></a>Remover uma máquina virtual de um balanceador de carga

Você pode usar Remove-AzureEndpoint para remover um ponto de extremidade de máquina virtual do balanceador de carga

```powershell
Get-azureVM -ServiceName mytestcloud  -Name web1 |Remove-AzureEndpoint -Name httpin | Update-AzureVM
```

## <a name="next-steps"></a>Próximas etapas

Você também pode [começar a criar um balanceador de carga interno](load-balancer-get-started-ilb-classic-ps.md) e configurar o tipo de [modo de distribuição](load-balancer-distribution-mode.md) para um comportamento específico de tráfego de rede do balanceador de carga.

Se seu aplicativo precisar manter conexões ativas para servidores por trás de um balanceador de carga, você poderá entender mais sobre as [configurações de tempo limite de TCP ocioso para um balanceador de carga](load-balancer-tcp-idle-timeout.md). Assim, você saberá mais sobre o comportamento da conexão ociosa quando estiver usando um Balanceador de Carga do Azure.



<!--HONumber=Dec16_HO1-->


