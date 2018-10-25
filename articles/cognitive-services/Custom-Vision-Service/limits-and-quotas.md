---
title: Limites e cotas – Serviço de Visão Personalizada
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre os limites e cotas do Serviço de Visão Personalizada.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: anroth
ms.openlocfilehash: ce06effbce12abb6271e050829d3218f4fbbfbf4
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902851"
---
# <a name="limits-and-quotas"></a>Limites e cotas

Há três camadas de chaves para o Serviço de Visão Personalizada. Recursos F0 e S0 são obtidos por meio do portal do Azure. Detalhes sobre definições de preços e transações estão na [página de preço](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/).  Projetos F0 podem ser atualizados para projetos S0.

Recursos de projeto de Avaliação Limitada são anexados ao seu logon de Visão Personalizada (ou seja, uma conta do AAD ou conta MSA.) Eles se destinam a serem usados para breves testes de serviço.  As contas criadas durante a versão prévia gratuita inicial, antes da introdução das versões prévias do Azure (1 de março de 2018) manterão suas cotas anteriores para Avaliações Limitadas. 

||**Avaliação Limitada**|**F0 (Azure)**|**S0 (Azure)**|
|-----|-----|-----|-----|
|Projetos|2|2|100|
|Imagens de treinamento por projeto, Classificação|5.000|5.000|50.000|
|Imagens de treinamento por projeto, Detecção de objeto|5.000|5.000|10.000|
|Previsões/mês|10.000 |10.000|Ilimitado|
|Marcas/projeto|50|50|250|
|Iterações |10|10|10|
|Mínimo de imagens rotuladas por Marcação, Classificação (recomendam-se +50) |5|5|5|
|Mínimo de imagens rotuladas por Marcação, Detecção de Objeto (recomendam-se +50)|15|15|15|
|Por quanto tempo as imagens de previsão são armazenadas|30 dias|30 dias|30 dias|
|Operações de [Previsão](https://go.microsoft.com/fwlink/?linkid=865445) com armazenamento (Transações por Segundo)|2|2|10|
|Operações de [Previsão](https://go.microsoft.com/fwlink/?linkid=865445) sem armazenamento (Transações por Segundo)|2|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (chamadas à API por Segundo)|2|2|10|
|[Outras chamadas à API](https://go.microsoft.com/fwlink/?linkid=865446) (Transações por Segundo)|10|10|10|
|Tamanho máximo da imagem (upload da imagem de treinamento) |6 MB|6 MB|6 MB|
|Tamanho máximo da imagem (previsão)|4 MB|4 MB|4 MB|

Limitações ao *nº de imagens de treinamento por projeto* e ao *nº de marcas/projeto* devem aumentar ao longo do tempo para projetos S0. 
