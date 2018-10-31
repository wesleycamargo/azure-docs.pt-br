---
title: Monitorar eventos dos Serviços de Mídia do Azure com a Grade de Eventos usando a CLI | Microsoft Docs
description: Este artigo mostra como se inscrever no Event Grid para monitorar eventos do Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 8145b4eb3c39511eb9cd0ed052c36b8338191d4f
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49389489"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-cli"></a>Criar e monitorar eventos dos Serviços de Mídia com a Grade de Eventos usando a CLI do Azure

A Grade de Eventos do Azure é um serviço de eventos para a nuvem. Neste artigo, você usará a CLI do Azure para assinar eventos da sua conta de Serviços de Mídia do Azure. Em seguida, você aciona eventos para exibir o resultado. Normalmente, você envia eventos para um ponto de extremidade que processa os dados de evento e realiza ações. Neste artigo, você enviará eventos para um aplicativo Web que coleta e exibe as mensagens.

## <a name="prerequisites"></a>Pré-requisitos

- Ter uma assinatura ativa do Azure.
- [Crie uma conta de Serviços de Mídia](create-account-cli-how-to.md).

    Lembre-se dos valores que você usou para o nome do grupo de recursos e o nome da conta de Serviços de Mídia.

- Instale a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Este artigo requer a CLI do Azure versão 2.0 ou posterior. Execute `az --version` descobrir a versão que você tem. Você também pode usar o [Azure Cloud Shell](https://shell.azure.com/bash).

## <a name="create-a-message-endpoint"></a>Criar um ponto de extremidade de mensagem

Antes de assinar os eventos da conta dos Serviços de Mídia, vamos criar o terminal para a mensagem do evento. Normalmente, o ponto de extremidade executa ações com base nos dados de evento. Neste artigo, você implanta uma [aplicativo web criado previamente](https://github.com/Azure-Samples/azure-event-grid-viewer) que exibe as mensagens de evento. A solução implantada inclui um plano do Serviço de Aplicativo, um aplicativo Web do Aplicativo do Serviço de e o código-fonte do GitHub.

1. Selecione **Implantar no Azure** para implantar a solução na sua assinatura. No portal do Azure, forneça os valores para os parâmetros.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. A implantação pode levar alguns minutos para ser concluída. Depois que a implantação for bem-sucedida, exiba seu aplicativo Web para garantir que ele esteja em execução. Em um navegador da Web, navegue até: `https://<your-site-name>.azurewebsites.net`

Se você alternar para o site "Visualizador de grade de eventos do Azure", verá que ele ainda não há eventos.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon no [Portal do Azure](http://portal.azure.com) e inicie o **CloudShell** para executar comandos da CLI, conforme mostra as etapas a seguir.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se você optar por instalar e usar a CLI localmente, este tópico exigirá a CLI do Azure versão 2.0 ou posterior. Execute `az --version` descobrir a versão que você tem. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

## <a name="set-the-azure-subscription"></a>Definir a assinatura do Azure

No comando a seguir, forneça a ID de assinatura do Azure que você deseja usar para a conta de Serviços de Mídia. Veja uma lista das assinaturas as quais você tem acesso navegando até [Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli-interactive
az account set --subscription mySubscriptionId
```
 
[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

## <a name="subscribe-to-media-services-events"></a>Assinar eventos de serviços de mídia

Você se inscreve em um artigo para indicar à Grade de Eventos quais eventos você deseja acompanhar. O exemplo a seguir assina a conta dos Serviços de Mídia que você criou e transmite a URL do site criado como o ponto de extremidade para a notificação de eventos. 

Substitua `<event_subscription_name>` por um nome exclusivo para a assinatura de evento. Para `<resource_group_name>` e `<ams_account_name>`, use os valores que você escolheu ao criar a conta de Serviços de Mídia. Para o `<endpoint_URL>`, forneça a URL do seu aplicativo Web e adicione `api/updates` à URL da página inicial. Ao especificar um ponto de extremidade durante a assinatura, a Grade de Eventos manipula o roteamento dos eventos para esse ponto de extremidade. 

1. Obter a ID do recurso

    ```azurecli-interactive
    amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)
    ```

    Por exemplo: 

    ```
    amsResourceId=$(az ams account show --name amsaccount --resource-group amsResourceGroup --query id --output tsv)
    ```

2. Assinar os eventos

    ```azurecli-interactive
    az eventgrid event-subscription create \
    --resource-id $amsResourceId \
    --name <event_subscription_name> \
    --endpoint <endpoint_URL>
    ```

    Por exemplo: 

    ```
    az eventgrid event-subscription create --resource-id $amsResourceId --name amsTestEventSubscription --endpoint https://amstesteventgrid.azurewebsites.net/api/updates/
    ```    

    > [!TIP]
    > Você poderá receber um aviso de validação de handshake. Aguarde alguns minutos e o handshake será validado.

Agora, vamos acionar eventos para ver como o Event Grid distribui a mensagem para o seu endpoint.

## <a name="send-an-event-to-your-endpoint"></a>Enviar um evento para o ponto de extremidade

Você pode acionar eventos para a conta do Media Services executando uma tarefa de codificação. Você pode seguir [este guia de início rápido](stream-files-dotnet-quickstart.md) para codificar um arquivo e iniciar o envio de eventos. 

Exiba novamente o seu aplicativo Web e observe que um evento de validação de assinatura foi enviado a ele. A Grade de Eventos envia o evento de validação de modo que o ponto de extremidade possa verificar se ele deseja receber os dados de evento. O ponto de extremidade deve definir `validationResponse` para `validationCode`. Para saber mais, confira [Event Grid security and authentication](../../event-grid/security-authentication.md) (Segurança e autenticação da Grade de Eventos). Você pode visualizar o código do aplicativo da Web para ver como ele valida a assinatura.

> [!TIP]
> Selecione o ícone de olho para expandir os dados de evento. Não atualize a página, se você quiser ver todos os eventos.

![Exibição do evento de assinatura](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Próximas etapas

[Carregar, codificar e transmitir](stream-files-tutorial-with-api.md)

