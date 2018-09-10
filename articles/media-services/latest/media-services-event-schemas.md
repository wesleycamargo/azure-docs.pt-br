---
title: Esquemas de Grade de Eventos do Azure para eventos dos Serviços de Mídia
description: Descreve as propriedades que são fornecidas para eventos dos Serviços de Mídia com a Grade de Eventos do Azure
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: reference
ms.date: 08/17/2018
ms.author: juliako
ms.openlocfilehash: a6a6c459e170627d26aa1445f4f4dd193965fe70
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/17/2018
ms.locfileid: "42142022"
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>Esquemas de Grade de Eventos do Azure para eventos dos Serviços de Mídia

Este artigo fornece as propriedades e os esquemas para eventos de Serviços de Mídia.

Para obter uma lista de scripts de exemplo e tutoriais, consulte [Origem do evento dos Serviços de Mídia](../../event-grid/event-sources.md#azure-subscriptions).

## <a name="available-event-types"></a>Tipos de evento disponíveis

Os Serviços de Mídia emitem os seguintes tipos de evento:

| Tipo de evento | DESCRIÇÃO |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| Estado das alterações de trabalho. |
| Microsoft.Media.LiveEventConnectionRejected | A tentativa de conexão do codificador foi rejeitada. |
| Microsoft.Media.LiveEventEncoderConnected | O codificador estabelece conexão com o evento ao vivo. |
| Microsoft.Media.LiveEventEncoderDisconnected | O codificador desconecta. |
| Microsoft.Media.LiveEventIncomingDataChunkDropped | O servidor de mídia descarta parte dos dados porque o tempo limite deles foi atingido ou eles têm um carimbo de data/hora de sobreposição (o carimbo de data/hora da nova parte de dados é anterior à hora de término da parte de dados anterior). |
| Microsoft.Media.LiveEventIncomingStreamReceived | O servidor de mídia recebe a primeira parte de dados para cada faixa no fluxo ou na conexão. |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | O servidor de mídia detecta que os fluxos de áudio e vídeo estão fora de sincronização. Use como um aviso porque a experiência do usuário não pode ser afetada. |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | O servidor de mídia detecta que um dos dois fluxos de vídeo provenientes do codificador externo está fora de sincronia. Use como um aviso porque a experiência do usuário não pode ser afetada. |
| Microsoft.Media.LiveEventIngestHeartbeat | Publicado a cada 20 segundos para cada faixa quando o evento ao vivo está em execução. Fornece resumo de integridade de ingestão. |
| Microsoft.Media.LiveEventTrackDiscontinuityDetected | O servidor de mídia detecta descontinuidade na faixa de entrada. |

Há duas categorias para os eventos **Ao Vivo**: eventos em nível de fluxo e eventos de nível de faixa. 

Os eventos em nível de fluxo são acionados por conexão ou fluxo. Cada evento tem um parâmetro `StreamId` que identifica a conexão ou o fluxo. Cada conexão ou fluxo tem uma ou mais faixas de tipos diferentes. Por exemplo, uma conexão de um codificador pode ter uma faixa de áudio e quatro faixas de vídeo. Os tipos de evento de fluxo são:

* LiveEventConnectionRejected
* LiveEventEncoderConnected
* LiveEventEncoderDisconnected

Os eventos em nível de faixa são acionados por faixa. Os tipos de evento de faixa são:

* LiveEventIncomingDataChunkDropped
* LiveEventIncomingStreamReceived
* LiveEventIncomingStreamsOutOfSync
* LiveEventIncomingVideoStreamsOutOfSync
* LiveEventIngestHeartbeat
* LiveEventTrackDiscontinuityDetected

## <a name="jobstatechange"></a>JobStateChange

O exemplo a seguir mostra o esquema do evento **JobStateChange**: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
    "eventType": "Microsoft.Media.JobStateChange",
    "eventTime": "2018-04-20T21:26:13.8978772",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | DESCRIÇÃO |
| -------- | ---- | ----------- |
| previousState | string | O estado do trabalho antes do evento. |
| state | string | O novo estado do trabalho que está sendo notificado nesse evento. Por exemplo, "Na fila: o trabalho está aguardando recursos" ou "Agendado: o trabalho está pronto para começar".|

Quando o estado do Trabalho puder ser um deste valores: *Na fila*, *Agendado*, *Processando*, *Concluído*, *Erro*, *Cancelado*, *Cancelando*

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

O exemplo a seguir mostra o esquema do evento **LiveEventConnectionRejected**: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventConnectionRejected",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "b303db59-d5c1-47eb-927a-3650875fded1",
    "data": { 
      "StreamId":"Mystream1",
      "IngestUrl": "http://abc.ingest.isml",
      "EncoderIp": "118.238.251.xxx",
      "EncoderPort": 52859,
      "ResultCode": "MPE_INGEST_CODEC_NOT_SUPPORTED"
    },
    "dataVersion": "1.0"
  }
]
```

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | DESCRIÇÃO |
| -------- | ---- | ----------- |
| StreamId | string | Identificador do fluxo ou da conexão. O codificador ou o cliente é responsável por adicionar essa ID na URL de ingestão. |  
| IngestUrl | string | URL de ingestão fornecida pelo evento ao vivo. |  
| EncoderIp | string | IP do codificador. |
| EncoderPort | string | Porta do codificador de onde vem esse fluxo. |
| ResultCode | string | O motivo pelo qual a conexão foi rejeitada. Os códigos de resultado estão listadas na tabela a seguir. |

Os códigos de resultado são:

| Código de resultado | DESCRIÇÃO |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | URL de ingestão incorreta |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | O IP do codificador não está presente na lista de permissões de IP configurada |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | O codificador não enviou metadados sobre o fluxo. |
| MPE_INGEST_CODEC_NOT_SUPPORTED | O codec especificado não é suportado. |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED | Recebido um fragmento antes do recebimento e cabeçalho para esse fluxo. |
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED | O número de qualidades especificado excede o limite máximo permitido. |
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED | A taxa de bits agregada excede o limite máximo permitido. |
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | O carimbo de data/hora para FLVTag de áudio ou vídeo é inválido a partir do codificador RTMP. |

## <a name="liveeventencoderconnected"></a>LiveEventEncoderConnected

O exemplo a seguir mostra o esquema do evento **LiveEventEncoderConnected**: 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderConnected",
    "eventTime": "2018-08-07T23:08:09.1710643",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | DESCRIÇÃO |
| -------- | ---- | ----------- |
| StreamId | string | Identificador do fluxo ou da conexão. O codificador ou o cliente é responsável por fornecer essa ID na URL de ingestão. |
| IngestUrl | string | URL de ingestão fornecida pelo evento ao vivo. |
| EncoderIp | string | IP do codificador. |
| EncoderPort | string | Porta do codificador de onde vem esse fluxo. |

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

O exemplo a seguir mostra o esquema do evento **LiveEventEncoderDisconnected**: 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderDisconnected",
    "eventTime": "2018-08-07T23:08:09.1710872",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "resultCode": "S_OK"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | DESCRIÇÃO |
| -------- | ---- | ----------- |
| StreamId | string | Identificador do fluxo ou da conexão. O codificador ou o cliente é responsável por adicionar essa ID na URL de ingestão. |  
| IngestUrl | string | URL de ingestão fornecida pelo evento ao vivo. |  
| EncoderIp | string | IP do codificador. |
| EncoderPort | string | Porta do codificador de onde vem esse fluxo. |
| ResultCode | string | O motivo para a desconexão do codificador. Isso se deve à desconexão normal ou a um erro. Os códigos de resultado estão listadas na tabela a seguir. |

Os códigos de resultado de erro são:

| Código de resultado | DESCRIÇÃO |
| ----------- | ----------- |
| MPE_RTMP_SESSION_IDLE_TIMEOUT | A sessão RTMP atingiu o tempo limite depois de ficar ociosa por um limite de tempo permitido. |
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | O carimbo de data/hora para FLVTag de áudio ou vídeo é inválido a partir do codificador RTMP. |
| MPE_CAPACITY_LIMIT_REACHED | Envio muito rápido de dados pelo codificador. |
| Códigos de Erro Desconhecidos | Esses códigos de erro podem variar de erro de memória para entradas duplicadas no mapa de hash. |

Os códigos de resultado de desconexão normal são:

| Código de resultado | DESCRIÇÃO |
| ----------- | ----------- |
| S_OK | Codificador desconectado com êxito. |
| MPE_CLIENT_TERMINATED_SESSION | Codificador desconectado (RTMP). |
| MPE_CLIENT_DISCONNECTED | Codificador desconectado (FMP4). |
| MPI_REST_API_CHANNEL_RESET | O comando de redefinição de canal é recebido. |
| MPI_REST_API_CHANNEL_STOP | Comando de parada de canal recebido. |
| MPI_REST_API_CHANNEL_STOP | Canal passando por manutenção. |
| MPI_STREAM_HIT_EOF | O fluxo de EOF é enviado pelo codificador. |

## <a name="liveeventincomingdatachunkdropped"></a>LiveEventIncomingDataChunkDropped

O exemplo a seguir mostra o esquema do evento **LiveEventIncomingDataChunkDropped**: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingDataChunkDropped",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "03da9c10-fde7-48e1-80d8-49936f2c3e7d",
    "data": { 
      "TrackType": "Video",
      "TrackName": "Video",
      "Bitrate": 300000,
      "Timestamp": 36656620000,
      "Timescale": 10000000,
      "ResultCode": "FragmentDrop_OverlapTimestamp"
    },
    "dataVersion": "1.0"
  }
]
```

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | DESCRIÇÃO |
| -------- | ---- | ----------- |
| TrackType | string | Tipo de faixa (Áudio/Vídeo). |
| TrackName | string | Nome da faixa. |
| Bitrate | inteiro | Taxa de bits da faixa. |
| Timestamp | string | O carimbo de data/hora da parte de dados é descartado. |
| Escala de tempo | string | Escala de tempo do carimbo de data/hora. |
| ResultCode | string | Motivo do descarte da parte de dados. **FragmentDrop_OverlapTimestamp** ou **FragmentDrop_NonIncreasingTimestamp**. |

## <a name="liveeventincomingstreamreceived"></a>LiveEventIncomingStreamReceived

O exemplo a seguir mostra o esquema do evento **LiveEventIncomingStreamReceived**: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamReceived",
    "eventTime": "2018-08-07T23:08:10.5069288Z",
    "id": "7f939a08-320c-47e7-8250-43dcfc04ab4d",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml/Streams(15864-stream0)15864-stream0",
      "trackType": "video",
      "trackName": "video",
      "bitrate": 2962000,
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "timestamp": "15336831655032322",
      "duration": "20000000",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | DESCRIÇÃO |
| -------- | ---- | ----------- |
| TrackType | string | Tipo de faixa (Áudio/Vídeo). |
| TrackName | string | Nome da faixa (fornecido pelo codificador ou, no caso de RTMP, o servidor gera no formato *TrackType_Bitrate*). |
| Bitrate | inteiro | Taxa de bits da faixa. |
| IngestUrl | string | URL de ingestão fornecida pelo evento ao vivo. |
| EncoderIp | string  | IP do codificador. |
| EncoderPort | string | Porta do codificador de onde vem esse fluxo. |
| Timestamp | string | O primeiro carimbo de data/hora da parte de dados recebida. |
| Escala de tempo | string | Escala de tempo em que o carimbo de data/hora é representado. |

## <a name="liveeventincomingstreamsoutofsync"></a>LiveEventIncomingStreamsOutOfSync

O exemplo a seguir mostra o esquema do evento **LiveEventIncomingStreamsOutOfSync**: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamsOutOfSync",
    "eventTime": "2018-08-10T02:26:20.6269183Z",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "minLastTimestamp": "319996",
      "typeOfStreamWithMinLastTimestamp": "Audio",
      "maxLastTimestamp": "366000",
      "typeOfStreamWithMaxLastTimestamp": "Video"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | DESCRIÇÃO |
| -------- | ---- | ----------- |
| MinLastTimestamp | string | Mínimo dos últimos carimbos de data/hora entre todas as faixas (áudio ou vídeo). |
| TypeOfTrackWithMinLastTimestamp | string | Tipo de faixa (áudio ou vídeo) com o último carimbo de data/hora mínimo. |
| MaxLastTimestamp | string | Máximo de todos os carimbos de data/hora entre todas as faixas (áudio ou vídeo). |
| TypeOfTrackWithMaxLastTimestamp | string | Tipo de faixa (áudio ou vídeo) com o último carimbo de data/hora máximo. |

## <a name="liveeventincomingvideostreamsoutofsync"></a>LiveEventIncomingVideoStreamsOutOfSync

O exemplo a seguir mostra o esquema do evento **LiveEventIncomingVideoStreamsOutOfSync**: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/LiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "6dd4d862-d442-40a0-b9f3-fc14bcf6d750",
    "data": {
      "FirstTimestamp": "2162058216",
      "FirstDuration": "2000",
      "SecondTimestamp": "2162057216",
      "SecondDuration": "2000"
    },
    "dataVersion": "1.0"
  }
]
```

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | DESCRIÇÃO |
| -------- | ---- | ----------- |
| FirstTimestamp | string | O carimbo de data/hora recebido para uma das faixas/níveis de qualidade do vídeo de tipo. |
| FirstDuration | string | Duração da parte de dados com primeiro carimbo de data/hora. |
| SecondTimestamp | string  | O carimbo de data/hora recebido para algum outro nível de qualidade/faixa do vídeo de tipo. |
| SecondDuration | string | Duração da parte de dados com segundo carimbo de data/hora. |

## <a name="liveeventingestheartbeat"></a>LiveEventIngestHeartbeat

O exemplo a seguir mostra o esquema do evento **LiveEventIngestHeartbeat**: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIngestHeartbeat",
    "eventTime": "2018-08-07T23:17:57.4610506",
    "id": "7f450938-491f-41e1-b06f-c6cd3965d786",
    "data": {
      "trackType": "audio",
      "trackName": "audio",
      "bitrate": 160000,
      "incomingBitrate": 155903,
      "lastTimestamp": "15336837535253637",
      "timescale": "10000000",
      "overlapCount": 0,
      "discontinuityCount": 0,
      "nonincreasingCount": 0,
      "unexpectedBitrate": false,
      "state": "Running",
      "healthy": true
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | DESCRIÇÃO |
| -------- | ---- | ----------- |
| TrackType | string | Tipo de faixa (Áudio/Vídeo). |
| TrackName | string | Nome da faixa (fornecido pelo codificador ou, no caso de RTMP, o servidor gera no formato *TrackType_Bitrate*). |
| Bitrate | inteiro | Taxa de bits da faixa. |
| IncomingBitrate | inteiro | Taxa de bits calculada com base nas partes de dados provenientes do codificador. |
| LastTimestamp | string | Carimbo de data/hora mais recente recebido para uma faixa nos últimos 20 segundos. |
| Escala de tempo | string | Escala de tempo na qual os carimbos de data/hora são expressos. |
| OverlapCount | inteiro | O número de partes de dados que sobrepôs os carimbos de data/hora nos últimos 20 segundos. |
| DiscontinuityCount | inteiro | Número do descontinuidades observadas nos últimos 20 segundos. |
| NonIncreasingCount | inteiro | Número de partes de dados com os carimbos de data/hora no passado recebidas nos últimos 20 segundos. |
| UnexpectedBitrate | bool | Se as taxas de bits esperadas e reais apresentarem diferença superior ao limite permitido nos últimos 20 segundos. É true se e somente se, IncomingBitrate >= 2* taxa de bits OR IncomingBitrate <= taxa de bits/2 OR IncomingBitrate = 0. |
| Estado | string | Estado do evento ao vivo. |
| Healthy | bool | Indica se a ingestão está íntegra com base nas contagens e nos sinalizadores. Healthy será true se OverlapCount = 0 && DiscontinuityCount = 0 && NonIncreasingCount = 0 && UnexpectedBitrate = false. |

## <a name="liveeventtrackdiscontinuitydetected"></a>LiveEventTrackDiscontinuityDetected

O exemplo a seguir mostra o esquema do evento **LiveEventTrackDiscontinuityDetected**: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventTrackDiscontinuityDetected",
    "eventTime": "2018-08-07T23:18:06.1270405Z",
    "id": "5f4c510d-5be7-4bef-baf0-64b828be9c9b",
    "data": {
      "trackName": "video",
      "previousTimestamp": "15336837615032322",
      "trackType": "video",
      "bitrate": 2962000,
      "newTimestamp": "15336837619774273",
      "discontinuityGap": "575284",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | DESCRIÇÃO |
| -------- | ---- | ----------- |
| TrackType | string | Tipo de faixa (Áudio/Vídeo). |
| TrackName | string | Nome da faixa (fornecido pelo codificador ou, no caso de RTMP, o servidor gera no formato *TrackType_Bitrate*). |
| Bitrate | inteiro | Taxa de bits da faixa. |
| PreviousTimestamp | string | Carimbo de data/hora do fragmento anterior. |
| NewTimestamp | string | Carimbo de data/hora do fragmento atual. |
| DiscontinuityGap | string | Lacuna entre os dois carimbos de data/hora acima. |
| Escala de tempo | string | Escala de tempo na qual o carimbo de data/hora e a lacuna de descontinuidade são representados. |

## <a name="common-event-properties"></a>Propriedades comuns de evento

Um evento tem os seguintes dados de nível superior:

| Propriedade | Tipo | DESCRIÇÃO |
| -------- | ---- | ----------- |
| topic | string | O tópico EventGrid. Essa propriedade tem a ID de recurso para a conta de Serviços de Mídia. |
| subject | string | O caminho do recurso para o canal dos Serviços de Mídia sob a conta de Serviços de Mídia. A concatenação de tópico e assunto dê a você o recurso de ID para o trabalho. |
| eventType | string | Um dos tipos de evento registrados para a origem do evento. Por exemplo, "Microsoft.Media.JobStateChange". |
| eventTime | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| ID | string | Identificador exclusivo do evento. |
| data | objeto | Dados de eventos dos Serviços de Mídia. |
| dataVersion | string | A versão do esquema do objeto de dados. O fornecedor define a versão do esquema. |
| metadataVersion | string | A versão do esquema do metadados de evento. Grade de Eventos define o esquema de propriedades de nível superior. Grade de Eventos fornece esse valor. |

## <a name="next-steps"></a>Próximas etapas

[Registre-se para eventos de alteração de estado do trabalho](job-state-events-cli-how-to.md)