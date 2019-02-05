---
title: 'Exemplo: Chamar a API de Detecção de Emoções para Vídeo'
titlesuffix: Azure Cognitive Services
description: Saiba como chamar a API de Detecção de Emoções para Vídeo em Serviços Cognitivos.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: sample
ms.date: 02/06/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: dd2df32ed43fd540a0516b7d5c1debc6a4f49f4f
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55211161"
---
# <a name="example-call-emotion-api-for-video"></a>Exemplo: Chamar API de Detecção de Emoções para Vídeo

> [!IMPORTANT]
> A API de Detecção de Emoções será preterida em 15 de fevereiro de 2019. A funcionalidade de Detecção de Emoções agora está disponível como parte da [API de Detecção Facial](https://docs.microsoft.com/azure/cognitive-services/face/). 

Este guia demonstra como chamar a API de Detecção de Emoções para Vídeo. Os exemplos são gravados em C# usando a biblioteca de clientes da API de Detecção de Emoções para Vídeo.

### <a name="Prep">Preparação</a>
Para usar a API de Detecção de Emoções para Vídeo, será necessário um vídeo que inclua pessoas, preferencialmente vídeos em que as pessoas estejam voltadas para a câmera.

### <a name="Step1">Etapa 1: Autorizar a chamada à API</a>
Todas as chamadas para a API de Detecção de Emoções para Vídeo exigem uma chave de assinatura. Essa chave precisa ser passada por um parâmetro de cadeia de caracteres de consulta ou especificada no cabeçalho da solicitação. Para passar a chave de assinatura por meio de uma cadeia de caracteres de consulta, consulte a URL de solicitação abaixo para a API de Detecção de Emoções para Vídeo como um exemplo:

```
https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognizeInVideo&subscription-key=<Your subscription key>
```

Como alternativa, a chave de assinatura também pode ser especificada no cabeçalho da solicitação HTTP:

```
ocp-apim-subscription-key: <Your subscription key>
```

Ao usar uma biblioteca de clientes, a chave de assinatura é passada através do construtor da classe VideoServiceClient. Por exemplo: 

```
var emotionServiceClient = new emotionServiceClient("Your subscription key");
```
Para obter uma chave de assinatura, consulte [Assinaturas](https://azure.microsoft.com/try/cognitive-services/).

### <a name="Step2">Etapa 2: Carregar um vídeo para o serviço e verifique o status</a>
A maneira mais básica de executar qualquer uma das APIs de Detecção de Emoções para Vídeo é carregar um vídeo diretamente. Isso é feito, enviando uma solicitação "POST" com o tipo de conteúdo application/octet-stream junto com os dados lidos de um arquivo de vídeo. O tamanho máximo do vídeo é de 100 MB.

Usando a biblioteca de clientes, a estabilização por meio do upload é feita passando em um objeto de fluxo. Veja o exemplo abaixo:

```
Operation videoOperation;
using (var fs = new FileStream(@"C:\Videos\Sample.mp4", FileMode.Open))
{
      videoOperation = await videoServiceClient.CreateOperationAsync(fs, OperationType.recognizeInVideo);
}
```

Note que o método CreateOperationAsync de VideoServiceClient é assíncrono. O método de chamada também deverá ser marcado como assíncrono para usar a cláusula await.
Se o vídeo já estiver na Web e tiver uma URL pública, a API de Detecção de Emoções para Vídeo poderá ser acessada fornecendo a URL. Neste exemplo, o corpo da solicitação será uma cadeia de caracteres JSON, que contém a URL.

Usando a biblioteca de clientes, a estabilização por meio de uma URL pode ser facilmente executada usando outra sobrecarga do método CreateOperationAsync.


```
var videoUrl = "http://www.example.com/sample.mp4";
Operation videoOperation = await videoServiceClient.CreateOperationAsync(videoUrl, OperationType. recognizeInVideo);

```

Esse método de upload será o mesmo para todas as chamadas da API de Detecção de Emoções para Vídeo.

Após o upload de um vídeo, a próxima operação é verificar o status. Como os arquivos de vídeo geralmente são maiores e mais diversos do que outros arquivos, os usuários podem esperar um longo tempo de processamento nesta etapa. O tempo depende do tamanho e do comprimento do arquivo.

Usando a biblioteca de clientes, é possível recuperar o status e o resultado da operação usando o método GetOperationResultAsync.


```
var operationResult = await videoServiceClient.GetOperationResultAsync(videoOperation);

```
Normalmente, o lado do cliente deve recuperar periodicamente o status da operação até que o status seja mostrado como "Com êxito" ou "Com falha".

```
OperationResult operationResult;
while (true)
{
      operationResult = await videoServiceClient.GetOperationResultAsync(videoOperation);
      if (operationResult.Status == OperationStatus.Succeeded || operationResult.Status == OperationStatus.Failed)
      {
           break;
      }

      Task.Delay(30000).Wait();
}

```

Quando o status de VideoOperationResult é mostrado como "Com êxito", o resultado pode ser recuperado, convertendo o VideoOperationResult em um VideoOperationInfoResult<VideoAggregateRecognitionResult> e acessando o campo ProcessingResult.

```
var emotionRecognitionJsonString = ((VideoOperationInfoResult<VideoAggregateRecognitionResult>)operationResult).ProcessingResult;
```

### <a name="Step3">Etapa 3: Recuperar e entender o reconhecimento de emoções e acompanhar a saída JSON</a>

O resultado da saída contém os metadados das faces dentro do arquivo fornecido no formato JSON.

Conforme explicado na Etapa 2, a saída JSON está disponível no campo ProcessingResult de OperationResult, quando o status é mostrado como "Com êxito".

O JSON de detecção e acompanhamento facial inclui os seguintes atributos:

Atributo | DESCRIÇÃO
-------------|-------------
Versão | Refere-se à versão da API de Detecção de Emoções para Vídeo JSON.
Escala de tempo | "Tiques" por segundo do vídeo.
Deslocamento  |A diferença de tempo para carimbos de data/hora. Na versão 1.0 da API de Detecção de Emoções para Vídeos, isso sempre será 0. Nos futuros cenários com suporte, esse valor poderá alterar.
Taxa de quadros | Quadros por segundo do vídeo.
Fragmentos   | Os metadados são cortados em diferentes partes menores chamadas fragmentos. Cada fragmento contém um início, uma duração, um número de intervalo e evento(s).
Iniciar   | A hora de início do primeiro evento, em tiques.
Duration |  A duração do fragmento, em tiques.
Intervalo |  O intervalo de cada entrada de evento dentro do fragmento, em tiques.
Eventos  | Um matriz de eventos. A matriz externa representa um intervalo de tempo. A matriz interna é composta por 0 ou mais eventos que ocorreram nesse ponto no tempo.
windowFaceDistribution |    Porcentagem de faces para ter uma emoção específica durante o evento.
windowMeanScores |  Pontuações médias para cada emoção das faces na imagem.

O motivo para formatar o JSON dessa maneira é configurar as APIs para cenários futuros, onde será importante recuperar os metadados rapidamente e gerenciar um grande fluxo de resultados. Essa formatação está usando ambas as técnicas de fragmentação (permitindo que você divida os metadados em partes baseadas no tempo, em que é possível baixar apenas o que for necessário) e segmentação (permitindo que você divida os eventos se ficarem muito grandes). Alguns cálculos simples podem ajudar você a transformar os dados. Por exemplo, se um evento tiver iniciado em 6300 (tiques), com uma escala de tempo de 2997 (tiques/s) e uma taxa de quadros de 29,97 (quadros/s), então:

*   Início/Escala de tempo = 2,1 segundos
*   Segundos x (Taxa de quadros/Escala de tempo) = 63 quadros

A seguir, um exemplo simples de como extrair o JSON em um formato por quadro para detecção e acompanhamento facial:

```
var emotionRecognitionTrackingResultJsonString = operationResult.ProcessingResult;
var emotionRecognitionTracking = JsonConvert.DeserializeObject<EmotionRecognitionResult>(emotionRecognitionTrackingResultJsonString, settings);
```
Como as emoções são atenuadas com o tempo, se você compilar uma visualização para sobrepor os resultados no vídeo original, subtraia 250 milissegundos dos carimbos de data/hora fornecidos.

### <a name="Summary">Resumo</a>
Neste guia, você aprendeu sobre as funcionalidades da API de Detecção de Emoções para Vídeo: como fazer upload de um vídeo, verificar o status, recuperar metadados de reconhecimento de emoção.

Para obter mais informações sobre os detalhes da API, consulte o guia de referência de API “[Referência de API de Detecção de Emoções para Vídeo](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f8d40e1984551ec0a0984e)”.
