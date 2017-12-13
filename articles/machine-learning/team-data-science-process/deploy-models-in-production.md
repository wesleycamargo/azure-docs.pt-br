---
title: "Implantar modelos em produção – Azure Machine Learning | Microsoft Docs"
description: "Como implantar modelos em produção, possibilitando que desempenham um papel ativo na tomada de decisões de negócios."
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2017
ms.author: bradsev;
ms.openlocfilehash: c7be9eda2d6f37951eb120250861cf4a39b0141b
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2017
---
# <a name="deploy-models-in-production"></a>Implantar modelos em produção

A implantação de produção permite que um modelo execute uma função ativa em uma empresa. Previsões de um modelo implantado podem ser usadas para decisões de negócios.

## <a name="production-platforms"></a>Plataformas de produção
Há várias abordagens e plataformas para colocar modelos em produção. Veja algumas opções:


- [Implantação de modelo no Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/preview/model-management-overview)
- [Implantação de um modelo no SQL-server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)


>[!NOTE]
>Antes da implantação, é preciso garantir que a latência da pontuação de modelo é baixa o suficiente para usar em produção.
>


>[!NOTE]
>Para implantações que usam o Azure Machine Learning Studio, consulte [Implantar um serviço Web do Machine Learning](../studio/publish-a-machine-learning-web-service.md).
>

## <a name="ab-testing"></a>Testes de A/B
Quando vários modelos estão em produção, pode ser útil executar [Testes de A/B](https://en.wikipedia.org/wiki/A/B_testing) para comparar o desempenho dos modelos. 

 
## <a name="next-steps"></a>Próximas etapas

Também são fornecidas instruções passo a passo que demonstram todas as etapas do processo para **cenários específicos**. Eles serão listados e vinculados a descrições em miniatura no artigo [Instruções passo a passo de exemplo](walkthroughs.md). Eles ilustram como combinar a nuvem, as ferramentas locais e os serviços em um fluxo de trabalho ou pipeline para criar um aplicativo inteligente. 
 


