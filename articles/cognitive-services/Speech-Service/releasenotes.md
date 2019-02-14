---
title: Notas sobre a versão – Serviços de Fala
titlesuffix: Azure Cognitive Services
description: Veja um log de execução de versões de recursos, aprimoramentos, correções de bugs e problemas conhecidos dos Serviços de Fala do Azure.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: a5b8cd7da465bc2dc58c2b89852903669c18bf4b
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55871788"
---
# <a name="release-notes"></a>Notas de versão

## <a name="speech-sdk-120-2018-december-release"></a>SDK dE Fala 1.2.0: Versão de dezembro de 2018

**Novos recursos**

* Python
  * A versão Beta do suporte do Python (3.5 e posterior) está disponível com esta versão. Para obter mais detalhes, [consulte aqui](quickstart-python.md).
* JavaScript
  * O SDK de Fala para o JavaScript tem sido livre. O código-fonte está disponível no [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  * Agora damos suporte a node. js, mais informações podem ser encontradas [aqui](quickstart-js-node.md).
  * A restrição de comprimento para sessões de áudio foi removida, a reconexão ocorrerá automaticamente sob a tampa.
* Objeto de Conexão
  * É possível acessar um objeto de Conexão do Reconhecedor. Esse objeto permite iniciar a conexão de serviço e inscrever-se para se conectar e desconectar de eventos explicitamente.
    (Isso ainda não está disponível do JavaScript e Python.)
* Suporte para Ubuntu 18.04.
* Android
  * Suporte do ProGuard habilitado durante a geração de APK.

**Melhorias**

* Melhorias no uso de thread interno, reduzindo o número de threads, bloqueios e exclusões mútuas.
* Relatório/informações de erros aprimorados. Em muitos casos as mensagens de erro não são propagadas completamente para fora.
* As dependências de desenvolvimento atualizadas do JavaScript para usar módulos atualizados.

**Correções de bug**

* Vazamentos de memória fixa devido a um tipo de incompatibilidade no RecognizeAsync.
* Em alguns casos, as exceções foram sendo vazadas.
* Corrigindo o vazamento de memória em argumentos de evento de tradução.
* Corrigido um problema de bloqueio na reconexão longa de sessões em execução.
* Corrigido um problema que poderia levar ao resultado final para traduções com falha.
* C#: Se uma operação assíncrona não foi colocada em espera no thread principal, era possível que o reconhecedor pudesse ser descartado antes que a tarefa assíncrona fosse concluída.
* Java: Corrigido um problema que resultou em uma falha da máquina virtual Java.
* Objective-C: Mapeamento enum fixo; RecognizedIntent foi retornado em vez de RecognizingIntent.
* JavaScript: Formato de saída padrão definido em 'simples' no SpeechConfig.
* JavaScript: Removendo a inconsistência entre as propriedades no objeto de configuração em JavaScript e outras linguagens.

**Exemplos**

* Vários exemplos atualizados e corrigidos (por exemplo: vozes de saída para a conversão, etc).
* Adicionados exemplos do Node. js no [repositório de exemplo](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>SDK de Fala 1.1.0

**Novos recursos**

* Suporte para Android x86/x64.
* Suporte do proxy: no objeto SpeechConfig, agora você pode chamar uma função para definir as informações do proxy (nome do host, porta, nome de usuário e senha). Este recurso ainda não está disponível no iOS.
* Melhor código de erro e mensagens. Se um reconhecimento retornou um erro, isso já definiu `Reason` (no evento cancelado) ou `CancellationDetails` (no resultado do reconhecimento) para `Error`. O evento cancelado agora contém dois membros adicionais, `ErrorCode` e `ErrorDetails`. Se o servidor retornou informações de erro adicionais com o erro relatado, agora ele estará disponível nos novos membros.

**Melhorias**

* Adicionada verificação adicional na configuração do reconhecedor e adicionada outra mensagem de erro.
* Manipulação aprimorada de silêncio de longa duração no meio de um arquivo de áudio.
* Pacote NuGet: para projetos do .NET Framework, ele impede a construção com a configuração AnyCPU.

**Correções de bug**

* Corrigido várias exceções encontradas em reconhecedores. Além disso, as exceções são capturadas e convertidas em evento Cancelado.
* Corrigir um vazamento de memória no gerenciamento de propriedades.
* Corrigido o erro no qual um arquivo de entrada de áudio poderia travar o reconhecedor.
* Corrigido um bug no qual os eventos podiam ser recebidos após um evento de parada da sessão.
* Corrigidas algumas condições de corrida no threading.
* Corrigido um problema de compatibilidade do iOS que poderia resultar em uma falha.
* Melhorias de estabilidade para suporte de microfone Android.
* Corrigido um erro em que um reconhecedor em JavaScript ignoraria o idioma de reconhecimento.
* Corrigido um bug que impedia a configuração do EndpointId (em alguns casos) em JavaScript.
* Alterou a ordem dos parâmetros no AddIntent em JavaScript e adicionou a assinatura JavaScript ausente do AddIntent.

**Exemplos**

* Adicionado o C++ e C# samplea para uso de fluxo de pull e push na [repositório de exemplo](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-101"></a>SDK de Fala 1.0.1

Melhorias na confiabilidade e correções de bugs:

* Corrigido erro fatal potencial devido à condição de corrida no reconhecedor de descarte
* Corrigido erro fatal potencial no caso de propriedades não definidas.
* Adicionado erro adicional e verificação de parâmetros.
* Objective-C: corrigido possível erro fatal causado por substituição de nome em NSString.
* Objective-C: visibilidade ajustada da API
* JavaScript: corrigido em relação a eventos e conteúdos.
* Melhorias na documentação.

Em nosso [repositório de exemplos](https://aka.ms/csspeech/samples), um novo exemplo para JavaScript foi adicionado.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>SDK de Fala dos Serviços Cognitivos 1.0.0: versão de setembro de 2018

**Novos recursos**

* Suporte para Objective-C no iOS. Confira nosso [Início Rápido do Objective-C para iOS](quickstart-objectivec-ios.md).
* Suporte para JavaScript no navegador. Confira nosso [Início Rápido do JavaScript](quickstart-js-browser.md).

**Alterações da falha**

* Com essa versão, várias alterações significativas foram introduzidas.
  Confira [esta página](https://aka.ms/csspeech/breakingchanges_1_0_0) para obter detalhes.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>SDK de Fala dos Serviços Cognitivos 0.6.0: versão de agosto de 2018

**Novos recursos**

* Os aplicativos UWP criados com o SDK de Fala agora podem ser aprovados pelo WACK (Kit de Certificação de Aplicativos Windows).
  Confira o [Início Rápido do UWP](quickstart-csharp-uwp.md).
* Suporte para .NET Standard 2.0 no Linux (Ubuntu 16.04 x64).
* Experimental: dê suporte Java 8 no Windows (64 bits) e no Linux (Ubuntu 16.04 x64).
  Confira o [Início Rápido do Java Runtime Environment](quickstart-java-jre.md).

**Alteração funcional**

* Expor informações de detalhe de erro adicionais sobre erros de conexão.

**Alterações da falha**

* No Java (Android), a função `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` não requer mais um parâmetro de caminho. Agora, o caminho é detectado automaticamente em todas as plataformas com suporte.
* O get-accessor da propriedade `EndpointUrl` em Java e C# foi removido.

**Correções de bug**

* Em Java, o resultado da síntese de áudio no reconhecedor de tradução agora está implementado.
* Foi corrigido um bug que podia causar threads inativos e um grande número de soquetes abertos e não usados.
* Foi corrigido um problema em que o reconhecimento de execução longa podia terminar no meio da transmissão.
* Corrigida uma condição de corrida no desligamento do reconhecedor.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>SDK de Fala dos Serviços Cognitivos 0.5.0: versão de julho de 2018

**Novos recursos**

* Suporte à plataforma Android (API 23: Android 6.0 Marshmallow ou superior). Confira o [Início Rápido para Android](quickstart-java-android.md).
* Suporte para .NET Standard 2.0 no Windows. Confira o [Início Rápido para .NET Core](quickstart-csharp-dotnetcore-windows.md).
* Experimental: suporte à UWP no Windows (versão 1709 ou posterior).
  * Confira o [Início Rápido do UWP](quickstart-csharp-uwp.md).
  * Observação: os aplicativos UWP compilados com o SDK de Fala ainda não são aprovados pelo WACK (Kit de Certificação de Aplicativos Windows).
* Suporte ao reconhecimento de execução longa com reconexão automática.

**Alterações funcionais**

* O `StartContinuousRecognitionAsync()` dá suporte ao reconhecimento de execução longa.
* O resultado do reconhecimento contém mais campos. Eles são deslocados do início do áudio e da duração (ambos em tiques) do texto reconhecido e dos valores adicionais que representam o status de reconhecimento, por exemplo, `InitialSilenceTimeout` e `InitialBabbleTimeout`.
* Suporte para AuthorizationToken para criar instâncias de fábrica.

**Alterações da falha**

* Eventos de reconhecimento: o tipo de evento NoMatch foi mesclado ao evento Erro.
* SpeechOutputFormat em C# foi renomeado para OutputFormat para permanecer alinhado com o C++.
* O tipo de retorno de alguns métodos da interface `AudioInputStream` foi um pouco alterado:
   * Em Java, o método `read` agora retorna `long` em vez de `int`.
   * Em C#, o método `Read` agora retorna `uint` em vez de `int`.
   * Em C++, os métodos `Read` e `GetFormat` agora retornam `size_t` em vez de `int`.
* C++: as instâncias de fluxos de entrada de áudio agora podem ser passadas apenas como um `shared_ptr`.

**Correções de bug**

* Foram corrigidos os valores retornados incorretos no resultado quando `RecognizeAsync()` atinge o tempo limite.
* A dependência das bibliotecas do Media Foundation no Windows foi removida. O SDK agora usa as APIs Core Audio.
* Correção da documentação: uma página de [regiões](regions.md) foi adicionada para descrever as regiões com suporte.

**Problema conhecido**

* O SDK de Fala para Android não relata os resultados da síntese de fala para tradução. Esse problema será corrigido na próxima versão.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>SDK de Fala dos Serviços Cognitivos 0.4.0: versão de junho de 2018

**Alterações funcionais**

- AudioInputStream

  Agora, um reconhecedor pode consumir um fluxo como a fonte de áudio. Para obter mais detalhes, confira o [guia de instruções](how-to-use-audio-input-streams.md) relacionado.

- Formato de saída detalhado

  Ao criar um `SpeechRecognizer`, você pode solicitar o formato de saída `Detailed` ou `Simple`. O `DetailedSpeechRecognitionResult` contém uma pontuação de confiança, texto reconhecido, forma léxica bruta, forma normalizada e forma normalizada com obscenidades mascaradas.

**Alterações da falha**

- Alterado para `SpeechRecognitionResult.Text` de `SpeechRecognitionResult.RecognizedText` em C#.

**Correções de bug**

- Foi corrigido um possível problema de retorno de chamada na camada USP durante o desligamento.

- Se um reconhecedor consumir um arquivo de entrada de áudio, ele manteve o identificador de arquivo por mais tempo do que o necessário.

- Foram removidos vários deadlocks entre a bomba de mensagens e o reconhecedor.

- Dispare um resultado `NoMatch` quando o tempo de resposta do serviço esgotar.

- As bibliotecas do Media Foundation no Windows são carregadas com atraso. Essa biblioteca é necessária apenas para entrada do microfone.

- A velocidade de carregamento de dados de áudio é limitada a duas vezes a velocidade do áudio original.

- No Windows, agora os assemblies .NET em C# têm nomes fortes.

- Correção de documentação: `Region` são as informações necessárias para criar um reconhecedor.

Mais exemplos foram adicionados e são atualizados constantemente. Para obter o último conjunto de exemplos, confira o [Repositório GitHub de exemplos do SDK de Fala](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>SDK de Fala dos Serviços Cognitivos 0.2.12733: versão de maio de 2018

Esta versão é a primeira versão prévia pública do SDK de Fala dos Serviços Cognitivos.
