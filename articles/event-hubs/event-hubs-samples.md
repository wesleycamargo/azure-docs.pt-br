---
title: Exemplos de Hubs de Eventos do Azure | Microsoft Docs
description: Exemplos de Hubs de Eventos
services: event-hubs
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: 5b02054546ebf25766be6bb2837aebc502b67a67
ms.openlocfilehash: 9c50b7caefe06bceac8035a0d274e13a96547fea

---

# <a name="event-hubs-samples"></a>Exemplos de Hubs de Eventos 

Os exemplos de Hubs de Eventos demonstram os recursos principais dos [Hubs de Eventos Azure](/azure/event-hubs/). Este tópico categoriza e descreve os exemplos disponíveis, com links para cada um.

No momento da redação deste artigo, exemplos de Hubs de Eventos estão localizados em vários lugares diferentes:

- [Exemplos de código do desenvolvedor do MSDN](https://code.msdn.microsoft.com/site/search?query=event%20hubs&f%5B0%5D.Value=event%20hubs&f%5B0%5D.Type=SearchText&ac=5)
- [GitHub](https://github.com/Azure/azure-event-hubs-dotnet/tree/master/samples)

Para saber mais sobre as diferentes versões do .NET Framework, veja [estruturas e destinos](/dotnet/articles/standard/frameworks).

Mais exemplos será adicionado ao longo do tempo, então verifique novamente com frequência para atualizações.

## <a name="net-standard"></a>.NET Standard

Os exemplos a seguir demonstram como enviar e receber eventos usando o [cliente dos Hubs de Eventos](https://github.com/Azure/azure-event-hubs-dotnet/blob/master/readme.md) para a [biblioteca do .NET Standard](/dotnet/articles/standard/library).

### <a name="send-events"></a>Enviar eventos 

O exemplo de [Introdução ao envio](https://github.com/Azure/azure-event-hubs-dotnet/tree/master/samples/SampleSender) mostra como escrever um aplicativo do console .NET Core que envia eventos para um Hub de Eventos.

### <a name="receive-events"></a>Receber eventos 

O exemplo de [Introdução ao recebimento com o Host do Processador de Eventos](https://github.com/Azure/azure-event-hubs-dotnet/tree/master/samples/SampleEphReceiver) é um aplicativo de console .NET Core que recebe mensagens de um Hub de Eventos usando o [Host do Processador de Eventos](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/). 

## <a name="net-framework"></a>.NET Framework   

Estes exemplos demonstram vários outros recursos dos Hubs de Eventos do Azure, direcionados para a [biblioteca do .NET Framework](https://msdn.microsoft.com/library/w0x726c2.aspx).
 
### <a name="notify-users-of-events-received"></a>Notificar os usuários de eventos recebidos

O [AppToNotifyUsers](https://github.com/Azure-Samples/event-hubs-dotnet-user-notifications) exemplo notifica os usuários de dados recebidos de sensores ou outros sistemas.

### <a name="get-started-with-event-hubs"></a>Introdução aos Hubs de Eventos 

O exemplo de [Introdução aos Hubs de Eventos](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097) demonstra os recursos básicos de Hubs de Eventos, por exemplo, como criar um Hub de Eventos, enviar eventos para um Hub de Eventos e consumir eventos usando o [Host do Processador de Eventos](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/).

### <a name="scale-out-event-processing"></a>Processamento de eventos de escala horizontal 

O exemplo de [Expandir o processamento de eventos](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3) demonstra como usar o [Host do Processador de Eventos](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) para distribuir a carga de trabalho de consumo de fluxo de Hubs de Eventos. Ele mostra como implementar a **EventProcessor** e **EventProcessorFactory** objetos para gerenciar o fluxo de eventos. 

###  <a name="pull-data-from-sql-into-an-event-hub"></a>Extrair dados do SQL para um Hub de Eventos

O exemplo de [Extração de dados do SQL](https://github.com/Azure-Samples/event-hubs-dotnet-import-from-sql) mostra como extrair dados de uma tabela SQL e enviá-los para um Hub de Eventos a ser usado como uma entrada em aplicativos analíticos de downstream.

### <a name="pull-web-data-into-an-event-hub"></a>Extrair dados da Web para um Hub de Eventos 

O exemplo de [Importar dados da Web](https://github.com/Azure-Samples/event-hubs-dotnet-importfromweb) mostra como extrair dados de feeds públicos (como feed de informações do departamento de transporte tráfego) e enviá-los para um Hub de Eventos.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as versões do .NET Framework visitando os links a seguir:

- [Estruturas e destinos](/dotnet/articles/standard/frameworks)
- [.NET Framework 4.6 e 4.5](https://msdn.microsoft.com/library/w0x726c2.aspx)

Você pode saber mais sobre os Hubs de Eventos nestes artigos:

- [Visão Geral dos Hubs de Eventos](event-hubs-what-is-event-hubs.md)
- [Criar um Hub de Eventos](event-hubs-create.md)
- [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)


<!--HONumber=Feb17_HO1-->


