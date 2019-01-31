---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: 79ae38db73d55021572d04f693e5cb809e9bd056
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228960"
---
Os dados retornados com as margens superiores e inferiores padrão e o valor esperado. Na prática, você pode definir um parâmetro [sensibilidade] e, em seguida, usar (ExpectedValue + sensibilidade * UpperMargin) como o limite superior e (ExpectedValue - sensibilidade * LowerMargin) como o limite inferior para ajustar o ponto de anomalia por si próprio. O valor de [sensibilidade] deve ser maior que 1. Abaixo estão alguns diagramas para ajuste.

> [!NOTE]
> Os diagramas não são gerados pelo aplicativo de exemplo. Eles são criados por uma ferramenta separada com o aplicativo de exemplo.

![Ajuste: sensibilidade = 1.0](../media/sensitivity_1.png)
![Ajuste: sensibilidade = 1.5](../media/sensitivity_1.5.png)
![Ajuste: sensibilidade = 2](../media/sensitivity_2.png)
![Ajuste: sensibilidade = 3.5](../media/sensitivity_3.5.png)
