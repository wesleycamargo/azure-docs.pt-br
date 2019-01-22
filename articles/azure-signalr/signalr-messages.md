---
title: Mensagens e conexões no Azure SignalR
description: Uma visão geral dos principais conceitos em torno de mensagens e conexões no Serviço do Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: c2348df7a1a55584807a03216e294486ddadfc52
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352590"
---
# <a name="message-and-connection-in-azure-signalr-service"></a>Mensagens e conexões no Serviço do Azure SignalR

O Serviço do Azure SignalR tem o modelo de cobrança com base no número de conexões e no número de mensagens. O modo como as mensagens e as conexões são definidas e contadas para fins de cobrança é explicado abaixo.

## <a name="message-formats-supported"></a>Formatos de mensagem compatíveis

O Serviço do Azure SignalR dá suporte aos mesmos formatos aos quais o ASP.NET Core SignalR dá suporte: [JSON](https://www.json.org/) e [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)

## <a name="message-size"></a>Tamanho da mensagem

O Serviço do Azure SignalR não tem nenhum limite de tamanho de mensagem.

Na prática, uma mensagem grande é dividida em mensagens menores, com não mais do que 2 KB cada, que são transmitidas como mensagens separadas. Os SDKs manipulam a divisão e a montagem de mensagens. Não é necessário nenhum esforço por parte do desenvolvedor.

No entanto, uma mensagem grande tem um impacto negativo no desempenho do sistema de mensagens. Use tamanhos de mensagem menores sempre que possível e teste para escolher o tamanho ideal para cada cenário de caso de uso.

## <a name="how-to-count-messages-for-billing-purpose"></a>Como contar as mensagens para fins de cobrança?

Contamos apenas as mensagens de saída do Serviço do SignalR e ignoramos as mensagens de ping entre clientes e servidores.

Mensagens maiores do que 2 KB são contadas como várias mensagens de 2 KB cada. O gráfico de contagem de mensagens no portal do Azure é atualizado a cada 100 mensagens por hub.

Por exemplo, você tem três clientes e um servidor de aplicativos. Um cliente envia uma mensagem de 4 KB para permitir que o servidor difunda para todos os clientes. A contagem de mensagens é 8: Uma mensagem do serviço para o servidor de aplicativos, três mensagens do serviço para os clientes, com cada mensagem contada como duas mensagens de 2 KB.

A contagem de mensagens mostrada no portal do Azure ainda é 0, até que ela se acumule e ultrapasse 100.

## <a name="how-to-count-connections"></a>Como contar conexões?

Existem conexões de servidor e conexões de cliente. Por padrão, cada servidor de aplicativos tem cinco conexões por hub com o Serviço do SignalR e cada cliente tem uma conexão de cliente com o Serviço do SignalR.

A contagem de conexões mostrada no portal do Azure inclui conexões de servidor e conexões de cliente.

Por exemplo, você tem dois servidores de aplicativos e define cinco hubs em códigos. A contagem de conexão do servidor é 50: 2 servidores de aplicativos * 5 hubs * 5 conexões/hub.

O SignalR do ASP.NET calcula as conexões do servidor de outro modo. Ele tem um hub padrão, além dos hubs definidos pelo cliente. Cada servidor de aplicativos precisa de mais 5 conexões de servidor por padrão. A contagem de conexões do hub padrão se mantém consistente com outros hubs.

## <a name="how-to-count-inbound-traffic--outbound-traffic"></a>Como contar o Tráfego de Entrada/Tráfego de Saída

A entrada/saída é calculada da perspectiva do Serviço do SignalR. O tráfego é calculado em Bytes. Assim como a contagem de mensagens, o tráfego também tem sua taxa de amostragem. O gráfico de Entrada/Saída no portal do Azure é atualizado a cada 100 KB por hub.

## <a name="related-resources"></a>Recursos relacionados

- [Tipo de agregação no Azure Monitor](/azure/azure-monitor/platform/metrics-supported#microsoftsignalrservicesignalr )
- [Configuração do ASP.NET Core SignalR](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)