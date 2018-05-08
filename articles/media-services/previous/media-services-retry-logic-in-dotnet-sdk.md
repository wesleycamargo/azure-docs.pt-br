---
title: Lógica de repetição no SDK dos Serviços de Mídia para .NET | Microsoft Docs
description: Este tópico fornece uma visão geral da lógica de repetição no SDK dos Serviços de Mídia para .NET.
author: Juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 527b61a6-c862-4bd8-bcbc-b9aea1ffdee3
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: juliako
ms.openlocfilehash: 34125712c59938b3a74e7cdc150f3f16b694b92f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Lógica de repetição no SDK de Serviços de Mídia para .NET
Ao trabalhar com os serviços do Microsoft Azure, algumas falhas transitórias podem ocorrer. Se alguma ocorrer, na maioria dos casos, depois de algumas tentativas a operação é bem-sucedida. O SDK dos Serviços de Mídia para .NET implementa a lógica de repetição para lidar com falhas transitórias associadas a exceções e erros causados por solicitações da Web, execução de consultas, gravação de alterações e operações de armazenamento.  Por padrão, o SDK dos Serviços de Mídia para .NET executa quatro tentativas antes de lançar novamente a exceção para o seu aplicativo. Assim, o código em seu aplicativo deve tratar essa exceção corretamente.  

 Veja a seguir uma breve orientação sobre as políticas de Solicitação da Web, Armazenamento, Consulta e SaveChanges:  

* A política de Armazenamento é usada para operações de armazenamento de blobs (uploads ou download de arquivos de ativo).  
* A política de Solicitação da Web é usada para solicitações genéricas da Web (por exemplo, para obter um token de autenticação e resolver o ponto de extremidade do cluster de usuários).  
* A política de Consulta é usada para consultar entidades do REST (por exemplo, mediaContext.Assets.Where(...)).  
* A política SaveChanges é usada para fazer qualquer coisa que altere os dados dentro do serviço (por exemplo, criar uma entidade atualizando uma entidade, chamar uma função de serviço para uma operação).  
  
  Este tópico lista os tipos de exceção e os códigos de erro tratados pelo SDK dos Serviços de Mídia para .NET.  

## <a name="exception-types"></a>Tipos de exceção
A tabela a seguir descreve as exceções que o SDK dos Serviços de Mídia para .NET trata ou não para algumas operações que podem causar falhas transitórias.  

| Exceção | Solicitação da Web | Armazenamento | Consultar | SaveChanges |
| --- | --- | --- | --- | --- |
| WebException<br/>Para saber mais, consulte a seção [Códigos de status WebException](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus). |sim |sim |sim |sim |
| DataServiceClientException<br/> Para saber mais, consulte [Códigos de status de erro HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Não  |sim |sim |sim |
| DataServiceQueryException<br/> Para saber mais, consulte [Códigos de status de erro HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Não  |sim |sim |sim |
| DataServiceRequestException<br/> Para saber mais, consulte [Códigos de status de erro HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Não  |sim |sim |sim |
| DataServiceTransportException |Não  |Não  |sim |sim |
| TimeoutException |sim |sim |sim |Não  |
| SocketException |sim |sim |sim |sim |
| StorageException |Não  |sim |Não |Não  |
| IOException |Não  |sim |Não |Não  |

### <a name="WebExceptionStatus"></a> Códigos de status WebException
A tabela a seguir mostra para quais códigos de erro WebException a lógica de repetição é implementada. A enumeração [WebExceptionStatus](http://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx) define os códigos de status.  

| Status | Solicitação da Web | Armazenamento | Consultar | SaveChanges |
| --- | --- | --- | --- | --- |
| ConnectFailure |sim |sim |sim |sim |
| NameResolutionFailure |sim |sim |sim |sim |
| ProxyNameResolutionFailure |sim |sim |sim |sim |
| SendFailure |sim |sim |sim |sim |
| PipelineFailure |sim |sim |sim |Não  |
| ConnectionClosed |sim |sim |sim |Não  |
| KeepAliveFailure |sim |sim |sim |Não  |
| UnknownError |sim |sim |sim |Não  |
| ReceiveFailure |sim |sim |sim |Não  |
| RequestCanceled |sim |sim |sim |Não  |
| Tempo limite |sim |sim |sim |Não  |
| ProtocolError <br/>A repetição em ProtocolError é controlada pela manipulação do código de status HTTP. Para saber mais, consulte [Códigos de status de erro HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |sim |sim |sim |sim |

### <a name="HTTPStatusCode"></a> Códigos de status de erro HTTP
Quando as operações de Consulta e SaveChanges lançam DataServiceClientException, DataServiceQueryException ou DataServiceQueryException, o código de status de erro HTTP retorna na propriedade StatusCode.  A tabela a seguir mostra para quais códigos de erro a lógica de repetição é implementada.  

| Status | Solicitação da Web | Armazenamento | Consultar | SaveChanges |
| --- | --- | --- | --- | --- |
| 401 |Não  |sim |Não |Não  |
| 403 |Não  |sim<br/>Tratar repetições sem esperas longas. |Não  |Não  |
| 408 |sim |sim |sim |sim |
| 429 |sim |sim |sim |sim |
| 500 |sim |sim |sim |Não  |
| 502 |sim |sim |sim |Não  |
| 503 |sim |sim |sim |sim |
| 504 |sim |sim |sim |Não  |

Se você quiser dar uma olhada na implementação real do SDK dos Serviços de Mídia para .NET, consulte [azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

