---
title: O que é Azure SignalR | Microsoft Docs
description: Uma visão geral do Serviço Azure SignalR.
services: signalr
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.service: signalr
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 04/17/2018
ms.author: wesmc
ms.openlocfilehash: bc144fb1d7db9251871e7e181b012417a32de7e6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33868102"
---
# <a name="what-is-azure-signalr-service"></a>O que é o Serviço Azure SignalR

O Microsoft Azure SignalR Service está atualmente, o serviço está na [Visualização Pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O Serviço Azure SignalR é um serviço do Azure com base no [ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction). O ASP.NET Core SignalR é uma [biblioteca de software livre](https://github.com/aspnet/signalr) que simplifica o processo de adição da funcionalidade da Web em tempo real aos aplicativos por HTTP. Essa funcionalidade em tempo real permite que o servidor Web envie atualizações de conteúdo por push para os clientes conectados. Como resultado, os clientes são atualizados sem a necessidade de sondar o servidor, ou enviar novas solicitações HTTP para atualizações.

Este artigo fornece uma visão geral do Serviço Azure SignalR. Se quiser, comece com o [Início rápido do ASP.NET Core](signalr-quickstart-dotnet-core.md).

## <a name="what-is-signalr-service-used-for"></a>Qual é a função do Serviço SignalR? 

Há muitos tipos de aplicativos que exigem atualizações de conteúdo em tempo real. Os exemplos de tipo de aplicativo a seguir são bons candidatos ao uso do Serviço Azure SignalR:

* Aplicativos que exigem atualizações de alta frequência do servidor. Por exemplo, jogos, redes sociais, votação, leilão, mapas e aplicativos de GPS.
* Painéis e aplicativos de monitoramento. Por exemplo, painéis de empresa, atualizações de vendas instantâneas ou alertas de viagem.
* Aplicativos de colaboração. Aplicativos de quadro de comunicação e software de reunião de equipe são exemplos de aplicativos de colaboração.
* Aplicativos que exigem notificações. Redes sociais, email, chat, jogos, alertas de viagem e muitos outros aplicativos usam notificações.

Internamente, o SignalR é uma abstração sobre várias técnicas usadas para criar aplicativos Web em tempo real. [WebSockets](https://wikipedia.org/wiki/WebSocket) é o transporte ideal, mas outras técnicas, como [SSE (Eventos enviados pelo servidor)](https://wikipedia.org/wiki/Server-sent_events) e Sondagem Longa são usadas quando outras opções não estão disponíveis. O SignalR detecta e inicializa automaticamente o transporte apropriado com base nos recursos com suporte no cliente e no servidor.

## <a name="developing-signalr-apps"></a>Desenvolvendo aplicativos SignalR

Atualmente, há duas versões do SignalR que podem ser usadas com seus aplicativos Web: SignalR para ASP.NET e ASP.NET Core SignalR, que é a versão mais recente. O Serviço Azure SignalR é um serviço gerenciado do Azure baseado no ASP.NET Core SignalR. 

O ASP.NET Core SignalR é uma reformulação da versão anterior. Como resultado, o ASP.NET Core SignalR não é compatível com a versão anterior do SignalR. As APIs e os comportamentos são diferentes. O SDK do ASP.NET Core SignalR é o .NET Standard, então você ainda pode usá-lo com o .NET Framework. No entanto, você deve usar as novas APIs em vez das antigas. Se você estiver usando o SignalR e quiser mudar para o ASP.NET Core SignalR, ou para o Serviço Azure SignalR, será necessário alterar o código para lidar com as diferenças nas APIs.

Com o Serviço Azure SignalR, o componente do lado do servidor do ASP.NET Core SignalR é hospedado no Azure. No entanto, como a tecnologia tem base no ASP.NET Core, você tem a capacidade de executar seu aplicativo Web real em várias plataformas (Windows, Linux e MacOS) durante a hospedagem com o [Serviço de Aplicativo Azure](../app-service/app-service-web-overview.md), [IIS](https://docs.microsoft.com/aspnet/core/host-and-deploy/iis/index), [Nginx](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-nginx), [Apache](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-apache), [Docker](https://docs.microsoft.com/aspnet/core/host-and-deploy/docker/index). Você também pode usar a auto-hospedagem em seu próprio processo.

Se os objetivos de seu aplicativo incluírem: suporte à funcionalidade mais recente para atualização de clientes da Web com atualizações de conteúdo em tempo real, execução em várias plataformas (Azure, Windows, Linux e MacOS) e hospedagem em ambientes diferentes, a melhor escolha seria aproveitar o Serviço Azure SignalR.


## <a name="why-not-deploy-signalr-myself"></a>Por que eu mesmo não posso implantar o SignalR?

Ainda é uma abordagem válida implantar seu próprio aplicativo Web do Azure com suporte para o ASP.NET Core SignalR como um componente de back-end para seu aplicativo Web geral.

Um dos principais motivos para usar o Serviço Azure SignalR é a simplicidade. Com o Serviço Azure SignalR, você não precisa lidar com problemas de desempenho, escalabilidade, disponibilidade. Esses problemas são tratados para você com um contrato de nível de serviço de 99,9%.

Além disso, os WebSockets normalmente são a técnica preferencial para dar suporte a atualizações de conteúdo em tempo real. No entanto, o balanceamento de carga de uma grande quantidade de conexões WebSocket persistentes se torna um problema complicado para resolver ao dimensionar. As soluções comuns aproveitam: o balanceamento de carga de DNS, balanceadores de carga de hardware e balanceamento de carga de software. O Serviço Azure SignalR trata esse problema para você.

Outro motivo pode ser a falta de necessidade de hospedar um aplicativo Web. A lógica do seu aplicativo Web pode aproveitar a [Computação sem servidor](https://azure.microsoft.com/overview/serverless-computing/). Por exemplo, talvez seu código seja hospedado e executado apenas sob demanda, com gatilhos do [Azure Functions](https://docs.microsoft.com/azure/azure-functions/). Esse cenário pode ser complicado, pois seu código só é executado sob demanda e não mantém conexões longas com clientes. O Serviço Azure SignalR pode lidar com essa situação, uma vez que o serviço já gerencia conexões para você.

## <a name="how-does-it-scale"></a>Como é possível dimensioná-lo?

É comum dimensionar o SignalR com SQL Server, Barramento de Serviço do Azure ou Cache Redis. O Serviço Azure SignalR trata do dimensionamento para você. O desempenho e o custo dessas abordagens são parecidos, sem a complexidade de lidar com os outros serviços. Basta atualizar a contagem de unidade para o seu serviço. Cada unidade de serviço oferece suporte a até 1000 conexões de cliente.

## <a name="next-steps"></a>Próximas etapas
* [Início Rápido: Criar uma sala de chat com o Azure SignalR](signalr-quickstart-dotnet-core.md)  
  

