---
title: Usar pronúncia personalizada com o Serviço de Fala Personalizada no Azure | Microsoft Docs
description: Aprenda como criar um modelo de linguagem com o Serviço de Fala Personalizada nos Serviços Cognitivos.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 11/23/2017
ms.author: panosper
ms.openlocfilehash: a74b69b84cc80809a25f18b580a18abb5721b8b1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363452"
---
# <a name="enable-custom-pronunciation"></a>Habilitar pronúncia personalizada
A pronúncia personalizada permite aos usuários definir o formulário fonético e a exibição de uma palavra ou termo. É útil para lidar com termos personalizados, como nomes de produtos ou acrônimos. Tudo o que você precisa é um arquivo de pronúncia (um arquivo. txt simples).

Veja como ele funciona. Em um arquivo. txt único, você pode inserir várias entradas de pronúncia personalizada. A estrutura é a seguinte:

```
Display form <Tab> Spoken form <Newline>
```

*Exemplos*:

| Forma de exibição | Forma falada |
|----------|-------|
| C3PO | see three pea o |
| L8R | late are |
| CNTK | see n tea k|

## <a name="requirements-for-the-spoken-form"></a>Requisitos para a forma falada
A forma falada deve estar em minúscula, que pode ser forçada durante a importação. Além disso, você deve fornecer as verificações no importador de dados. Não é permitida nenhuma guia na forma de exibição ou na forma falada. Pode haver, no entanto, mais caracteres proibidos na forma de exibição (por exemplo, ~ e ^).

Cada arquivo. txt pode ter várias entradas. Pro exemplo, veja a seguinte captura de tela:

![Captura de tela do bloco de notas com várias entradas de pronúncia de acrônimos](../../../media/cognitive-services/custom-speech-service/custom-speech-pronunciation-file.png)

A forma falada é a sequência fonética da forma de exibição. Ela é composto de letras, palavras ou sílabas. Atualmente, não há nenhuma orientação adicional ou um conjunto de padrões para ajudá-lo a formular a forma falada. 

## <a name="supported-pronunciation-characters"></a>Caracteres de pronúncia com suporte
A pronúncia personalizada é suportada atualmente para inglês dos EUA (en-US) e alemão (de-DE). O conjunto de caracteres que pode ser usado para expressar a forma falada de um termo (no arquivo de pronúncia personalizada) é mostrado na tabela a seguir: 

| Linguagem | Caracteres |
|---------- |----------|
| Inglês (en-us) | a, b, c, d, e, f, g, h, i, j, k, l, o, p, q, r, s, t, u, v, w, x, y, z |
| Alemão (de-DE) | ä, ö, ü, ẞ, a, b, c, d, e, f, g, h, i, j, k, l, o, p, q, r, s, t, u, v, w, x, y, z |

>[OBSERVAÇÃO] Uma forma de exibição do termo (em um arquivo de pronúncia) deve ser escrito da mesma forma em um conjunto de dados de adaptação de idioma.

## <a name="requirements-for-the-display-form"></a>Requisitos para a forma de exibição
Uma forma de exibição só pode ser uma palavra personalizada, termo, acrônimo ou palavras compostas que combinam palavras existentes. Você também pode inserir pronúncias alternativas de palavras comuns. 

>[!NOTE]
Não é recomendável usar esse recurso para reformular palavras comuns ou para modificar a forma falada. É melhor executar o decodificador para ver se algumas palavras incomuns (como abreviações, palavras técnicas e palavras estrangeiras) não são decodificadas corretamente. Se elas forem, adicione-as ao arquivo de pronúncia personalizada. No modelo de idioma, você deve sempre e somente usar a forma de exibição de uma palavra. 

## <a name="requirements-for-the-file-size"></a>Requisitos para o tamanho do arquivo
O tamanho do arquivo. txt que contém as entradas de pronúncia é limitado a 1 MB. Normalmente, você não precisa carregar grandes quantidades de dados por meio desse arquivo. É provável que a maioria dos arquivos de pronúncia personalizada possuam poucos KBs em tamanho. A codificação do arquivo. txt para todas as localidades deve ser UTF-8 BOM. Para a localidade inglês, ANSI também é aceitável.

## <a name="next-steps"></a>Próximas etapas
* Melhore a precisão do reconhecimento criando seu [modelo acústico personalizado](cognitive-services-custom-speech-create-acoustic-model.md).
* [Crie um ponto de extremidade de conversão de fala em texto personalizado](cognitive-services-custom-speech-create-endpoint.md), que pode ser usado em um aplicativo.
