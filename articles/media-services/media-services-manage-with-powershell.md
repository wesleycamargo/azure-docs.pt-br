---
title: "Gerenciar contas dos Serviços de Mídia do Azure com o PowerShell"
description: "Saiba como gerenciar contas dos Serviços de Mídia do Azure com cmdlets do PowerShell."
author: Juliako
manager: erikre
editor: 
services: media-services
documentationcenter: 
ms.assetid: 17a10c25-d94f-421c-b6bc-ae0958e2ac96
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 2549c876ee35d6a7fa425d43e2f86d24131ca791
ms.openlocfilehash: 3d999d9e27844bc0164cc3572522b9ec022118a1


---
# <a name="manage-azure-media-services-accounts-with-powershell"></a>Gerenciar contas dos Serviços de Mídia do Azure com o PowerShell
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-create-account.md)
> * [PowerShell](media-services-manage-with-powershell.md)
> * [REST](https://docs.microsoft.com/rest/api/media/mediaservice)
> 
> [!NOTE]
> Para poder criar uma conta de Serviços de Mídia do Azure, você deve ter uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Avaliação Gratuita do Azure</a>.
> 
> 

## <a name="overview"></a>Visão geral
Este artigo lista os cmdlets do Azure PowerShell para os Serviços de Mídia do Azure (AMS) na estrutura do Azure Resource Manager. Os cmdlets existem no namespace **Microsoft.Azure.Commands.Media** .

## <a name="versions"></a>Versões
**ApiVersion**:   "2015-10-01"

## <a name="new-azurermmediaservice"></a>New-AzureRmMediaService
Cria um serviço de mídia.

### <a name="syntax"></a>Sintaxe
Conjunto de Parâmetros: StorageAccountIdParamSet

    New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccountId] <string> [-Tags <hashtable>]  [<CommonParameters>]

Conjunto de Parâmetros: StorageAccountsParamSet

    New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccounts] <PSStorageAccount[]> [-Tags <hashtable>]  [<CommonParameters>]

### <a name="parameters"></a>parâmetros
**-ResourceGroupName &lt;Cadeia de caracteres&gt;**

Especifica o nome do grupo de recursos ao qual pertence este serviço de mídia.

| Aliases | nenhum |
| --- | --- |
| Obrigatório? |verdadeiro |
| Position? |0 |
| Valor padrão |nenhum |
| Aceitar entrada do Pipeline? |true(ByPropertyName) |
| Aceitar caracteres curinga? |false |

**-AccountName &lt;Cadeia de caracteres&gt;**

Especifica o nome do serviço de mídia.

| Aliases | Name |
| --- | --- |
| Obrigatório? |verdadeiro |
| Position? |1 |
| Valor padrão |nenhum |
| Aceitar entrada do Pipeline? |false |
| Aceitar caracteres curinga? |false |

**-Location &lt;Cadeia de caracteres&gt;**

Especifica a localização do recurso do serviço de mídia.

| Aliases | nenhum |
| --- | --- |
| Obrigatório? |verdadeiro |
| Position? |2 |
| Valor padrão |nenhum |
| Aceitar entrada do Pipeline? |true(ByPropertyName) |
| Aceitar caracteres curinga? |false |

**-StorageAccountId &lt;Cadeia de caracteres&gt;**

Especifica uma conta de armazenamento principal associada ao serviço de mídia.

* Nova conta de armazenamento (criada com a API do Gerenciador de Recursos) com suporte apenas.
* A conta de armazenamento deve existir e ter o mesmo local do serviço de mídia.

| Aliases | nenhum |
| --- | --- |
| Obrigatório? |verdadeiro |
| Position? |3 |
| Valor padrão |nenhum |
| Aceitar entrada do Pipeline? |true(ByPropertyName) |
| Nome do conjunto de parâmetros |StorageAccountIdParamSet |
| Aceitar caracteres curinga? |false |

**-StorageAccounts &lt;PSStorageAccount\[\]&gt;**

Especifica as contas de armazenamento associadas ao serviço de mídia.

* Nova conta de armazenamento (criada com a API do Gerenciador de Recursos) com suporte apenas.
* A conta de armazenamento deve existir e ter o mesmo local do serviço de mídia.
* Apenas uma conta de armazenamento pode ser especificada como principal.

| Aliases | nenhum |
| --- | --- |
| Obrigatório? |verdadeiro |
| Position? |3 |
| Valor padrão |nenhum |
| Aceitar entrada do Pipeline? |true(ByPropertyName) |
| Nome do conjunto de parâmetros |StorageAccountsParamSet |
| Aceitar caracteres curinga? |false |

**-Tags &lt;Hashtable&gt;**

Especifica uma tabela de hash das marcações associadas ao serviço de mídia.

* Exemplo: @{"tag1"="value1";"tag2"=:value2"}

| Aliases | nenhum |
| --- | --- |
| Obrigatório? |false |
| Position? |nomeado |
| Valor padrão |nenhum |
| Aceitar entrada do Pipeline? |false |
| Aceitar caracteres curinga? |false |

**&lt;CommandParameters&gt;**

Este cmdlet oferece suporte aos parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.

### <a name="inputs"></a>Entradas
O tipo de entrada é o tipo dos objetos que você pode enviar ao cmdlet.

### <a name="outputs"></a>Saídas
O tipo de saída é o tipo dos objetos que o cmdlet transmite.

## <a name="set-azurermmediaservice"></a>Set-AzureRmMediaService
Atualiza um serviço de mídia.

### <a name="syntax"></a>Sintaxe
    Set-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Tags <hashtable>] [-StorageAccounts <PSStorageAccount[]>]  [<CommonParameters>]

### <a name="parameters"></a>parâmetros
**-ResourceGroupName &lt;Cadeia de caracteres&gt;**

Especifica o nome do grupo de recursos ao qual pertence este serviço de mídia.

| Aliases | nenhum |
| --- | --- |
| Obrigatório? |verdadeiro |
| Position? |0 |
| Valor padrão |nenhum |
| Aceitar entrada do Pipeline? |true(ByPropertyName) |
| Aceitar caracteres curinga? |false |

**-AccountName &lt;Cadeia de caracteres&gt;**

Especifica o nome do serviço de mídia.

| Aliases | Name |
| --- | --- |
| Obrigatório? |verdadeiro |
| Position? |1 |
| Valor padrão |nenhum |
| Aceitar entrada do Pipeline? |true(ByPropertyName) |
| Aceitar caracteres curinga? |Falso |

**-StorageAccounts &lt;PSStorageAccount\[\]&gt;**

Especifica as contas de armazenamento associadas ao serviço de mídia.

* Nova conta de armazenamento (criada com a API do Gerenciador de Recursos) com suporte apenas.
* A conta de armazenamento deve existir e ter o mesmo local do serviço de mídia.
* Apenas uma conta de armazenamento pode ser especificada como principal.

| Aliases | nenhum |
| --- | --- |
| Obrigatório? |false |
| Position? |nomeado |
| Valor padrão |nenhum |
| Aceitar entrada do Pipeline? |true(ByPropertyName) |
| Nome do conjunto de parâmetros |StorageAccountsParamSet |
| Aceitar caracteres curinga? |false |

**-Tags &lt;Hashtable&gt;**

Especifica uma tabela de hash das marcações associadas a este serviço de mídia.

* As marcações associadas ao serviço de mídia são substituídas pelo valor especificado pelo cliente.

| Aliases | nenhum |
| --- | --- |
| Obrigatório? |false |
| Position? |nomeado |
| Valor padrão |nenhum |
| Aceitar entrada do Pipeline? |true(ByPropertyName) |
| Aceitar caracteres curinga? |false |

**&lt;CommandParameters&gt;**

Este cmdlet oferece suporte aos parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.

### <a name="inputs"></a>Entradas
O tipo de entrada é o tipo dos objetos que você pode enviar ao cmdlet.

### <a name="outputs"></a>Saídas
O tipo de saída é o tipo dos objetos que o cmdlet transmite.

## <a name="remove-azurermmediaservice"></a>Remove-AzureRmMediaService
Remove um serviço de mídia.

### <a name="syntax"></a>Sintaxe
    Remove-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>parâmetros
**-ResourceGroupName &lt;Cadeia de caracteres&gt;**

Especifica o nome do grupo de recursos ao qual pertence este serviço de mídia.

| Aliases | nenhum |
| --- | --- |
| Obrigatório? |verdadeiro |
| Position? |0 |
| Valor padrão |nenhum |
| Aceitar entrada do Pipeline? |true(ByPropertyName) |
| Aceitar caracteres curinga? |false |

**-AccountName &lt;Cadeia de caracteres&gt;**

Especifica o nome do serviço de mídia.

| Aliases | nenhum |
| --- | --- |
| Obrigatório? |verdadeiro |
| Position? |2 |
| Valor padrão |nenhum |
| Aceitar entrada do Pipeline? |true(ByPropertyName) |
| Aceitar caracteres curinga? |Falso |

**&lt;CommandParameters&gt;**

Este cmdlet oferece suporte aos parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.

### <a name="inputs"></a>Entradas
O tipo de entrada é o tipo dos objetos que você pode enviar ao cmdlet.

### <a name="outputs"></a>Saídas
O tipo de saída é o tipo dos objetos que o cmdlet transmite.

## <a name="get-azurermmediaservice"></a>Get-AzureRmMediaService
Obtém todos os serviços de mídia em um grupo de recursos ou um serviço de mídia com um determinado nome.

### <a name="syntax"></a>Sintaxe
ParameterSet: ResourceGroupParameterSet

    Get-AzureRmMediaService [-ResourceGroupName] <string>  [<CommonParameters>]    

ParameterSet: AccountNameParameterSet

    Get-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>parâmetros
**-ResourceGroupName &lt;Cadeia de caracteres&gt;**

Especifica o nome do grupo de recursos ao qual pertence este serviço de mídia.

| Aliases | nenhum |
| --- | --- |
| Obrigatório? |verdadeiro |
| Position? |0 |
| Valor padrão |nenhum |
| Aceitar entrada do Pipeline? |true(ByPropertyName) |
| Nome do conjunto de parâmetros |ResourceGroupParameterSet, AccountNameParameterSet |

Aceitar caracteres curinga?   false

**-AccountName &lt;Cadeia de caracteres&gt;**

Especifica o nome do serviço de mídia.

| Aliases | nenhum |
| --- | --- |
| Obrigatório? |verdadeiro |
| Position? |1 |
| Valor padrão |nenhum |
| Aceitar entrada do Pipeline? |true(ByPropertyName) |
| Nome do conjunto de parâmetros |AccountNameParameterSet |
| Aceitar caracteres curinga? |false |

**&lt;CommandParameters&gt;**

Este cmdlet oferece suporte aos parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.

### <a name="inputs"></a>Entradas
O tipo de entrada é o tipo dos objetos que você pode enviar ao cmdlet.

### <a name="outputs"></a>Saídas
O tipo de saída é o tipo dos objetos que o cmdlet transmite.

## <a name="get-azurermmediaservicekeys"></a>Get-AzureRmMediaServiceKeys
Obtém as chaves de um serviço de mídia.

### <a name="syntax"></a>Sintaxe
    Get-AzureRmMediaServiceKeys [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>parâmetros
**-ResourceGroupName &lt;Cadeia de caracteres&gt;**

Especifica o nome do grupo de recursos ao qual pertence este serviço de mídia.

| Aliases | nenhum |
| --- | --- |
| Obrigatório? |verdadeiro |
| Position? |0 |
| Valor padrão |nenhum |
| Aceitar entrada do Pipeline? |true(ByPropertyName) |
| Aceitar caracteres curinga? |false |

**-AccountName &lt;Cadeia de caracteres&gt;**

Especifica o nome do serviço de mídia.

| Aliases | nenhum |
| --- | --- |
| Obrigatório? |verdadeiro |
| Position? |1 |
| Valor padrão |nenhum |
| Aceitar entrada do Pipeline? |true(ByPropertyName) |
| Aceitar caracteres curinga? |false |

**&lt;CommandParameters&gt;**

Este cmdlet oferece suporte aos parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.

### <a name="inputs"></a>Entradas
O tipo de entrada é o tipo dos objetos que você pode enviar ao cmdlet.

### <a name="outputs"></a>Saídas
O tipo de saída é o tipo dos objetos que o cmdlet transmite.

## <a name="set-azurermmediaservicekey"></a>Set-AzureRmMediaServiceKey
Regenera uma chave primária ou secundária de um serviço de mídia.

### <a name="syntax"></a>Sintaxe
    Set-AzureRmMediaServiceKey [-ResourceGroupName] <string> [-AccountName] <string> [-KeyType] <KeyType> {Primary | Secondary}  [<CommonParameters>]

### <a name="parameters"></a>parâmetros
**-ResourceGroupName &lt;Cadeia de caracteres&gt;**

Especifica o nome do grupo de recursos ao qual pertence este serviço de mídia.

| Aliases | nenhum |
| --- | --- |
| Obrigatório? |verdadeiro |
| Position? |0 |
| Valor padrão |nenhum |
| Aceitar entrada do Pipeline? |true(ByPropertyName) |
| Aceitar caracteres curinga? |false |

**-AccountName &lt;Cadeia de caracteres&gt;**

Especifica o nome do serviço de mídia.

| Aliases | nenhum |
| --- | --- |
| Obrigatório? |verdadeiro |
| Position? |1 |
| Valor padrão |nenhum |
| Aceitar entrada do Pipeline? |true(ByPropertyName) |
| Aceitar caracteres curinga? |false |

**-KeyType &lt;KeyType&gt;**

Especifica o tipo de chave do serviço de mídia.

* Primária ou Secundária

| Aliases | nenhum |
| --- | --- |
| Obrigatório? |verdadeiro |
| Position? |2 |
| Valor padrão |nenhum |
| Aceitar entrada do Pipeline? |false |
| Aceitar caracteres curinga? |false |

**&lt;CommandParameters&gt;**

Este cmdlet oferece suporte aos parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.

### <a name="inputs"></a>Entradas
O tipo de entrada é o tipo dos objetos que você pode enviar ao cmdlet.

### <a name="outputs"></a>Saídas
O tipo de saída é o tipo dos objetos que o cmdlet transmite.

## <a name="sync-azurermmediaservicestoragekeys"></a>Sync-AzureRmMediaServiceStorageKeys
Sincroniza as chaves da conta de armazenamento para uma conta de armazenamento associada ao serviço de mídia.

### <a name="syntax"></a>Sintaxe
    Sync-AzureRmMediaServiceStorageKeys [-ResourceGroupName] <string> [-MediaServiceAccountName] <string>    [-StorageAccountId] <string>  [<CommonParameters>]

### <a name="parameters"></a>parâmetros
**-ResourceGroupName &lt;Cadeia de caracteres&gt;**

Especifica o nome do grupo de recursos ao qual pertence este serviço de mídia.

| Aliases | nenhum |
| --- | --- |
| Obrigatório? |verdadeiro |
| Position? |0 |
| Valor padrão |nenhum |
| Aceitar entrada do Pipeline? |true(ByPropertyName) |
| Aceitar caracteres curinga? |false |

**-AccountName &lt;Cadeia de caracteres&gt;**

Especifica o nome do serviço de mídia.

| Aliases | nenhum |
| --- | --- |
| Obrigatório? |verdadeiro |
| Position? |1 |
| Valor padrão |nenhum |
| Aceitar entrada do Pipeline? |true(ByPropertyName) |
| Aceitar caracteres curinga? |false |

**-StorageAccountId &lt;Cadeia de caracteres&gt;**

Especifica a conta de armazenamento associada ao serviço de mídia.

| Aliases | ID |
| --- | --- |
| Obrigatório? |verdadeiro |
| Position? |2 |
| Valor padrão |nenhum |
| Aceitar entrada do Pipeline? |true(ByPropertyName) |
| Aceitar caracteres curinga? |false |

**&lt;CommandParameters&gt;**

Este cmdlet oferece suporte aos parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.

### <a name="inputs"></a>Entradas
O tipo de entrada é o tipo dos objetos que você pode enviar ao cmdlet.

### <a name="outputs"></a>Saídas
O tipo de saída é o tipo dos objetos que o cmdlet transmite.

## <a name="next-step"></a>Próxima etapa
Confira os roteiros de aprendizagem dos Serviços de Mídia.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Nov16_HO5-->


