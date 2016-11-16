---
title: Como configurar o roteamento de um circuito da ExpressRoute usando o Portal do Azure | Microsoft Docs
description: "Este artigo fornece uma orientação sobre as etapas de criação e de provisionamento do emparelhamento público, privado e da Microsoft de um circuito de Rota Expressa. Este artigo também mostra como verificar o status, atualizar ou excluir emparelhamentos de seu circuito."
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 8c2a7ed2-ae5c-4e49-81f6-77cf9f2b2ac9
ms.service: expressroute
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1d78bdb6cbb9870298b050207fd1fa9c5e71d674


---
# <a name="create-and-modify-routing-for-an-expressroute-circuit"></a>Criar e modificar o roteamento de um circuito da Rota Expressa
> [!div class="op_single_selector"]
> [Portal do Azure - Gerenciador de Recursos](expressroute-howto-routing-portal-resource-manager.md)
> [PowerShell - Gerenciador de Recursos](expressroute-howto-routing-arm.md)
> [PowerShell - Clássico](expressroute-howto-routing-classic.md)
> 
> 

Esse artigo explica as etapas para criar e gerenciar a configuração de roteamento de um circuito da Rota Expressa usando o portal do Azure e o modelo de implantação do Gerenciador de Recursos.

**Sobre modelos de implantação do Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Pré-requisitos de configuração
* Verifique se você leu a página de [pré-requisitos](expressroute-prerequisites.md), a página de [requisitos do roteamento](expressroute-routing.md) e a página [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.
* Você deve ter um circuito da Rota Expressa ativo. Antes de continuar, siga as instruções para [criar um circuito da Rota Expressa](expressroute-howto-circuit-arm.md) e para que o circuito seja habilitado pelo provedor de conectividade. O circuito da Rota Expressa deve estar em um estado provisionado e habilitado e para que você possa executar os cmdlets descritos abaixo.

Estas instruções se aplicam apenas a circuitos criados com provedores de serviço que oferecem serviços de conectividade de Camada 2. Se você estiver usando um provedor de serviços que oferece serviços gerenciados de Camada 3 (normalmente um IPVPN, como MPLS), seu provedor de conectividade configurará e gerenciará o roteamento para você. 

> [!IMPORTANT]
> Atualmente, não anunciamos emparelhamentos configurados pelos provedores de serviço por meio do portal de gerenciamento de serviço. Estamos trabalhando para habilitar esse recurso em breve. Entre em contato com seu provedor de serviços antes de configurar os emparelhamentos BGP.
> 
> 

Você pode configurar um, dois ou todos os três emparelhamentos (privado e público do Azure e da Microsoft) para um circuito da Rota Expressa. Você pode configurar emparelhamentos em qualquer ordem escolhida. No entanto, você deve concluir a configuração de um emparelhamento por vez. 

## <a name="azure-private-peering"></a>Emparelhamento privado do Azure
Esta seção fornece instruções sobre como criar, obter, atualizar e excluir a configuração de emparelhamento privado do Azure para um circuito da Rota Expressa. 

### <a name="to-create-azure-private-peering"></a>Criar um emparelhamento privado do Azure
1. Configure o circuito da Rota Expressa. Verifique se o circuito foi totalmente provisionado pelo provedor de conectividade antes de continuar.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Configure o emparelhamento privado do Azure para o circuito. Verifique se você tem os seguintes itens antes de continuar com as próximas etapas:
   
   * Uma sub-rede /30 para o link principal. Ela não deve fazer parte de qualquer espaço de endereçamento reservado para redes virtuais.
   * Uma sub-rede /30 para o link secundário. Ela não deve fazer parte de qualquer espaço de endereçamento reservado para redes virtuais.
   * Uma ID válida de VLAN para estabelecer esse emparelhamento. Verifique se nenhum outro emparelhamento no circuito usa a mesma ID de VLAN.
   * Número de AS para emparelhamento. Você pode usar um número de AS de 2 e de 4 bytes. Você pode usar um número de AS privado para esse emparelhamento. Não use 65515.
   * Um Hash MD5, se você optar por usar um. **Isso é opcional**.
3. Selecione a linha de emparelhamento Privado do Azure, conforme mostrado abaixo.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rprivate1.png)
4. Configure o emparelhamento privado. A imagem abaixo mostra um exemplo de configuração.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)
5. Salve a configuração depois que você tiver especificado todos os parâmetros. Depois que a configuração for aceito com êxito, você verá algo semelhante ao exemplo a seguir.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="to-view-azure-private-peering-details"></a>Para exibir detalhes sobre o emparelhamento privado do Azure
Você pode exibir as propriedades de emparelhamento privado do Azure selecionando o emparelhamento.

![](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="to-update-azure-private-peering-configuration"></a>Atualizar a configuração de emparelhamento privado do Azure
Você pode selecionar a linha para emparelhamento e modificar as propriedades de emparelhamento. 

![](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)

### <a name="to-delete-azure-private-peering"></a>Excluir um emparelhamento privado do Azure
Você pode remover a configuração de emparelhamento selecionando o ícone Excluir, conforme mostrado abaixo.

![](./media/expressroute-howto-routing-portal-resource-manager/rprivate4.png)

## <a name="azure-public-peering"></a>Emparelhamento público do Azure
Esta seção fornece instruções sobre como criar, obter, atualizar e excluir a configuração de emparelhamento público do Azure para um circuito da Rota Expressa. 

### <a name="to-create-azure-public-peering"></a>Criar o emparelhamento público do Azure
1. Configure o circuito da Rota Expressa. Verifique se o circuito foi totalmente provisionado pelo provedor de conectividade antes de continuar.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Configure o emparelhamento público do Azure para o circuito. Verifique se você tem os seguintes itens antes de continuar com as próximas etapas:
   
   * Uma sub-rede /30 para o link principal. 
   * Uma sub-rede /30 para o link secundário. 
   * Todos os endereços IP usados para configurar o emparelhamento devem ser endereços IPv4 públicos válidos.
   * Uma ID válida de VLAN para estabelecer esse emparelhamento. Verifique se nenhum outro emparelhamento no circuito usa a mesma ID de VLAN.
   * Número de AS para emparelhamento. Você pode usar um número de AS de 2 e de 4 bytes.
   * Um Hash MD5, se você optar por usar um. **Isso é opcional**.
3. Selecione a linha de emparelhamento público do Azure, conforme mostrado abaixo.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rpublic1.png)
4. Configure o emparelhamento público. A imagem abaixo mostra um exemplo de configuração.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)
5. Salve a configuração depois que você tiver especificado todos os parâmetros. Depois que a configuração for aceito com êxito, você verá algo semelhante ao exemplo a seguir.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="to-view-azure-public-peering-details"></a>Para exibir detalhes sobre o emparelhamento público do Azure
Você pode exibir as propriedades de emparelhamento público do Azure selecionando o emparelhamento.

![](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="to-update-azure-public-peering-configuration"></a>Atualizar a configuração de emparelhamento público do Azure
Você pode selecionar a linha para emparelhamento e modificar as propriedades de emparelhamento. 

![](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)

### <a name="to-delete-azure-public-peering"></a>Excluir o emparelhamento público do Azure
Você pode remover a configuração de emparelhamento selecionando o ícone Excluir, conforme mostrado abaixo.

![](./media/expressroute-howto-routing-portal-resource-manager/rpublic4.png)

## <a name="microsoft-peering"></a>Emparelhamento da Microsoft
Esta seção fornece instruções sobre como criar, obter, atualizar e excluir a configuração de emparelhamento da Microsoft para um circuito da Rota Expressa. 

### <a name="to-create-microsoft-peering"></a>Criar emparelhamento da Microsoft
1. Configure o circuito da Rota Expressa. Verifique se o circuito foi totalmente provisionado pelo provedor de conectividade antes de continuar.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Configurar o emparelhamento da Microsoft para o circuito. Você precisa ter as seguintes informações antes de continuar:
   
   * Uma sub-rede /30 para o link principal. Este valor deve ser um prefixo IPv4 público válido próprio e registrado em um RIR/IRR.
   * Uma sub-rede /30 para o link secundário. Este valor deve ser um prefixo IPv4 público válido próprio e registrado em um RIR/IRR.
   * Uma ID válida de VLAN para estabelecer esse emparelhamento. Verifique se nenhum outro emparelhamento no circuito usa a mesma ID de VLAN.
   * Número de AS para emparelhamento. Você pode usar um número de AS de 2 e de 4 bytes.
   * **Prefixos anunciados:** você deve fornecer uma lista de todos os prefixos que planeja anunciar na sessão BGP. Somente prefixos de endereços IP públicos são aceitos. Caso você planeje enviar um conjunto de prefixos, envie uma lista separada por vírgulas. Esses prefixos devem ser registrados em seu nome em um RIR/IRR.
   * **ASN de cliente:** se você estiver anunciando prefixos não registrados com o número AS de emparelhamento, especifique o número AS com o qual eles estão registrados. **Isso é opcional**.
   * **Nome do registro de roteamento:** você pode especificar o RIR/IRR com base no qual o número de AS e os prefixos estão registrados. **Isso é opcional.**
   * Um hash MD5, se você optar por usar um. **Isso é opcional.**
3. Você pode selecionar o emparelhamento que deseja configurar conforme mostrado abaixo. Selecione a linha de emparelhamento da Microsoft.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft1.png)
4. Configure o emparelhamento da Microsoft. A imagem abaixo mostra um exemplo de configuração.
   
   ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft2.png)
5. Salve a configuração depois que você tiver especificado todos os parâmetros. 
   
    Se o circuito for para um estado de validação necessária (como mostrado abaixo), você deverá abrir um tíquete de suporte para mostrar a prova de propriedade dos prefixos à nossa equipe de suporte.    
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft5.png)

    Você pode abrir um tíquete de suporte diretamente no portal, conforme mostrado abaixo     

    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft6.png)


1. Depois que a configuração for aceito com êxito, você verá algo semelhante ao exemplo a seguir.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="to-view-microsoft-peering-details"></a>Para exibir detalhes de emparelhamento da Microsoft
Você pode exibir as propriedades de emparelhamento público do Azure selecionando o emparelhamento.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft3.png)

### <a name="to-update-microsoft-peering-configuration"></a>Atualizar a configuração de emparelhamento da Microsoft
Você pode selecionar a linha para emparelhamento e modificar as propriedades de emparelhamento. 

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="to-delete-microsoft-peering"></a>Excluir emparelhamento da Microsoft
Você pode remover a configuração de emparelhamento selecionando o ícone Excluir, conforme mostrado abaixo.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft4.png)

## <a name="next-steps"></a>Próximas etapas
A próxima etapa será [Vincular uma Rede Virtual a um circuito da Rota Expressa](expressroute-howto-linkvnet-arm.md).

* Para saber mais sobre fluxos de trabalho da Rota Expressa, confira [Fluxos de trabalho da Rota Expressa](expressroute-workflows.md).
* Para obter mais informações sobre o emparelhamento de circuito, veja [Circuitos e domínios de roteamento da Rota Expressa](expressroute-circuit-peerings.md).
* Para saber mais sobre redes virtuais, confira [Visão geral da rede virtual](../virtual-network/virtual-networks-overview.md).




<!--HONumber=Nov16_HO2-->


