---
title: Base de dados de conhecimento – QnA Maker
titleSuffix: Azure Cognitive Services
description: Uma base de dados de conhecimento de QnA Maker consiste em um conjunto de pares de pergunta/resposta (QnA) e metadados opcionais associados a cada par de QnA.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: cb3426a960a6644b3ae149f02055cdb083febca7
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47040754"
---
# <a name="knowledge-base"></a>Base de dados de conhecimento

Uma base de dados de conhecimento de QnA Maker consiste em um conjunto de pares de pergunta/resposta (QnA) e metadados opcionais associados a cada par de QnA.

## <a name="key-knowledge-base-concepts"></a>Conceitos principais da base de dados de conhecimento

* **Perguntas** – uma pergunta contém texto que melhor representa uma consulta de usuário. 
* **Respostas** – uma resposta é a resposta que é retornada quando uma consulta de usuário é comparada com a pergunta associada.  
* **Metadados** – os metadados são marcas associadas a um par de QnA e são representados como pares chave-valor. Metadados são usados para filtrar pares QnA e limitar o conjunto sobre o qual a correspondência da consulta será executada.

Um único QnA, representado por uma ID numérica do QnA, tem diversas variantes de uma pergunta (perguntas alternativas) que são mapeadas para uma única resposta. Além disso, cada par pode ter vários campos de metadados associados a ele.

![Bases de conhecimento do QnA Maker](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>Formato do conteúdo da base de dados de conhecimento

Ao receber o conteúdo avançado em uma base de dados de conhecimento, o QnA Maker tenta converter o conteúdo em markdown. Leia [este](https://aka.ms/qnamaker-docs-markdown-support) blog para entender os formatos de markdown reconhecidos pela maioria dos clientes de chat.

Os campos de metadados consistem em pares chave-valor separados por dois-pontos **(Produto: Shredder)**. A chave e o valor devem ser somente texto. A chave de metadados não deve conter espaços.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Ciclo de vida de desenvolvimento de uma base de dados de conhecimento](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Consulte também

[Visão geral do QnA Maker](../Overview/overview.md)