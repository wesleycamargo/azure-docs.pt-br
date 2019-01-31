---
title: Revisar texto no Azure Content Moderator – Content Moderator
description: Saiba como revisar o texto no Content Moderator para ver a pontuação e detectar marcas. Use as informações para determinar se o conteúdo é apropriado.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 896956653ed924b7e30e63c1ae5cfa90dd0cc94d
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55219780"
---
# <a name="let-human-reviewers-review-text"></a>Deixar os revisores humanos revisarem o texto

É possível usar o Content Moderator do Azure para analisar o texto usando pontuações e marcas detectadas. Usar as informações para determinar se o conteúdo é apropriado. 

## <a name="select-or-enter-the-text-to-review"></a>Selecione ou insira o texto para analisar

No Content Moderator, selecione a guia **Tentar**. Em seguida, selecione a opção **Texto** para acessar a tela inicial de moderação de texto. Insira qualquer texto ou envie o texto de exemplo padrão para moderação de texto automática. É possível inserir no máximo 1.024 caracteres.

## <a name="get-ready-to-review-results"></a>Prepare-se para analisar os resultados

A ferramenta Análise primeiro chama a API de Moderação de Texto. Em seguida, gera análises de texto usando as marcas detectadas. A ferramenta Análise corresponde aos resultados da pontuação para a atenção da equipe.

## <a name="review-text-results"></a>Analisar os resultados do texto

Os resultados detalhados aparecem nas janelas. Os resultados incluem marcas e termos detectados que foram retornados pela API de Moderação de Texto. Para alternar o status de seleção de uma marca, selecione a marca. Também é possível trabalhar com qualquer marca personalizada que possa ter sido criada.

![Captura de tela da ferramenta de revisão mostrando o texto sinalizado em uma janela do navegador Chrome](../images/reviewresults_text.png)
