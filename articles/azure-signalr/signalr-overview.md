---
title: O que é Azure SignalR | Microsoft Docs
description: Uma visão geral do Serviço Azure SignalR.
services: signalr
documentationcenter: ''
author: sffamily
manager: cfowler
editor: ''
ms.service: signalr
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: a159833936ec4762213f063e235fa4f9237af95b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951093"
---
# <a name="what-is-azure-signalr-service"></a>O que é o Serviço Azure SignalR

O Azure SignalR Service simplifica o processo de adição da funcionalidade Web em tempo real aos aplicativos por HTTP. Essa funcionalidade em tempo real permite que o serviço envie por push atualizações de conteúdo a clientes conectados, como um aplicativo móvel ou Web de página única. Como resultado, os clientes são atualizados sem a necessidade de sondar o servidor, ou enviar novas solicitações HTTP para atualizações.

Este artigo fornece uma visão geral do Azure SignalR Service.

## <a name="what-is-azure-signalr-service-used-for"></a>Para que o Azure SignalR Service é usado? 

Há muitos tipos de aplicativos que exigem atualizações de conteúdo em tempo real. Os exemplos a seguir são bons exemplos para o uso do Azure SignalR Service:

* Aplicativos que exigem atualizações frequentes do servidor. Por exemplo, jogos, votação, leilão, mapas e aplicativos de GPS.
* Painéis e aplicativos de monitoramento. Por exemplo, painéis de empresa e atualizações de vendas instantâneas.
* Aplicativos de colaboração. Aplicativos de quadro de comunicação e software de reunião de equipe são exemplos de aplicativos de colaboração.
* Aplicativos que exigem notificações. Redes sociais, email, chat, jogos, alertas de viagem e muitos outros aplicativos usam notificações.

O SignalR fornece uma abstração sobre várias técnicas usadas para criar aplicativos Web em tempo real. [WebSockets](https://wikipedia.org/wiki/WebSocket) é o transporte ideal, mas outras técnicas, como [SSE (Eventos enviados pelo servidor)](https://wikipedia.org/wiki/Server-sent_events) e Sondagem Longa são usadas quando outras opções não estão disponíveis. O SignalR detecta e inicializa automaticamente o transporte apropriado com base nos recursos com suporte no cliente e no servidor.

Além disso, o SignalR fornece um modelo de programação para aplicativos em tempo real que permite que o servidor envie mensagens para todas as conexões ou a um subconjunto de conexões que pertencem a um usuário específico ou que foram colocadas em um grupo arbitrário.

## <a name="how-to-use-azure-signalr-service"></a>Como usar o Azure SignalR Service

Atualmente, há três maneiras de usar o Azure SignalR Service:

- **[Dimensionar um aplicativo SignalR do ASP.NET Core](signalr-overview-scale-aspnet-core.md)**  – integre o Azure SignalR Service com um aplicativo SignalR do ASP.NET Core para escalar horizontalmente centenas de milhares de conexões.
- **[Criar aplicativos em tempo real sem servidor](signalr-overview-azure-functions.md)** – use a integração do Azure Functions com o SignalR Service para criar aplicativos em tempo real sem servidor em linguagens como JavaScript, C# e Java.
- **[Enviar mensagens do servidor para clientes por meio da API REST](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)** – o Azure SignalR Service fornece a API REST para habilitar aplicativos para publicar mensagens a clientes conectados com o SignalR Service, em qualquer linguagem de programação compatível com REST.

