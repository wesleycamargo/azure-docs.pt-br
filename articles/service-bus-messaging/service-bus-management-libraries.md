---
title: "Bibliotecas de gerenciamento do Barramento de Serviço do Azure | Microsoft Docs"
description: "Gerenciar entidades de mensagens e namespaces do Barramento de Serviço por meio do .NET"
services: service-bus-messaging
cloud: na
documentationcenter: na
author: jtaubensee
manager: timlt
ms.assetid: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/03/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: ec9f2fa3d88f59172d320b58287208deb084856f
ms.lasthandoff: 04/11/2017


---

# <a name="service-bus-management-libraries"></a>Bibliotecas de gerenciamento do Barramento de Serviço

As bibliotecas de gerenciamento do Barramento de Serviço podem provisionar dinamicamente entidades e namespaces de Barramento de Serviço. Isso permite implantações e cenários de mensagens complexos, possibilitando que você determine programaticamente quais entidades provisionar. Essas bibliotecas estão atualmente disponíveis para .NET.

## <a name="supported-functionality"></a>Funcionalidade com suporte

* Criação, atualização, exclusão de namespace
* Criação da fila, atualização, exclusão
* Criação de tópico, atualização, exclusão
* Criação da assinatura, atualização, exclusão

## <a name="prerequisites"></a>Pré-requisitos

Para começar a usar as bibliotecas de gerenciamento do Barramento de Serviço, você deverá se autenticar com o Azure Active Directory (AAD). AAD exige que você autentique como uma entidade de serviço que fornece acesso aos recursos do Azure. Para saber mais sobre como criar uma entidade de serviço, veja um dos seguintes artigos:  

* [Usar o portal do Azure para criar um aplicativo e entidade de serviço do Active Directory que pode acessar recursos](/azure/azure-resource-manager/resource-group-create-service-principal-portal)
* [Usar o Azure PowerShell para criar uma entidade de serviço a fim de acessar recursos](/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Usar a CLI do Azure para criar uma entidade de serviço a fim de acessar recursos](/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)

Estes tutoriais fornecem uma `AppId` (ID do Cliente), `TenantId` e `ClientSecret` (chave de autenticação), todas usadas para autenticação pelas bibliotecas de gerenciamento. Você deve ter as permissões 'Proprietário' para o grupo de recursos no qual você deseja executar.

## <a name="programming-pattern"></a>Padrão de programação

O padrão para manipular qualquer recurso do Barramento de Serviço segue um protocolo comum:

1. Obtenha um token do Azure Active Directory usando a biblioteca **Microsoft.IdentityModel.Clients.ActiveDirectory**.
    ```csharp
    var context = new AuthenticationContext($"https://login.windows.net/{tenantId}");

    var result = await context.AcquireTokenAsync(
        "https://management.core.windows.net/",
        new ClientCredential(clientId, clientSecret)
    );
    ```

1. Crie o objeto `ServiceBusManagementClient`.
    ```csharp
    var creds = new TokenCredentials(token);
    var sbClient = new ServiceBusManagementClient(creds)
    {
        SubscriptionId = SettingsCache["SubscriptionId"]
    };
    ```

1. Defina os parâmetros `CreateOrUpdate` com os valores especificados.
    ```csharp
    var queueParams = new QueueCreateOrUpdateParameters()
    {
        Location = SettingsCache["DataCenterLocation"],
        EnablePartitioning = true
    };
    ```

1. Execute a chamada.
    ```csharp
    await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
    ```

## <a name="next-steps"></a>Próximas etapas
* [Exemplo do Gerenciamento do .NET](https://github.com/Azure-Samples/service-bus-dotnet-management/)
* [Referência do Microsoft.Azure.Management.ServiceBus](/dotnet/api/Microsoft.Azure.Management.ServiceBus)

