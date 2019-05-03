---
title: Preparar os dados de teste para conversão de fala personalizado – serviços de fala
titlesuffix: Azure Cognitive Services
description: Se você estiver testando para ver quão preciso reconhecimento de fala da Microsoft é ou seus próprios modelos de treinamento, você precisará de dados (na forma de áudio e/ou texto). Nessa página, abordaremos os tipos de dados, como eles são usados e como gerenciá-los.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 1e689d7ce65fda43e5657383ed44890c90c095cd
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025878"
---
# <a name="prepare-data-for-custom-speech"></a>Preparar dados para conversão de fala personalizado

Se você estiver testando para ver quão preciso reconhecimento de fala da Microsoft é ou seus próprios modelos de treinamento, você precisará de dados na forma de texto e áudio. Nessa página, abordaremos os tipos de dados, como eles são usados e como gerenciá-los.

## <a name="data-types"></a>Tipos de dados

Esta tabela lista os tipos de dados aceitos, quando cada tipo de dados deve ser usado e a quantidade recomendada. Nem todo tipo de dados é necessário para criar um modelo. Requisitos de dados irão variar dependendo se você estiver criando um teste ou treinamento de um modelo.

| Tipo de dados | Usado de teste | Quantidade | Usado para treinamento | Quantidade |
|-----------|-----------------|----------|-------------------|----------|
| [Áudio](#audio-data-for-testing) | Sim<br>Usado para inspeção visual | arquivos de áudio 5 + | Não  | N/a |
| [Transcrições de áudio + rotulada como humanos](#audio--human-labeled-transcript-data-for-testingtraining) | Sim<br>Usado para avaliar a precisão | 0,5 - 5 horas de áudio | Sim | 1 - 1.000 horas de áudio |
| [Texto relacionado](##related-text-data-for-training) | Não  | N/a | Sim | 1 a 200 MB de texto relacionado |

Os arquivos devem ser agrupados por tipo de um conjunto de dados e carregados como um arquivo zip. Cada conjunto de dados pode conter apenas um único tipo de dados.

## <a name="upload-data"></a>Carregar dados

Quando você estiver pronto para carregar seus dados, clique em **carregar dados** para iniciar o assistente e criar seu primeiro conjunto de dados. Você será solicitado para selecionar um tipo de dados de fala para o conjunto de dados, antes de permitir que você carregue seus dados.

![Selecione áudio a partir do Portal de fala](./media/custom-speech/custom-speech-select-audio.png)

Cada conjunto de dados que você carregar deve cumprir os requisitos para o tipo de dados que você escolher. É importante Formatar corretamente seus dados antes de serem carregado. Isso garante que os dados serão processados com precisão pelo serviço de fala personalizado. Requisitos são listados nas seções a seguir.

Depois que o conjunto de dados é carregado, você tem algumas opções:

* Você pode navegar para o **testes** guia e inspecionar visualmente os dados de transcrição de áudio + rotulada como humanos ou somente áudio.
* Você pode navegar para o **treinamento** guia e nos dados de transcrição de áudio + humana ou dados de texto relacionadas para treinar um modelo personalizado.

## <a name="audio-data-for-testing"></a>Dados de áudio para teste

Dados de áudio são ideais para testar a precisão do modelo de fala em texto de linha de base da Microsoft ou um modelo personalizado. Tenha em mente, dados de áudio são usados para inspecionar a exatidão de fala ao desempenho de um modelo específico. Se você estiver procurando para quantificar a precisão de um modelo, use [dados de transcrição de áudio + rotulada como humanos](#audio--human-labeled-transcript-data-for-testingtraining).

Use esta tabela para garantir que seus arquivos de áudio estão formatados corretamente para uso com a conversão de fala personalizado:

| Propriedade | Value |
|----------|-------|
| Formato de arquivo | RIFF (WAV) |
| Taxa de amostragem | 8.000 ou 16.000 Hz |
| Canais | 1 (mono) |
| Comprimento máximo por áudio | 2 horas |
| Formato de exemplo | PCM, 16 bits |
| Formato de arquivo | .zip |
| Tamanho máximo de arquivo | 2 GB |

Se o seu áudio não atendem a essas propriedades ou você deseja verificar se ele faz, sugerimos baixando [sox](http://sox.sourceforge.net) para verificar ou converta o áudio. Abaixo estão alguns exemplos de como cada uma dessas atividades pode ser feita por meio da linha de comando:

| Atividade | DESCRIÇÃO | Comando SOX |
|----------|-------------|-------------|
| Verifique o formato de áudio | Use este comando para verificar o formato de arquivo de áudio. | `soxi <filename>.wav` |
| Converter o formato de áudio | Use este comando para converter o arquivo de áudio para o canal único, de 16 bits, 48 KHz. | `sox <filename>.wav -b 16 -3 signed-integer -c l -r 48k -t wav <filename>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Dados de transcrição de áudio + rotulada como humanos para treinamento/teste

Para medir a precisão de precisão de fala em texto da Microsoft durante o processamento de arquivos de áudio, você deve fornecer transcrições rotulada como humanos (palavra por palavra) para comparação. Enquanto a transcrição rotulada como humanos geralmente é demorada, é necessário para avaliar a precisão e para treinar o modelo para seus casos de uso. Tenha em mente, as melhorias no reconhecimento só poderá ser tão bons quanto os dados fornecidos. Por esse motivo, é importante que as transcrições de alta qualidade só são carregadas.  

| Propriedade | Value |
|----------|-------|
| Formato de arquivo | RIFF (WAV) |
| Taxa de amostragem | 8.000 ou 16.000 Hz |
| Canais | 1 (mono) |
| Comprimento máximo por áudio | 60 s |
| Formato de exemplo | PCM, 16 bits |
| Formato de arquivo | .zip |
| Tamanho máximo de zip | 2 GB |

Para resolver problemas como a exclusão do word ou substituição, uma quantidade significativa de dados é necessário para melhorar o reconhecimento. Em geral, é recomendável fornecer transcrições de palavra por palavra por aproximadamente 10 a 1.000 horas de áudio. As transcrições para todos os arquivos WAV devem estar contidas em um único arquivo de texto sem formatação. Cada linha do arquivo de transcrição deve conter o nome de um dos arquivos de áudio, seguido pela transcrição correspondente. O nome do arquivo e transcrição devem ser separados por uma tabulação (\t).

  Por exemplo: 
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```
> [!NOTE]
> A transcrição deve ser codificada como BOM (marca de ordem de byte) UTF-8.

As transcrições são normalizadas para texto para processamento pelo sistema. No entanto, há algumas normalizações importantes que devem ser executadas pelo usuário _antes_ de carregar os dados para o Serviço de Fala Personalizado. Para o idioma apropriado usar quando você prepara seu transcrições, consulte [como criar uma transcrição de rotulado como humanos](how-to-custom-speech-human-labeled-transcriptions.md)

Depois que você coletou de seus arquivos de áudio e transcrições correspondentes, deve ser empacotados como um único arquivo. zip antes de carregar no portal de fala personalizado. Este é um conjunto de dados de exemplo com três arquivos de áudio e um arquivo de transcrição rotulada como humanos:

![Selecione áudio a partir do Portal de fala](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>Dados de texto relacionadas para treinamento

Se você tiver nomes de produto ou recursos que são exclusivos, e você deseja certificar-se de que eles são reconhecidos corretamente, é importante incluir dados de texto relacionada para treinamento. Dois tipos de dados de texto relacionadas podem ser fornecidos para melhorar o reconhecimento:

| Tipo de dados | Como esses dados melhoram o reconhecimento |
|-----------|------------------------------------|
| Declarações de e/ou frases | Esses podem melhorar a precisão ao reconhecer nomes de produto ou vocabulário específico do setor dentro do contexto de uma sentença. |
| Pronúncia | Eles podem melhorar pronúncia de termos incomuns, acrônimos ou outras palavras, com pronúncia indefinida. |

Declarações podem ser fornecidas como um único ou vários arquivos de texto. Quanto mais próximo o texto de dados são o que será falado, quanto maior a probabilidade de que a precisão é maior. Pronúncia deve ser fornecida como um único arquivo de texto. Tudo o que pode ser empacotado como um único arquivo zip e carregado no portal de fala personalizado.

### <a name="guidelines-to-create-an-utterances-file"></a>Diretrizes para criar um arquivo de declarações

Para criar um modelo personalizado usando o texto relacionado, você precisará fornecer uma lista de declarações de amostra. Essas declarações não precisam ser frases ou corrigir gramaticalmente, mas eles devem refletir com precisão a entrada de fala esperado na produção. Se você quiser determinados termos terem maior peso, você pode adicionar várias sentenças ao seu arquivo de dados relacionados que incluem esses termos específicos.

Use esta tabela para garantir que seu arquivo de dados relacionados para declarações está formatado corretamente:

| Propriedade | Value |
|----------|-------|
| Codificação de texto | BOM para UTF-8 |
| Nº de enunciados por linha | 1 |
| Tamanho máximo do arquivo | 200 MB |

Além disso, você vai querer considerar as seguintes restrições:

* Evite repetir caracteres mais de quatro vezes. Por exemplo: "aaaa" ou "uuuu".
* Não use caracteres especiais ou caracteres UTF-8 acima U + 00A1.
* URIs serão rejeitadas.

### <a name="guidelines-to-create-a-pronunciation-file"></a>Diretrizes para criar um arquivo de pronúncia

Se há termos incomuns sem pronúncia padrão que os usuários serão encontrados ou usar, você pode fornecer um arquivo de pronúncia personalizados para melhorar o reconhecimento.

> [!IMPORTANT]
> Não é recomendável usar esse recurso para alterar a pronúncia de palavras comuns.

Isso inclui exemplos de uma declaração falada e uma pronúncia personalizada para cada:

| Forma falada | Formulário exibido/reconhecido |
|--------------|--------------------------|
| o três p de c | 3CPO |  
| k c t n | CNTK |
| i triplo e | IEE |

A forma falada é a sequência de fonética Esclarecida. Ele pode ser composto de letras, palavras, sílabas ou uma combinação de todos os três.

Pronúncia personalizada está disponível em inglês (en-US) e alemão (de-DE). Esta tabela mostra os caracteres com suporte pela linguagem:

| Linguagem | Local | Caracteres |
|----------|--------|------------|
| Inglês | en-US | a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |
| Alemão | de-DE | ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |

Use esta tabela para garantir que seu arquivo de dados relacionados para pronúncia está formatado corretamente. Arquivos de pronúncia são pequenos e não devem exceder algumas KBs.

| Propriedade | Value |
|----------|-------|
| Codificação de texto | BOM de UTF-8 (ANSI também há suporte para inglês) |
| n º de pronúncia por linha | 1 |
| Tamanho máximo do arquivo | 1 MB (1 KB para a camada gratuita) |

## <a name="next-steps"></a>Próximas etapas

* [Inspecionar seus dados](how-to-custom-speech-inspect-data.md)
* [Avaliar seus dados](how-to-custom-speech-evaluate-data.md)
* [Treinar seu modelo](how-to-custom-speech-train-model.md)
* [Implantar seu modelo](how-to-custom-speech-deploy-model.md)
