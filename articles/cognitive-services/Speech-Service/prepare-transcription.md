---
title: Diretrizes de transcrição para treinamento de Fala
description: Saiba como preparar o texto para personalizar modelos acústicos e de idioma e as fontes de voz para o serviço de Fala.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/01/2018
ms.author: panosper
ms.openlocfilehash: db324b6c5444955debdc6a3e09906a0de47ff819
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/11/2018
ms.locfileid: "41929948"
---
# <a name="transcription-guidelines-for-using-speech-service"></a>Diretrizes de transcrição para usar o serviço Fala

Para personalizar a **Conversão de Fala em Texto** ou a **Conversão de Texto em Fala**, você deverá fornecer o texto junto com a fala. Cada linha no texto corresponde a uma única declaração. O texto deve corresponder à fala da maneira mais exata possível. O texto é chamado de *transcrição* e você deverá criá-lo em um formato específico.

O serviço de Fala normaliza a entrada para manter o texto consistente. 

Este artigo descreve os dois tipos de normalização. As diretrizes variam ligeiramente para vários idiomas.

## <a name="us-english-en-us"></a>Inglês dos EUA (en-US)

Os dados de texto devem ser gravados um enunciado por linha, em texto sem formatação, usando apenas o conjunto de caracteres ASCII.

Evite o uso de caracteres de pontuação estendidos (Latin-1) ou Unicode. Esses caracteres podem ser incluídos inadvertidamente ao preparar os dados em um programa de processamento de texto ou capturar dados de páginas da Web. Substitua esses caracteres pelos correspondentes apropriados de ASCII. Por exemplo: 

| Caracteres a evitar | Substituição |
|----- | ----- |
| “Hello world” (abrir e fechar aspas duplas) | "Hello world" (aspas duplas) |
| John’s day (aspa simples à direita) | John's day (apóstrofo) |
| it was good—no, it was great! (travessão) | it was good--no, it was great! (hifens) |

### <a name="text-normalization-rules-for-english"></a>Regras de normalização de texto para inglês

O serviço de Fala realiza as seguintes regras de normalização.

*   Maiúsculas e minúsculas em todo o texto
*   Remover toda a pontuação exceto apóstrofos internos da palavra
*   Expansão de números para a forma falada, incluindo quantias em dinheiro

Veja alguns exemplos

| Texto original | Após normalização |
|----- | ----- |
| "Holy cow!" said Batman. | holy cow said batman |
| "What?" said Batman's sidekick, Robin. | what said batman's sidekick robin |
| Go get -em! | go get em |
| I'm double-jointed | I'm double jointed |
| 104 Elm Street | one oh four Elm street |
| Tune to 102.7 | tune to one oh two seven |
| Pi is about 3.14 | pi is about three point one four |
| It costs $3.14 | it costs three fourteen |

Aplique a normalização a seguir a suas transcrições de texto.

*   Abreviaturas devem ser gravadas em palavras
*   As cadeias de caracteres numéricas não padrão (por exemplo, algumas data ou formulários de contabilidade) devem ser escritas em palavras
*   As palavras com caracteres não alfabéticos ou caracteres alfanuméricos mistos devem ser transcritas como são pronunciadas
*   Deixe as abreviações pronunciadas como palavras inalteradas. Por exemplo, radar, laser, RAM, NATO.
*   Escreva as abreviações pronunciadas como letras separadas, com letras separadas por espaços. Por exemplo, IBM, CPU, FBI, TBD, NaN. 

Estes são alguns exemplos:

| Texto original | Após normalização |
|----- | ----- |
| 14 NE 3rd Dr. | fourteen northeast third drive |
| Dr. Bruce Banner | Doctor Bruce Banner |
| James Bond, 007 | James Bond, double oh seven |
| Ke$ha | Kesha |
| How long is the 2x4 | How long is the two by four |
| The meeting goes from 1-3pm | The meeting goes from one to three pm |
| my blood type is O+ | My blood type is O positive |
| water is H20 | water is H 2 O |
| play OU812 by Van Halen | play O U 8 1 2 by Van Halen |
| UTF-8 with BOM | U T F 8 with BOM |

## <a name="chinese-zh-cn"></a>Chinês (zh-CN)

Os dados de texto carregados para o Serviço de Fala Personalizado devem usar a codificação UTF-8 com o marcador de ordem de bytes. O arquivo deve ser gravado com um enunciado por linha.

Evite o uso de caracteres de pontuação de meia largura. Esses caracteres podem ser incluídos inadvertidamente ao preparar os dados em um programa de processamento de texto ou capturar dados de páginas da Web. Substitua-os pelos correspondentes apropriados de largura total. Por exemplo: 

| Caracteres a evitar | Substituição |
|----- | ----- |
| "你好" (abrir e fechar aspas duplas) | "你好" (aspas duplas) |
| 需要什么帮助? (ponto de interrogação) | 需要什么帮助？ |

### <a name="text-normalization-rules-for-chinese"></a>Regras de normalização de texto para chinês

O serviço de Fala realiza as seguintes regras de normalização.

*   Remover toda a pontuação
*   Expandindo números para a forma falada
*   Convertendo letras de largura total para letras de meia largura
*   Letras maiúsculas e minúsculas em todas as palavras

Veja alguns exemplos.

| Texto original | Após normalização |
|----- | ----- |
| 3.1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z | w f y z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

Aplique a normalização a seguir ao texto antes de importá-lo.

*   Abreviaturas devem ser gravadas em palavras (como na forma falada)
*   Grave cadeia de caracteres numéricas na forma falada.

Veja alguns exemplos.

| Texto original | Após normalização |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="other-languages"></a>Outras linguagens

Os dados de texto carregados para o serviço **Fala para texto** devem usar a codificação UTF-8 com o marcador de ordem de bytes. O arquivo deve ser gravado com um enunciado por linha.

> [!NOTE]
> Esses exemplos usam alemão. No entanto, essas diretrizes aplicam-se a todos os idiomas que não sejam inglês (EUA) ou chinês.

### <a name="text-normalization-rules-for-german"></a>Regras de normalização de texto para alemão

O serviço de Fala realiza as seguintes regras de normalização.

*   Maiúsculas e minúsculas em todo o texto
*   Removendo todas as pontuações, incluindo vários tipos de aspas ("test", 'test', "test„ ou «test» estão ok)
*   Descartando linhas com qualquer caractere especial do conjunto  ¢ ¤ ¥ ¦ § © ª ¬ ® ° ± ² µ × ÿ Ø¬¬
*   Expansão de números para a forma escrita, incluindo quantias em dólares ou euros
*   Diéreses são aceitas apenas para a, o, u; outros serão substituídos por "th" ou descartados

Veja alguns exemplos

| Texto original | Após normalização |
|----- | ----- |
| Frankfurter Ring | frankfurter ring |
| ¡Eine Frage! | eine frage |
| wir, haben | wir haben |

Aplique a normalização a seguir ao texto antes de importá-lo.

*   Ponto decimal deve ser "," e não "."
*   O separador de tempo entre as horas e minutos deve ser ":" e não ".": 12:00 Uhr
*   Abreviações como 'ca.' não são substituídas. Recomendamos que você use a forma completa.
*   Os quatro principais operadores matemáticos são removidos: +, -, \*, /. É recomendável substituí-los pelas formas literais: plus, minus, mal, geteilt.
*   O mesmo aplica-se a operadores de comparação (=, <, >) – gleich, kleiner als, grösser als
*   Usar frações, como 3/4, na forma de palavra (por exemplo, 'drei viertel' em vez de ¾)
*   Substitua o símbolo € pela forma da palavra "Euro"

Veja alguns exemplos.

| Texto original | Após a normalização do usuário | Após a normalização do sistema
|--------  | ----- | -------- |
| Es ist 12.23Uhr | Es ist 12:23Uhr | es ist zwölf uhr drei und zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf ||
| 2 + 3 - 4 | 2 plus 3 minus 4 | zwei plus drei minus vier|

## <a name="next-steps"></a>Próximas etapas

- [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
- [Reconhecer fala em C#](quickstart-csharp-dotnet-windows.md)
