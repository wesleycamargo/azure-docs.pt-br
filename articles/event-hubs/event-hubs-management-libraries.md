---
title: Bibliotecas de gerenciamento dos Hubs de Eventos do Azure | Microsoft Docs
description: Gerenciar namespaces de Hubs de eventos e entidades do .NET
services: event-hubs
cloud: na
documentationcenter: na
author: jtaubensee
manager: timlt
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 4/10/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: a9023448c4ced1edf54c84bb103454cbd76fbfba
ms.lasthandoff: 04/12/2017


---

# <a name="event-hubs-management-libraries"></a>Bibliotecas de gerenciamento dos Hubs de Eventos

As bibliotecas de gerenciamento de Hubs de eventos podem provisionar dinamicamente entidades e namespaces de Hubs de eventos. Isso permite implantações e cenários de mensagens complexos, de modo que você possa determinar de forma programática quais entidades provisionar. Essas bibliotecas estão atualmente disponíveis para .NET.

## <a name="supported-functionality"></a>Funcionalidade com suporte

* Criação, atualização, exclusão de namespace
* Criação de Hubs de eventos, atualização, exclusão
* Criação de grupo de consumidores, atualização, exclusão

## <a name="prerequisites"></a>Pré-requisitos

Para começar a usar as bibliotecas de gerenciamento de Hubs de eventos, você deverá se autenticar com o Azure Active Directory (AAD). AAD exige que você autentique como uma entidade de serviço, que fornece acesso aos recursos do Azure. Para saber mais sobre como criar uma entidade de serviço, veja um dos seguintes artigos:  

* [Usar o portal do Azure para criar um aplicativo e entidade de serviço do Active Directory que pode acessar recursos](../azure-resource-manager/resource-group-create-service-principal-portal.md)
* [Usar o Azure PowerShell para criar uma entidade de serviço a fim de acessar recursos](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [Usar a CLI do Azure para criar uma entidade de serviço a fim de acessar recursos](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

Estes tutoriais fornecerão uma `AppId` (ID do Cliente), `TenantId` e `ClientSecret` (Chave de Autenticação), todos usados para autenticação pelas bibliotecas de gerenciamento. Você deve ter as permissões 'Proprietário' para o grupo de recursos no qual você deseja executar.

## <a name="programming-pattern"></a>Padrão de programação

O padrão para manipular qualquer recurso de Hubs de eventos a seguir, um protocolo comum:

1. Obter um token do Azure Active Directory usando a biblioteca `Microsoft.IdentityModel.Clients.ActiveDirectory`.
    ```csharp
    var context = new AuthenticationContext($"https://login.windows.net/{tenantId}");

    var result = await context.AcquireTokenAsync(
        "https://management.core.windows.net/",
        new ClientCredential(clientId, clientSecret)
    );
    ```

1. Crie o objeto `EventHubManagementClient`.
    ```csharp
    var creds = new TokenCredentials(token);
    var ehClient = new EventHubManagementClient(creds)
    {
        SubscriptionId = SettingsCache["SubscriptionId"]
    };
    ```

1. Defina os parâmetros `CreateOrUpdate` com os valores especificados.
    ```csharp
    var ehParams = new EventHubCreateOrUpdateParameters()
    {
        Location = SettingsCache["DataCenterLocation"]
    };
    ```

1. Execute a chamada.
    ```csharp
    await ehClient.EventHubs.CreateOrUpdateAsync(resourceGroupName, namespaceName, EventHubName, ehParams);
    ```

## <a name="next-steps"></a>Próximas etapas
* [Exemplo do Gerenciamento do .NET](https://github.com/Azure-Samples/event-hubs-dotnet-management/)
* [Referência de Microsoft.Azure.Management.EventHub](/dotnet/api/Microsoft.Azure.Management.EventHub) 

