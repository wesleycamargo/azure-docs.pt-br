---
title: "Introdução às conexões híbridas de retransmissão | Microsoft Docs"
description: "Como escrever um aplicativo de console em C# para Conexões Híbridas"
services: service-bus
documentationcenter: .net
author: jtaubensee
manager: timlt
editor: 
ms.assetid: d1386900-b942-4abf-acfc-38d2ef826253
ms.service: service-bus
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/28/2016
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 92d7935596ab1dede6dc1d613cb635c32d52e3ab


---
# <a name="get-started-with-relay-hybrid-connections"></a>Introdução às Conexões Híbridas de Retransmissão
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

## <a name="what-will-be-accomplished"></a>O que será realizado
Como as Conexões híbridas exigem um componente de cliente e de servidor, criaremos dois aplicativos de console neste tutorial. Siga estas etapas:

1. Criar um namespace de retransmissão usando o Portal do Azure.
2. Criar uma Conexão Híbrida usando o Portal do Azure.
3. Escrever um aplicativo de console do servidor para enviar mensagens.
4. Escrever um aplicativo de console de cliente para enviar mensagens.

## <a name="prerequisites"></a>Pré-requisitos
1. [Visual Studio 2013 ou Visual Studio 2015](http://www.visualstudio.com). Os exemplos neste tutorial usam o Visual Studio 2015.
2. Uma assinatura do Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Criar um namespace usando o portal do Azure
Se você já tiver um namespace de retransmissão criado, vá até a seção [Criar uma Conexão Híbrida usando o Portal do Azure](#2-create-a-hybrid-connection-using-the-azure-portal).

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2. Criar uma Conexão Híbrida usando o Portal do Azure
Se você já tiver uma Conexão Híbrida criada, vá até a seção [Criar um aplicativo de servidor](#3-create-a-server-application-listener).

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. Criar um aplicativo de servidor (escuta)
Para escutar e receber mensagens da retransmissão, escreveremos um aplicativo de console em C# usando o Visual Studio.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. Criar um aplicativo de cliente (remetente)
Para enviar mensagens para a retransmissão, escreveremos um aplicativo de console em C# usando o Visual Studio.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. Executar os aplicativos
1. Execute o aplicativo de servidor.
2. Execute o aplicativo de cliente e insira algum texto.
3. Certifique-se de que o console do aplicativo de servidor exiba o texto inserido no aplicativo de cliente.

![running-applications](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

Parabéns, você criou um aplicativo de Conexões Híbridas completo.

## <a name="next-steps"></a>Próximas etapas:
* [Perguntas frequentes sobre retransmissão](relay-faq.md)
* [Criar um namespace](relay-create-namespace-portal.md)
* [Introdução ao Node](relay-hybrid-connections-node-get-started.md)




<!--HONumber=Nov16_HO2-->


