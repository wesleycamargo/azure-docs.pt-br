---
title: Receber eventos usando Go - Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo fornece um passo a passo para a criação de um aplicativo Go que recebe eventos dos Hubs de Eventos do Azure.
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: db952b82172928e42e951563d98bb32b275e8af7
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53084984"
---
# <a name="receive-events-from-event-hubs-using-go"></a>Receber eventos de Hubs de Eventos usando o Go

Os Hubs de Eventos do Azure são um sistema gerenciamento de inclusão altamente dimensionável que pode receber milhões de eventos por segundo, permitindo que um aplicativo processe e analise grandes quantidades de dados produzidos por dispositivos conectados e outros sistemas. Depois de coletados em um hub de eventos, você pode receber e manipular eventos usando manipuladores em processo ou por encaminhamento para outros sistemas de análise.

Para saber mais sobre os Hubs de Eventos, consulte a [Visão geral dos Hubs de Eventos][Event Hubs overview].

Este tutorial descreve como receber eventos de um hub de eventos em um aplicativo Go. Para saber como enviar eventos consultar [o artigo de envio correspondente](event-hubs-go-get-started-send.md).

O código deste tutorial é retirado [destes exemplos do GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs), que você pode examinar para ver todo o aplicativo de trabalho, incluindo instruções de importação e declarações de variável.

Outros exemplos estão disponíveis [no repositório de pacote dos Hubs de Eventos do Azure](https://github.com/Azure/azure-event-hubs-go/tree/master/_examples).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes pré-requisitos:

* Go instalado Localmente. Siga [estas instruções](https://golang.org/doc/install), se necessário.
* Uma conta ativa do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita][] antes de começar.
* Para receber mensagens, deve haver mensagens no hub de eventos de destino. Saiba como enviar mensagens no [tutorial de envio](event-hubs-go-get-started-send.md).
* Um hub de eventos existente (consulte a seção a seguir).
* Uma conta de armazenamento existente e contêiner (consulte a seção após a próxima seção).

### <a name="create-an-event-hub"></a>Criar um Hub de Evento

Este tutorial inicia com um namespace de Hubs de Eventos existente e hub de eventos. Você pode criar essas entidades seguindo as instruções deste [artigo](event-hubs-create.md).

### <a name="create-a-storage-account-and-container"></a>Criar uma conta e um contêiner de armazenamento

Afirme como as concessões em partições e pontos de verificação no evento fluxo são compartilhados entre os destinatários usando um contêiner de Armazenamento do Microsoft Azure. Você pode criar uma conta de armazenamento e um contêiner com o SDK Go, mas você também pode criar um seguindo as instruções em [Sobre Contas de armazenamento do Azure](../storage/common/storage-create-storage-account.md).

Exemplos para a criação de artefatos de armazenamento com o SDK Go estão disponíveis na [repositório de exemplos do Go](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage) e na amostra correspondente a este tutorial.

## <a name="receive-messages"></a>Receber mensagens

Para receber as mensagens, obter os pacotes de Go para Hubs de Eventos com `go get` ou `dep`:

```bash
go get -u github.com/Azure/azure-event-hubs-go/...
go get -u github.com/Azure/azure-amqp-common-go/...
go get -u github.com/Azure/go-autorest/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
dep ensure -add github.com/Azure/go-autorest
```

## <a name="import-packages-in-your-code-file"></a>Importe os pacotes no seu arquivo de código

Para importar os pacotes Go, use o exemplo de código a seguir:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
    eph "github.com/Azure/azure-event-hubs-go/eph"
    storageLeaser "github.com/Azure/azure-event-hubs-go/storage"
    azure "github.com/Azure/go-autorest/autorest/azure"
)
```

## <a name="create-service-principal"></a>Criar uma entidade de serviço

Criar um entidade de serviço seguindo as instruções em [Criar uma entidade de serviço com a CLI do Azure 2.0](/cli/azure/create-an-azure-service-principal-azure-cli). Salve as credenciais fornecidas no ambiente com os nomes a seguir: O SDK do Azure para linguagem Go e os pacotes de Hubs de Eventos são pré-configurados para procurar esses nomes de variáveis.

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Em seguida, crie um provedor de autorização para seu cliente de Hubs de Eventos que usa estas credenciais:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

## <a name="get-metadata-struct"></a>Obter struct de metadados

Obtenha um struct com metadados sobre seu ambiente do Azure usando o SDK Go do Azure. Operações posteriores usam esse struct para localizar pontos de extremidade corretos.

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

## <a name="create-credential-helper"></a>Criar auxiliar credencial 

Crie um auxiliar credencial que usa as credenciais do Azure Active Directory (AAD) anterior para criar uma credencial de assinatura de acesso compartilhado (SAS) para armazenamento. O último parâmetro informa esse construtor para usar as mesmas variáveis de ambiente que foi usada anteriormente:

```go
cred, err := storageLeaser.NewAADSASCredential(
    subscriptionID,
    resourceGroupName,
    storageAccountName,
    storageContainerName,
    storageLeaser.AADSASCredentialWithEnvironmentVars())
if err != nil {
    log.Fatalf("could not prepare a storage credential: %s\n", err)
}
```

## <a name="create-checkpointer-and-leaser"></a>Criar ponto de verificação e Leaser 

Criar uma **Leaser**, responsável pela concessão de uma partição para um destinatário específico e uma **Checkpointer**, responsáveis por gravar pontos de verificação para o fluxo de mensagens para que podem começar a outros destinatários lendo o deslocamento correto.

Atualmente, um único **StorageLeaserCheckpointer** está disponível que usa o mesmo contêiner de armazenamento para gerenciar as concessões e pontos de verificação. Além dos nomes de conta e o contêiner de armazenamento, o **StorageLeaserCheckpointer** precisa de credenciais criadas na etapa anterior e o struct de ambiente do Azure para acessar corretamente o contêiner.

```go
leaserCheckpointer, err := storageLeaser.NewStorageLeaserCheckpointer(
    cred,
    storageAccountName,
    storageContainerName,
    azureEnv)
if err != nil {
    log.Fatalf("could not prepare a storage leaserCheckpointer: %s\n", err)
}
```

## <a name="construct-event-processor-host"></a>Construir o Host do processador de eventos

Agora você tem as partes necessárias para construir um EventProcessorHost, da seguinte maneira. O mesmo **StorageLeaserCheckpointer** é usado como um Leaser e Checkpointer, conforme descrito anteriormente:

```go
ctx := context.Background()
p, err := eph.New(
    ctx,
    nsName,
    hubName,
    tokenProvider,
    leaserCheckpointer,
    leaserCheckpointer)
if err != nil {
    log.Fatalf("failed to create EPH: %s\n", err)
}
defer p.Close(context.Background())
```

## <a name="create-handler"></a>Criar Manipulador 

Agora, crie um manipulador e registre-o com o Host do processador de eventos. Quando o host é iniciado, ele se aplica esse e outros manipuladores especificados às mensagens de entrada:

```go
handler := func(ctx context.Context, event *eventhubs.Event) error {
    fmt.Printf("received: %s\n", string(event.Data))
    return nil
}

// register the handler with the EPH
_, err := p.RegisterHandler(ctx, handler)
if err != nil {
    log.Fatalf("failed to register handler: %s\n", err)
}
```

## <a name="receive-messages"></a>Receber mensagens

Com tudo configurado, você pode iniciar o Host do processador de eventos com `Start(context)` para mantê-lo permanentemente em execução, ou com `StartNonBlocking(context)` apenas executar, desde que as mensagens estão disponíveis.

Este tutorial é iniciado e executado como se segue; Consulte o exemplo do GitHub para obter um exemplo usando `StartNonBlocking`:

```go
ctx := context.Background()
err = p.Start()
if err != nil {
    log.Fatalf("failed to start EPH: %s\n", err)
}
```

## <a name="notes"></a>Observações

Este tutorial usa uma única instância do **EventProcessorHost**. Para aumentar a taxa de transferência e confiabilidade, você deve executar várias instâncias do **EventProcessorHost** em sistemas diferentes. O sistema Leaser garante que apenas um receptor de Leaser está associado e recebe mensagens de uma partição especificada em um horário especificado.

## <a name="next-steps"></a>Próximas etapas
Neste início rápido, você criou um aplicativo Go que recebeu mensagens de um hub de eventos. Para saber como enviar eventos para um hub de eventos usando o Go, confira [Enviar eventos do hub de eventos – Go](event-hubs-go-get-started-send.md).

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[conta gratuita]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
