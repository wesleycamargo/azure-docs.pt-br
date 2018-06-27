---
title: Atualizar e dimensionar uma instância de Gerenciamento de API do Azure | Microsoft Docs
description: Este tópico descreve como atualizar e dimensionar uma instância de Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/18/2018
ms.author: apimpm
ms.openlocfilehash: ca32c72b1582b2a09f9f1754ad778cf1b682a1c2
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36293305"
---
# <a name="upgrade-and-scale-an-api-management-instance"></a>Atualizar e dimensionar uma instância de Gerenciamento de API  

Os clientes podem dimensionar uma instância de APIM (Gerenciamento de API) adicionando e removendo unidades. Uma **unidade** é composta por recursos do Azure dedicados e tem uma determinada capacidade de carga expressa como um número de chamadas à API por mês. Esse número não representa um limite de chamada; ele é mais um valor de produtividade máxima para permitir o planejamento da capacidade aproximada. A taxa de transferência e a latência reais variam muito dependendo de fatores como o número e a taxa de conexões simultâneas, o tipo e o número de políticas configuradas, os tamanhos de solicitação e resposta e a latência de back-end.

A capacidade e o preço de cada unidade dependem da **camada** na qual a unidade existe. Você pode escolher quatro camadas: **Desenvolvedor**, **Básico**, **Padrão**, **Premium**. Se você precisar aumentar a capacidade de um serviço dentro de uma camada, deverá adicionar uma unidade. Se a camada selecionada no momento em sua instância de APIM não permitir a adição de mais unidades, será preciso atualizar para uma camada de nível superior.

O preço de cada unidade e os recursos disponíveis (por exemplo, a implantação em várias regiões) dependem da camada que você escolheu para sua instância do APIM. O artigo [detalhes de preços](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) explica quais preços por unidade e recursos obtidos em cada camada. 

>[!NOTE]
>O artigo [detalhes de preços](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) mostra o número aproximado de capacidade da unidade em cada camada. Para obter números mais precisos, você precisará examinar um cenário realista para suas APIs. Consulte o artigo [Capacidade de uma instância de Gerenciamento de API do Azure](api-management-capacity.md).

## <a name="prerequisites"></a>pré-requisitos

Para executar as etapas deste artigo, é necessário:

+ Ter uma assinatura ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Ter uma instância APIM. Para obter mais informações, consulte [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).

+ Reconhecer o conceito de [Capacidade de uma instância do Gerenciamento de API do Azure](api-management-capacity.md).

## <a name="upgrade-and-scale"></a>Atualizar e dimensionar  

Você pode escolher quatro camadas: **Desenvolvedor**, **Básico**, **Standard** e **Premium**. A camada de **Desenvolvedor** deve ser usada para avaliar o serviço; ela não deve ser usada para a produção. A camada de **Desenvolvedor** não tem um SLA e não é possível dimensioná-la (adicionar/remover unidades). 

**Básico**, **Padrão** e **Premium** são camadas de produção que têm SLA e podem ser dimensionadas. O nível **Básico** o mais barato que tem um SLA e pode ser expandido até 2 unidades, e o nível **Padrão** pode ser dimensionado para até quatro unidades. Você pode adicionar qualquer número de unidades para a camada **Premium**.

A camada **Premium** permite que você distribua uma única instância de gerenciamento de API em qualquer número de regiões do Azure desejadas. Quando você cria inicialmente um serviço de gerenciamento de API, a instância contém apenas uma unidade e reside em uma única região do Azure. A região inicial é designada como região **primária**. Regiões adicionais podem ser facilmente incluídas. Ao adicionar uma região, você pode especificar o número de unidades que deseja alocar. Por exemplo, você pode ter uma unidade na região **primária** e cinco unidades em alguma outra região. Você pode personalizar o número de unidades para o tráfego existente em cada região. Para obter mais informações, consulte [Como implantar uma instância do serviço de Gerenciamento de API do Azure em várias regiões do Azure](api-management-howto-deploy-multi-region.md).

Você pode atualizar e fazer downgrade de e para qualquer camada. Observe que a atualização ou o downgrade pode remover alguns recursos, por exemplo, VNETs ou implantação de várias regiões, ao fazer o downgrade da camada Standard ou Básico para a Premium.

>[!NOTE]
>O processo de atualização ou escala pode levar de 15 a 45 minutos para ser aplicado. Você receberá uma notificação quando tiver terminado.

## <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Use o portal do Azure para atualizar e dimensionar

![Escala APIM no portal do Azure](./media/upgrade-and-scale/portal-scale.png)

1. Navegue para sua instância do APIM no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Escala e preços** no menu.
3. Escolha a camada desejada.
4. Especifique o número de **unidades** que você deseja adicionar. Você pode usar o controle deslizante ou digitar o número de unidades.  
    Se você escolher a camada **Premium**, primeiro precisará selecionar uma região.
5. Pressione **Salvar**

## <a name="next-steps"></a>Próximas etapas

[Como implantar uma instância do serviço de Gerenciamento de API do Azure para várias regiões do Azure](api-management-howto-deploy-multi-region.md)