---
title: Conversão de dados
titleSuffix: Language Understanding - Azure Cognitive Services
description: Saiba como as expressões podem ser alteradas antes das previsões no Reconhecimento vocal (LUIS)
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: dc9040661eee4cafc655deb2436130f1abcfcfd5
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53094780"
---
# <a name="convert-data-format-of-utterances"></a>Converter o formato de dados de declarações
O LUIS usa o serviço de Fala dos Serviços Cognitivos para converter enunciados falados em enunciados de texto antes da previsão. 

## <a name="speech-to-intent-conversion-concepts"></a>Conceitos de conversão de fala em intenção
Conversão de fala em texto no LUIS permite que você envie expressões faladas a um ponto de extremidade e receba uma resposta de previsão LUIS. O processo é uma integração entre o serviço de [Fala](https://docs.microsoft.com/azure/cognitive-services/Speech) com LUIS. 

### <a name="key-requirements"></a>Requisitos de chave
Você não precisa criar uma chave de **API de Fala do Bing** para esta integração. Uma chave de **Reconhecimento vocal** criada no portal do Azure funciona para este exercício. Não use a chave de starter LUIS, não funcionará para esta integração.

### <a name="new-endpoint"></a>Novo ponto de extremidade 
Esta integração cria um novo ponto de extremidade e modelo de [preço](luis-boundaries.md#key-limits). O ponto de extremidade, por meio do [SDK de Fala](https://github.com/Azure-Samples/cognitive-services-speech-sdk), pode receber expressões de fala e texto, permitindo que você use-o como um único ponto de extremidade. 

### <a name="quota-usage"></a>Uso da cota
Consulte [limites de chave](luis-boundaries.md#key-limits) para obter informações. 

### <a name="data-retention"></a>Retenção de dados
Os dados enviados ao ponto de extremidade, por meio do SDK de Fala, independentemente de se for fala ou texto, é usado somente para aprimorar o modelo de fala. Ele não é usado fora do seu modelo para melhorar a Fala ou LUIS em uma capacidade geral. Quando o aplicativo LUIS é excluído, os dados retidos também são excluídos.

<!-- TBD: Machine translation conversion concepts -->

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Usar Conversão de fala em texto](luis-tutorial-speech-to-intent.md)

