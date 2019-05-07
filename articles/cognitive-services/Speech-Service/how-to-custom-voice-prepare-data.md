---
title: Como preparar dados para voz personalizada – serviços de fala
titlesuffix: Azure Cognitive Services
description: Crie uma voz personalizadas para sua marca com os serviços de fala do Azure. Fornecer gravações studio e os scripts associados, o serviço gera um modelo de voz exclusiva ajustado para a voz gravada. Use essa voz para sintetizador de fala em seus produtos, ferramentas e aplicativos.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: erhopf
ms.openlocfilehash: 18e1bb486c47baf7648a74e31451e2db73f72250
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65156854"
---
# <a name="prepare-data-to-create-a-custom-voice"></a>Preparar dados para criar uma voz personalizadas

Quando estiver pronto para criar uma voz de texto em fala personalizada para seu aplicativo, a primeira etapa é reunir gravações de áudio e scripts associados para iniciar o treinamento do modelo de voz. O serviço usa esses dados para criar uma voz única ajustada de acordo com a voz nas gravações. Depois que você treinou a voz, você pode iniciar sintetizar fala em seus aplicativos.

Você pode iniciar com uma pequena quantidade de dados para criar uma prova de conceito. No entanto, quanto mais dados que você fornecer, mais natural soará sua voz personalizada. Antes de você pode treinar seu próprio modelo de texto em fala voz, você precisará gravações de áudio e transcrições de texto associado. Nessa página, vamos examinar os tipos de dados, como eles são usados e como gerenciar cada um.

## <a name="data-types"></a>Tipos de dados

Um conjunto de dados de treinamento de voz inclui gravações de áudio e um arquivo de texto com as transcrições associados. Cada arquivo de áudio deve conter uma única declaração (um único ou uma única sentença ligar para um sistema de caixa de diálogo) e ter menos de 15 segundos.

Em alguns casos, você pode não ter o conjunto de dados à direita pronto e deseja testar o treinamento de voz personalizadas com arquivos de áudio disponíveis, curtos ou longos, com ou sem transcrições. Podemos fornecer ferramentas (beta) para ajudar você a segmentar o seu áudio em declarações e preparar transcrições usando o [API do lote transcrição](batch-transcription.md).

Esta tabela lista os tipos de dados e como cada um é usada para criar um modelo de texto em fala de voz personalizadas.

| Tipo de dados | DESCRIÇÃO | Quando usar | Serviço adicional necessário | Quantidade para treinar um modelo | Localidades |
| --------- | ----------- | ----------- | --------------------------- | ----------------------------- | --------- |
| **Declarações individuais + transcrição correspondente** | Uma coleção (. zip) de arquivos de áudio (. wav) como declarações individuais. Cada arquivo de áudio deve ser de 15 segundos ou menos, emparelhado com uma transcrição formatada (. txt). | Gravações de profissionais com transcrições de correspondência | Pronto para treinamento. | Nenhum requisito de disco rígido para en-US e zh-CN. Mais de 2.000 declarações distintas para outras localidades. | Todas as localidades de voz personalizada |
| **Áudio longo + transcrição (beta)** | Uma coleção (. zip) longo, não segmentados de arquivos de áudio (mais de 20 segundos), emparelhado com uma transcrição (. txt) que contém todas as palavras faladas. | Você tem arquivos de áudio e transcrições correspondentes, mas eles não são segmentados em declarações. | Segmentação (usando a transcrição do lote).<br>Transformação de formato de áudio onde for necessário. | Nenhum requisito de disco rígido para en-US e zh-CN. | `en-US` e `zh-CN` |
| **Somente áudio (beta)** | Uma coleção (. zip) de arquivos sem uma transcrição de áudio. | Você só pode ter arquivos de áudio disponíveis, sem transcrições. | Segmentação + geração de transcrição (usando a transcrição do lote).<br>Transformação de formato de áudio onde for necessário.| Sem a necessidade de disco rígida `en-US` e `zh-CN`. | `en-US` e `zh-CN` |

Os arquivos devem ser agrupados por tipo de um conjunto de dados e carregados como um arquivo zip. Cada conjunto de dados pode conter apenas um único tipo de dados.

> [!NOTE]
> O número máximo de conjuntos de dados de permissão para ser importado por assinatura é 10 arquivos. zip gratuitamente arquivos de usuários de assinatura (F0) e 500 para usuários de assinatura standard (S0).

## <a name="individual-utterances--matching-transcript"></a>Declarações individuais + transcrição correspondente

Você pode preparar gravações de declarações individuais e a transcrição correspondente de duas maneiras. Ou escrever um script e o tiver lido por um talento de voz ou utilizam áudio disponível publicamente e transcrevê-los ao texto. No último caso, edite os erros de fluência dos arquivos de áudio, como "um" e outros sons de preenchimento, gagueira, palavras murmuradas ou pronunciamentos incorretos.

Para produzir uma fonte de voz BOM, crie as gravações em uma sala silenciosa com um microfone de alta qualidade. Volume consistente, falando em taxa de fala pitch e detectar expressivos gestos de fala são essenciais.

> [!TIP]
> Para criar uma voz para uso de produção, é recomendável utilizar um estúdio de gravação profissional e um talento de voz. Para mais informações, consulte [Como gravar amostras de voz para uma voz personalizada](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/record-custom-voice-samples).

### <a name="audio-files"></a>Arquivos de áudio

Cada arquivo de áudio deve conter uma única declaração (uma única sentença ou um único desativar de um sistema de caixa de diálogo), a menos de 15 segundos. Todos os arquivos devem estar na mesma linguagem falada. Multilíngues vozes de texto em fala personalizadas não tem suporte, exceto o bilíngues inglês chinês. Cada arquivo de áudio deve ter um nome de arquivo numérico exclusivo com a extensão de nome de arquivo. wav.

Siga estas diretrizes ao preparar o áudio.

| Propriedade | Value |
| -------- | ----- |
| Formato de arquivo | RIFF (. wav), agrupados em um arquivo. zip |
| Taxa de amostragem | pelo menos 16.000 Hz |
| Formato de exemplo | PCM, 16 bits |
| Nome do arquivo | Numérico, com a extensão. wav. Nenhum nome de arquivo duplicado permitido. |
| Comprimento de áudio | Menor do que 15 segundos |
| Formato de arquivo | .zip |
| Tamanho máximo de arquivo | 200 MB |

> [!NOTE]
> arquivos. wav com uma taxa de amostragem menor do que 16.000 Hz serão rejeitados. Se um arquivo. zip contém arquivos. wav com taxas de amostragem diferentes, somente aqueles igual a ou maior que 16.000 Hz será importado. Atualmente, o portal importa arquivos .zip de até 200 MB. No entanto, vários arquivos podem ser enviados.

### <a name="transcripts"></a>Transcrições

O arquivo de transcrição é um arquivo de texto sem formatação. Use estas diretrizes para preparar seu transcrições.

| Propriedade | Value |
| -------- | ----- |
| Formato de arquivo | Texto sem formatação (.txt) |
| Formato de codificação | ANSI/ASCII, UTF-8, UTF-8-BOM, LE de UTF-16 ou UTF-16-BE. Para zh-CN, não há suporte para codificações ANSI/ASCII e UTF-8. |
| Nº de enunciados por linha | **Um** -cada linha do arquivo de transcrição deve conter o nome de um dos arquivos de áudio, seguidos de transcrição correspondente. O nome do arquivo e transcrição devem ser separados por uma tabulação (\t). |
| Tamanho máximo do arquivo | 50 MB |

Abaixo está um exemplo de como as transcrições são organizadas a expressão a expressão em um arquivo. txt:

```
0000000001[tab] This is the waistline, and it's falling.
0000000002[tab] We have trouble scoring.
0000000003[tab] It was Janet Maslin.
```
É importante que as transcrições são transcrições de 100% precisas do áudio correspondente. Erros nas transcrições apresentará a perda de qualidade durante o treinamento.

> [!TIP]
> Ao criar produção texto em fala vozes, selecionadas declarações (ou escrever scripts) que levem em conta fonética cobertura e a eficiência. Tendo problemas para a obtenção dos resultados você deseja? [Entre em contato com a voz personalizada](mailto:speechsupport@microsoft.com) equipe para descobrir mais sobre ter nos consulte.

## <a name="long-audio--transcript-beta"></a>Áudio longo + transcrição (beta)

Em alguns casos, você pode não ter segmentada o áudio disponível. Nós fornecemos um serviço (beta) por meio do portal de voz personalizadas para ajudar você a segmentar os arquivos de áudio longa e transcrições de criar. Mantenha em mente, este serviço será cobrado na direção de seu uso da assinatura de fala em texto.

> [!NOTE]
> O serviço de segmentação de áudio longa aproveitará o recurso de transcrição de lote de fala para texto, que dá suporte apenas a usuários de assinatura standard (S0). Durante o processamento da segmentação, seus arquivos de áudio e as transcrições serão também enviadas para o serviço de fala personalizado para refinar o modelo de reconhecimento, portanto, é possível melhorar a precisão para seus dados. Não há dados serão mantidos durante esse processo. Depois que a segmentação for feita, somente as declarações segmentadas e suas transcrições de mapeamento serão armazenadas para o download e o treinamento.

### <a name="audio-files"></a>Arquivos de áudio

Siga estas diretrizes ao preparar o áudio para segmentação.

| Propriedade | Value |
| -------- | ----- |
| Formato de arquivo | RIFF (. wav) com uma taxa de amostragem de pelo menos 16 khz 16-bit no PCM ou. mp3 com uma taxa de bits de pelo menos 256 KBps, agrupado em um arquivo. zip |
| Nome do arquivo | Somente caracteres ASCII. Caracteres Unicode no nome falhará (por exemplo, os caracteres do chinês ou símbolos, como "—"). Não há nomes duplicados permitidos. |
| Comprimento de áudio | Mais de 20 segundos |
| Formato de arquivo | .zip |
| Tamanho máximo de arquivo | 200 MB |

Todos os arquivos de áudio devem ser agrupados em um arquivo zip. Ele é Okey para colocar os arquivos. wav e. mp3 em um zip de áudio, mas nenhuma subpasta é permitida no arquivo zip. Por exemplo, você pode carregar um arquivo zip que contém um arquivo de áudio denominado 'kingstory.wav', 45 de segundo de longa e outro uma nomeada 'queenstory.mp3', 200-segundo-longa, sem todas as subpastas. Todos os arquivos. mp3 serão transformados em formato. wav após o processamento.

### <a name="transcripts"></a>Transcrições

Transcrições devem estar preparadas para as especificações listadas nesta tabela. Cada arquivo de áudio deve ser combinado com uma transcrição.

| Propriedade | Value |
| -------- | ----- |
| Formato de arquivo | Texto sem formatação (. txt), agrupados em um. zip |
| Nome do arquivo | Use o mesmo nome que o arquivo de áudio |
| Formato de codificação | UTF-8-BOM somente |
| Nº de enunciados por linha | Sem limite |
| Tamanho máximo do arquivo | 50 MILHÕES |

Todos os arquivos de transcrições nesse tipo de dados devem ser agrupados em um arquivo zip. Nenhuma subpasta é permitida no arquivo zip. Por exemplo, você carregou um arquivo zip que contém um arquivo de áudio denominado 'kingstory.wav' 45 segundos e outra uma nomeada 'queenstory.mp3', 200 longa segundos. Você precisará carregar outro arquivo zip que contém dois transcrições, uma nomeada 'kingstory.txt', o outro um 'queenstory.txt'. Dentro de cada arquivo de texto sem formatação, você fornecerá a transcrição completo correta para o áudio correspondente.

Depois que o conjunto de dados é carregado com êxito, nós ajudaremos a segmentar o arquivo de áudio em declarações com base na transcrição fornecida. Você pode verificar as declarações segmentadas e as transcrições correspondentes ao baixar o conjunto de dados. IDs exclusivas serão atribuídas automaticamente para as declarações segmentadas. É importante que você se certifique de que as transcrições que você fornecer são 100% precisas. Erros nas transcrições podem reduzir a precisão durante a segmentação de áudio e introduzir ainda mais a perda de qualidade na fase de treinamento que vem mais tarde.

## <a name="audio-only-beta"></a>Somente áudio (beta)

Se você não tiver transcrições para gravações de áudio, use o **somente áudio** opção para carregar seus dados. Nosso sistema pode ajudar você a segmentar e transcrever seus arquivos de áudio. Tenha em mente, este serviço será contam para seu uso da assinatura de fala em texto.

Siga estas diretrizes ao preparar o áudio.

> [!NOTE]
> O serviço de segmentação de áudio longa aproveitará o recurso de transcrição de lote de fala para texto, que dá suporte apenas a usuários de assinatura standard (S0).

| Propriedade | Value |
| -------- | ----- |
| Formato de arquivo | RIFF (. wav) com uma taxa de amostragem de pelo menos 16 khz 16-bit no PCM ou. mp3 com uma taxa de bits de pelo menos 256 KBps, agrupado em um arquivo. zip |
| Nome do arquivo | Somente caracteres ASCII. Caracteres Unicode no nome falhará (por exemplo, os caracteres do chinês ou símbolos, como "—"). Nenhum nome duplicado permitido. |
| Comprimento de áudio | Mais de 20 segundos |
| Formato de arquivo | .zip |
| Tamanho máximo de arquivo | 200 MB |

Todos os arquivos de áudio devem ser agrupados em um arquivo zip. Nenhuma subpasta é permitida no arquivo zip. Depois que o conjunto de dados é carregado com êxito, nós ajudaremos a segmentar o arquivo de áudio em declarações com base em nosso serviço de transcrição de fala em lotes. IDs exclusivas serão atribuídas automaticamente para as declarações segmentadas. Transcrições de correspondência será gerado por meio do reconhecimento de fala. Todos os arquivos. mp3 serão transformados em formato. wav após o processamento. Você pode verificar as declarações segmentadas e as transcrições correspondentes ao baixar o conjunto de dados.

## <a name="next-steps"></a>Próximas etapas

- [Criar uma voz personalizadas](how-to-custom-voice-create-voice.md)
- [Guia: Registre seus exemplos de voz](record-custom-voice-samples.md)
