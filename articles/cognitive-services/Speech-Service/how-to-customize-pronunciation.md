---
title: Serviço de Fala dos Serviços Cognitivos do Azure
description: Saiba como personalizar a pronúncia com os Serviços Cognitivos do Serviço de Fala.
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 07/02/2018
ms.author: panosper
ms.openlocfilehash: c7c06fc2f33baa7357fd5f945414daf2bc6e4858
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284931"
---
# <a name="enable-custom-pronunciation"></a>Habilitar pronúncia personalizada
A pronúncia personalizada permite aos usuários definir o formulário fonético e a exibição de uma palavra ou termo. É útil para lidar com termos personalizados, como nomes de produtos ou acrônimos. Tudo o que você precisa é um arquivo de pronúncia (um arquivo. txt simples).

Veja como ele funciona. Em um arquivo. txt único, você pode inserir várias entradas de pronúncia personalizada. A estrutura é a seguinte:

```
Display form <Tab> Spoken form <Newline>
```

*Exemplos*:

| Formulário de exibição | Forma falada |
|----------|-------|
| C3PO | see three pea o |
| L8R | late are |
| CNTK | see n tea k|

## <a name="requirements-for-the-spoken-form"></a>Requisitos para a forma falada
A forma falada deve estar em minúscula, que pode ser forçada durante a importação. Além disso, você deve fornecer as verificações no importador de dados. Não é permitida nenhuma guia na forma de exibição ou na forma falada. Pode haver, no entanto, mais caracteres proibidos na forma de exibição (por exemplo, ~ e ^).

Cada arquivo. txt pode ter várias entradas. Pro exemplo, veja a seguinte captura de tela:

![Captura de tela do bloco de notas com várias entradas de pronúncia de acrônimos](media/stt/custom-speech-pronunciation-file.png)

A forma falada é a sequência fonética da forma de exibição. Ela é composto de letras, palavras ou sílabas. Atualmente, não há nenhuma orientação adicional ou um conjunto de padrões para ajudá-lo a formular a forma falada. 

## <a name="supported-pronunciation-characters"></a>Caracteres de pronúncia com suporte
A pronúncia personalizada é suportada atualmente para inglês dos EUA (en-US) e alemão (de-DE). O conjunto de caracteres que pode ser usado para expressar a forma falada de um termo (no arquivo de pronúncia personalizada) é mostrado na tabela a seguir: 

| Linguagem | Caracteres |
|---------- |----------|
| Inglês (en-us) | a, b, c, d, e, f, g, h, i, j, k, l, o, p, q, r, s, t, u, v, w, x, y, z |
| Alemão (de-DE) | ä, ö, ü, ẞ, a, b, c, d, e, f, g, h, i, j, k, l, o, p, q, r, s, t, u, v, w, x, y, z |

> [!NOTE]
> O formulário de exibição de um termo (em um arquivo de pronúncia) deve ser gravado da mesma maneira em um conjunto de dados de adaptação de idioma.

## <a name="requirements-for-the-display-form"></a>Requisitos para a forma de exibição
Uma forma de exibição só pode ser uma palavra personalizada, termo, acrônimo ou palavras compostas que combinam palavras existentes. Você também pode inserir pronúncias alternativas de palavras comuns. 

>[!NOTE]
>Não é recomendável usar esse recurso para reformular palavras comuns ou para modificar a forma falada. É melhor executar o decodificador para ver se algumas palavras incomuns (como abreviações, palavras técnicas e palavras estrangeiras) não são decodificadas corretamente. Se elas forem, adicione-as ao arquivo de pronúncia personalizada. No modelo de idioma, você deve sempre e somente usar a forma de exibição de uma palavra. 

## <a name="requirements-for-the-file-size"></a>Requisitos para o tamanho do arquivo
O tamanho do arquivo. txt que contém as entradas de pronúncia é limitado a 1 MB. Normalmente, você não precisa carregar grandes quantidades de dados por meio desse arquivo. É provável que a maioria dos arquivos de pronúncia personalizada possuam poucos KBs em tamanho. A codificação do arquivo. txt para todas as localidades deve ser UTF-8 BOM. Para a localidade inglês, ANSI também é aceitável.

## <a name="next-steps"></a>Próximas etapas
* Melhore a precisão do reconhecimento, criando um [modelo acústico personalizado](how-to-customize-acoustic-models.md)
* Melhore a precisão do reconhecimento, criando um [modelo de linguagem personalizada](how-to-customize-language-model.md)
