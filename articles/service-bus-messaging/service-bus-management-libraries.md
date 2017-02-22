---
title: "Bibliotecas de gerenciamento do Barramento de Serviço do Azure | Microsoft Docs"
description: "Gerenciar entidades e namespaces de Barramento de Serviço do .NET"
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
ms.date: 1/6/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: dfd1ae52cc56a4d4b4c7ee3f69f0c454be607401
ms.openlocfilehash: 7b04227327235f788ecf4018b6c159d33113b63d


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

* [Usar o Portal do Azure para criar um aplicativo e entidade de serviço do Active Directory que pode acessar recursos](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal)
* [Usar o Azure PowerShell para criar uma entidade de serviço a fim de acessar recursos](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Usar a CLI do Azure para criar uma entidade de serviço a fim de acessar recursos](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)

Estes tutoriais fornecerão uma `AppId` (ID do Cliente), `TenantId` e `ClientSecret` (Chave de Autenticação), todos usados para autenticação pelas bibliotecas de gerenciamento. Você deve ter as permissões 'Proprietário' para o grupo de recursos no qual você deseja executar.

## <a name="programming-pattern"></a>Padrão de programação

O padrão para manipular qualquer recurso do Barramento de Serviço segue um protocolo comum:

1. Obter um token do Azure Active Directory usando a biblioteca `Microsoft.IdentityModel.Clients.ActiveDirectory`.
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

1. Defina os parâmetros de CreateOrUpdate para os valores especificados.
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



<!--HONumber=Jan17_HO3-->


