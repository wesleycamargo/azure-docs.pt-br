---
title: Identidade gerenciada para o Data Factory | Microsoft Docs
description: Saiba mais sobre identidade gerenciada para o Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: jingwang
ms.openlocfilehash: 3663526dc32b0a607c9fca3d7c76496bfb5566f4
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57549130"
---
# <a name="managed-identity-for-data-factory"></a>Identidade gerenciada para o Data Factory

Este artigo ajuda você a entender o que é a identidade gerenciada para o Data Factory (anteriormente conhecido como identidade/MSI de serviço gerenciado) e como ele funciona.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Visão geral

Ao criar um data factory, uma identidade gerenciada pode ser criada junto com a criação de fábrica. A identidade gerenciada é um aplicativo gerenciado registrado no Azure Active Directory e representa a data factory específica.

Identidade gerenciada para o Data Factory se beneficia os seguintes recursos:

- [Store credencial no Azure Key Vault](store-credentials-in-key-vault.md), caso em que a identidade do data factory gerenciado é usada para autenticação de Azure Key Vault.
- Conectores incluindo [armazenamento de Blobs do Azure](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Banco de Dados SQL do Azure](connector-azure-sql-database.md), e [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md).
- [Atividade da Web](control-flow-web-activity.md).

## <a name="generate-managed-identity"></a>Gerar a identidade gerenciada

Identidade gerenciada para o Data Factory é gerada da seguinte maneira:

- Ao criar o data factory por meio **portal do Azure ou PowerShell**gerenciado identidade sempre será criada automaticamente.
- Ao criar o data factory por meio **SDK**gerenciado identidade será criada somente se você especificar "identidade = new factoryidentity ()" no objeto de fábrica para criação. Consulte o exemplo do [início rápido do .NET - criar data factory](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Ao criar o data factory por meio **API REST**gerenciado identidade será criada somente se você especificar a seção "identity" no corpo da solicitação. Consulte o exemplo do [início rápido do REST - criar data factory](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Se você achar que seu data factory não tem uma identidade gerenciada associada seguindo [recuperar identidade gerenciada](#retrieve-managed-identity) instruções, você pode gerar uma atualizando o data factory com o iniciador de identidade por meio de programação:

- [Gerar a identidade gerenciada usando o PowerShell](#generate-managed-identity-using-powershell)
- [Gerar a identidade gerenciada usando a API REST](#generate-managed-identity-using-rest-api)
- Gerar a identidade gerenciada usando um modelo do Azure Resource Manager
- [Gerar a identidade gerenciada usando o SDK](#generate-managed-identity-using-sdk)

>[!NOTE]
>- Identidade gerenciada não pode ser modificada. Atualizar um data factory que já tem uma identidade gerenciada não terá nenhum impacto, a identidade gerenciada permanecerá inalterada.
>- Se você atualizar um data factory que já tem uma identidade gerenciada sem especificar o parâmetro "identity" no objeto do alocador ou sem especificar a seção "identity" no corpo da solicitação REST, você receberá um erro.
>- Quando você exclui um data factory, a identidade gerenciada associada será excluída junto.

### <a name="generate-managed-identity-using-powershell"></a>Gerar a identidade gerenciada usando o PowerShell

Chame **AzDataFactoryV2 conjunto** comando novamente, em seguida, você verá campos "Identity" sendo recentemente gerados:

```powershell
PS C:\WINDOWS\system32> Set-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-managed-identity-using-rest-api"></a>Gerar a identidade gerenciada usando a API REST

Chame a API abaixo com a seção "identity" no corpo da solicitação:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**Corpo da solicitação**: adicionar "identity": {"type": "SystemAssigned" }.

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**Resposta**: identidade gerenciada é criada automaticamente e a seção "identity" é preenchida de acordo.

```json
{
    "name": "<dataFactoryName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>"
}
```

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>Gerar a identidade gerenciada usando um modelo do Azure Resource Manager

**Modelo**: adicionar "identity": {"type": "SystemAssigned" }.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<dataFactoryName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```

### <a name="generate-managed-identity-using-sdk"></a>Gerar a identidade gerenciada usando o SDK

Chame a função create_or_update do data factory com Identity=new FactoryIdentity(). Exemplo de código usando .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>Recuperar a identidade gerenciada

Você pode recuperar a identidade gerenciada do portal do Azure ou programaticamente. As seções a seguir mostram alguns exemplos.

>[!TIP]
> Se você não vir a identidade gerenciada, [gerar a identidade gerenciada](#generate-managed-identity) atualizando seu alocador.

### <a name="retrieve-managed-identity-using-azure-portal"></a>Recuperar a identidade gerenciada usando o portal do Azure

Você pode encontrar as informações de identidade gerenciada do portal do Azure -> seu data factory -> Configurações -> Propriedades:

- ID DA IDENTIDADE DO SERVIÇO
- LOCATÁRIO DA IDENTIDADE DO SERVIÇO
- **ID DO APLICATIVO DA IDENTIDADE DO SERVIÇO** > copie esse valor

![Recuperar a identidade gerenciada](media/data-factory-service-identity/retrieve-service-identity-portal.png)

### <a name="retrieve-managed-identity-using-powershell"></a>Recuperar a identidade gerenciada usando o PowerShell

A identidade gerenciada, ID da entidade e a ID de locatário será retornada quando você receber um data factory específico da seguinte maneira:

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Copie a ID da entidade de segurança, em seguida, execute o comando do Azure Active Directory abaixo com a ID da entidade de segurança como parâmetro para obter o **ApplicationId**, que você pode usar para conceder acesso:

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>Próximas etapas
Consulte os tópicos a seguir que apresentam quando e como usar data factory identidade gerenciada:

- [Armazenar credencial no Azure Key Vault](store-credentials-in-key-vault.md)
- [Copiar dados de/para o Azure Data Lake Store usando identidades gerenciadas para autenticação de recursos do Azure](connector-azure-data-lake-store.md)

Ver [identidades gerenciadas de visão geral dos recursos do Azure](/azure/active-directory/managed-identities-azure-resources/overview) para obter mais informações sobre identidades gerenciadas para recursos do Azure, a identidade gerenciada do qual o data factory se baseia. 
