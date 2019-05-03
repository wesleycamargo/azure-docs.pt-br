---
title: Diretrizes de rotulado como humanos transcrições - serviços de fala
titlesuffix: Azure Cognitive Services
description: Se você estiver procurando para melhorar a precisão do reconhecimento, especialmente os problemas são causados quando palavras são excluídas ou substituídas incorretamente, você desejará usar rotulada como humanos transcrições, juntamente com seus dados de áudio. Quais são rotulados por humanos transcrições? Isso é fácil, eles são transcrições de palavra por palavra, textuais de um arquivo de áudio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 7f0b467284872f3d936984741c6d092705008a5a
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025917"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>Como criar rotulada como humanos transcrições

Se você estiver procurando para melhorar a precisão do reconhecimento, especialmente os problemas são causados quando palavras são excluídas ou substituídas incorretamente, você desejará usar rotulada como humanos transcrições, juntamente com seus dados de áudio. Quais são rotulados por humanos transcrições? Isso é fácil, eles são transcrições de palavra por palavra, textuais de um arquivo de áudio.

Uma grande amostra de dados de transcrição é necessário para melhorar o reconhecimento, sugerimos que você fornecer entre 10 e 1.000 horas de dados de transcrição. Nessa página, vamos examinar as diretrizes projetadas para ajudá-lo a criar as transcrições de alta qualidade. Este guia é dividido por localidade, com seções para inglês (EUA), chinês Mandarim e alemão.

## <a name="us-english-en-us"></a>Inglês dos EUA (en-US)

Rotulado como humanos transcrições de áudio em inglês devem ser fornecidas como texto sem formatação, usando apenas caracteres ASCII. Evite o uso de caracteres de pontuação de Latim-1 ou Unicode. Esses caracteres são geralmente inadvertidamente adicionados ao copiar o texto de um aplicativo de processamento de texto ou a captura de dados de páginas da web. Se esses caracteres estão presentes, certifique-se de atualizá-los com a substituição de ASCII apropriada.

Veja alguns exemplos:

| Caracteres a evitar | Substituição | Observações |
|---------------------|--------------|-------|
| "Hello world" | "Olá, Mundo" | As aspas de abertura e fechamento tiverem sido substituídas com os caracteres ASCII apropriados. |
| Dia de José | Dia de José | O apóstrofo foi substituído com o caractere ASCII adequado. |
| it was good—no, it was great! | it was good--no, it was great! | O travessão foi substituído por dois hifens. |

### <a name="text-normalization-for-us-english"></a>Normalização de texto para inglês (EUA)

Normalização do texto é a transformação de palavras em um formato consistente usado ao treinar um modelo. Algumas regras de normalização são aplicadas automaticamente ao texto, no entanto, recomendamos usar essas diretrizes como você prepara os dados de transcrição rotulada como humanos:

* Gravar as abreviações em palavras.
* Gravar cadeias de caracteres numéricas não padrão em palavras (como termos de contabilidade).
* Caracteres não alfabéticos ou mistos caracteres alfanuméricos devem ser transcrita tão pronunciados.
* As abreviações que são pronunciadas como palavras não devem ser editadas (por exemplo, "radar", "laser", "RAM" ou "OTAN").
* Gravação out as abreviações que são pronunciado como letras separadas a cada letra separada por um espaço.

Aqui estão alguns exemplos de normalização que devem ser executadas na transcrição:

| Texto original | Texto após a normalização |
|---------------|--------------------------|
| Dr. Bruce Banner | Doctor Bruce Banner |
| James Bond, 007 | James Bond, double oh seven |
| Ke$ha | Kesha |
| How long is the 2x4 | How long is the two by four |
| The meeting goes from 1-3pm | The meeting goes from one to three pm |
| meu tipo de sangue é O + | My blood type is O positive |
| água é H20 | água é H 2 O |
| reproduzir OU812 por Van Halen | executar O U 8 1 2 por Van Halen |
| UTF-8 with BOM | U T F 8 with BOM |

As seguintes regras de normalização são aplicadas automaticamente ao transcrições:

* Use letras minúsculas.
* Remova todos os pontos, exceto os apóstrofos dentro de palavras.
* Expanda os números em formato palavras/falado, como quantias em dinheiro.

Aqui estão alguns exemplos de normalização executado automaticamente em transcrição:

| Texto original | Texto após a normalização |
|---------------|--------------------------|
| "Holy cow!" said Batman. | holy cow said batman |
| "What?" said Batman's sidekick, Robin. | what said batman's sidekick robin |
| Go get -em! | go get em |
| I'm double-jointed | I'm double jointed |
| 104 Elm Street | one oh four Elm street |
| Tune to 102.7 | tune to one oh two seven |
| Pi is about 3.14 | pi is about three point one four |
It costs $3.14| it costs three fourteen |

## <a name="mandarin-chinese-zh-cn"></a>Mandarim chinês (zh-cn)

Rotulado como humanos transcrições de áudio de chinês Mandarim devem ser codificado com um marcador de ordem de byte UTF-8. Evite o uso de caracteres de pontuação de meia largura. Esses caracteres podem ser incluídos inadvertidamente ao preparar os dados em um programa de processamento de texto ou extrair dados de páginas da web. Se esses caracteres estão presentes, certifique-se de atualizá-los com a substituição apropriada de largura inteira.

Veja alguns exemplos:

| Caracteres a evitar | Substituição | Observações |
|---------------------|--------------|-------|
| "你好" | "你好" | As aspas de abertura e fechamento tiverem sido substituídas com caracteres apropriados. |
| 需要什么帮助? | 需要什么帮助？ | O ponto de interrogação foi substituído por caractere apropriado. |

### <a name="text-normalization-for-mandarin-chinese"></a>Normalização do texto para chinês Mandarim

Normalização do texto é a transformação de palavras em um formato consistente usado ao treinar um modelo. Algumas regras de normalização são aplicadas automaticamente ao texto, no entanto, recomendamos usar essas diretrizes como você prepara os dados de transcrição rotulada como humanos:

* Gravar as abreviações em palavras.
* Grave cadeia de caracteres numéricas na forma falada.

Aqui estão alguns exemplos de normalização que devem ser executadas na transcrição:

| Texto original | Texto após a normalização |
|---------------|--------------------------|
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

As seguintes regras de normalização são aplicadas automaticamente ao transcrições:

* Remover todos os pontos
* Expandir números para o formato falado
* Converter letras de largura inteira em meia largura letras
* Usando letras maiusculas para todas as palavras em inglês

Aqui estão alguns exemplos de normalização executado automaticamente em transcrição:

| Texto original | Texto após a normalização |
|---------------|--------------------------|
| 3.1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z |w f y z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

## <a name="german-de-de-and-other-languages"></a>Alemão (de-DE) e outras linguagens

Rotulado como humanos transcrições de áudio alemão (e outros inglês ou idiomas de chinês Mandarim) devem ser codificado com um marcador de ordem de byte UTF-8. Uma transcrição de rotulado como humanos deve ser fornecida para cada arquivo de áudio.

### <a name="text-normalization-for-german"></a>Normalização do texto para o alemão

Normalização do texto é a transformação de palavras em um formato consistente usado ao treinar um modelo. Algumas regras de normalização são aplicadas automaticamente ao texto, no entanto, recomendamos usar essas diretrizes como você prepara os dados de transcrição rotulada como humanos:

*   Gravar pontos decimais como ","e não".".
*   Gravar os separadores de hora, como ":"e não"." (por exemplo: 12:00 Uhr).
*   Abreviações como "ca". não são substituídas. É recomendável que você use a forma falada completa.
*   Os quatro operadores matemáticos principais (+, -, \*, e /) são removidos. É recomendável substituí-los com a forma escrita: "Além disso," "menos," "sites perigosos" e "geteilt".
*   Operadores de comparação são removidos (=, <, e >). É recomendável substituí-los por "gleich", "kleiner als," e "als grösser".
*   Gravar frações, como 3 e 4, na forma escrita (por exemplo: "drei viertel" em vez de 3 e 4).
*   Substituir o símbolo de "€" com sua forma escrita "Euro".

Aqui estão alguns exemplos de normalização que devem ser executadas na transcrição:

| Texto original | Texto após a normalização de usuário | Texto após a normalização do sistema |
|---------------|-------------------------------|---------------------------------|
| Es ist 12.23 Uhr | Es ist 12:23 Uhr | es ist zwölf uhr drei und zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf |
| 2 + 3 - 4 | 2 plus 3 minus 4 | zwei plus drei minus vier |

As seguintes regras de normalização são aplicadas automaticamente ao transcrições:

* Use letras minúsculas para todo o texto.
* Remover todos os pontos, incluindo vários tipos de aspas ("teste", 'test', "test" e «testar» é Okey).
* Descartar linhas com caracteres especiais desse conjunto: relatório ¤ ¥ ¦ § © ª ¬® ° ± ² nossa × ÿ Ø¬¬.
* Expanda números no formato falado, incluindo dólar ou valores de Euro.
* Aceitar caracteres especiais somente para um, o e você. Outras pessoas serão substituídas por "th" ou descartadas.

Aqui estão alguns exemplos de normalização executado automaticamente em transcrição:

| Texto original | Texto após a normalização |
|---------------|--------------------------|
| Frankfurter Ring | frankfurter ring |
| ¡Eine Frage! | eine frage |
| wir, haben | wir haben |

## <a name="next-steps"></a>Próximas etapas

* [Preparar e testar seus dados](how-to-custom-speech-test-data.md)
* [Inspecionar seus dados](how-to-custom-speech-inspect-data.md)
* [Avaliar seus dados](how-to-custom-speech-evaluate-data.md)
* [Treinar seu modelo](how-to-custom-speech-train-model.md)
* [Implantar seu modelo](how-to-custom-speech-deploy-model.md)
