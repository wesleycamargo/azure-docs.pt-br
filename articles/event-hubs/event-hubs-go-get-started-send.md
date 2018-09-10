---
title: Enviar eventos para Hubs de Eventos do Azure usando Go | Microsoft Docs
description: Introdução ao envio de eventos para Hubs de Eventos usando o Go
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 07/23/2018
ms.author: shvija
ms.openlocfilehash: 40b3aa82c3e9e8ab9a30362c0a41998877655725
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005590"
---
# <a name="send-events-to-event-hubs-using-go"></a>Enviar eventos para os Hubs de Eventos usando Go

Os Hubs de Eventos do Azure são um sistema gerenciamento de inclusão altamente dimensionável que pode receber milhões de eventos por segundo, permitindo que um aplicativo processe e analise grandes quantidades de dados produzidos por dispositivos conectados e outros sistemas. Depois de coletados em um hub de eventos, você pode receber e manipular eventos usando manipuladores em processo ou por encaminhamento para outros sistemas de análise.

Para saber mais sobre os Hubs de Eventos, consulte a [Visão geral dos Hubs de Eventos][Event Hubs overview].

Este tutorial descreve como enviar eventos para um hub de eventos de um aplicativo escrito em Ir. Para receber eventos, use o **Vá eph** (Host do processador de eventos) conforme descrito em [artigo Receive correspondente](event-hubs-go-get-started-receive-eph.md).

O código deste tutorial é retirado [destes exemplos do GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs), que você pode examinar para ver todo o aplicativo de trabalho, incluindo instruções de importação e declarações de variável.

Outros exemplos estão disponíveis [no repositório de pacote dos Hubs de Eventos do Azure](https://github.com/Azure/azure-event-hubs-go/tree/master/_examples).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes pré-requisitos:

* Go instalado Localmente. Siga [estas instruções](https://golang.org/doc/install), se necessário.
* Uma conta ativa do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita][] antes de começar.
* Um namespace dos Hubs de Eventos existente e Hub de Eventos do Azure. Você pode criar essas entidades seguindo as instruções deste [artigo](event-hubs-create.md).

## <a name="install-go-package"></a>Instalar o pacote do Go

Obtenha o pacote do Go para Hubs de Eventos com `go get` ou `dep`. Por exemplo: 

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

## <a name="import-packages-in-your-code-file"></a>Importe os pacotes no seu arquivo de código

Para importar os pacotes Go, use o exemplo de código a seguir:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

## <a name="create-service-principal"></a>Criar uma entidade de serviço

Criar um entidade de serviço seguindo as instruções em [Criar uma entidade de serviço com a CLI do Azure 2.0](/cli/azure/create-an-azure-service-principal-azure-cli). Salve as credenciais fornecidas no seu ambiente com os nomes a seguir. O SDK do Azure para linguagem Go e os pacotes de Hubs de Eventos são pré-configurados para procurar esses nomes de variáveis:

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Agora, crie um provedor de autorização para seu cliente de Hubs de Eventos que usa estas credenciais:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

## <a name="create-event-hubs-client"></a>Criar cliente dos Hubs de Eventos

O código a seguir cria um novo Hub de Eventos:

```go
hub, err := eventhubs.NewHub("namespaceName", "hubName", tokenProvider)
ctx := context.WithTimeout(context.Background(), 10 * time.Second)
defer hub.Close(ctx)
if err != nil {
    log.Fatalf("failed to get hub %s\n", err)
}
```

## <a name="send-messages"></a>Enviar mensagens

No trecho a seguir, use (1) para enviar mensagens de forma interativa em um terminal ou (2) para enviar mensagens dentro do seu programa:

```go
// 1. send messages at the terminal
ctx = context.Background()
reader := bufio.NewReader(os.Stdin)
for {
    fmt.Printf("Input a message to send: ")
    text, _ := reader.ReadString('\n')
    hub.Send(ctx, eventhubs.NewEventFromString(text))
}

// 2. send messages within program
ctx = context.Background()
hub.Send(ctx, eventhubs.NewEventFromString("hello Azure!")
```

## <a name="extras"></a>Extras

Obtenha as IDs das partições no seu hub de eventos:

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n, info.PartitionIDs)
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os Hubs de Eventos,visite as páginas a seguir:

* [Receber eventos usando o EventProcessorHost](event-hubs-go-get-started-receive-eph.md)
* [Visão Geral dos Hubs de Eventos][Event Hubs overview]
* [Criar um hub de eventos](event-hubs-create.md)
* [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[conta gratuita]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
