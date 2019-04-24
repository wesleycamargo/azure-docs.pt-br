---
title: Implantar modelos em produção - Processo de Ciência de Dados de Equipe
description: Como implantar modelos em produção, possibilitando que desempenham um papel ativo na tomada de decisões de negócios.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/30/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1a75c842989cfbaf7bb1880831fda2bc6994622b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60238339"
---
# <a name="deploy-models-to-production-to-play-an-active-role-in-making-business-decisions"></a>Implantar modelos para produção para desempenhar um papel ativo na tomada de decisões de negócios

A implantação de produção permite que um modelo execute uma função ativa em uma empresa. Previsões de um modelo implantado podem ser usadas para decisões de negócios.

## <a name="production-platforms"></a>Plataformas de produção

Há várias abordagens e plataformas para colocar modelos em produção. Veja algumas opções:

- [Onde implantar modelos com os serviços do Azure Machine Learning](../service/how-to-deploy-and-where.md)
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

Também são fornecidas instruções passo a passo que demonstram todas as etapas do processo para **cenários específicos**. Eles estão listados e vinculados a descrições em miniatura no artigo [Instruções passo a passo de exemplo](walkthroughs.md). Eles ilustram como combinar a nuvem, as ferramentas locais e os serviços em um fluxo de trabalho ou pipeline para criar um aplicativo inteligente. 
