---
title: Conceitos de Fala do Bing | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Conceitos básicos usados no serviço de fala da Microsoft.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: 6089f053472faaa7fa8c957904f73c7061cb2d3f
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344918"
---
# <a name="basic-concepts"></a>Conceitos básicos

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Esta página descreve alguns conceitos básicos no serviço de reconhecimento de fala Microsoft. Recomendamos que você leia esta página antes de usar a API de reconhecimento de fala Microsoft em seu aplicativo.

## <a name="understanding-speech-recognition"></a>Entendendo o reconhecimento de fala

Se esta for a primeira vez que você estiver criando um aplicativo habilitado para fala ou se for a primeira vez em que você está adicionando os recursos de fala a um aplicativo existente, esta seção ajuda você a começar. Se você já tiver alguma experiência com aplicativos habilitados para fala, você pode optar por ler apenas esta seção, ou você pode ignorá-lo inteiramente se estiver disponível uma fala antiga e você desejar obter o direito aos detalhes de protocolo.

### <a name="audio-streams"></a>Fluxos de áudio

O principal entre os conceitos básicos de fala é o *fluxo de áudio*. Ao contrário de um pressionamento de tecla, que ocorre em um único ponto no tempo e contém uma única informação, uma solicitação de falada é distribuída entre centenas de milissegundos e contém muitos quilobytes de informações. A duração de enunciados falados apresenta dificuldades para os desenvolvedores que desejam fornecer uma experiência de fala discreta e simplificada para seu aplicativo. Os computadores atuais e algoritmos executam transcrição de fala aproximadamente metade da duração da expressão, portanto, uma expressão de 2 segundos pode ser transcrita em aproximadamente 1 segundo, mas qualquer aplicativo que passa por um atraso de 1 segundo no usuário de processamento não é simplificado e nem elegante.

Felizmente, há maneiras de "ocultar" a hora de transcrição executando transcrição em uma parte da expressão, enquanto o usuário está falando outra parte. Por exemplo, ao dividir uma expressão de 1 segundo em 10 partes de 100 milissegundos e executar a transcrição em cada bloco por sua vez, mais de 450 dos total 500 milissegundos necessários para a transcrição pode ser "oculto" para que o usuário não fique ciente da transcrição que está sendo executada enquanto ele está falando. Ao pensar neste exemplo, lembre-se de que o serviço está executando a transcrição nos 100 milissegundos anteriores de áudio enquanto o usuário está falando próximos 100, portanto quando o usuário para falando, o serviço só precisam transcrever aproximadamente 100 milissegundos de áudio para produzir um resultado.

Para alcançar essa experiência de usuário, as informações de áudio faladas são coletadas em partes e transcritas conforme o usuário fala. Essas partes de áudio coletivamente do *fluxo de áudio*, e o processo de enviar esses blocos de áudio para o serviço é chamado *streaming de áudio.* Streaming de áudio é uma parte importante de qualquer aplicativo habilitado para fala; ajustar o tamanho da parte e otimizar a implementação de fluxo são algumas das maneiras mais impacto de melhorar a experiência do usuário do seu aplicativo.

### <a name="microphones"></a>Microfones

Pessoas processam áudio falado usando seus ouvidos, mas hardware inanimado usa microfones. Para habilitar a fala em qualquer aplicativo, você precisa integrar com o microfone fornecendo o fluxo de áudio para seu aplicativo.

As APIs para o microfone deve permitir iniciar e interromper o recebimento de bytes de áudio do microfone. Você precisa decidir quais ações do usuário irá disparar o microfone ao iniciar a escuta de fala. Você pode optar por ter um disparo de pressionamento de botão, ou você pode optar por ter uma *palavra-chave* ou spotter de *wake word* sempre escutando o microfone e usando a saída do módulo para gatilho enviando áudio para o serviço de fala da Microsoft.

### <a name="end-of-speech"></a>Fim de fala

Detectando *quando* o falante *parou* de falar parece simples o suficiente para os usuários, mas é um problema bastante difícil fora das condições de laboratório. Não é suficiente para simplesmente procurar puro silêncio após uma expressão, pois geralmente é muito ruído de ambiente para complicar as coisas. O Serviço de Fala da Microsoft faz um excelente trabalho de rapidamente detectar quando um usuário parou de falar e o serviço pode informar ao seu aplicativo esse fato, mas essa organização significa que seu aplicativo é o último saber quando o usuário parar de falar. Isso não está em todos as outras formas de entrada em que seu aplicativo é o *primeiro* a saber quando a entrada do usuário inicia *e* termina.

### <a name="asynchronous-service-responses"></a>Respostas de serviço assíncrona

O fato de que seu aplicativo precisar ser informado sobre quando a entrada do usuário for concluída não impõe qualquer penalidades de desempenho ou problemas de programação em seu aplicativo, mas requer que você pense sobre solicitações de fala diferente da solicitação de entrada / padrões de resposta com o qual você está familiarizado. Desde que o aplicativo não saiba quando o usuário para de falar, o aplicativo deve continuar a fluxo de áudio para o serviço enquanto simultaneamente e de maneira assíncrona aguarda uma resposta do serviço. Esse padrão é diferente de outros protocolos de web de solicitação/resposta HTTP. Nesses protocolos, você deve concluir uma solicitação antes de receber nenhuma resposta; no protocolo de serviço de fala da Microsoft, você recebe respostas *enquanto você ainda tem o fluxo de áudio para a solicitação*.

> [!NOTE]
> Não há suporte para esse recurso ao usar a API de REST de HTTP de fala.

### <a name="turns"></a>Ativa

A fala é uma operadora de informações. Quando você fala, você está tentando transmitir informações em sua posse para alguém que está escutando obter essas informações. Ao transmitir informações, você geralmente fala e escuta. Da mesma forma, seu aplicativo habilitado para fala interage com os usuários, como alternativa, ouvindo e respondendo, embora seu aplicativo geralmente faça a maioria da escuta. A entrada do usuário falada e a resposta do serviço para essa entrada é chamada um *ativar*. Um *ativar* inicia quando o usuário fala e termina quado seu aplicativo tiver concluído o manuseio da resposta de serviço de fala.

### <a name="telemetry"></a>Telemetria

Criar um aplicativo ou dispositivo habilitado para fala pode ser um desafio, mesmo para desenvolvedores experientes. Protocolos baseados em fluxo geralmente parecem difícil a princípio e detalhes importantes, como a detecção de silêncio pode ser completamente nova. Com muitas mensagens que precisam ser enviadas e recebidas com êxito para concluir um solicitação única/par de resposta, é *muito* importante para coletar dados completas e dados precisos sobre essas mensagens. O protocolo de Serviço de Fala da Microsoft fornece a coleção desses dados. Você deve verificar todos os esforços para fornecer os dados necessários mais precisamente possível; fornecendo dados completos e precisos, você irá ajudar por conta própria – se você nunca precisar de Ajuda da equipe de serviço de fala da Microsoft em sua implementação do cliente de solução de problemas, a qualidade dos dados de telemetria que você coletou será essencial para a análise do problema.

> [!NOTE]
> Esse recurso não é compatível ao usar a API REST de reconhecimento de fala.

### <a name="speech-application-states"></a>Estados de aplicativo de fala

As etapas para habilitar a entrada de fala em seu aplicativo são um pouco diferentes das etapas para outras formas de entrada, como cliques do mouse ou toques de dedo. Você deve manter controle quando seu aplicativo estiver ouvindo o microfone e enviar dados para o serviço de fala, quando ele estiver aguardando uma resposta do serviço e quando estiver em um estado ocioso. A relação entre esses estados é mostrada no diagrama a seguir.

![Diagrama de estado do aplicativo de fala](Images/speech-application-state-diagram.png)

Uma vez que o serviço de fala Microsoft participa de alguns dos estados, o protocolo de serviço define as mensagens que ajudam a transição entre estados de aplicativo. Seu aplicativo precisa interpretar e agir sobre essas mensagens de protocolo para controlar e gerenciar os estados de aplicativo de fala.

## <a name="using-the-speech-recognition-service-from-your-apps"></a>Usar o serviço de reconhecimento de fala de seus aplicativos

O serviço de reconhecimento de fala Microsoft fornece duas maneiras para que desenvolvedores adicionem fala para seus aplicativos.

- [APIs REST](GetStarted/GetStartedREST.md): os desenvolvedores podem usar chamadas HTTP de seus aplicativos para o serviço de reconhecimento de fala.
- [Bibliotecas de cliente](GetStarted/GetStartedClientLibraries.md): para recursos avançados, os desenvolvedores podem baixar as bibliotecas de cliente do Microsoft Speech e vincular a seus aplicativos.  As bibliotecas de cliente estão disponíveis em várias plataformas (Windows, Android, iOS) usando diferentes linguagens (C#, Java, JavaScript, ObjectiveC).

| Casos de uso | [APIs REST](GetStarted/GetStartedREST.md) | [Bibliotecas de cliente](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Converter um áudio curto falado, por exemplo, comandos (tamanho do áudio < 15 s) sem resultados intermediários | SIM | SIM |
| Converter um áudio longo (> 15 s) | Não  | SIM |
| Fluxo de áudio desejado com resultados intermediários | Não  | SIM |
| Entender o texto convertido de áudio usando LUIS | Não  | SIM |

 Se a linguagem ou plataforma ainda não tiver um SDK, você pode criar sua própria implementação baseada na [documentação de protocolo](API-Reference-REST/websocketprotocol.md).

## <a name="recognition-modes"></a>Modos de reconhecimento

Há três modos de reconhecimento: `interactive`, `conversation`, e `dictation`. O modo de reconhecimento ajusta o reconhecimento de fala com base em como os usuários têm probabilidade de fala. Escolha o modo de reconhecimento apropriado para seu aplicativo.

> [!NOTE]
> Os modos de reconhecimento podem ter comportamentos diferentes no [protocolo REST](#rest-speech-recognition-api) do [protocolo WebSocket](#webSocket-speech-recognition-api). Por exemplo, a API REST não oferece suporte a reconhecimento contínuo, mesmo no modo de conversa ou ditado.
> [!NOTE]
> Esses modos são aplicáveis quando você usar o protocolo REST ou WebSocket diretamente. As [bibliotecas de cliente](GetStarted/GetStartedClientLibraries.md) usam parâmetros diferentes para especificar o modo de reconhecimento. Para obter mais informações, consulte a biblioteca de cliente de sua escolha.

O serviço de fala Microsoft retorna apenas um resultado de frase de reconhecimento para todos os modos de reconhecimento. Há um limite de 15 segundos para qualquer expressão única.

### <a name="interactive-mode"></a>Modo interativo

Em `interactive` modo, um usuário faz solicitações curtas e espera que o aplicativo execute uma ação em resposta.

As seguintes características são típicas de aplicativos de modo interativo:

- Os usuários sabem que eles estão falando para uma máquina e não para outro humano.
- Os usuários do aplicativo sabem antecipadamente o que querem dizer, com base no que desejam o que o aplicativo faça.
- As declarações normalmente durante cerca de 2 a 3 segundos.

### <a name="conversation-mode"></a>Modo de conversa

No `conversation` modo, os usuários estão envolvidos em uma conversa humanos para humanos.

As seguintes características são típicas de aplicativos de modo de conversa:

- Os usuários sabem que estão se comunicando com outra pessoa.
- O reconhecimento de fala aprimora as conversas humanas, permitindo que um ou ambos os participantes vejam o texto falado.
- Os usuários não planejam sempre o que querem dizer.
- Os usuários frequentemente usam gírias e outras falas informais.

### <a name="dictation-mode"></a>Modo de Ditado

No `dictation` modo, os usuários informam declarações para o aplicativo para processamento adicional.

As seguintes características são típicas de aplicativos de modo de conversa:

- Os usuários sabem que estão se comunicando com uma máquina.
- Os usuários são mostrados os resultados de texto de reconhecimento de fala.
- Os usuários geralmente planejam que querem dizer e usam a linguagem mais formal.
- Os usuários empregam sentenças completas que duram 5 a 8 segundos.

> [!NOTE]
> Nos modos de ditado e conversa, o serviço de fala da Microsoft não retorna resultados parciais. Em vez disso, o serviço retorna resultados de frase estável após os limites de silêncio no fluxo de áudio. A Microsoft pode melhorar o protocolo de voz para melhorar a experiência do usuário nesses modos de reconhecimento contínuos.

## <a name="recognition-languages"></a>Idiomas de reconhecimento

O *idioma de reconhecimento* especifica o idioma que o usuário do aplicativo fala. Especifique o *idioma de reconhecimento* com o parâmetro da consulta de URL de *idioma* na conexão. O valor do parâmetro de consulta do *idioma*  usa a marca de idioma da IETF [BCP 47](https://en.wikipedia.org/wiki/IETF_language_tag), e **deve** ser um dos idiomas com suporte pela API de reconhecimento de fala. A lista completa dos idiomas com suporte no serviço de voz pode ser encontrada na página [Suporte para Idiomas](API-Reference-REST/supportedlanguages.md).

O serviço de fala Microsoft rejeita solicitações de conexão inválida, exibindo uma `HTTP 400 Bad Request` resposta. Uma solicitação inválida é que:

- Não inclui um valor de parâmetro de consulta do *idioma*.
- Inclui um parâmetro de consulta do *idioma* que não está formatado incorretamente.
- Inclui um parâmetro de consulta do *idioma* que não é uma das linguagens de suporte de consulta.

Você pode optar por criar um aplicativo que oferece suporte a um ou todos os idiomas com suporte pelo serviço.

### <a name="example"></a>Exemplo

No exemplo a seguir, um aplicativo usa o modo de reconhecimento de voz da *conversa* para falante em inglês dos EUA.

```HTTP
https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

## <a name="transcription-responses"></a>Respostas de transcrição

As respostas de transcrição retornam o texto convertido de áudio para clientes. A resposta de transcrição contém os campos a seguir:

- `RecognitionStatus` especifica o status de reconhecimento. Os valores possíveis são fornecidos na tabela a seguir.

| Status | DESCRIÇÃO |
| ------------- | ---------------- |
| Sucesso | O reconhecimento foi bem-sucedido e o campo de texto para exibição está presente |
| NoMatch | A fala foi detectada no fluxo de áudio, mas nenhuma palavra do idioma de destino foi combinada. Consulte [Status de Reconhecimento NoMatch (#nomatch-recognition-status) para obter mais detalhes  |
| InitialSilenceTimeout | O início do fluxo de áudio continha apenas silêncio e o serviço de tempo limite de espera de fala |
| BabbleTimeout | O início do fluxo de áudio continha apenas silêncio e o serviço de tempo limite de espera de fala |
| Erro | O serviço de reconhecimento encontrou um erro interno e não pode continuar |

- `DisplayText` representa a frase reconhecida depois de maiusculas e minúsculas, pontuação e o inverso de texto de normalização foram aplicados e profanidade foi mascarada com asteriscos. O campo de texto para exibição está presente *somente* se o `RecognitionStatus` campo tiver o valor `Success`.

- `Offset` especifica o deslocamento (em unidades de 100 nanossegundos) no qual a expressão foi reconhecida, em relação ao início do fluxo de áudio.

- `Duration`especifica a duração (em unidades de 100 nanossegundos) dessa frase de fala.

Uma resposta de transcrição retorna mais informações, se desejado. Consulte [formato de saída](#output-format) para saber como retornar mais saídas detalhadas.

O Serviço de Fala da Microsoft dá suporte ao processo de transcrição adicional que inclui a adição de maiúsculas e minúsculas e pontuação, mascaramento de profanidade e normalizar texto para formas comuns. Por exemplo, se um usuário fizer uma frase representada pelas palavras "lembrar-me de comprar seis iPhones", os serviços de fala da Microsoft retornaram o texto transcrito “Lembrar de comprar 6 iPhones”. O processo que converte a palavra "seis" para o número "6" é chamado *Normalização de Texto Inverso* (*ITN* abreviada).

### <a name="nomatch-recognition-status"></a>Status de reconhecimento de NoMatch

Retorna a resposta de transcrição `NoMatch` em `RecognitionStatus` quando o serviço de fala Microsoft detecta fala no fluxo de áudio, mas não pode corresponder a essa fala à gramática do  idioma que está sendo usado para a solicitação. Por exemplo, uma condição *NoMatch* pode ocorrer se um usuário disser algo em alemão, quando o reconhecedor espera inglês (EUA) como o idioma. O padrão de forma de onda da expressão deve indicar a presença de fala humana, mas nenhuma das palavras faladas corresponderia ao léxico em inglês dos EUA sendo usado pelo reconhecedor.

Outra condição de *NoMatch* ocorre quando o algoritmo de reconhecimento não consegue encontrar uma correspondência precisa de sons contidos no fluxo de áudio. Quando essa condição ocorre, o serviço de fala da Microsoft pode produzir *speech.hypothesis* mensagens que contêm  *texto hipotético,* mas produzirá uma  mensagem *speech.phrase* no qual o *RecognitionStatus* é *NoMatch*. Esta condição é normal. Você não deve fazer suposições sobre a precisão ou a fidelidade do texto na mensagem *speech.hypothesis*. Além disso, você não deve presumir que porque o serviço de fala Microsoft produz mensagens *speech.hypothesis* que o serviço é capaz de gerar uma mensagem *speech.phrase* com  *RecognitionStatus* *Success*.

## <a name="output-format"></a>Formato da saída

O Serviço de fala da Microsoft pode retornar uma variedade de formatos de conteúdo nas respostas de transcrição. Todos conteúdos são estruturas JSON.

Você pode controlar o formato do resultado de frase especificando o `format` parâmetro de consulta de URL. Por padrão, o serviço retorna `simple` resultados.

| Formatar | DESCRIÇÃO |
|-----|-----|
| `simple` | Um resultado de frase simplificada que contém o status de reconhecimento e o texto reconhecido no formato de exibição. |
| `detailed` | Um status de reconhecimento e uma lista de melhor N dos resultados de frase em que o resultado de cada frase contém todas as quatro formas de reconhecimento e uma pontuação de confiança. |

O `detailed` formato contém [valores de melhor N](#n-best-values), além `RecognitionStatus`, `Offset`, e `duration`, na resposta.

### <a name="n-best-values"></a>Valores de melhor N

Ouvintes, humanos ou máquinas, nunca podem estar certos de que ouviram *exatamente* o que foi dito. Um ouvinte pode atribuir um *probabilidade* apenas para uma interpretação específica de uma expressão. 

Em condições normais, ao falar com outras pessoas com as quais interagem com frequência, as pessoas com uma alta probabilidade de reconhecer as palavras que foram faladas. Ouvintes de fala baseados em máquina se esforçam para alcançar os níveis de precisão semelhantes e, sob as condições, [obtêm paridade com as pessoas](https://blogs.microsoft.com/next/2016/10/18/historic-achievement-microsoft-researchers-reach-human-parity-conversational-speech-recognition/#sm.001ykosqs14zte8qyxj2k9o28oz5v).

Os algoritmos que são usados no reconhecimento de fala exploram interpretações alternativas  de uma expressão como parte do processamento normal. Geralmente, essas alternativas são descartadas como a evidência em favor de uma única interpretação se torna grande. Em condições ideais, no entanto, o reconhecedor de fala termina com uma lista de alternativas interpretações possíveis. As alternativas *N* superiores nessa lista são chamadas de *lista de melhor N*. Cada alternativa é atribuída uma [pontuação de confiança](#confidence). Intervalo de pontuações de confiança de 0 a 1. Uma pontuação de 1 representa o nível mais alto de confiança. Uma pontuação de 0 representa o nível mais baixo de confiança.

> [!NOTE]
> O número de entradas na lista de melhor N varia de acordo com várias declarações. O número de entradas pode variar entre vários reconhecimentos da *mesma* declaração. Essa variação é um resultado natural e esperado da natureza probabilística do algoritmo de reconhecimento de fala.

Cada entrada da lista lista de melhor N contém

- `Confidence`, que representa as [pontuações de confiança](#confidence) desta entrada.
- `Lexical`, que é a [forma léxica](#lexical-form) do texto reconhecido.
- `ITN`, que é a [forma ITN](#itn-form) do texto reconhecido.
- `MaskedITN`, que é a [forma ITN mascarada](#masked-itn-form) do texto reconhecido.
- `Display`, que é a [forma de visualização ](#display-form) do texto reconhecido.

### Pontuações de Confiança<a id="confidence"></a>

As pontuações de confiança são essenciais para sistemas de reconhecimento de fala. O serviço de fala Microsoft obtém pontuações de confiança de um *classificador de confiança*. A Microsoft treina o classificador de confiança em um conjunto de recursos que foram projetados para discriminar o máximo reconhecimento correto e incorreto. As pontuações de confiança são avaliadas para palavras individuais e declarações inteiras.

Se você optar por usar as pontuações de confiança que são retornadas pelo serviço, lembre-se dos seguintes comportamentos:

- Pontuações de confiança podem ser comparadas somente dentro do mesmo modo de reconhecimento e o idioma. Não compare classificações entre diferentes idiomas e modos diferentes de reconhecimento. Por exemplo, uma pontuação no modo de reconhecimento interativo *não* tem correlação com uma pontuação de confiança no modo de ditado.
- As pontuações de confiança são melhor usadas em um conjunto restrito de apresentações. Naturalmente, há um alto grau de flexibilidade nas pontuações de um conjunto grande de declarações.

Se você optar por usar um valor de pontuação de confiança como um *limite* em que o aplicativo funciona, use o reconhecimento de fala para estabelecer os valores de limite.

- Execute o reconhecimento de fala em uma amostra representativa de declarações para seu aplicativo.
- Colete as pontuações de confiança para cada reconhecimento do conjunto de exemplo.
- Baseie o valor de limite em alguma porcentagem de confiança para esse exemplo.

Nenhum valor de limite simples é apropriado para todos os aplicativos. Uma pontuação de confiança aceitável para um aplicativo pode ser inaceitável para outro aplicativo.

### <a name="lexical-form"></a>forma léxica

A forma léxica é o texto reconhecido, exatamente como ocorreu na expressão e sem pontuação ou maiusculas e minúsculas. Por exemplo, a forma léxica do endereço "1020 Enterprise Way" seria *dez vinte enterprise way*, supondo que  foi falado dessa maneira. É a forma léxica da frase "lembrar-me de comprar 5 Lápis" *é lembrar-me comprar cinco lápis*.

A forma léxica é mais adequada para aplicativos que precisam realizar a normalização do texto não padrão. A forma léxica também é adequada para aplicativos que precisam de palavras de reconhecimento não processado.

Obscenidades nunca é mascarada na forma léxica.

### <a name="itn-form"></a>Formulário ITN

A normalização de texto é o processo de conversão de texto de um formulário em outro formato "canônico". Por exemplo, o número de telefone "555-1212" pode ser convertido para a forma canônica *cinco cinco cinco um dois um dois*. *Inverso* a normalização do texto (ITN)  inverte esse processo, convertendo as palavras "cinco cinco cinco um dois um dois" para a forma canônica invertida *555-1212*. O formulário ITN de um resultado de reconhecimento não inclui o uso de maiúsculas ou pontuação.

O formulário ITN é mais adequado para aplicativos que atuam em texto reconhecido. Por exemplo, um aplicativo que permite que um usuário fala termos de pesquisa e, em seguida, usa esses termos em uma consulta de web usaria o formulário ITN. Obscenidade nunca é mascarada na forma léxica. Para mascarar obscenidades, use o *formulário  ITN mascarada*.

### <a name="masked-itn-form"></a>Formulário ITN mascarada

Como obscenidade naturalmente é uma parte do idioma falado, o serviço de fala Microsoft reconhece essas palavras e frases quando são faladas. Obscenidades não podem ser apropriadas para todos os aplicativos, especialmente os com um público restrito de não adultos.

O formulário ITN mascarado se aplica ao mascaramento de obscenidade para o formulário de normalização do texto inverso. Para mascarar obscenidades, defina o valor do valor do parâmetro obscenidades para `masked`. Quando as obscenidades são mascaradas, palavras que são reconhecidas como parte do léxico de obscenidades do idioma são substituídas por asteriscos. Por exemplo: *lembrar-me de comprar 5 * lápis*. O formulário ITN de um resultado de reconhecimento não inclui o uso de maiúsculas ou pontuação.

> [!NOTE]
> Se o valor do parâmetro de consulta de obscenidades for definido como `raw`, o formulário ITN mascarado é o mesmo que o formulário ITN. Obscenidade é *não* mascarado.

### <a name="display-form"></a>Formulário de exibição

Sinal de pontuação e capitalização onde a pausa e assim por diante, o que torna mais fácil de entender o texto de sinal de pontuação e o uso de maiúsculas. O formato de exibição adiciona maiusculas e minúsculas e pontuação para resultados de reconhecimento, tornando o formulário mais adequado para aplicativos que exibem o texto falado.

Como exibir formulário estende o formulário ITN mascarado, você pode definir o valor do parâmetro obscenidades `masked` ou `raw`. Se o valor for definido como `raw`, os resultados do reconhecimento incluem qualquer obscenidade falada pelo usuário. Se o valor for definido como `masked`, palavras reconhecidas como parte do léxico de obscenidades do idioma são substituídas por asteriscos.

### <a name="sample-responses"></a>Respostas de exemplo

Todos conteúdos são estruturas JSON.

O formato de conteúdo do `simple` resultado da frase:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

O formato de conteúdo do `detailed` resultado da frase:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="profanity-handling-in-speech-recognition"></a>Tratamento de obscenidades no reconhecimento de fala

O serviço de fala Microsoft reconhece todos os formatos de fala humana, incluindo palavras e frases que muitas pessoas deseja classificar como "obscenidades". Você pode controlar como o serviço trata obscenidades usando o parâmetro de consulta de *obscenidades*. Por padrão, o serviço mascara obscenidades nos resultados *speech.phrase* e não retorna mensagens *speech.hypothesis* que contêm obscenidades.

| *Valor de Obscenidades* | DESCRIÇÃO |
| - | - |
| `masked` | Máscaras obscenidades com asteriscos. Esse é o comportamento padrão. | 
| `removed` | Remove obscenidades de todos os resultados. |
| `raw` | Reconhece e retorna obscenidades em todos os resultados. |

### <a name="profanity-value-masked"></a>Valor de obscenidades`Masked`

Para mascarar obscenidades, defina o parâmetro de consulta de  *obscenidades* para o valor mascarado*. Quando o parâmetro de consulta *obscenidades* tiver esse valor ou não for especificado para uma solicitação, o serviço *máscara* as obscenidades. O serviço executa mascaramento, substituindo obscenidades, os resultados do reconhecimento por asteriscos. Quando você especifica o tratamento de mascaramento de obscenidades, o serviço não retorna as mensagens *speech.hypothesis* que contêm obscenidades.

### <a name="profanity-value-removed"></a>Valor de obscenidades`Removed`

Quando o parâmetro de consulta de *obscenidades* tem o valor *removido*, o serviço remove obscenidades das mensagens *speech.phrase* e *speech.hypothesis*. Os resultados são os mesmos *como se as palavras obscenas não fossem faladas*.

#### <a name="profanity-only-utterances"></a>Somente declarações obscenas

Um usuário pode falar *somente* obscenidades quando um aplicativo tiver configurado o serviço para remover obscenidades. Para este cenário, se o modo de reconhecimento é *ditado* ou for *conversa*, o serviço não retorna um *speech.result*. Se o modo de reconhecimento é *interativo*, o serviço retorna um *speech.result* com o código de status *NoMatch*. 

### <a name="profanity-value-raw"></a>Valor de obscenidades`Raw`

Quando o parâmetro de consulta de *obscenidades* tem o valor *bruto*, o serviço remove obscenidades das mensagens *speech.phrase* e *speech.hypothesis*.
