---
title: Contagens de caractere - API de Tradução de Texto
titlesuffix: Azure Cognitive Services
description: Como a API de Tradução de Texto conta caracteres.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: v-jansko
ms.openlocfilehash: c56622ee5e25fc422d02cec6ecfaa1f847e9e769
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55226427"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Como a API de Tradução de Texto conta caracteres

A API de Tradução de Texto conta todos os caracteres da entrada. Caracteres no sentido de Unicode, não em bytes. Um caractere não-Unicode conta como dois caracteres. Espaço em branco e marcação contam como caracteres. O tamanho da resposta não é importante.

Chamadas para os métodos Detect e BreakSentence não são contadas no consumo de caractere. No entanto, esperamos que as chamadas para os métodos Detect e BreakSentence sejam de uma proporção razoável para o uso de outras funções que são contadas. A Microsoft se reserva o direito de começar a contar os caracteres para os métodos Detect e BreakSentence.

Veja mais informações sobre contagens de caracteres em [Perguntas frequentes sobre o Microsoft Translator](https://www.microsoft.com/en-us/translator/faq.aspx).
