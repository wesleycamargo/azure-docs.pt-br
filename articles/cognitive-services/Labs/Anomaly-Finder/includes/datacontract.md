---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: e37d3ef5b6f65ad31bc19f9f8c15350014d1c9ad
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364009"
---
Com a [API de Localização de Anomalias](https://labs.cognitive.microsoft.com/en-us/project-anomaly-finder), você pode carregar dados de série temporal no formato JSON no ponto de extremidade da API e, em seguida, ler o resultado da resposta da API. Você pode carregar os dados de série temporal, cada ponto de dados inclui:  
* Timestamp - o carimbo de data/hora para o ponto de dados. Certifique-se de que ele usa uma cadeia de caracteres de data/hora em UTC, por exemplo, “2017-08-01T00:00:00Z”
* Value - A medida desse ponto de dados

Os resultados são formados por:
* Period - A periodicidade usada pela API para detectar as anomalias
* WarningText - As informações de aviso possíveis
* ExpectedValue - O valor previsto pelo modelo baseado em aprendizado
* IsAnomaly - O resultado que diz se os pontos de dados são anomalias ou não em ambas as direções (picos ou quedas)
* IsAnomaly_Neg - O resultado que diz se os pontos de dados são anomalias na direção negativa (quedas)
* IsAnomaly_Pos - O resultado que diz se os pontos de dados são anomalias na direção positiva (picos)
* UpperMargin - A soma de ExpectedValue e UpperMargin determina o limite superior do ponto de dados para o qual ele ainda é considerado como normal
* LowerMargin - (ExpectedValue - LowerMargin) determina se o limite inferior do ponto de dados ainda é considerado como normal

Detalhes do contrato de dados podem ser encontrados [aqui](../apiref.md).

