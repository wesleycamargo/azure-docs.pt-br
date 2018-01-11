---
title: Configurar o Load Balancer para o Always On do Microsoft SQL Server | Microsoft Docs
description: "Configurar o Load Balancer para trabalhar com o Always On do SQL Server, e aprender a usar o PowerShell para criar um balanceador de carga para a implementação do SQL Server"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: d7bc3790-47d3-4e95-887c-c533011e4afd
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 5e890f8314c8f191dbfa6c6818d810b91d0e829d
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="configure-a-load-balancer-for-sql-server-always-on"></a>Configurar o Load Balancer para o Always On do Microsoft SQL Server

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Grupos de disponibilidade do Always On do Microsoft SQL Server agora podem funcionar com um balanceador de carga interno. O grupo de disponibilidade é uma solução fundamental do Microsoft SQL Server para alta disponibilidade e recuperação de desastre. O ouvinte do grupo de disponibilidade permite que os aplicativos do cliente se conectem continuamente com a réplica primária, independentemente do número de réplicas na configuração.

O nome do ouvinte (DNS) é mapeado para um endereço IP com balanceamento de carga. O Azure Load Balancer direciona o tráfego de entrada para somente o servidor primário no conjunto de réplicas.

Você pode usar o suporte interno do Load Balancer para pontos de extremidade (ouvinte) do Always On do Microsoft SQL Server. Agora você tem controle sobre a acessibilidade do ouvinte. Você pode escolher o endereço IP com balanceamento de carga de uma sub-rede específica na sua rede virtual.

Usando o balanceador de carga interno no ouvinte, o ponto de extremidade do Microsoft SQL Server (por exemplo, Server=tcp:ListenerName,1433;Database=DatabaseName) é acessível somente por:

* Serviços e VMs na mesma rede virtual.
* Serviços e VMs de redes locais conectadas.
* Serviços e VMs de redes virtuais interconectadas.

![Balanceador de carga interno para o Always On do Microsoft SQL Server](./media/load-balancer-configure-sqlao/sqlao1.png)

## <a name="add-an-internal-load-balancer-to-the-service"></a>Adicionar balanceador de carga interno ao serviço

1. No exemplo a seguir, você configura uma rede virtual que contém uma sub-rede chamada "Subnet-1":

    ```powershell
    Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc
    ```
2. Adicione pontos de extremidade com balanceamento de carga para um balanceador de carga interno em cada VM.

    ```powershell
    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -
    DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM
    ```

    No exemplo anterior, você tem duas VMs chamadas "sqlsvc1" e "sqlsvc2" que são executadas no serviço de nuvem "Sqlsvc". Depois de criar o balanceador de carga interno com a chave `DirectServerReturn`, adicione os pontos de extremidade com balanceamento de carga ao balanceador de carga interno. Os pontos de extremidade com balanceamento de carga permitem que o Microsoft SQL Server configure os ouvintes para os grupos de disponibilidade.

Para obter mais informações sobre o Always On do Microsoft SQL Server, consulte [Configurar um balanceador de carga interno para um grupo de disponibilidade do Always On no Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

## <a name="see-also"></a>Consulte também
* [Introdução à configuração de um balanceador de carga público](load-balancer-get-started-internet-arm-ps.md)
* [Introdução à configuração de um balanceador de carga interno](load-balancer-get-started-ilb-arm-ps.md)
* [Configurar um modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md)
* [Definir configurações de tempo limite de TCP ocioso para o balanceador de carga](load-balancer-tcp-idle-timeout.md)
