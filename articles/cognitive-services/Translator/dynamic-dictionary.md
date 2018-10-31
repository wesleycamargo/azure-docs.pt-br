---
title: Dicionário Dinâmico - API de Texto do Tradutor
titlesuffix: Azure Cognitive Services
description: Como usar o recurso de dicionário dinâmico da API de Tradução de Texto.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: 0b96325b2d29abd230e4c389b176e97542a70282
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49648219"
---
# <a name="how-to-use-the-dynamic-dictionary-feature-of-the-translator-text-api"></a>Como usar o recurso de dicionário dinâmico da API de Tradução de Texto

Se você já souber a tradução que deseja aplicar a uma palavra ou frase, poderá fornecê-la como marcação dentro da solicitação. O dicionário dinâmico é seguro somente para substantivos compostos como nomes próprios e nomes de produtos.

**Sintaxe:**

<mstrans:dictionary translation=”translation of phrase”>frase</mstrans:dictionary>

**Exemplo: en-de:**

Entrada de origem: a palavra <mstrans:dictionary translation=\"wordomatic\">palavra ou frase</mstrans:dictionary> é uma entrada de dicionário.

Saída de destino: Das Wort "wordomatic" ist ein Wörterbucheintrag.

Esse recurso funciona da mesma forma com e sem o modo HTML.

O recurso deve ser usado com moderação. A maneira apropriada e muito melhor de personalizar a tradução é usando o Tradutor Personalizado. O Tradutor Personalizado faz uso total das probabilidades de estatística e contexto. Se você tiver ou puder criar dados de treinamento que mostrem o trabalho ou a frase no contexto, obterá resultados muito melhores. Você pode encontrar mais informações sobre o Tradutor Personalizado em [http://aka.ms/CustomTranslator](http://aka.ms/CustomTranslator).
