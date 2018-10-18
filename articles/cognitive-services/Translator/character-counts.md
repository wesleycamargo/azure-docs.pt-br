---
title: Contagens de caracteres da API de Tradução de Texto
titlesuffix: Azure Cognitive Services
description: Como a API de Tradução de Texto conta caracteres.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: v-jansko
ms.openlocfilehash: c6234a46ae55d73739dcc23110c5e0f6375c3f96
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46128733"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Como a API de Tradução de Texto conta caracteres

A API de Tradução de Texto conta todos os caracteres da entrada. Caracteres no sentido de Unicode, não em bytes. Um caractere não-Unicode conta como dois caracteres. Espaço em branco e marcação contam como caracteres. O tamanho da resposta não é importante.

Chamadas para os métodos Detect e BreakSentence não são contadas no consumo de caractere. No entanto, esperamos que as chamadas para os métodos Detect e BreakSentence sejam de uma proporção razoável para o uso de outras funções que são contadas. A Microsoft se reserva o direito de começar a contar os caracteres para os métodos Detect e BreakSentence. 

Veja mais informações sobre contagens de caracteres em [Perguntas frequentes sobre o Microsoft Translator](https://www.microsoft.com/en-us/translator/faq.aspx).
