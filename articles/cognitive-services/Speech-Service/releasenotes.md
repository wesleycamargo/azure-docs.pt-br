---
title: Documentação do SDK de Fala de Serviços Cognitivos
description: Notas de versão – o que mudou nas versões mais recentes
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/17/2018
ms.author: wolfma
ms.openlocfilehash: f346241e1d10d16eae08e389296f4be9149ec086
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2018
ms.locfileid: "39502451"
---
# <a name="release-notes"></a>Notas de versão

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>SDK de Fala dos Serviços Cognitivos 0.5.0: versão de julho de 2018

**Novos recursos**

* Suporte a plataforma Android (API 23: Android 6.0 Marshmallow ou superior).
  Confira o [Início Rápido para Android](quickstart-java-android.md).
* Suporte para .NET Standard 2.0 no Windows.
  Confira o [Início Rápido para .NET Core](quickstart-csharp-dotnetcore-windows.md).
* Experimental: Suporte a UWP no Windows (versão 1709 ou posterior)
  * Confira o [Início Rápido para UWP](quickstart-csharp-uwp.md).
  * Observação: os aplicativos UWP compilados com o SDK de Fala ainda não são aprovados pelo WACK (Kit de Certificação de Aplicativos Windows).
* Suporte para reconhecimento de longa duração com reconexão automática.

**Alterações funcionais**

* `StartContinuousRecognitionAsync()` suporte para reconhecimento de longa duração
* O resultado de reconhecimento contém mais campos: deslocamento do início e duração do áudio (ambos em tiques) do texto reconhecido, valores adicionais representando o status do reconhecimento, por exemplo, `InitialSilenceTimeout`, `InitialBabbleTimeout`.
* Suporte para AuthorizationToken para criar instâncias de fábrica.

**Alterações da falha**

* Eventos de reconhecimento: o tipo de evento NoMatch é mesclado no evento de Erro.
* SpeechOutputFormat em C# é renomeado para OutputFormat para manter o alinhamento com C++.
* O tipo de retorno de alguns métodos da interface `AudioInputStream` foi ligeiramente alterado:
   * Em Java, o método `read` agora retorna `long` em vez de `int`.
   * Em C#, o método `Read` agora retorna `uint` em vez de `int`.
   * Em C++, os métodos `Read` e `GetFormat` agora retornam `size_t` em vez de `int`.
* C++: instâncias de fluxos de entrada de áudio agora só podem ser passadas como um `shared_ptr`.

**Correções de bug**

* Corrigidos valores de retorno incorretos no resultado quando `RecognizeAsync()` expirar.
* A dependência de bibliotecas de bases de mídia no Windows é removida. O SDK agora está usando as APIs do Core Audio.
* Correção de documentação: adicionada uma página de região para descrever quais são as regiões com suporte.

**Problemas conhecidos**

* O SDK de Fala para Android não informa resultados de síntese de fala para tradução.
  Isso será corrigido na próxima versão.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>SDK de Fala de Serviços Cognitivos 0.4.0: versão de junho de 2018

**Alterações funcionais**

- AudioInputStream

  Agora, um reconhecedor pode consumir um fluxo como a fonte de áudio. Para obter informações detalhadas, consulte o [guia de instruções](how-to-use-audio-input-streams.md) relacionado.

- Formato de saída detalhado

  Ao criar um `SpeechRecognizer`, você pode solicitar o formato de saída `Detailed` ou `Simple`. O `DetailedSpeechRecognitionResult` contém uma pontuação de confiança, texto reconhecido, forma léxica bruta, forma normalizada e forma normalizada com obscenidades mascaradas.

**Alterações da falha**

- Alterar para `SpeechRecognitionResult.Text` de `SpeechRecognitionResult.RecognizedText` em C#.

**Correções de bug**

- Corrige um possível problema de retorno de chamada na camada USP durante o desligamento.

- Se um reconhecedor consumir um arquivo de entrada de áudio, ele manteve o identificador de arquivo por mais tempo do que o necessário.

- Remover vários deadlocks entre a bomba de mensagens e o reconhecedor.

- Dispare um resultado `NoMatch` quando o tempo de resposta do serviço esgotar.

- As bibliotecas de base de mídia no Windows são carregadas com atraso. Essa biblioteca só é necessária para a entrada do microfone.

- A velocidade de carregamento de dados de áudio é limitada a duas vezes a velocidade do áudio original.

- No Windows, agora os assemblies .NET em C# têm nomes fortes.

- Correção de documentação: `Region` são as informações necessárias para criar um reconhecedor.

Mais exemplos foram adicionados e são atualizados constantemente. Para obter o último conjunto de exemplos, consulte o [Repositório GitHub de exemplo do SDK de Fala](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>SDK de Fala de Serviços Cognitivos 0.2.12733: versão de maio de 2018

A primeira versão prévia pública do SDK de Fala dos Serviços Cognitivos.
