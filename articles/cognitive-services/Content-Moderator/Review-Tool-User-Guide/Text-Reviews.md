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
ms.openlocfilehash: 60a8d03a6562c9a66c2f6781ebbc330c989b343e
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2018
ms.locfileid: "51852088"
---
# <a name="review-text"></a>Analisar texto

É possível usar o Content Moderator do Azure para analisar o texto usando pontuações e marcas detectadas. Usar as informações para determinar se o conteúdo é apropriado. 

## <a name="select-or-enter-the-text-to-review"></a>Selecione ou insira o texto para analisar

No Content Moderator, selecione a guia **Tentar**. Em seguida, selecione a opção **Texto** para acessar a tela inicial de moderação de texto. Insira qualquer texto ou envie o texto de exemplo padrão para moderação de texto automática. É possível inserir no máximo 1.024 caracteres.

## <a name="get-ready-to-review-results"></a>Prepare-se para analisar os resultados

A ferramenta Análise primeiro chama a API de Moderação de Texto. Em seguida, gera análises de texto usando as marcas detectadas. A ferramenta Análise corresponde aos resultados da pontuação para a atenção da equipe.

## <a name="review-text-results"></a>Analisar os resultados do texto

Os resultados detalhados aparecem nas janelas. Os resultados incluem marcas e termos detectados que foram retornados pela API de Moderação de Texto. Para alternar o status de seleção de uma marca, selecione a marca. Também é possível trabalhar com qualquer marca personalizada que possa ter sido criada.

![Captura de tela da ferramenta de revisão mostrando o texto sinalizado em uma janela do navegador Chrome](../images/reviewresults_text.png)
