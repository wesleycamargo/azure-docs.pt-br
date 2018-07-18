---
title: Exibir o consumo de endereço IP público na pilha do Azure | Microsoft Docs
description: Os administradores podem exibir o consumo de endereços IP públicos em uma região
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 0f77be49-eafe-4886-8c58-a17061e8120f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: 50bf01d6de6105d3041c6bb88e803f3d110f751d
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2018
ms.locfileid: "29742451"
---
# <a name="view-public-ip-address-consumption-in-azure-stack"></a>Exibir o consumo de endereço IP público na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Como um administrador de nuvem, você pode exibir:
 - O número de endereços IP públicos que foram alocados para locatários.
 - O número de endereços IP públicos que ainda estão disponíveis para alocação.
 - A porcentagem de endereços IP públicos que tenham sido alocados nesse local.

O **uso de pools de IP público** bloco mostra o número de endereços IP públicos consumido em pools de endereços IP públicos. Para cada endereço IP, o bloco mostra o uso do locatário IaaS VM instâncias, serviços de infraestrutura de malha e recursos de endereço IP públicos que foram explicitamente criados por locatários.

A finalidade do bloco é dar uma ideia do número de endereços IP públicos usados neste local de operadores de pilha do Azure. O número ajuda os administradores determinar se eles estão em execução baixos nesse recurso.

O **endereços IP públicos** item de menu no **recursos de locatário** lista apenas os endereços IP públicos que foram *criados explicitamente por locatários*. Você pode encontrar o item de menu no **provedores de recursos**, **rede** painel. O número de **usado** endereços IP públicos de **uso de pools de IP público** bloco sempre é diferente de (maior que) o número no **endereços IP públicos** lado a lado em  **Recursos de locatário**.

## <a name="view-the-public-ip-address-usage-information"></a>Exibir as informações de uso de endereço IP públicas
Para exibir o número total de endereços IP públicos que tiverem sido consumidos na região:

1. No portal do administrador de pilha do Azure, selecione **mais serviços**, em **recursos administrativos**, selecione **provedores de recursos**.
2. Na lista de **provedores de recursos**, selecione **rede**.
3. O **rede** painel exibe o **uso de pools de IP público** lado a lado no **visão geral** seção.

![Painel do provedor de recursos de rede](media/azure-stack-viewing-public-ip-address-consumption/image01.png)

O **usado** número representa o número de atribuídos endereços IP públicos de pools de endereços IP públicos. O **livre** número representará o número de IP público endereços de IP público endereço pools que não foram atribuídos e ainda estão disponíveis. O **% usada da** número representa o número de usado ou endereços atribuídos como uma porcentagem do número total de endereços IP públicos em pools de endereços IP públicos nesse local.

## <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>Exibir os endereços IP públicos que foram criados pelas assinaturas do locatário
Selecione **endereços IP públicos** em **recursos de locatário**. Examine a lista de endereços IP públicos explicitamente criadas pelo assinaturas do locatário em uma região específica.

![Endereços IP públicos de locatário](media/azure-stack-viewing-public-ip-address-consumption/image02.png)

Você poderá notar que alguns endereços IP públicos que tenham sido alocados dinamicamente aparecem na lista. No entanto, um endereço não foi associado a eles ainda. O recurso de endereço foi criado no provedor de recursos de rede, mas ainda não o controlador de rede.

O controlador de rede não atribuir um endereço para o recurso até que ela está associada a uma interface, uma placa de interface de rede (NIC), um balanceador de carga ou um gateway de rede virtual. Quando o endereço IP público associado a uma interface, controlador de rede aloca um endereço IP. O endereço aparece no **endereço** campo.

## <a name="view-the-public-ip-address-information-summary-table"></a>Exibir a público informações resumida tabela de endereços IP
Em casos diferentes, os endereços IP públicos são atribuídos que determinam se o endereço é exibido em uma lista ou outro.

| **Caso de atribuição de endereço IP público** | **Aparece no resumo do uso** | **Aparece na lista de endereços IP pública do locatário** |
| --- | --- | --- |
| Endereço IP dinâmico público ainda não foram atribuído a um balanceador de carga ou NIC (temporário) |Não  |Sim |
| Dinâmico endereço IP público atribuído a um balanceador de carga ou NIC. |Sim |Sim |
| Endereço IP público estático atribuído a um balanceador de carga ou NIC de locatário. |Sim |Sim |
| Endereço IP público estático atribuído a um ponto de extremidade de serviço de infraestrutura de malha. |Sim |Não  |
| Endereço IP público implicitamente criado para instâncias de VM IaaS e usado para NAT de saída em uma rede virtual. Eles são criados em segundo plano sempre que um locatário cria uma instância de VM para que VMs podem enviar informações para a Internet. |Sim |Não  |

## <a name="next-steps"></a>Próximas etapas
[Gerenciar contas de armazenamento na pilha do Azure](azure-stack-manage-storage-accounts.md)