---
title: "Mova os circuitos de ExpressRoute do clássico para o Resource Manager | Microsoft Docs"
description: "Esta página descreve como mover um circuito clássico para o modelo de implantação do Resource Manager usando o PowerShell."
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 08152836-23e7-42d1-9a56-8306b341cd91
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/03/2017
ms.author: ganesr;cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 7f04ac92d7a9eff9db49ff493cdaa8693ce188c6
ms.contentlocale: pt-br
ms.lasthandoff: 04/27/2017


---
# <a name="move-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model-using-powershell"></a>Mover os circuitos de ExpressRoute do modelo de implantação clássico para o do Resource Manager usando o PowerShell

Para usar um circuito do ExpressRoute para os modelos de implantação clássico e do Resource Manager, você deve mover o circuito para o modelo de implantação do Resource Manager. As seções a seguir explica as etapas para mover o circuito usando o PowerShell.

## <a name="before-you-begin"></a>Antes de começar
* Verifique se você tem a versão mais recente dos módulos do Azure PowerShell (pelo menos a versão 1.0). Para saber mais, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview):
* Certifique-se de que você leu os [pré-requisitos](expressroute-prerequisites.md), os [requisitos de roteamento](expressroute-routing.md) e os [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.
* Examine as informações fornecidas em [Como mover um circuito de ExpressRoute do clássico para o Resource Manager](expressroute-move.md). Certifique-se de entender completamente os limites e limitações.
* Verifique se o circuito está totalmente operacional no modelo de implantação clássico.
* Verifique se você tem um grupo de recursos que foi criado no modelo de implantação do Gerenciador de Recursos.

## <a name="move-an-expressroute-circuit"></a>Mover um circuito de ExpressRoute

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>Etapa 1: Coletar detalhes do circuito do modelo de implantação clássico
Entre no ambiente clássico do Azure e obtenha a chave de serviço.

1. Entre na sua conta do Azure.

        Add-AzureAccount

2. Selecione a assinatura do Azure apropriada.

        Select-AzureSubscription "<Enter Subscription Name here>"

3. Importar os módulos do PowerShell para Azure e ExpressRoute.

        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

4. Use o cmdlet abaixo para obter as chaves de serviço para todos os seus circuitos do ExpressRoute. Após a recuperação das chaves, copie a **chave de serviço** do circuito que você deseja mover para o modelo de implantação do Resource Manager.

        Get-AzureDedicatedCircuit

### <a name="step-2-sign-in-and-create-a-resource-group"></a>Etapa 2: Entrar e criar um grupo de recursos
Entre no ambiente do Resource Manager e crie um novo grupo de recursos.

1. Entre no ambiente do Azure Resource Manager.

        Login-AzureRmAccount

2. Selecione a assinatura do Azure apropriada.

        Get-AzureRmSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzureRmSubscription

3. Modifique o trecho a seguir para criar um novo grupo de recursos se você ainda não tiver um grupo de recursos.

        New-AzureRmResourceGroup -Name "DemoRG" -Location "West US"

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Etapa 3: Mover o circuito de Rota Expressa para o modelo de implantação do Gerenciador de Recursos
Agora você está pronto para mover o circuito de ExpressRoute do modelo de implantação clássico para o do Resource Manager. Antes de prosseguir, examine as informações fornecidas em [Como mover circuitos de ExpressRoute do modelo de implantação clássico para o Resource Manager](expressroute-move.md).

Para mover o circuito, modifique e execute o trecho a seguir:

    Move-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"

> [!NOTE]
> Após a movimentação, o novo nome que está relacionado no cmdlet anterior será usado para o recurso de endereço. O circuito essencialmente será renomeado.
> 

## <a name="modify-circuit-access"></a>Modificar o acesso de circuito

### <a name="to-enable-expressroute-circuit-access-for-both-deployment-models"></a>Para habilitar o acesso ao circuito de ExpressRoute para ambos os modelos de implantação
Depois de mover o circuito do ExpressRoute clássico para o modelo de implantação do Gerenciador de recursos, você pode habilitar o acesso a ambos os modelos de implantação. Execute os cmdlets a seguir para habilitar o acesso a ambos os modelos de implantação:

1. Obtenha os detalhes do circuito.

        $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

2. Defina "Permitir operações clássicas" como TRUE.

        $ckt.AllowClassicOperations = $true

3. Atualize o circuito. Depois que a operação for concluída com êxito, você poderá exibir o circuito no modelo de implantação clássico.

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

4. Execute o cmdlet a seguir para obter os detalhes do circuito de ExpressRoute. Você deve ser capaz de ver a chave de serviço relacionada. 

        get-azurededicatedcircuit

5. Agora você pode gerenciar links para o circuito do ExpressRoute usando os comandos do modelo de implantação clássico para redes virtuais clássicas e os comandos do Gerenciador de recursos para VNets do Gerenciador de recursos. Os artigos a seguir explicam como gerenciar links para o circuito de Rota Expressa:

    * [Vincule sua rede virtual ao circuito de Rota Expressa no modelo de implantação do Gerenciador de Recursos](expressroute-howto-linkvnet-arm.md)
    * [Vincule sua rede virtual ao circuito de Rota Expressa no modelo de implantação clássico](expressroute-howto-linkvnet-classic.md)

### <a name="to-disable-expressroute-circuit-access-to-the-classic-deployment-model"></a>Para desabilitar o circuito de ExpressRoute para o modelo de implantação clássico
Execute os cmdlets a seguir para desabilitar o acesso ao modelo de implantação clássico.

1. Obtenha detalhes do circuito de ExpressRoute.

        $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

2. Defina "Permitir operações clássicas" como FALSE.

        $ckt.AllowClassicOperations = $false

3. Atualize o circuito. Depois que a operação for concluída com êxito, você não poderá exibir o circuito no modelo de implantação clássico.

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## <a name="next-steps"></a>Próximas etapas

* [Criar e modificar o roteamento do circuito da Rota Expressa](expressroute-howto-routing-arm.md)
* [Vincular a rede virtual ao circuito da Rota Expressa](expressroute-howto-linkvnet-arm.md)


