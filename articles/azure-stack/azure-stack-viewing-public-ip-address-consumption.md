---
title: Exibir consumo de endereço IP público no Azure Stack | Microsoft Docs
description: Os administradores podem exibir o consumo de endereços IP públicos em uma região
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/14/2019
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: d97674940f0af91bf50af87cfe96fda9644d469b
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55242045"
---
# <a name="view-public-ip-address-consumption-in-azure-stack"></a>Exibir consumo de endereço IP público no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Como um administrador de nuvem, você pode exibir:
 - O número de endereços IP públicos que foram alocados para locatários.
 - O número de endereços IP públicos que ainda estão disponíveis para alocação.
 - O percentual de endereços IP públicos que foram alocados nesse local.

O **uso de pools de IP público** bloco mostra o número de endereços IP públicos consumido em pools de endereços IP públicos. Para cada endereço IP, o bloco mostra o uso do locatário VM IaaS instâncias, os serviços de infraestrutura de malha e recursos de endereço IP públicos que foram explicitamente criados por locatários.

A finalidade do bloco é dar uma noção do número de endereços IP públicos usados neste local de operadores do Azure Stack. O número ajuda os administradores determinar se eles estão em execução baixa nesse recurso.

O **endereços IP públicos** item de menu no **recursos de locatário** lista apenas esses endereços IP públicos que foram *explicitamente criada por locatários*. Você pode encontrar o item de menu sobre o **provedores de recursos**, **rede** painel. O número de **usado** endereços IP públicos na **uso de pools de IP público** bloco sempre é diferente de (maior que) o número no **endereços IP públicos** lado a lado sob  **Recursos de locatário**.

## <a name="view-the-public-ip-address-usage-information"></a>Exibir as informações de uso de endereço IP públicas

Para exibir o número total de endereços IP públicos que foram consumidos na região:

1. No portal do administrador do Azure Stack, selecione **todos os serviços**. Em seguida, sob o **ADMINISTRATION** categoria, selecione **rede**.
1. O **rede** painel exibe as **uso de pools de IP público** lado a lado no **visão geral** seção.

![Painel do provedor de recursos de rede](media/azure-stack-viewing-public-ip-address-consumption/image01.png)

O **usado** número representa o número de endereços IP públicos atribuídos de pools de endereços IP públicos. O **gratuito** número representará o número de IP público endereços de IP público endereço pools que ainda não foram atribuídos e ainda estão disponíveis. O **% usada da** número representa o número de usado ou endereços atribuídos como uma porcentagem do número total de endereços IP públicos em pools de endereços IP públicos no local.

## <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>Exibir os endereços IP públicos que foram criados por assinaturas de locatários

Selecione **endereços IP públicos** sob **recursos de locatário**. Examine a lista de endereços IP públicos criados explicitamente por assinaturas de locatários em uma região específica.

![Endereços IP públicos de locatário](media/azure-stack-viewing-public-ip-address-consumption/image02.png)

Você pode notar que alguns endereços IP públicos que foram alocados dinamicamente aparecem na lista. No entanto, um endereço não foi associado a eles ainda. O recurso de endereço foi criado no provedor de recursos de rede, mas ainda não no controlador de rede.

O controlador de rede não atribui um endereço para o recurso até que ele se vincula a uma interface, uma placa de interface de rede (NIC), um balanceador de carga ou um gateway de rede virtual. Quando o endereço IP público é associado a uma interface, o controlador de rede aloca um endereço IP. O endereço aparece na **endereço** campo.

## <a name="view-the-public-ip-address-information-summary-table"></a>Exibir a público informações resumida tabela de endereços IP

Em casos diferentes, os endereços IP públicos são atribuídos que determinam se o endereço aparece na um lista ou outro.

| **Caso de atribuição de endereço IP público** | **É exibida no resumo do uso** | **É exibida na lista de endereços IP pública do locatário** |
| --- | --- | --- |
| Endereço IP dinâmico público ainda não foram atribuído a um NIC ou balanceador de carga (temporário) |Não  |Sim |
| Endereço IP público dinâmico atribuído a um NIC ou balanceador de carga. |Sim |Sim |
| Endereço IP público estático atribuído a um balanceador de carga ou a NIC de locatário. |Sim |Sim |
| Endereço IP público estático atribuído a um ponto de extremidade de serviço de infraestrutura de malha. |Sim |Não  |
| Endereço IP público implicitamente criado para instâncias de VM de IaaS e usado para NAT de saída em uma rede virtual. Eles são criados nos bastidores, sempre que um locatário cria uma instância de VM para que as VMs podem enviar informações para a Internet. |Sim |Não  |

## <a name="next-steps"></a>Próximas etapas

[Gerenciar contas de armazenamento no Azure Stack](azure-stack-manage-storage-accounts.md)