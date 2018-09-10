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
ms.openlocfilehash: df7326cb8e671d0f71924e813a1354dfef1e20c7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363991"
---
Os dados retornados com as margens superiores e inferiores padrão e o valor esperado. Na prática, você pode definir um parâmetro [sensibilidade] e, em seguida, usar (ExpectedValue + sensibilidade * UpperMargin) como o limite superior e (ExpectedValue - sensibilidade * LowerMargin) como o limite inferior para ajustar o ponto de anomalia por si próprio. O valor de [sensibilidade] deve ser maior que 1. Abaixo estão alguns diagramas para ajuste.

> [!NOTE]
> Os diagramas não são gerados pelo aplicativo de exemplo. Eles são criados por uma ferramenta separada com o aplicativo de exemplo.

![Ajuste: sensibilidade = 1.0](../media/sensitivity_1.png)
![Ajuste: sensibilidade = 1.5](../media/sensitivity_1.5.png)
![Ajuste: sensibilidade = 2](../media/sensitivity_2.png)
![Ajuste: sensibilidade = 3.5](../media/sensitivity_3.5.png)