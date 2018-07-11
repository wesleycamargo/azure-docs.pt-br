---
title: Enviar eventos personalizados da Grade de Eventos do Azure para a conexão híbrida | Microsoft Docs
description: Use a Grade de Eventos do Azure e a CLI do Azure para publicar um tópico e assinar esse evento. Uma conexão híbrida é usada para o ponto de extremidade.
services: event-grid
keywords: ''
author: tfitzmac
ms.author: tomfitz
ms.date: 06/29/2018
ms.topic: tutorial
ms.service: event-grid
ms.openlocfilehash: ee504f805c536ba9a6186514206546c3df1f0f1a
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37127706"
---
# <a name="route-custom-events-to-azure-relay-hybrid-connections-with-azure-cli-and-event-grid"></a>Encaminhe eventos personalizados para as Conexões Híbridas de Retransmissão do Azure com a CLI do Azure e a Grade de Eventos

A Grade de Eventos do Azure é um serviço de eventos para a nuvem. Conexões Híbridas de Retransmissão do Azure são um dos manipuladores de eventos com suporte. Você usa conexões híbridas como o manipulador de eventos quando precisa processar eventos de aplicativos que não têm um ponto de extremidade público. Esses aplicativos podem estar dentro de sua rede corporativa. Neste artigo, você pode usar a CLI do Azure para criar um tópico personalizado, assinar o tópico e disparar o evento para exibir o resultado. Você envia os eventos para a conexão híbrida.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já tem uma conexão híbrida e um aplicativo de ouvinte. Para começar a usar conexões híbridas, consulte [Introdução às Conexões Híbridas de Retransmissão - .NET](../service-bus-relay/relay-hybrid-connections-dotnet-get-started.md) ou [Introdução às Conexões Híbridas de Retransmissão - Nó](../service-bus-relay/relay-hybrid-connections-node-get-started.md).

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Os tópicos de Grade de Eventos são recursos do Azure e devem ser colocados em um grupo de recursos do Azure. O grupo de recursos do Azure é uma coleção lógica na qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az_group_create). 

O exemplo a seguir cria um grupo de recursos chamado *gridResourceGroup* no local *westus2*.

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

## <a name="create-a-custom-topic"></a>Criar um tópico personalizado

Um tópico de grade de evento fornece um ponto de extremidade definido pelo usuário no qual você posta seus eventos. O exemplo a seguir cria o tópico personalizado no seu grupo de recursos. Substitua `<topic_name>` por um nome exclusivo para o tópico. O nome do tópico deve ser exclusivo, pois é representado por uma entrada DNS.

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create --name <topic_name> -l westus2 -g gridResourceGroup
```

## <a name="subscribe-to-a-topic"></a>Assinar um tópico

Assine um tópico para indicar à Grade de Eventos quais eventos você deseja acompanhar. O exemplo a seguir assina o tópico que você criou e transmite a ID de recurso da conexão híbrida para o ponto de extremidade. A ID de conexão híbrida está no formato:

`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Relay/namespaces/<relay-namespace>/hybridConnections/<hybrid-connection-name>`

O script a seguir obtém a ID de recurso do namespace de retransmissão. Ele constrói a ID para a conexão híbrida e assina um tópico de grade de eventos. O script define o tipo de ponto de extremidade como `hybridconnection` e usa a ID de conexão híbrida do ponto de extremidade.

```azurecli-interactive
relayname=<namespace-name>
relayrg=<resource-group-for-relay>
hybridname=<hybrid-name>

relayid=$(az resource show --name $relayname --resource-group $relayrg --resource-type Microsoft.Relay/namespaces --query id --output tsv)
hybridid="$relayid/hybridConnections/$hybridname"

az eventgrid event-subscription create \
  --topic-name <topic_name> \
  -g gridResourceGroup \
  --name <event_subscription_name> \
  --endpoint-type hybridconnection \
  --endpoint $hybridid
```

## <a name="create-application-to-process-events"></a>Criar aplicativo para processar eventos

Você precisa de um aplicativo que possa recuperar eventos da conexão híbrida. O [exemplo de Consumidor de Conexão Híbrida da Grade de Eventos do Azure da Microsoft para C#](https://github.com/Azure-Samples/event-grid-dotnet-hybridconnection-destination) executa a operação. Você já atendeu às etapas de pré-requisito.

1. Certifique-se de que tenha o Visual Studio 2017 Versão 15.5 ou posterior.

1. Clone o repositório em seu computador local.

1. Carregue o projeto HybridConnectionConsumer no Visual Studio.

1. Em Program.cs, substitua `<relayConnectionString>` e `<hybridConnectionName>` pela cadeia de conexão de restransmissão e o nome de conexão híbrida que você criou.

1. Compile e execute o aplicativo no Visual Studio.

## <a name="send-an-event-to-your-topic"></a>Enviar um evento para o tópico

Vamos disparar um evento para ver como a Grade de Eventos distribui a mensagem para o ponto de extremidade. Este artigo mostra como usar a CLI do Azure para disparar o evento. Como alternativa, você pode usar o [aplicativo publicador de Grade de Eventos](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/tree/master/EventGridPublisher).

Primeiro, vamos obter a URL e a chave para o tópico personalizado. Novamente, use o nome do tópico em `<topic_name>`.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

Para simplificar este artigo, use dados de evento de exemplo para enviar ao tópico. Normalmente, um aplicativo ou serviço do Azure enviaria os dados de evento. CURL é um utilitário que envia solicitações HTTP. Neste artigo, use o CURL para enviar o evento ao tópico.  O exemplo a seguir envia três eventos para o tópico de grade de eventos:

```azurecli-interactive
body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/customevent.json)'")
curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

O aplicativo ouvinte deve receber a mensagem de evento.

## <a name="clean-up-resources"></a>Limpar recursos
Caso planeje continuar a trabalhar com esse evento, não limpe os recursos criados neste artigo. Caso contrário, use os comandos a seguir para excluir os recursos criados por você neste artigo.

```azurecli-interactive
az group delete --name gridResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe como criar tópicos e assinaturas de evento, saiba mais sobre como a Grade de Eventos pode ajudá-lo:

- [Sobre a Grade de Eventos](overview.md)
- [Rotear eventos do Armazenamento de Blobs para um ponto de extremidade da Web personalizado](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitorar alterações de máquina virtual com a Grade de Eventos do Azure e os Aplicativos Lógicos](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Transmitir Big Data para um data warehouse](event-grid-event-hubs-integration.md)
