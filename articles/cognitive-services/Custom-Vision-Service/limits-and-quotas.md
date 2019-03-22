---
title: Preços e limites - Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre os limites e cotas do Serviço de Visão Personalizada.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: anroth
ms.openlocfilehash: 58109e17ed33e6af8dedf3ed8c1cc9ddf546a05e
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588662"
---
# <a name="pricing-and-limits"></a>Preços e limites

Existem três níveis de chaves para o serviço Custom Vision. Os recursos do projeto de avaliação limitada são anexados ao seu logon do Custom Vision (ou seja, uma conta do Azure Active Directory ou uma conta do MSA). Eles se destinam a serem usados para breves testes de serviço. Você pode se inscrever para uma assinatura F0 (gratuita) ou S0 (padrão) por meio do portal do Azure. Consulte a [página de preços de serviços cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) correspondente para obter detalhes sobre preços e transações.

As contas criadas durante a pré-visualização gratuita antecipada, antes da introdução das visualizações do Azure (1º de março de 2018), manterão suas cotas anteriores para avaliações limitadas.

Espera-se que o número de imagens de treinamento por projeto e tags por projeto aumente com o tempo para projetos S0.

||**Avaliação Limitada**|**F0**|**S0**|
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
|Tamanho máximo da imagem (upload da imagem de treinamento) |6 MB|6 MB|6 MB|
|Tamanho máximo da imagem (previsão)|4 MB|4 MB|4 MB|


