---
title: "Identidade de serviço do Azure Data Factory | Microsoft Docs"
description: "Saiba mais sobre a identidade de serviço de data factory no Azure Data Factory."
services: data-factory
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: jingwang
ms.openlocfilehash: 350ce0fd9a10d862e811dd1eaa39e0e90076e1ef
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2017
---
# <a name="azure-data-factory-service-identity"></a>Identidade do serviço Azure Data Factory

Este artigo ajuda você a entender o que é a identidade de serviço de data factory e como ela funciona.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que já está disponível (GA), confira a [documentação do Data Factory versão 1](v1/data-factory-introduction.md).

## <a name="overview"></a>Visão geral

Ao criar um data factory, uma identidade do serviço pode ser criada ao mesmo tempo. A identidade do serviço é um aplicativo gerenciado registrado no Azure Active Directory e representa esse data factory específico.

Identidade do serviço de data factory se beneficia dos dois seguintes recursos:

- [Armazenar credenciais no Azure Key Vault](store-credentials-in-key-vault.md), caso em que a identidade do serviço de data factory é usada para autenticação de Cofre de chaves do Azure.
- [Copiar dados de/para o Azure Data Lake Store](connector-azure-data-lake-store.md), caso em que a identidade do serviço de data factory pode ser usada como um dos tipos de autenticação com suporte do Data Lake Store.

## <a name="generate-service-identity"></a>Gerar a identidade do serviço

Identidade do serviço de data factory é gerada da seguinte maneira:

- Ao criar o data factory por meio de **portal do Azure ou do PowerShell**, a identidade do serviço sempre será criada automaticamente a partir da visualização pública ADF V2.
- Ao criar o data factory por meio do **SDK**, a identidade do serviço será criada somente se você especificar "identidade = FactoryIdentity() nova" no objeto de fábrica para a criação. Consulte o exemplo do [início rápido do .NET - criar data factory](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Ao criar o data factory por meio da **API REST**, a identidade do serviço será criada somente se você especificar a seção "identidade" no corpo da solicitação. Consulte o exemplo do [início rápido do REST - criar data factory](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Se você achar que seu data factory não tem uma identidade do serviço associada seguindo a instrução [recuperar a identidade de serviço](#retrieve-service-identity), você pode gerar uma atualizando o data factory com o iniciador de identidade do forma programática:

- [Gerar a identidade de serviço usando o PowerShell](#generate-service-identity-using-powershell)
- [Gerar a identidade de serviço usando o API REST](#generate-service-identity-using-rest-api)
- [Gerar a identidade de serviço usando o SDK](#generate-service-identity-using-sdk)

>[!NOTE]
>- A identidade do serviço não pode ser modificada. Atualizar um data factory que já tem uma identidade do serviço não terá nenhum impacto, a identidade do serviço permanecerá inalterada.
>- Se você atualizar um data factory que já tem uma identidade do serviço sem especificar o parâmetro "identity" no objeto do alocador ou sem especificar a seção "identity" no corpo da solicitação REST, você obterá um erro.
>- Quando você exclui um data factory, a identidade de serviço associado será excluída junto.

### <a name="generate-service-identity-using-powershell"></a>Gerar a identidade de serviço usando o PowerShell

Chame o comando **Set-AzureRmDataFactoryV2** novamente, em seguida, você verá os campos "Identity" sendo recentemente gerados:

```powershell
PS C:\WINDOWS\system32> Set-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-service-identity-using-rest-api"></a>Gerar a identidade de serviço usando o API REST

Chame a API abaixo com a seção "identity" no corpo da solicitação:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2017-09-01-preview
```

**Corpo da solicitação**: adicionar "identity": {"type": "SystemAssigned"}.

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

**Resposta**: a identidade do serviço é criada automaticamente e a seção "identity" é preenchida de acordo.

```json
{
    "name": "ADFV2DemoFactory",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2017-09-01-preview"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "EastUS"
}
```

### <a name="generate-service-identity-using-sdk"></a>Gerar a identidade de serviço usando o SDK

Chame a função create_or_update do data factory com Identity=new FactoryIdentity(). Exemplo de código usando .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-service-identity"></a>Recuperar a identidade do serviço

Você pode recuperar a identidade do serviço no portal do Azure ou programaticamente. As seções a seguir mostram alguns exemplos.

>[!TIP]
> Se você não vir a identidade do serviço, [gere a identidade de serviço](#generate-service-identity) atualizando seu alocador.

### <a name="retrieve-service-identity-using-azure-portal"></a>Recuperar a identidade de serviço usando o portal do Azure

Você pode encontrar as informações sobre a identidade do serviço no Portal do Azure -> seu data factory -> Configurações -> Propriedades:

- ID DA IDENTIDADE DO SERVIÇO
- LOCATÁRIO DA IDENTIDADE DO SERVIÇO
- **ID DO APLICATIVO DA IDENTIDADE DO SERVIÇO** > copie esse valor

![Recuperar a identidade do serviço](media/data-factory-service-identity/retrieve-service-identity-portal.png)

### <a name="retrieve-service-identity-using-powershell"></a>Recuperar a identidade de serviço usando PowerShell

A ID da entidade de segurança da identidade do serviço e a ID de locatário serão retornadas ao obter um data factory específico da seguinte maneira:

```powershell
PS C:\WINDOWS\system32> (Get-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Copie a ID da entidade de segurança, em seguida, execute o comando do Azure Active Directory abaixo com a ID da entidade de segurança como parâmetro para obter o **ApplicationId**, que você pode usar para conceder acesso:

```powershell
PS C:\WINDOWS\system32> Get-AzureRmADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>Próximas etapas
Consulte os tópicos a seguir que apresentam quando e como usar a identidade do serviço de data factory:

- [Armazenar credencial no Azure Key Vault](store-credentials-in-key-vault.md)
- [Copiar dados do/para o Azure Data Lake Store usando a autenticação de identidade de serviço gerenciado](connector-azure-data-lake-store.md)

Consulte [Visão geral do MSI](~/articles/active-directory/msi-overview.md) para obter mais informações sobre identidade de serviço gerenciado, em que a identidade de serviço de data factory se baseia. 