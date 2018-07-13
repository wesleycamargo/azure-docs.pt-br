---
title: Contagens de caractere da API de Tradução de Texto da Microsoft | Microsoft Docs
description: Como a API de Tradução de Texto da Microsoft conta os caracteres.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/20/2017
ms.author: v-jansko
ms.openlocfilehash: ebe3e3606a0413730e1fbfd704a6403f77275f89
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363752"
---
# <a name="how-the-microsoft-translator-text-api-counts-characters"></a>Como a API de Tradução de Texto da Microsoft conta os caracteres

O Microsoft Translator conta cada caractere de entrada. Caracteres no sentido de Unicode, não em bytes. Um caractere não-Unicode conta como dois caracteres. Espaço em branco e marcação contam como caracteres. O tamanho da resposta não é importante.

Chamadas para os métodos Detect e BreakSentence não são contadas no consumo de caractere. No entanto, esperamos que as chamadas para os métodos Detect e BreakSentence sejam de uma proporção razoável para o uso de outras funções que são contadas. A Microsoft se reserva o direito de começar a contar os caracteres para os métodos Detect e BreakSentence. 

Translate oferece a detecção automática, se você omitir o parâmetro From language. 

Veja mais informações sobre contagens de caracteres em [Perguntas frequentes sobre o Microsoft Translator](https://www.microsoft.com/en-us/translator/faq.aspx).
