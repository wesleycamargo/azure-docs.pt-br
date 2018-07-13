---
title: Limites e cotas para o Serviço de Visão Personalizada – Serviços Cognitivos do Azure | Microsoft Docs
description: Saiba mais sobre os limites e a cota para os o Serviço de Visão Personalizada dos Serviços Cognitivos do Azure.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 03/16/2018
ms.author: anroth
ms.openlocfilehash: 44666d5d7f2a51e4017c704205d21b1f6d06908c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364575"
---
# <a name="limits-and-quotas"></a>Limites e cotas

Há três camadas de chaves para o Serviço de Visão Personalizada. Recursos F0 e S0 são obtidos por meio do portal do Azure. Detalhes sobre definições de preços e transações estão na [página de preço](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/).  Projetos F0 podem ser atualizados para projetos S0.

Recursos de projeto de Avaliação Limitada são anexados ao seu logon de Visão Personalizada (ou seja, uma conta do AAD ou conta MSA.) Eles se destinam a serem usados para breves testes de serviço.  As contas criadas durante a versão prévia gratuita inicial, antes da introdução das versões prévias do Azure (1 de março de 2018) manterão suas cotas anteriores para Avaliações Limitadas. 

||**Avaliação Limitada**|**F0 (Azure)**|**S0 (Azure)**|
|-----|-----|-----|-----|
|Projetos|2|2|100|
|Imagens de treinamento por projeto|5.000|5.000|50.000|
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
