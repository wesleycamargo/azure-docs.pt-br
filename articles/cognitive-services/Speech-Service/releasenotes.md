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
ms.date: 4/5/2019
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: d53019294a255e42c4cf66f59226c9234a5adf73
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2019
ms.locfileid: "59359747"
---
# <a name="release-notes"></a>Notas de versão

## <a name="speech-sdk-141"></a>1.4.1 do SDK da fala

Esta é uma versão somente em JavaScript. Nenhum recurso foi adicionado. Foram feitas as seguintes correções:

* Impedir o webpack de carregar o agente de proxy de https.

## <a name="speech-sdk-140-2019-april-release"></a>SDK do 1.4.0 de fala: Versão de abril de 2019

**Novos recursos** 

* O SDK agora suporta o serviço de texto em fala como uma versão beta. Ele tem suporte no Windows e Linux da área de trabalho do C++, C#e Java. Para obter mais informações, consulte o [visão geral sobre TTS](text-to-speech.md#get-started-with-text-to-speech).
* O SDK agora dá suporte a arquivos de áudio MP3 e Opus/Ogg como arquivos de fluxo de entrada. Esse recurso está disponível somente no Linux do C++ e C# e está atualmente em versão beta (mais detalhes [aqui](how-to-use-compressed-audio-input-streams.md)).
* O Speech SDK para Java, .NET core, Objective-C e C++ tido suporte do macOS. O suporte de Objective-C para macOS está atualmente em versão beta.
* iOS: O Speech SDK para iOS (Objective-C) agora também é publicado como um CocoaPod.
* JavaScript: Suporte para o microfone de não-padrão como um dispositivo de entrada.
* JavaScript: Suporte de proxy para Node. js.

**Exemplos**

* Exemplos de uso do SDK de fala com C++ e Objective-C no macOS foram adicionados.
* Exemplos demonstram o uso do serviço de texto em fala foram adicionados.

**Melhorias de alterações**

* Python: Propriedades adicionais dos resultados do reconhecimento são agora expostas por meio de `properties` propriedade.
* Para obter suporte adicional de desenvolvimento e depuração, você pode redirecionar informações de registro em log e diagnóstico do SDK em um arquivo de log (mais detalhes [aqui](how-to-use-logging.md)).
* JavaScript: Melhore o desempenho de processamento de áudio.

**Correções de bug**

* Mac/iOS: Foi corrigido um bug que levou a uma longa espera quando uma conexão para o serviço de fala não pôde ser estabelecida.
* Python: melhore o tratamento de erros para argumentos nos retornos de chamada do Python.
* JavaScript: Fixo estado incorreto de relatórios para fala terminou em RequestSession.

## <a name="speech-sdk-131-2019-february-refresh"></a>Fala SDK 1.3.1: Atualização de fevereiro de 2019

Esta é uma versão de correção de bug e que afeta somente o SDK nativo/gerenciado. Ele não está afetando a versão do SDK do JavaScript.

**Correção de bug**

* Correção de um vazamento de memória ao usar a entrada do microfone. Stream com base ou o arquivo de entrada não é afetado.

## <a name="speech-sdk-130-2019-february-release"></a>SDK de Fala 1.3.0: Versão de fevereiro de 2019

**Novos recursos**

* O Speech SDK dá suporte à seleção de entrada microfone por meio da classe AudioConfig. Isso permite que você transmitir áudio dados para os serviços de fala de um microfone não padrão. Para obter mais informações, consulte a documentação que descreve [seleção de dispositivo de entrada de áudio](how-to-select-audio-input-devices.md). Isso ainda não está disponível do JavaScript.
* O Speech SDK agora dá suporte ao Unity em uma versão beta. Forneça seus comentários por meio da seção de problema no [repositório de exemplo do GitHub](https://aka.ms/csspeech/samples). Essa versão dá suporte ao Unity no Windows x86 e x64 (área de trabalho ou aplicativos da Plataforma Universal do Windows) e Android (ARM32/64, x86). Mais informações estão disponíveis em nosso [início rápido do Unity](quickstart-csharp-unity.md).
* O arquivo `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (fornecido em versões anteriores) não é mais necessário. A funcionalidade agora está integrada ao SDK do core.


**Exemplos**

O novo conteúdo a seguir está disponível no nosso [repositório de exemplo](https://aka.ms/csspeech/samples):

* Exemplos adicionais para AudioConfig.FromMicrophoneInput.
* Mais exemplos do Python para conversão e reconhecimento de intenção.
* Exemplos adicionais para usar o objeto de Conexão no iOS.
* Exemplos adicionais de Java para tradução com saída de áudio.
* Novo exemplo para usar a [API REST de Transcrição de Lote](batch-transcription.md).

**Melhorias de alterações**

* Python
  * Mensagens de erro e verificação de parâmetro aprimoradas em SpeechConfig.
  * Adicione suporte para o objeto de Conexão.
  * Suporte para Python de 32 bits (x86) no Windows.
  * O Speech SDK para Python está fora do beta.
* iOS
  * O SDK agora é construído com relação ao SDK versão 12.1 do iOS.
  * O SDK agora dá suporte a iOS versões 9.2 e posteriores.
  * Melhore a documentação de referência e conserte vários nomes de propriedade.
* JavaScript
  * Adicione suporte para o objeto de Conexão.
  * Adicione arquivos de definição de tipo para JavaScript agrupado
  * Suporte inicial e implementação para dicas de frase.
  * Retornar a coleção de propriedades com o serviço de JSON para reconhecimento
* DLLs do Windows agora contêm um recurso de versão.
* Se você criar um reconhecedor `FromEndpoint` você pode adicionar parâmetros diretamente para a URL de ponto de extremidade. Usando `FromEndpoint` não é possível configurar o reconhecedor por meio das propriedades de configuração padrão.

**Correções de bug**

* Nome de usuário de proxy e senha de proxy vazios não foram tratados corretamente. Com esta versão, se você definir o nome de usuário de proxy e a senha de proxy como uma cadeia de caracteres vazia, eles não serão enviados ao conectarem-se ao proxy.
* As SessionIds criadas pelo SDK nem sempre eram realmente aleatórias para alguns idiomas&nbsp;/ambientes. Adicionada inicialização do gerador aleatória para consertar isso.
* Melhore o tratamento do token de autorização. Se você quiser usar um token de autorização, especifique em SpeechConfig e deixe a chave de assinatura vazia. Em seguida, crie o reconhecedor como de costume.
* Em alguns casos, o objeto Conexão não foi lançado corretamente. Esse problema foi corrigido.
* O exemplo de JavaScript foi corrigido para dar suporte para saída de áudio para síntese de conversão também no Safari.

## <a name="speech-sdk-121"></a>Speech SDK 1.2.1

Esta é uma versão somente em JavaScript. Nenhum recurso foi adicionado. Foram feitas as seguintes correções:

* Acionar o final do fluxo no turn.end, não no speech.end.
* Consertar um bug na bomba de áudio que não agendou o próximo envio em caso de falha do envio atual.
* Consertar reconhecimento contínuo com o token de autenticação.
* Correção de bug para diferentes reconhecedores/pontos de extremidade.
* Melhorias na documentação.

## <a name="speech-sdk-120-2018-december-release"></a>SDK dE Fala 1.2.0: Versão de dezembro de 2018

**Novos recursos**

* Python
  * A versão Beta do suporte do Python (3.5 e posterior) está disponível com esta versão. Para obter mais informações, consulte here](quickstart-python.md).
* JavaScript
  * O SDK de Fala para o JavaScript tem sido livre. O código-fonte está disponível no [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  * Agora damos suporte a node. js, mais informações podem ser encontradas [aqui](quickstart-js-node.md).
  * A restrição de comprimento para sessões de áudio foi removida, a reconexão ocorrerá automaticamente sob a tampa.
* Objeto de Conexão
  * Do reconhecedor, você pode acessar um objeto de Conexão. Esse objeto permite iniciar a conexão de serviço e inscrever-se para se conectar e desconectar de eventos explicitamente.
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
* Corrigido um problema que poderia levar a resultado final para traduções com falha.
* C#: Se uma operação assíncrona não foi colocada em espera no thread principal, era possível que o reconhecedor pudesse ser descartado antes que a tarefa assíncrona fosse concluída.
* Java: Corrigido um problema que resultou em uma falha da máquina virtual Java.
* Objective-C: Mapeamento enum fixo; RecognizedIntent foi retornado em vez de RecognizingIntent.
* JavaScript: Formato de saída padrão definido em 'simples' no SpeechConfig.
* JavaScript: Removendo a inconsistência entre as propriedades no objeto de configuração em JavaScript e outras linguagens.

**Exemplos**

* Atualizados e corrigidos vários exemplos (por exemplo saída vozes para conversão, etc.).
* Adicionados exemplos do Node. js no [repositório de exemplo](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>SDK de Fala 1.1.0

**Novos recursos**

* Suporte para Android x86/x64.
* Suporte do proxy: No objeto SpeechConfig, agora você pode chamar uma função para definir as informações de proxy (nome de host, porta, nome de usuário e senha). Este recurso ainda não está disponível no iOS.
* Melhor código de erro e mensagens. Se um reconhecimento retornou um erro, isso já definiu `Reason` (no evento cancelado) ou `CancellationDetails` (no resultado do reconhecimento) para `Error`. O evento cancelado agora contém dois membros adicionais, `ErrorCode` e `ErrorDetails`. Se o servidor retornou informações de erro adicionais com o erro relatado, agora ele estará disponível nos novos membros.

**Melhorias**

* Adicionada verificação adicional na configuração do reconhecedor e adicionada outra mensagem de erro.
* Manipulação aprimorada de silêncio de longa duração no meio de um arquivo de áudio.
* Pacote NuGet: para projetos do .NET Framework, ele impede a construção com a configuração AnyCPU.

**Correções de bug**

* Corrigido várias exceções encontradas em reconhecedores. Além disso, as exceções são capturadas e convertidas em evento cancelado.
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

**Alterações de última hora**

* Com esta versão, um número de alterações significativas é introduzido.
  Confira [esta página](https://aka.ms/csspeech/breakingchanges_1_0_0) para obter detalhes.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>SDK de Fala dos Serviços Cognitivos 0.6.0: versão de agosto de 2018

**Novos recursos**

* Os aplicativos UWP criados com o SDK de Fala agora podem ser aprovados pelo WACK (Kit de Certificação de Aplicativos Windows).
  Confira o [Início Rápido do UWP](quickstart-csharp-uwp.md).
* Suporte para .NET Standard 2.0 no Linux (Ubuntu 16.04 x64).
* Experimental: dê suporte Java 8 no Windows (64 bits) e no Linux (Ubuntu 16.04 x64).
  Confira o [Início Rápido do Java Runtime Environment](quickstart-java-jre.md).

**Alterações funcionais**

* Expor informações de detalhe de erro adicionais sobre erros de conexão.

**Alterações de última hora**

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

* `StartContinuousRecognitionAsync()` oferece suporte ao reconhecimento de longa execução.
* O resultado do reconhecimento contém mais campos. Eles são deslocados do início do áudio e da duração (ambos em tiques) do texto reconhecido e dos valores adicionais que representam o status de reconhecimento, por exemplo, `InitialSilenceTimeout` e `InitialBabbleTimeout`.
* Suporte para AuthorizationToken para criar instâncias de fábrica.

**Alterações de última hora**

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

**Alteração significativa**

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
