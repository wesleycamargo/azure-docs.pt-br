---
title: Analisar texto no Content Moderator do Azure| Microsoft Docs
description: Saiba como revisar o texto no Content Moderator para ver a pontuação e detectar marcas. Use as informações para determinar se o conteúdo é apropriado.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/03/2017
ms.author: sajagtap
ms.openlocfilehash: cb8774395b7374677e8de3b80630a2d4abf490f9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363396"
---
# <a name="review-text"></a>Analisar texto

É possível usar o Content Moderator do Azure para analisar o texto usando pontuações e marcas detectadas. Usar as informações para determinar se o conteúdo é apropriado. 

## <a name="select-or-enter-the-text-to-review"></a>Selecione ou insira o texto para analisar

No Content Moderator, selecione a guia **Tentar**. Em seguida, selecione a opção **Texto** para acessar a tela inicial de moderação de texto. Insira qualquer texto ou envie o texto de exemplo padrão para moderação de texto automática. É possível inserir no máximo 1.024 caracteres.

## <a name="get-ready-to-review-results"></a>Prepare-se para analisar os resultados

A ferramenta Análise primeiro chama a API de Moderação de Texto. Em seguida, gera análises de texto usando as marcas detectadas. A ferramenta Análise corresponde aos resultados da pontuação para a atenção da equipe.

## <a name="review-text-results"></a>Analisar os resultados do texto

Os resultados detalhados aparecem nas janelas. Os resultados incluem marcas e termos detectados que foram retornados pela API de Moderação de Texto. Para alternar o status de seleção de uma marca, selecione a marca. Também é possível trabalhar com qualquer marca personalizada que possa ter sido criada.

![Analisar os resultados do texto](images/3-review-text-2.png)
