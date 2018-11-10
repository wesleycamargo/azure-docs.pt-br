---
title: Lógica de repetição no SDK dos Serviços de Mídia para .NET | Microsoft Docs
description: Este tópico fornece uma visão geral da lógica de repetição no SDK dos Serviços de Mídia para .NET.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 527b61a6-c862-4bd8-bcbc-b9aea1ffdee3
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2018
ms.author: juliako
ms.openlocfilehash: a5171484bb4377e0f9cd84dc0a517f4ea84123e7
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51228310"
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
| WebException<br/>Para saber mais, consulte a seção [Códigos de status WebException](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus). |SIM |sim |sim |SIM |
| DataServiceClientException<br/> Para saber mais, consulte [Códigos de status de erro HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Não  |sim |sim |SIM |
| DataServiceQueryException<br/> Para saber mais, consulte [Códigos de status de erro HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Não  |sim |sim |SIM |
| DataServiceRequestException<br/> Para saber mais, consulte [Códigos de status de erro HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Não  |sim |sim |SIM |
| DataServiceTransportException |Não  |Não  |sim |SIM |
| TimeoutException |SIM |sim |sim |Não  |
| SocketException |SIM |sim |sim |SIM |
| StorageException |Não  |Sim |Não |Não  |
| IOException |Não  |Sim |Não |Não  |

### <a name="WebExceptionStatus"></a> Códigos de status WebException
A tabela a seguir mostra para quais códigos de erro WebException a lógica de repetição é implementada. A enumeração [WebExceptionStatus](https://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx) define os códigos de status.  

| Status | Solicitação da Web | Armazenamento | Consultar | SaveChanges |
| --- | --- | --- | --- | --- |
| ConnectFailure |SIM |sim |sim |SIM |
| NameResolutionFailure |SIM |sim |sim |SIM |
| ProxyNameResolutionFailure |SIM |sim |sim |SIM |
| SendFailure |SIM |sim |sim |SIM |
| PipelineFailure |SIM |sim |sim |Não  |
| ConnectionClosed |SIM |sim |sim |Não  |
| KeepAliveFailure |SIM |sim |sim |Não  |
| UnknownError |SIM |sim |sim |Não  |
| ReceiveFailure |SIM |sim |sim |Não  |
| RequestCanceled |SIM |sim |sim |Não  |
| Tempo limite |SIM |sim |sim |Não  |
| ProtocolError <br/>A repetição em ProtocolError é controlada pela manipulação do código de status HTTP. Para saber mais, consulte [Códigos de status de erro HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |SIM |sim |sim |SIM |

### <a name="HTTPStatusCode"></a> Códigos de status de erro HTTP
Quando as operações de Consulta e SaveChanges lançam DataServiceClientException, DataServiceQueryException ou DataServiceQueryException, o código de status de erro HTTP retorna na propriedade StatusCode.  A tabela a seguir mostra para quais códigos de erro a lógica de repetição é implementada.  

| Status | Solicitação da Web | Armazenamento | Consultar | SaveChanges |
| --- | --- | --- | --- | --- |
| 401 |Não  |Sim |Não |Não  |
| 403 |Não  |SIM<br/>Tratar repetições sem esperas longas. |Não  |Não  |
| 408 |SIM |sim |sim |SIM |
| 429 |SIM |sim |sim |SIM |
| 500 |SIM |sim |sim |Não  |
| 502 |SIM |sim |sim |Não  |
| 503 |SIM |sim |sim |SIM |
| 504 |SIM |sim |sim |Não  |

Se você quiser dar uma olhada na implementação real do SDK dos Serviços de Mídia para .NET, consulte [azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

