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
ms.openlocfilehash: 93fec1ea78263798588a43b2314ffdea736cdbbc
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/23/2018
ms.locfileid: "42745344"
---
# <a name="enable-custom-pronunciation"></a>Habilitar pronúncia personalizada
Usando a pronúncia personalizada, você pode definir o formulário fonético e a exibição de uma palavra ou termo. É útil para lidar com termos personalizados, como nomes de produtos ou acrônimos. Tudo o que você precisa é um arquivo de pronúncia (um arquivo. txt simples).

Veja como ele funciona. Em um arquivo. txt único, você pode inserir várias entradas de pronúncia personalizada. A estrutura é a seguinte:

```
Display form <Tab> Spoken form <Newline>
```

A tabela a seguir mostra vários exemplos:

| Formulário de exibição | Forma falada |
|----------|-------|
| C3PO | see three pea o |
| L8R | late are |
| CNTK | see n tea k|

## <a name="requirements-for-the-spoken-form"></a>Requisitos para a forma falada
A forma falada deve estar em minúsculas, que você pode ser forçar durante a importação. Além disso, você deve fornecer as verificações no importador de dados. Não é permitida nenhuma guia na forma de exibição ou na forma falada. No entanto, pode haver mais caracteres proibidos na forma de exibição (por exemplo, ~ e ^).

Cada arquivo .txt pode ter várias entradas, conforme mostrado na imagem a seguir:

![Exemplos de pronúncia de acrônimos](media/stt/custom-speech-pronunciation-file.png)

A forma falada é a sequência fonética da forma de exibição. Ela é composto de letras, palavras ou sílabas. Atualmente, não há nenhuma orientação adicional ou um conjunto de padrões para ajudá-lo a formular a forma falada. 

## <a name="supported-pronunciation-characters"></a>Caracteres de pronúncia com suporte
Atualmente, há suporte para a pronúncia personalizada nos idiomas inglês (en-US) e alemão (de-DE). O conjunto de caracteres que pode ser usado para expressar a forma falada de um termo (no arquivo de pronúncia personalizada) é mostrado na tabela a seguir: 

| Linguagem | Caracteres |
|---------- |----------|
| Inglês (en-us) | a, b, c, d, e, f, g, h, i, j, k, l, o, p, q, r, s, t, u, v, w, x, y, z |
| Alemão (de-DE) | ä, ö, ü, ẞ, a, b, c, d, e, f, g, h, i, j, k, l, o, p, q, r, s, t, u, v, w, x, y, z |

> [!NOTE]
> O formulário de exibição de um termo (em um arquivo de pronúncia) deve ser gravado da mesma maneira em um conjunto de dados de adaptação de idioma.

## <a name="requirements-for-the-display-form"></a>Requisitos para a forma de exibição
Uma forma de exibição só pode ser uma palavra personalizada, um termo, um acrônimo ou palavras compostas que combinem palavras existentes. Você também pode inserir pronúncias alternativas de palavras comuns. 

>[!NOTE]
>Não é recomendável usar esse recurso para reformular palavras comuns ou para modificar a forma falada. É melhor executar o decodificador para ver se algumas palavras incomuns (como abreviações, palavras técnicas e palavras estrangeiras) são decodificadas incorretamente. Se elas forem, adicione-as ao arquivo de pronúncia personalizada. No modelo de idioma, você deve sempre e somente usar a forma de exibição de uma palavra. 

## <a name="requirements-for-the-file-size"></a>Requisitos para o tamanho do arquivo
O tamanho do arquivo .txt que contém as entradas de pronúncia é limitado a 1 megabyte (MB). Normalmente, você não precisa carregar grandes quantidades de dados por meio desse arquivo. É provável que a maioria dos arquivos de pronúncia personalizada tenham apenas poucos kilobytes (KBs) de tamanho. A codificação do arquivo. txt para todas as localidades deve ser UTF-8 BOM. Para a localidade inglês, ANSI também é aceitável.

## <a name="next-steps"></a>Próximas etapas
* Melhore a precisão do reconhecimento criando um [modelo acústico personalizado](how-to-customize-acoustic-models.md).
* Melhore a precisão do reconhecimento criando um [modelo de linguagem personalizado](how-to-customize-language-model.md).
 