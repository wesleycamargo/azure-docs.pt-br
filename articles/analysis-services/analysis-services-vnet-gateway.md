---
title: Usar o gateway de dados local para fontes de dados da Rede Virtual do Azure | Microsoft Docs
description: Saiba como configurar um servidor para usar um gateway para fontes de dados no VNET.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 61c4463060016ecbd319b027d48c87f3a6a8ad48
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2018
---
# <a name="use-gateway-for-datasources-on-an-azure-virtual-network-vnet"></a>Usar gateway para fontes de dados em uma VNET (Rede Virtual) do Azure

Este artigo descreve a propriedade do servidor **AlwaysUseGateway** para uso quando as fontes de dados estiverem em uma [VNET (Rede Virtual) do Azure](../virtual-network/virtual-networks-overview.md).

## <a name="server-access-to-vnet-datasources"></a>Acesso ao servidor para fontes de dados VNET

Se as fontes de dados forem acessadas por meio de uma VNET, o servidor do Azure Analysis Services deverá conectar essas fontes de dados como se estivessem no local, no próprio ambiente. É possível configurar a propriedade do servidor **AlwaysUseGateway** para especificar o servidor a acessar todos os dados da fonte de dados por meio de um [Gateway local](analysis-services-gateway.md). 

> [!NOTE]
> Esta propriedade será efetiva somente quando um [Gateway de dados local](analysis-services-gateway.md) estiver instalado e configurado. O gateway pode estar na VNET.

## <a name="configure-alwaysusegateway-property"></a>Configurar a propriedade AlwaysUseGateway

1. Em SSMS > servidor > **Propriedades** > **Geral**, selecione **Mostrar Propriedades Avançadas (Todas)**.
2. Em **ASPaaS\AlwaysUseGateway**, selecione **true**.

    ![Sempre use propriedade de gateway](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Consulte também
[Conectando fontes de dados locais](analysis-services-gateway.md)   
[Instalar e configurar um gateway de dados local](analysis-services-gateway-install.md)   
[VNET (Rede Virtual) do Azure](../virtual-network/virtual-networks-overview.md)   

