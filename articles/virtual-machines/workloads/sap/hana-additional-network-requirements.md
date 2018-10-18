---
title: Requisitos de rede adicionais para SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Requisitos de rede adicionais para SAP HANA no Azure (Instâncias Grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8ee89553846309bd96f3cad3648e337488cf07d3
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44167568"
---
# <a name="additional-network-requirements"></a>Requisitos de rede adicionais

Às vezes, você pode ter requisitos de rede adicionais como parte da implantação do HANA em Instâncias Grandes. Este artigo ilustra os seguintes requisitos de rede:
- [Como adicionar mais endereços IP ou sub-redes ](#adding-more-ip-addresses-or-subnets)
- [Como adicionar redes virtuais](#adding-vnets)
- [Como aumentar a largura de banda do circuito do ExpressRoute](#increasing-expressroute-circuit-bandwidth)
- [Como adicionar um circuito do ExpressRoute adicional](#adding-an-additional-expressroute-circuit)
- [Como excluir uma sub-rede](#deleting-a-subnet)
- [Como excluir a rede virtual](#deleting-a-vnet)
- [Como excluir um circuito do ExpressRoute](#deleting-an-expressroute-circuit)


## <a name="adding-more-ip-addresses-or-subnets"></a>Como adicionar mais endereços IP ou sub-redes

Use o portal do Azure, o PowerShell ou a CLI ao adicionar mais endereços IP ou sub-redes.

Nesse caso, a recomendação é adicionar o novo intervalo de endereços IP como um novo intervalo ao Espaço de Endereço da VNet, em vez de gerar um novo intervalo agregado. Em ambos os casos, você precisa enviar essa alteração para a Microsoft para permitir a conectividade fora desse novo intervalo de endereços IP para as unidades de instância grande HANA no seu cliente. Abra uma solicitação de suporte do Azure para obter o novo espaço de endereço da VNet adicionado. Após receber a confirmação, realize as próximas etapas.

Para criar uma sub-rede adicional no portal do Azure, confira o artigo [Criar uma rede virtual usando o portal do Azure](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network) e para criar a partir do PowerShell, confira [Criar uma rede virtual usando o PowerShell](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network).

## <a name="adding-vnets"></a>Como adicionar redes virtuais

Depois de conectar inicialmente uma ou mais redes virtuais do Azure, talvez você queira adicionar mais que acessam SAP HANA no Azure (Instâncias Grandes). Primeiro, envie uma solicitação de suporte do Azure. Nessa solicitação inclua as informações específicas identificando a implantação específica do Azure e os intervalos de Espaço de Endereço IP para do Espaço de Endereço de Rede Virtual do Azure. O SAP HANA no Gerenciamento de Serviços do Azure fornece as informações necessárias que você precisa para conectar as redes virtuais adicionais e a ExpressRoute. Para cada VNet, você precisa de uma Chave de Autorização exclusiva para se conectar ao Circuito do ExpressRoute nas Instâncias Grandes do HANA.

Etapas para adicionar uma nova rede virtual do Azure:

1. Conclua a primeira etapa do processo de integração e consulte a seção _Criando uma VNet do Azure_.
2. Conclua a segunda etapa do processo de integração e consulte a seção _Criando uma sub-rede de gateway_.
3. Para conectar VNets adicionais ao circuito do ExpressRoute da Instância Grande do HANA, abra uma solicitação de suporte do Azure com informações sobre a nova VNet e solicite uma nova Chave de Autorização.
4. Quando notificado de que a autorização foi concluída, use as informações de autorização fornecidas pela Microsoft para concluir a terceira etapa do processo de integração e consulte a seção _Vinculando VNets_.

## <a name="increasing-expressroute-circuit-bandwidth"></a>Como aumentar a largura de banda do circuito de ExpressRoute

Consulte com o SAP HANA no Gerenciamento de Serviços do Azure. Se você for aconselhado a aumentar a largura de banda do SAP HANA no circuito de ExpressRoute do Azure (Instâncias Grandes), crie uma solicitação de suporte do Azure. (Você pode solicitar um aumento para uma largura de banda do circuito único até um máximo de 10 Gbps). Em seguida você receberá uma notificação após a operação ser concluída. Nenhuma ação adicional é necessária para habilitar essa velocidade superior no Azure.

## <a name="adding-an-additional-expressroute-circuit"></a>Como adicionar um circuito de ExpressRoute adicional

Consulte com o SAP HANA no Gerenciamento de Serviços do Azure, se você estiver ciente de que um circuito de ExpressRoute adicional é necessário. Crie uma solicitação de suporte do Azure para criar um novo circuito de ExpressRoute (e para obter informações de autorização para se conectar a ela). O espaço de endereço que será usado nas VNet deve ser definido antes de fazer a solicitação, para que o Gerenciamento de Serviços do SAP HANA no Azure forneça a autorização.

Depois que o novo circuito for criado e a configuração do Gerenciamento de Serviços do SAP HANA no Azure for concluída, você receberá uma notificação com as informações necessárias para continuar. Siga as etapas fornecidas acima para criar e conectar-se à nova rede virtual para este circuito adicional. Você não poderá se conectar às VNets do Azure com esse circuito adicional se elas já estiverem conectadas a outro circuito do ExpressRoute do SAP HANA no Azure (Instância Grande) na mesma região do Azure.

## <a name="deleting-a-subnet"></a>Como excluir uma sub-rede

Para remover uma sub-rede da VNet, o portal do Azure, o PowerShell ou a CLI podem ser usados. Caso o IP de Rede Virtual do Azure endereço intervalo/Azure Espaço de Endereço de rede virtual foi um intervalo agregado, não há nenhum acompanhamento para você com a Microsoft. Exceto se a VNet ainda estiver propagando o espaço de endereço da rota BGP que inclui a sub-rede excluída. Se você tiver definido o IP de Rede Virtual do Azure endereço intervalo/Azure Espaço de Endereço de rede virtual como vários intervalos de endereços IP que um deles foi atribuído à sua sub-rede excluído, você deve excluir que fora do seu Espaço de Endereço de rede virtual e informar subsequentemente SAP HANA em gerenciamento de serviços do Azure para removê-lo do SAP HANA no Azure (instâncias grandes) tem permissão para se comunicar com os intervalos.

Para excluir uma sub-rede, consulte [Excluir uma sub-rede](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet) para obter mais informações sobre a criação de sub-redes.

## <a name="deleting-a-vnet"></a>Como excluir uma rede virtual

Para excluir uma rede virtual, consulte [Excluir uma rede virtual](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network). O SAP HANA no Gerenciamento de Serviços do Azure remove as autorizações existentes no SAP HANA no circuito de ExpressRoute do Azure (Instâncias Grandes) e remove o intervalo de endereços IP de Rede Virtual do Aure/Espaço de Endereço de Rede Virtual do Aure para a comunicação com Instâncias Grandes HANA.

Depois que a rede virtual tiver sido removida, abra uma solicitação de suporte do Azure para fornecer os intervalos de endereço IP a serem removidos.

Para garantir que tudo seja removido, exclua os itens a seguir:

- A conexão do ExpressRoute, o Gateway da VNet, o IP Público do Gateway de VNet e a VNet

## <a name="deleting-an-expressroute-circuit"></a>Como excluir um circuito de ExpressRoute

Para remover um circuito adicional do ExpressRoute do SAP HANA no Azure (Instâncias Grandes), abra uma solicitação de suporte do Azure com o Gerenciamento de Serviços do SAP HANA no Azure e solicite a exclusão do circuito. Na assinatura do Azure, você pode excluir ou manter a rede virtual conforme necessário. No entanto, você deve excluir a conexão entre o circuito de ExpressRoute de instâncias grandes HANA e o gateway de rede virtual vinculado.

Se você também quiser remover uma rede virtual, siga as diretrizes sobre Como excluir uma rede virtual na seção acima.

**Próximas etapas**

- Veja [Como instalar e configurar o SAP HANA (instâncias grandes) no Azure](hana-installation.md).
