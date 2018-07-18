---
title: Dicionário dinâmico da API de Tradução de Texto da Microsoft | Microsoft Docs
description: Como usar o recurso de dicionário dinâmico da API de Tradução de Texto da Microsoft.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: a18348c9786669ac41c4e149577d97cd631d5531
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364774"
---
# <a name="how-to-use-the-dynamic-dictionary-feature-of-the-microsoft-translator-text-api"></a>Como usar o recurso de dicionário dinâmico da API de Tradução de Texto da Microsoft

Se você já souber a tradução que deseja aplicar a uma palavra ou frase, poderá fornecê-la como marcação dentro da solicitação. O dicionário dinâmico só é seguro para nomes compostos, como nomes próprios e nomes de produto. 

**Sintaxe:** 

<mstrans:dictionary translation=”translation of phrase”>frase</mstrans:dictionary>

**Exemplo: en-de:**

Entrada de origem: a palavra <mstrans:dictionary translation=\"wordomatic\">palavra ou frase</mstrans:dictionary> é uma entrada de dicionário.

Saída de destino: Das Wort "wordomatic" ist ein Wörterbucheintrag.

Esse recurso funciona da mesma forma com e sem o modo HTML. 

O recurso deve ser usado com moderação. A maneira apropriada e muito melhor de personalizar a tradução é usando o Hub do Microsoft Translator. O Hub faz uso total do contexto e de probabilidades estatísticas. Se você tiver ou puder arcar com a criação de dados de treinamento que mostrem seu trabalho ou frase no contexto, obterá resultados muito melhor. Você pode encontrar mais informações sobre o hub em [http://hub.microsofttranslator.com](http://hub.microsofttranslator.com).

