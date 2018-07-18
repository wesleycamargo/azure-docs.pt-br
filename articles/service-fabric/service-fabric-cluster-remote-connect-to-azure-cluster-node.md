---
title: Conexão remota a um nó do cluster do Azure Service Fabric | Microsoft Docs
description: Saiba como se conectar remotamente a uma instância de conjunto de dimensionamento (que é um nó de cluster do Service Fabric).
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: aljo
ms.openlocfilehash: 3c7b3626db0e38d28513d4665a83dd7155663034
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Conectar remotamente a uma instância do conjunto de dimensionamento de máquinas virtuais ou a um nó de cluster
Em um cluster do Service Fabric em execução no Azure, cada tipo de nó de cluster que você definir [configura um dimensionamento de máquina virtual separada](service-fabric-cluster-nodetypes.md).  Você pode conectar remotamente a instâncias de definição de dimensionamento específico (ou nós do cluster).  Ao contrário das máquinas virtuais de instância única, as instâncias de conjunto de dimensionamento da VM não recebem um endereço IP virtual próprio. Isso pode ser complicado quando você um endereço IP e uma porta que você queira usar para fazer a conexão remota com uma instância específica.

Para localizar um endereço IP e porta que você pode usar para se conectar remotamente a uma instância específica, conclua as etapas a seguir.

1. Localizar o endereço IP virtual para o tipo de nó, obtendo as regras de entrada de NAT para o protocolo RDP.

    Primeiro, obtenha os valores de regras de entrada NAT que foram definidos como parte da definição de recurso para `Microsoft.Network/loadBalancers`.
    
    No portal do Azure, na página do balanceador de carga, selecione **Configurações** > **Regras NAT de entrada**. Isso lhe dá o endereço IP e a porta que você pode usar para fazer a conexão remota com a primeira instância de conjunto de dimensionamento. 
    
    ![Balanceador de carga][LBBlade]
    
    Na figura a seguir, o endereço IP e porta são **104.42.106.156** e **3389**.
    
    ![Regras de NAT][NATRules]

2. Descobrir a porta que você pode usar para fazer conexão remota com o nó/instância do conjunto de dimensionamento específico.

    As instâncias do conjunto de dimensionamento mapeiam para os nós. Use as informações do conjunto de dimensionamento para determinar a porta exata a ser usada.
    
    As portas são alocadas em uma ordem crescente que corresponde à instância do conjunto de dimensionamento. Para o exemplo anterior do tipo de nó FrontEnd, a tabela a seguir lista as portas para cada uma das cinco instâncias de nó. Aplique o mesmo mapeamento para a instância do conjunto de dimensionamento.
    
    | **Instância do conjunto de dimensionamento de máquinas virtuais** | **Porta** |
    | --- | --- |
    | FrontEnd_0 |3389 |
    | FrontEnd_1 |3390 |
    | FrontEnd_2 |3391 |
    | FrontEnd_3 |3392 |
    | FrontEnd_4 |3393 |
    | FrontEnd_5 |3394 |

3. Conectar-se remotamente à instância do conjunto de dimensionamento específico.

    A figura a seguir demonstra como usar a Conexão de Área de Trabalho Remota para se conectar à instância do conjunto de dimensionamento FrontEnd_1:
    
    ![Conexões de Área de Trabalho Remota][RDP]


Para as próximas etapas, leia os seguintes artigos:
* Consulte [visão geral do recurso "Implantar em qualquer lugar" e comparação com clusters gerenciados do Azure](service-fabric-deploy-anywhere.md).
* Saiba mais sobre [segurança de cluster](service-fabric-cluster-security.md).
* [Atualizar os valores de intervalo de porta RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) nas VMs de cluster após a implantação
* [Alterar o nome de usuário administrador e a senha](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) para as VMs do cluster

<!--Image references-->
[LBBlade]: ./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/NATRules.png
[RDP]: ./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/RDP.png
