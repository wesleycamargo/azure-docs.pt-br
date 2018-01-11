---
title: Configurar a alta disponibilidade de portas para o Azure Load Balancer | Microsoft Docs
description: "Saiba como usar as portas de alta disponibilidade para o tráfego interno de balanceamento de carga em todas as portas"
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/02/2017
ms.author: kumud
ms.openlocfilehash: 36bc3d7a35f41384706cbc7101457d00848639b2
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="configure-high-availability-ports-for-an-internal-load-balancer"></a>Configurar a alta disponibilidade de portas para o balanceador de carga interno

Este artigo fornece um exemplo de implantação de Portas de Alta Disponibilidade em um Load Balancer Interno. Para obter mais informações sobre configurações específicas de soluções de virtualização de rede (NVAs), consulte os sites do provedor correspondente.

>[!NOTE]
> O recurso de Portas de Alta Disponibilidade está atualmente em versão prévia. Durante a versão prévia, o recurso pode não ter o mesmo nível de disponibilidade e confiabilidade que os recursos que estão na versão de disponibilidade geral. Para obter mais informações, consulte [Termos de Uso Complementares do Microsoft Azure para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A ilustração mostra a configuração a seguir do exemplo de implantação descrito neste artigo:

- Os NVAs são implantados no pool de back-end de um Load Balancer Interno por trás da configuração das Portas de Alta Disponibilidade. 
- A rota definida pelo usuário (UDR) aplicada à sub-rede DMZ roteia todo o tráfego para os NVAs, tornando o próximo salto como o IP Virtual do Load Balancer Interno. 
- O Load Balancer Interno distribui o tráfego para um dos NVAs ativos de acordo com o algoritmo de balanceamento de carga.
- O NVA processa o tráfego e o encaminha para o destino original na sub-rede de back-end.
- O caminho de retorno pode usar a mesma rota se uma UDR correspondente for configurada na sub-rede de back-end. 

![Implantação de exemplo de Portas de Alta Disponibilidade](./media/load-balancer-configure-ha-ports/haports.png)


## <a name="preview-sign-up"></a>Inscrição na versão prévia

Para participar da versão prévia do recurso das Portas de Alta Disponibilidade no Azure Load Balancer Standard, registre a sua assinatura para obter acesso usando a CLI do Azure 2.0 ou o PowerShell. Registre a sua assinatura da [Versão prévia do Load Balancer Standard](https://aka.ms/lbpreview#preview-sign-up).

>[!NOTE]
>O registro das versões prévias do Load Balancer Standard pode levar até uma hora.

## <a name="configure-high-availability-ports"></a>Configurar as Portas de Alta Disponibilidade

Para configurar as Portas de Alta Disponibilidade, defina um balanceador de carga interno com os NVAs no pool de back-end. Defina uma configuração de sonda de integridade do balanceador de carga correspondente para detectar a integridade NVA e a regra de balanceador de carga com Portas de Alta Disponibilidade. As configurações gerais relacionadas ao Load Balancer são abordadas em [Introdução](load-balancer-get-started-ilb-arm-portal.md). Este artigo destaca a configuração das Portas de Alta Disponibilidade.

A configuração envolve essencialmente a definição do valor da porta de back-end e da porta de front-end em **0**. Defina o valor de protocolo para **Todos**. Este artigo descreve como configurar as Portas de Alta Disponibilidade usando o portal do Azure, o PowerShell e a CLI do Azure 2.0.

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-the-azure-portal"></a>Configurar a regra de balanceador de carga de Portas de Alta Disponibilidade com o portal do Azure

Para configurar as Portas de Alta Disponibilidade usando o portal do Azure, selecione a caixa de seleção **Portas HA**. Quando selecionada, a configuração de porta e de protocolo relacionada é populada automaticamente. 

![Configuração das Portas de Alta Disponibilidade pelo portal do Azure](./media/load-balancer-configure-ha-ports/haports-portal.png)


### <a name="configure-a-high-availability-ports-load-balancing-rule-via-the-resource-manager-template"></a>Configurar uma regra de balanceamento de carga das Portas de Alta Disponibilidade pelo modelo do Gerenciador de Recursos

É possível configurar as Portas de Alta Disponibilidade usando a versão da API 2017-08-01 para Microsoft.Network/loadBalancers no recurso do Load Balancer. O trecho de código JSON a seguir ilustra as alterações na configuração do Load Balancer para Portas de Alta Disponibilidade por meio da API REST:

```json
    {
        "apiVersion": "2017-08-01",
        "type": "Microsoft.Network/loadBalancers",
        ...
        "sku":
        {
            "name": "Standard"
        },
        ...
        "properties": {
            "frontendIpConfigurations": [...],
            "backendAddressPools": [...],
            "probes": [...],
            "loadBalancingRules": [
             {
                "properties": {
                    ...
                    "protocol": "All",
                    "frontendPort": 0,
                    "backendPort": 0
                }
             }
            ],
       ...
       }
    }
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-powershell"></a>Configurar a regra de balanceador de carga de Portas de Alta Disponibilidade com o PowerShell

Use o comando a seguir para criar a regra de balanceador de carga de Portas de Alta Disponibilidade criando simultaneamente o balanceador de carga interno com o PowerShell:

```powershell
lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-azure-cli-20"></a>Configurar a regra de balanceador de carga de Portas de Alta Disponibilidade com a CLI do Azure 2.0

Na Etapa 4 de [Criar um conjunto de Load Balancer Interno](load-balancer-get-started-ilb-arm-cli.md), use o comando a seguir para criar a regra de balanceador de carga de Portas de Alta Disponibilidade:

```azurecli
azure network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as [Portas de Alta Disponibilidade](load-balancer-ha-ports-overview.md).
