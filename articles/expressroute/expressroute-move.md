---
title: "Movendo os circuitos da ExpressRoute do clássico para o Gerenciador de Recursos | Microsoft Docs"
description: "Esta página fornece uma visão geral do que você precisa saber ao fazer uma ponte entre os modelos de implantação clássico e do Gerenciador de Recursos."
documentationcenter: na
services: expressroute
author: ganesr
manager: carmonm
editor: 
ms.assetid: bdf01217-1a98-4ec0-a08e-d84fd37f78af
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: ganesr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 945923d9858ec0ed31272c23268b263f77b5c7a4


---
# <a name="moving-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>Mova os circuitos da Rota Expressa do clássico para o modelo de implantação do Gerenciador de Recursos
Este artigo fornece uma visão geral do que significa mover um circuito da Rota Expressa do clássico para o modelo de implantação do Azure Resource Manager.

[!INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Você pode usar um único circuito da Rota Expressa para conectar as redes virtuais implantadas nos modelos clássico e de implantação do Gerenciador de Recursos. Um circuito da Rota Expressa, independentemente de como é criado, agora pode ser vinculado às redes virtuais nos dois modelos de implantação.

![Um circuito da Rota Expressa que vincula as redes virtuais nos dois modelos de implantação](./media/expressroute-move/expressroute-move-1.png)

## <a name="expressroute-circuits-that-are-created-in-the-classic-deployment-model"></a>Circuitos da Rota Expressa criados no modelo de implantação clássico
Os circuitos da Rota Expressa criados no modelo de implantação clássico precisam ser movidos para o modelo de implantação do Gerenciador de Recursos primeiro para habilitar a conectividade com os modelos de implantação clássico e do Gerenciador de Recursos. Não há perda de conectividade nem interrupção quando uma conexão está sendo movida. Todos os links entre a rede virtual e o circuito no modelo de implantação clássico (dentro da mesma assinatura e entre as assinaturas) são preservados.

Depois da migração ser concluída com êxito, o circuito da Rota Expressa será parecido e executado exatamente como um circuito da Rota Expressa criado no modelo de implantação do Gerenciador de Recursos. Agora, você poderá criar conexões com as redes virtuais no modelo de implantação do Gerenciador de Recursos.

Depois de um circuito da Rota Expressa ser movido para o modelo de implantação do Gerenciador de Recursos, você poderá gerenciar o ciclo de vida desse circuito usando apenas o modelo de implantação do Gerenciador de Recursos. Isso significa que você pode realizar operações como adicionar/atualizar/excluir emparelhamentos, atualizar as propriedades do circuito (como largura de banda, SKU e tipo de cobrança) e excluir os circuitos apenas no modelo de implantação do Gerenciador de Recursos. Consulte a seção abaixo sobre os circuitos criados no modelo de implantação do Gerenciador de Recursos para obter mais detalhes sobre como você pode gerenciar o acesso a ambos os modelos de implantação.

Você não precisa envolver seu provedor de conectividade para realizar a migração.

## <a name="expressroute-circuits-that-are-created-in-the-resource-manager-deployment-model"></a>Circuitos da Rota Expressa criados no modelo de implantação do Gerenciador de Recursos
Você pode habilitar os circuitos da Rota Expressa criados no modelo de implantação do Gerenciador de Recursos para que sejam acessíveis a partir de ambos os modelos de implantação. Qualquer circuito da Rota Expressa em sua assinatura pode ser habilitado para ser acessado a partir de ambos os modelos de implantação.

* Os circuitos da Rota Expressa criados no modelo de implantação do Gerenciador de Recursos não têm acesso ao modelo de implantação clássico por padrão.
* Os circuitos da Rota Expressa movidos do modelo de implantação clássico para o modelo de implantação do Gerenciador de Recursos são acessíveis a partir de ambos os modelos de implantação por padrão.
* Um circuito da Rota Expressa sempre tem acesso ao modelo de implantação do Gerenciador de Recursos, independentemente dele ter sido criado no modelo de implantação do Gerenciador de Recursos ou clássico. Isso significa que você pode criar conexões com as redes virtuais criadas no modelo de implantação do Gerenciador de Recursos seguindo as instruções em [como vincular as redes virtuais](expressroute-howto-linkvnet-arm.md).
* O acesso ao modelo de implantação clássico é controlado pelo parâmetro **allowClassicOperations** no circuito da Rota Expressa.

> [!IMPORTANT]
> Todas as cotas documentadas na página de [limites do serviço](../azure-subscription-service-limits.md) aplicam-se. Por exemplo, um circuito padrão pode ter no máximo 10 links de rede virtual/conexões nos modelos de implantação clássico e do Gerenciador de Recursos.
> 
> 

## <a name="controlling-access-to-the-classic-deployment-model"></a>Controlando o acesso ao modelo de implantação clássico
Você pode habilitar um único circuito da Rota Expressa para vincular as redes virtuais em ambos os modelos de implantação definindo o parâmetro **allowClassicOperations** do circuito da Rota Expressa.

A definição de **allowClassicOperations** para TRUE permite vincular as redes virtuais de ambos os modelos de implantação ao circuito da Rota Expressa. Você pode vincular as redes virtuais no modelo de implantação clássico seguindo as diretrizes em [como vincular as redes virtuais no modelo de implantação clássico](expressroute-howto-linkvnet-classic.md). Você pode vincular as redes virtuais no modelo de implantação do Gerenciador de Recursos seguindo as diretrizes em [como vincular as redes virtuais no modelo de implantação do Gerenciador de Recursos](expressroute-howto-linkvnet-arm.md).

Definir **allowClassicOperations** para FALSE bloqueia o acesso ao circuito a partir do modelo de implantação clássico. Porém, todos os links da rede virtual no modelo de implantação clássico são preservados. Nesse caso, o circuito da Rota Expressa não é visível no modelo de implantação clássico.

## <a name="supported-operations-in-the-classic-deployment-model"></a>Operações com suporte no modelo de implantação clássico
As seguintes operações clássicas têm suporte em um circuito da Rota Expressa quando **allowClassicOperations** é definido para TRUE:

* Obter informações do circuito da Rota Expressa
* Criar/atualizar/get/excluir links da rede virtual para as redes virtuais clássicas
* Criar/atualizar/obter/excluir autorizações de link da rede virtual para a conectividade entre as assinaturas

Não é possível executar as seguintes operações clássicas quando **allowClassicOperations** está definido para TRUE:

* Criar/atualizar/obter/excluir emparelhamentos BGP (Border Gateway Protocol ) para os emparelhamentos dos Azures privado e público, e da Microsoft
* Excluir circuitos da Rota Expressa

## <a name="communication-between-the-classic-and-the-resource-manager-deployment-models"></a>Comunicação entre os modelos de implantação clássico do Gerenciador de Recursos
O circuito da Rota Expressa atua como uma ponte entre os modelos de implantação clássico e do Gerenciador de Recursos. O tráfego entre as máquinas virtuais nas redes virtuais no modelo de implantação clássico e aquelas nas redes virtuais no modelo de implantação do Gerenciador Recursos fluirá pela Rota Expressa se ambas as redes virtuais estiverem vinculadas ao mesmo circuito da Rota Expressa.

A taxa de transferência agregada é limitada pela capacidade da taxa de transferência do gateway de rede virtual. O tráfego não entra nas redes do provedor de conectividade nem em suas redes em tais casos. O fluxo do tráfego entre as redes virtuais está totalmente contido na rede da Microsoft.

## <a name="access-to-azure-public-and-microsoft-peering-resources"></a>Acesso aos recursos de emparelhamento do Azure público e da Microsoft
Você pode continuar a acessar os recursos normalmente acessíveis por meio do emparelhamento do Azure público e do emparelhamento da Microsoft sem interrupções.  

## <a name="whats-supported"></a>O que tem suporte
Esta seção descreve o que tem suporte para os circuitos da Rota Expressa:

* Você pode usar um único circuito da Rota Expressa para conectar as redes virtuais implantadas nos modelos de implantação clássico e do Gerenciador de Recursos.
* É possível mover um circuito da Rota Expressa do clássico para o modelo de implantação do Gerenciador de Recursos. Após a migração, o circuito da Rota Expressa é parecido e executado como qualquer outro circuito da Rota Expressa criado no modelo de implantação do Gerenciador de Recursos.
* É possível mover apenas o circuito da Rota Expressa. Os links do circuito, redes virtuais e gateways VPN não podem ser movidos com essa operação.
* Depois de um circuito da Rota Expressa ser movido para o modelo de implantação do Gerenciador de Recursos, você poderá gerenciar o ciclo de vida desse circuito usando apenas o modelo de implantação do Gerenciador de Recursos. Isso significa que você pode realizar operações como adicionar/atualizar/excluir emparelhamentos, atualizar as propriedades do circuito (como largura de banda, SKU e tipo de cobrança) e excluir os circuitos apenas no modelo de implantação do Gerenciador de Recursos.
* O circuito da Rota Expressa atua como uma ponte entre os modelos de implantação clássico e do Gerenciador de Recursos. O tráfego entre as máquinas virtuais nas redes virtuais no modelo de implantação clássico e aquelas nas redes virtuais no modelo de implantação do Gerenciador Recursos fluirá pela Rota Expressa se ambas as redes virtuais estiverem vinculadas ao mesmo circuito da Rota Expressa.
* A conectividade entre as assinaturas é suportada nos modelos de implantação clássico e do Gerenciador de Recursos.

## <a name="whats-not-supported"></a>O que não tem suporte
Esta seção descreve o que não tem suporte para os circuitos da Rota Expressa:

* Mover os links do circuito, gateways e redes virtuais do clássico para o modelo de implantação do Gerenciador de Recursos.
* Gerenciar o ciclo de vida de um circuito da Rota Expressa a partir do modelo de implantação clássico.
* Suporte RBAC (Controle de Acesso Baseado em Funções) para o modelo de implantação clássico. Não é possível executar os controles RBAC para um circuito no modelo de implantação clássico. Nenhum administrador/coadministrator da assinatura pode vincular ou desvincular as redes virtuais para o circuito.

## <a name="configuration"></a>Configuração
Siga as instruções descritas em [Mover um circuito da Rota Expressa do clássico para o modelo de implantação do Gerenciador de Recursos](expressroute-howto-move-arm.md).

## <a name="next-steps"></a>Próximas etapas
* Para obter informações sobre o fluxo de trabalho, consulte [Fluxos de trabalho de provisionamento e estados do circuito da Rota Expressa](expressroute-workflows.md)
* Para configurar sua conexão da Rota Expressa:
  
  * [Criar um circuito da Rota Expressa](expressroute-howto-circuit-arm.md)
  * [Configurar o roteamento](expressroute-howto-routing-arm.md)
  * [Vincular uma rede virtual a um circuito de Rota Expressa](expressroute-howto-linkvnet-arm.md)




<!--HONumber=Nov16_HO2-->


