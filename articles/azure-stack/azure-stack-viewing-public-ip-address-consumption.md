---
title: "Exibir o consumo de endereço IP público na pilha do Azure | Microsoft Docs"
description: "Os administradores podem exibir o consumo de endereços IP públicos em uma região"
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: darmour
editor: 
ms.assetid: 0f77be49-eafe-4886-8c58-a17061e8120f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: scottnap
ms.openlocfilehash: 7651565eebf6272f307a4ce4790ca19b41bfa826
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="view-public-ip-address-consumption-in-azure-stack"></a>Exibir o consumo de endereço IP público na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Como um administrador de nuvem, você pode exibir o número de endereços IP públicos que foram alocados para locatários, o número de endereços IP públicos que ainda estão disponíveis para alocação e a porcentagem de endereços IP públicos que tenham sido alocados nesse local.

O **uso de pools de IP público** bloco mostra o número total de endereços IP públicos que tiverem sido consumidos em todos os pools de endereços IP públicos na malha, se forem usados para o locatário instâncias de VM de IaaS, infraestrutura de malha serviços ou recursos de endereço IP públicos que foram explicitamente criados por locatários.

A finalidade desse bloco é dar aos administradores de pilha do Azure uma ideia do que o número total de endereços IP públicos que tiverem sido consumidos neste local. Isso ajuda os administradores determinar se eles estão em execução baixos nesse recurso.

No **provedores de recursos**, **rede** folha, o **endereços IP públicos** item de menu no **recursos de locatário** lista somente essas público Os endereços IP que foram *criados explicitamente por locatários*. Como tal, o número de **usado** endereços IP públicos de **uso de pools de IP público** bloco sempre é diferente de (maior que) o número no **endereços IP públicos** lado a lado em **recursos de locatário**.

## <a name="view-the-public-ip-address-usage-information"></a>Exibir as informações de uso de endereço IP públicas
Para exibir o número total de endereços IP públicos que tiverem sido consumidos na região:

1. No portal do administrador de pilha do Azure, clique em **mais serviços**, em **recursos administrativos**, clique em **provedores de recursos**.
2. Na lista de **provedores de recursos**, selecione **rede**.
3. O **rede** folha exibe o **uso de pools de IP público** lado a lado no **visão geral** seção.

![Folha de provedor de recursos de rede](media/azure-stack-viewing-public-ip-address-consumption/image01.png)

Lembre-se de que o **usado** número representa o número de endereços IP públicos de todos os públicos pools de endereços IP no local que são atribuídos. O **livre** número representará o número de IP público endereços de IP público de todos os endereços pools que não foram atribuídos e ainda estão disponíveis. O **% usada da** número representa o número de usado ou endereços atribuídos como uma porcentagem do número total de endereços IP públicos em todos os pools de endereços IP públicos nesse local.

## <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>Exibir os endereços IP públicos que foram criados pelas assinaturas do locatário
Para ver uma lista de endereços IP públicos que foram explicitamente criadas por assinaturas de locatários em uma região específica, clique em **endereços IP públicos** em **recursos de locatário**.

![Endereços IP públicos de locatário](media/azure-stack-viewing-public-ip-address-consumption/image02.png)

Você poderá notar que alguns endereços IP públicos que tenham sido alocados dinamicamente aparecem na lista, mas não tem um endereço associado a eles. Isso ocorre porque o recurso de endereço foi criado no provedor de recursos de rede, mas não no controlador de rede ainda.

O controlador de rede não atribuir um endereço para esse recurso até que realmente está associado a uma interface, uma placa de interface de rede (NIC), um balanceador de carga ou um gateway de rede virtual. Quando o endereço IP público está associado a uma interface, controlador de rede aloca um endereço IP a ele e aparece no **endereço** campo.

## <a name="view-the-public-ip-address-information-summary-table"></a>Exibir a público informações resumida tabela de endereços IP
Há um número de casos diferentes na qual os endereços IP públicos são atribuídos que determinam se o endereço é exibido em uma lista ou outro.

| **Caso de atribuição de endereço IP público** | **Aparece no resumo do uso** | **Aparece na lista de endereços IP pública do locatário** |
| --- | --- | --- |
| Endereço IP dinâmico público ainda não foram atribuído a um balanceador de carga ou NIC (temporário) |Não |Sim |
| Dinâmico endereço IP público atribuído a um balanceador de carga ou NIC. |Sim |Sim |
| Endereço IP público estático atribuído a um balanceador de carga ou NIC de locatário. |Sim |Sim |
| Endereço IP público estático atribuído a um ponto de extremidade de serviço de infraestrutura de malha. |Sim |Não |
| Endereço IP público implicitamente criado para instâncias de VM IaaS e usado para NAT de saída em uma rede virtual. Eles são criados em segundo plano sempre que um locatário cria uma instância de VM para que VMs podem enviar informações para a Internet. |Sim |Não |

## <a name="next-steps"></a>Próximas etapas
[Gerenciar contas de armazenamento na pilha do Azure](azure-stack-manage-storage-accounts.md)