---
title: Diretrizes de transcrição no Serviço de Fala Personalizada no Azure | Microsoft Docs
description: Saiba como preparar os dados para o Serviço de Fala Personalizada em Serviços Cognitivos.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 2772665107f94a273731d2147154e546225b22dd
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46999725"
---
# <a name="transcription-guidelines"></a>Diretrizes de transcrição
Para garantir o melhor uso de seus dados de texto para a personalização do modelo acústico e de linguagem, as seguintes diretrizes de transcrição devem ser seguidas. Essas diretrizes são específicas do idioma.

## <a name="text-normalization"></a>Normalização de texto

Para um uso ideal na personalização do modelo acústico ou de linguagem, os dados de texto devem ser normalizados, o que significa transformado em um formato padrão e não ambíguo, legível pelo sistema. Esta seção descreve a normalização de texto executada pelo Serviço de Fala Personalizada quando dados são importados e a normalização do texto que o usuário deve executar antes da importação de dados.

## <a name="inverse-text-normalization"></a>Normalização de texto inverso

O processo de conversão de texto não formatado “bruto” de volta ao texto formatado, isto é, com letras maiúsculas e pontuação, é chamado de ITN (normalização de texto inverso). A ITN é executada nos resultados retornados pela API de Fala dos Serviços Cognitivos da Microsoft. Um ponto de extremidade personalizado implantado usando o Serviço de Fala Personalizada usa a mesma ITN que a API de Fala dos Serviços Cognitivos da Microsoft. No entanto, esse serviço atualmente não oferece suporte para ITN personalizada, portanto os novos termos introduzidos por um modelo de linguagem personalizado não serão formatados nos resultados do reconhecimento.

## <a name="transcription-guidelines-for-en-us"></a>Diretrizes de transcrição para en-US

Os dados de texto carregados nesse serviço devem ser gravados em texto sem formatação usando apenas o conjunto de caracteres imprimível ASCII. Cada linha do arquivo deve conter o texto para somente uma única declaração.

É importante evitar o uso de caracteres de pontuação Unicode. Isso pode acontecer inadvertidamente ao preparar os dados em um programa de processamento de texto ou capturar dados de páginas da Web. Substitua esses caracteres pelos correspondentes apropriados de ASCII. Por exemplo: 

| Evitar Unicode | Substituição por ASCII |
|----- | ----- |
| "Hello world" (abrir e fechar aspas duplas) | "Hello world" (aspas duplas) |
| John’s day (aspa simples à direita) | John's day (apóstrofo) |

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>Normalização de texto executada pelo Serviço de Fala Personalizada

Esse serviço executará a normalização de texto a seguir nos dados importados como um conjunto de dados de linguagem ou transcrições para um conjunto de dados acústico. Isso inclui

*   Maiúsculas e minúsculas em todo o texto
*   Remover toda a pontuação exceto apóstrofos internos da palavra
*   Expansão de números para a forma falada, incluindo quantias em dinheiro

Veja alguns exemplos

| Texto original | Após normalização |
|----- | ----- |
| Starbucks Coffee | starbucks coffee |
| "Holy cow!" said Batman. | holy cow said batman |
| “What?” said Batman's sidekick, Robin. | what said batman's sidekick robin |
| Go get -em! | go get em |
| i'm double-jointed | i'm double jointed |
| 104 Main Street | one oh four main street |
| Tune to 102.7 | tune to one oh two point seven |
| Pi is about 3.14 | pi is about three point one four |
| It costs $3.14 | it costs three fourteen |

### <a name="text-normalization-required-by-users"></a>Normalização de texto exigida pelos usuários

Para garantir o melhor uso de seus dados, as seguintes regras de normalização devem ser aplicadas aos seus dados antes de importá-lo.

*   As abreviações devem ser escritas em palavras para refletir a forma falada
*   Cadeias de caracteres numéricas não padrão devem ser escritas em palavras
*   As palavras com caracteres não alfabéticos ou caracteres alfanuméricos mistos devem ser transcritas como são pronunciadas
*   Acrônimos comuns podem ser deixados como uma única entidade sem pontos ou espaços entre as letras, mas todos os outros acrônimos devem ser escritos em letras separadas, com cada letra separada por um único espaço

Veja alguns exemplos

| Texto original | Após normalização |
|----- | ----- |
| 14 NE 3rd Dr. | fourteen northeast third drive |
| Dr. Strangelove | Doctor Strangelove |
| James Bond 007 | James Bond double oh seven |
| Ke$ha | Kesha |
| How long is the 2x4 | How long is the two by four |
| The meeting goes from 1-3pm | The meeting goes from one to three pm |
| my blood type is O+ | My blood type is O positive |
| water is H20 | water is H 2 O |
| play OU812 by Van Halen | play O U 8 1 2 by Van Halen |

## <a name="transcription-guidelines-for-zh-cn"></a>Diretrizes de transcrição para zh-CN

Os dados de texto carregados para o Serviço de Fala Personalizada devem usar a **codificação UTF-8 (incl. BOM)**. Cada linha do arquivo deve conter o texto para somente uma única declaração.

É importante evitar o uso de caracteres de pontuação de meia largura. Isso pode acontecer inadvertidamente ao preparar os dados em um programa de processamento de texto ou capturar dados de páginas da Web. Substitua esses caracteres pelos correspondentes apropriados de largura total. Por exemplo: 

| Evitar Unicode | Substituição por ASCII |
|----- | ----- |
| "你好" (abrir e fechar aspas duplas) | "你好" (aspas duplas) |
| 需要什么帮助? (ponto de interrogação) | 需要什么帮助？ |

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>Normalização de texto executada pelo Serviço de Fala Personalizada

Esse serviço de fala executará a normalização de texto a seguir nos dados importados como um conjunto de dados de linguagem ou transcrições para um conjunto de dados acústico. Isso inclui

*   Remover toda a pontuação
*   Expansão de números para a forma falada
*   Converter letras de largura total para letras de meia largura.
*   Letras maiúsculas e minúsculas em todas as palavras

Estes são alguns exemplos:

| Texto original | Após normalização |
|----- | ----- |
| 3.1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z | w f y z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗 ? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

### <a name="text-normalization-required-by-users"></a>Normalização de texto exigida pelos usuários

Para garantir o melhor uso de seus dados, as seguintes regras de normalização devem ser aplicadas aos seus dados _antes_ de importá-lo.

*   As abreviações devem ser escritas em palavras para refletir a forma falada
*   Este serviço não aborda todas as quantidades numéricas. É mais seguro escrever cadeias de caracteres numéricos na forma falada

Veja alguns exemplos

| Texto original | Após normalização |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="transcription-guidelines-for-de-de"></a>Diretrizes de transcrição para de-DE

Os dados de texto carregados para o Serviço de Fala Personalizada devem usar somente a **codificação UTF-8 (incl. BOM)**. Cada linha do arquivo deve conter o texto para somente uma única declaração.

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>Normalização de texto executada pelo Serviço de Fala Personalizada

Esse serviço executará a normalização de texto a seguir nos dados importados como um conjunto de dados de linguagem ou transcrições para um conjunto de dados acústico. Isso inclui

*   Maiúsculas e minúsculas em todo o texto
*   Removendo todas as pontuações, incluindo aspas em inglês ou alemão ("test", 'test', "test„ ou «test» estão ok)
*   Descartando qualquer linha que contenha caracteres especiais incluindo: ^ ¢ £ ¤ ¥ ¦ § © ª ¬ ® ° ± ² µ × ÿ Ø¬¬
*   Expansão de números para a forma escrita, incluindo quantias em dólares ou euros
*   Tremas são aceitas apenas para a, o, u; outros serão substituídos por "th" ou descartados

Veja alguns exemplos

| Texto original | Após normalização |
|----- | ----- |
| Frankfurter Ring | frankfurter ring |
| "Hallo, Mama!" sagt die Tochter. | hallo mama sagt die tochter |
| ¡Eine Frage! | eine frage |
| wir, haben | wir haben |
| Das macht $10 | das macht zehn dollars |


### <a name="text-normalization-required-by-users"></a>Normalização de texto exigida pelos usuários

Para garantir o melhor uso de seus dados, as seguintes regras de normalização devem ser aplicadas aos seus dados antes de importá-lo.

*   Os pontos decimais devem ser "," e não "." por exemplo, 2,3% e não 2.3%
*   O separador de tempo entre as horas e minutos deve ser ":" e não ".", por exemplo 12:00 Uhr
*   As abreviações como 'ca.', 'bzw.' não são substituídas. É recomendável usar a forma completa para ter a pronúncia correta.
*   Os cinco principais operadores matemáticos são removidos: +, -, \*, /.
 É recomendável substituí-los pelas formas literais: plus, minus, mal, geteilt.
*   O mesmo aplica-se a operadores de comparação  (=, <, >) - gleich, kleiner als, grösser als
*   Usar frações, como 3/4, na forma de palavra 'drei viertel' em vez de ¾
*   Substitua o símbolo € pela forma da palavra "Euro"


Veja alguns exemplos

| Texto original | Após a normalização do usuário | Após a normalização do sistema
|--------  | ----- | -------- |
| Es ist 12.23Uhr | Es ist 12:23Uhr | es ist zwölf uhr drei und zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf |
| 3 < 5 | 3 kleiner als 5 | drei kleiner als vier |
| 2 + 3 - 4 | 2 plus 3 minus 4 | zwei plus drei minus vier|
| Das macht 12€ | Das macht 12 Euros | das macht zwölf euros |



## <a name="next-steps"></a>Próximas etapas
* [Como usar um ponto de extremidade personalizado de conversão de fala em texto](cognitive-services-custom-speech-create-endpoint.md)
* Aumentar a precisão com seu [modelo acústico personalizado](cognitive-services-custom-speech-create-acoustic-model.md)
* Aumentar a precisão com um [modelo de linguagem personalizado](cognitive-services-custom-speech-create-language-model.md)
