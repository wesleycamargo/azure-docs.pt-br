---
title: Dicionário Dinâmico - API de Texto do Tradutor
titlesuffix: Azure Cognitive Services
description: Como usar o recurso de dicionário dinâmico da API de Tradução de Texto.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-jansko
ms.openlocfilehash: 0b4362d78ef105c249aafb4c6b203f69754a56c8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58916590"
---
# <a name="how-to-use-the-dynamic-dictionary-feature-of-the-translator-text-api"></a>Como usar o recurso de dicionário dinâmico da API de Tradução de Texto

Se você já souber a tradução que deseja aplicar a uma palavra ou frase, poderá fornecê-la como marcação dentro da solicitação. O dicionário dinâmico é seguro somente para substantivos compostos como nomes próprios e nomes de produtos.

**Sintaxe:**

<mstrans:dictionary translation=”translation of phrase”>frase</mstrans:dictionary>

**Exemplo: en-de:**

Entrada de origem: A palavra <mstrans:dictionary translation=\"wordomatic\">palavra ou frase</mstrans:dictionary> é uma entrada de dicionário.

Saída de destino: Das Wort "wordomatic" ist ein Wörterbucheintrag.

Esse recurso funciona da mesma forma com e sem o modo HTML.

O recurso deve ser usado com moderação. A maneira apropriada e muito melhor de personalizar a tradução é usando o Tradutor Personalizado. O Tradutor Personalizado faz uso total das probabilidades de estatística e contexto. Se você tiver ou puder criar dados de treinamento que mostrem o trabalho ou a frase no contexto, obterá resultados muito melhores. Você pode encontrar mais informações sobre o Tradutor Personalizado em [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator).
