---
title: Introdução às Conexões Híbridas de Retransmissão do Azure de pedidos HTTP no .NET | Microsoft Docs
description: Escreva um aplicativo de console C# para Conexões Híbridas da Retransmissão do Azure de pedidos HTTP em .NET.
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: d1386900-b942-4abf-acfc-38d2ef826253
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 08/16/2018
ms.author: spelluru
ms.openlocfilehash: 4f7aa0b0c5142237f3b6fc66bb8593302c95f5af
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2018
ms.locfileid: "41924715"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-net"></a>Introdução às Conexões Híbridas de Retransmissão de pedidos HTTP no .NET
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Este tutorial fornece uma introdução às [Conexões Híbridas de Retransmissão do Azure](relay-what-is-it.md#hybrid-connections). Saiba como usar o Microsoft .NET para criar um aplicativo cliente que envia pedidos para um aplicativo de escuta correspondente. 

## <a name="what-will-be-accomplished"></a>O que será realizado
Conexões Híbridas requer um componente de cliente e um componente de servidor. Neste tutorial, você deve concluir estas etapas para criar dois aplicativos de console:

1. Criar um namespace de Retransmissão usando o Portal do Azure.
2. Crie uma conexão híbrida nesse namespace usando o Portal do Azure.
3. Escreva um aplicativo de console do servidor (ouvinte) para receber pedidos.
4. Escreva um aplicativo de console de cliente (remetente) para enviar pedidos.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes pré-requisitos:

* [Visual Studio 2015 ou posterior](http://www.visualstudio.com). Os exemplos neste tutorial usam o Visual Studio 2017.
* Uma assinatura do Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-by-using-the-azure-portal"></a>1. Criar um namespace usando o Portal do Azure
Se você já tiver um namespace de Retransmissão criado, vá até [Criar uma conexão híbrida usando o Portal do Azure](#2-create-a-hybrid-connection-using-the-azure-portal).

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-by-using-the-azure-portal"></a>2. Criar uma conexão híbrida usando o Portal do Azure
Se você já tiver criado uma conexão híbrida, vá até a seção [Criar um aplicativo para servidores](#3-create-a-server-application-listener).

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. Criar um aplicativo de servidor (escuta)
No Visual Studio, grave um aplicativo de console em C# para escutar e receber mensagens da retransmissão.

[!INCLUDE [relay-hybrid-connections-http-requests-dotnet-get-started-server](../../includes/relay-hybrid-connections-http-requests-dotnet-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. Criar um aplicativo de cliente (remetente)
No Visual Studio, grave um aplicativo de console em C# para enviar mensagens à retransmissão.

[!INCLUDE [relay-hybrid-connections-http-requests-dotnet-get-started-client](../../includes/relay-hybrid-connections-http-requests-dotnet-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. Executar os aplicativos
1. Execute o aplicativo de servidor.
2. Execute o aplicativo de cliente e insira algum texto.
3. Verifique se o console do aplicativo para servidores exibe o texto inserido no aplicativo cliente.

Parabéns, você criou um aplicativo de Conexões Híbridas de ponta a ponta!

## <a name="next-steps"></a>Próximas etapas

* [Perguntas frequentes sobre retransmissão](relay-faq.md)
* [Criar um namespace](relay-create-namespace-portal.md)
* [Introdução ao Node](relay-hybrid-connections-node-get-started.md)

