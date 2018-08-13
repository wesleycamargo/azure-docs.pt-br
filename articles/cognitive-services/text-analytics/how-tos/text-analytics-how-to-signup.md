---
title: Inscrever-se para usar a API de Análise de Texto (Serviços Cognitivos da Microsoft no Azure) | Microsoft Docs
description: Instruções sobre a inscrição para usar a análise de texto e sobre a operação dentro dos limites.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: get-started-article
ms.date: 3/07/2018
ms.author: heidist
ms.openlocfilehash: dfa5ba138a2e0db75dfc097ca2430fe9c82e826f
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39623244"
---
# <a name="how-to-sign-up-for-text-analytics-api"></a>Como se inscrever para usar a API de Análise de Texto

Os recursos de Análise de Texto ficam disponíveis 24 horas por dia, 7 dias por semana na nuvem. Antes de poder carregar seu conteúdo para análise, você deverá se inscrever para obter uma chave de acesso. Cada chamada à API requer uma chave de acesso na solicitação.

+ Comece com uma assinatura do Azure. É possível criar uma [conta gratuita](https://azure.microsoft.com/free/) para experimentar sem custo.

+ Crie uma [conta da API de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), escolhendo a **API de Análise de Texto**. Sua chave é gerada quando você se inscreve.

Na Análise de Texto, há uma Camada gratuita, para exploração e avaliação, e camadas faturáveis para cargas de trabalho de produção. Você pode ter várias inscrições em cada assinatura: uma gratuita, uma paga e assim por diante. Você poderá alternar para uma camada que ofereça mais transações se o volume de solicitações aumentar.

Não há nenhum contrato de nível de serviço para serviços na Versão Prévia ou na Camada gratuita. Para obter mais informações, consulte [SLA para Serviços Cognitivos](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/)

## <a name="how-to-change-tiers"></a>Como alterar as camadas

Comece com uma Camada gratuita e faça a transição para uma camada faturável em relação a cargas de trabalho de produção. A cobrança Standard é oferecida em diversos níveis. Você pode alternar as camadas e ainda manter as mesmas chaves de acesso e o mesmo ponto de extremidade.

1. Entre no [portal do Azure](https://portal.azure.com) e [localize seu serviço](text-analytics-how-to-access-key.md).

2. Clique em **Faixa de preço**.

   ![Comando de faixa de preço no menu de navegação à esquerda](../media/portal-pricing-tier.png)

3. Selecione uma camada e clique em **Selecionar**.  Os novos limites entram em vigor assim que a seleção é processada. 

   ![Blocos e botão de seleção na página de seleção de camada](../media/portal-choose-tier.png)

## <a name="how-billing-works"></a>Como funciona a cobrança

A cobrança baseia-se no número de transações. Você pode adquirir um bloco de transações em uma camada específica em um ciclo de cobrança mensal e, se ultrapassar essa quantidade, um pequeno encargo excedente é aplicado para cada transação. Se você rotineiramente ultrapassar o limite máximo, considere mudar para uma camada superior.

Para obter mais informações, consulte a [página de preço](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/).

### <a name="what-constitutes-a-transaction-in-the-text-analytics-api"></a>O que constitui uma transação na API de Análise de Texto?
Qualquer anotação em um documento conta como uma transação. As chamadas de pontuação do Lote também levarão em consideração o número de documentos que precisam ser pontuados na transação. Por exemplo, se mil documentos forem enviados para a análise de sentimento em uma única chamada à API, isso contará como mil transações.

## <a name="see-also"></a>Consulte também 

 [Visão geral da Análise de Texto](../overview.md)  
 [Perguntas frequentes (FAQ)](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Obter uma chave de acesso](text-analytics-how-to-access-key.md)
