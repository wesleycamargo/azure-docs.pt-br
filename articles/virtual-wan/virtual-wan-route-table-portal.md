---
title: Criar uma tabela de rotas de hub virtual WAN Virtual do Azure - portal do Azure | Microsoft Docs
description: Tabela de rotas do hub virtual de WAN virtual para conduzir o tráfego para um dispositivo de rede virtual usando o portal.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 2c8b3b4671fd14f9b10b8491861ae2c652f0188b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60461647"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Crie uma tabela de rotas do hub WAN Virtual para NVAs: Portal do Azure

Este artigo mostra como conduzir o tráfego de um hub para uma rede dispositivo Virtual (NVA).

![Diagrama de WAN virtual](./media/virtual-wan-route-table/vwanroute.png)

## <a name="before-you-begin"></a>Antes de começar

Verifique se você atende aos seguintes critérios:

*  Você tem um dispositivo Virtual de rede (NVA). Uma solução de virtualização de rede é um software de terceiros de sua escolha que normalmente é provisionado no Azure Marketplace em uma rede virtual.

    * Um endereço IP privado deve ser atribuído ao adaptador de rede NVA.

    * A NVA não está implantada no hub virtual. Ela deve ser implantada em uma VNet separada.

    *  A rede virtual de NVA pode ter uma ou várias redes virtuais conectados a ele. Neste artigo, nos referimos à VNet NVA como um 'indireta spoke rede virtual'. Essas redes virtuais podem ser conectados à rede virtual de NVA usando o emparelhamento de rede virtual.
*  Você criou 2 VNets. Eles serão usados como redes virtuais spoke.

    * Para este exercício, espaços de endereço de rede virtual spoke são: VNet1: 10.0.2.0/24 e VNet2: 10.0.3.0/24. Se você precisar obter informações sobre como criar uma rede virtual, consulte [criar uma rede virtual](../virtual-network/quick-create-portal.md).

    * Verifique se que não houver nenhum gateway de rede virtual em qualquer uma das redes virtuais.
    * Para essa configuração, essas redes virtuais não exigem uma sub-rede de gateway.

## <a name="signin"></a>1. Entrar

Em um navegador, acesse o [Portal do Azure](https://portal.azure.com) e entre com sua conta do Azure.

## <a name="vwan"></a>2. Criar uma WAN virtual

Crie uma WAN virtual. Para os fins deste exercício, você pode usar os seguintes valores:

* **Nome WAN virtual:** myVirtualWAN
* **Grupo de recursos:** testRG
* **Localização:** Oeste dos EUA

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="hub"></a>3. Criar um hub

Crie o hub. Para os fins deste exercício, você pode usar os seguintes valores:

* **Localização:** Oeste dos EUA
* **Nome:** westushub
* **Espaço de endereço privado do hub:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="route"></a>4. Criar e aplicar uma tabela de rotas do hub

Atualize o hub com uma tabela de rotas do hub. Para os fins deste exercício, você pode usar os seguintes valores:

* **Espaços de endereço de rede virtual spoke indireta:** 10.0.3.0/24 e 10.0.2.0/24 (VNet1 e VNet2)
* **Rede de Perímetro NVA interface privado endereço IP da rede:** 10.0.4.5

1. Navegue até sua WAN virtual.
2. Clique no hub para o qual você deseja criar uma tabela de rotas.
3. Clique o **...** e, em seguida, clique em **edição hub virtual**.
4. Sobre o **hub virtual de edição** página, role para baixo e selecione a caixa de seleção **tabela de uso para o roteamento**.
5. No **se o prefixo de destino for** coluna, adicionar os espaços de endereço. No **enviar para o próximo salto** coluna, adicione o endereço IP privado DMZ NVA rede interface.
6. Clique em **confirmar** para atualizar os recursos de hub com as configurações de tabela de rota.

## <a name="connections"></a>5. Criar as conexões de rede virtual

Crie uma conexão de cada spoke indireta de rede virtual (VNet1 e VNet2) para o hub. Em seguida, crie uma conexão de rede virtual de NVA para o hub.

 Para esta etapa, você pode usar os seguintes valores:

| Nome da VNet| Nome da conexão|
| --- | --- |
| VNet1 | testconnection1 |
| VNet2 | testconnection2 |
| NVAVNet | testconnection3 |

Repita o procedimento a seguir para cada rede virtual que você deseja se conectar.

1. Na página da WAN virtual, clique em **Conexões de rede virtual**.
2. Na página de conexão de rede virtual, clique em **+Adicionar conexão**.
3. Na página **Adicionar conexão**, preencha os seguintes campos:

    * **Nome da Conexão**: nomeie sua conexão.
    * **Hubs**: selecione o hub que você deseja associar a essa conexão.
    * **Assinatura**: verifique a assinatura.
    * **Rede virtual:** selecione a rede virtual que você deseja conectar a esse hub. A rede virtual não pode ter um gateway de rede virtual já existente.
4. Clique em **OK** para criar a conexão.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a WAN Virtual, consulte a página [Visão geral de WAN Virtual](virtual-wan-about.md).