<properties 
	pageTitle="Gerenciar contas dos Serviços de Mídia do Azure com o PowerShell" 
	description="Saiba como gerenciar contas dos Serviços de Mídia do Azure com cmdlets do PowerShell." 
	authors="Juliako" 
	manager="erikre" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/04/2016"
	ms.author="juliako"/>


#Gerenciar contas dos Serviços de Mídia do Azure com o PowerShell


> [AZURE.SELECTOR]
- [Portal](media-services-portal-create-account.md)
- [PowerShell](media-services-manage-with-powershell.md)
- [REST](http://msdn.microsoft.com/library/azure/dn194267.aspx)


> [AZURE.NOTE] Para poder criar uma conta de Serviços de Mídia do Azure, você deve ter uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Avaliação Gratuita do Azure</a>.

##Visão geral 

Este artigo lista os cmdlets do Azure PowerShell para os Serviços de Mídia do Azure (AMS) na estrutura do Azure Resource Manager. Os cmdlets existem no namespace **Microsoft.Azure.Commands.Media**.

## Versões

**ApiVersion**: "2015-10-01"
               

## New-AzureRmMediaService

Cria um serviço de mídia.

### Sintaxe

Conjunto de Parâmetros: StorageAccountIdParamSet

	New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccountId] <string> [-Tags <hashtable>]  [<CommonParameters>]

Conjunto de Parâmetros: StorageAccountsParamSet

	New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccounts] <PSStorageAccount[]> [-Tags <hashtable>]  [<CommonParameters>]

### Parâmetros

**-ResourceGroupName &lt;String&gt;**

Especifica o nome do grupo de recursos ao qual pertence este serviço de mídia.

Aliases | nenhum
---|---
Obrigatório? | verdadeiro
Position? | 0
Valor padrão |nenhum
Aceitar entrada do Pipeline? |true(ByPropertyName)
Aceitar caracteres curinga? |false

**-AccountName &lt;String&gt;**

Especifica o nome do serviço de mídia.

Aliases |Name
---|---
Obrigatório? |verdadeiro
Position? |1
Valor padrão |nenhum
Aceitar entrada do Pipeline? |false
Aceitar caracteres curinga? |false

**-Location &lt;String&gt;**

Especifica a localização do recurso do serviço de mídia.

Aliases |nenhum
---|---
Obrigatório? |verdadeiro
Position? |2
Valor padrão |nenhum
Aceitar entrada do Pipeline? |true(ByPropertyName)
Aceitar caracteres curinga? |false

**-StorageAccountId &lt;String&gt;**

Especifica uma conta de armazenamento principal associada ao serviço de mídia.

- Nova conta de armazenamento (criada com a API do Gerenciador de Recursos) com suporte apenas.

- A conta de armazenamento deve existir e ter o mesmo local do serviço de mídia.

Aliases |nenhum
---|---
Obrigatório? |verdadeiro
Position? |3
Valor padrão |nenhum
Aceitar entrada do Pipeline? |true(ByPropertyName)
Nome do conjunto de parâmetros |StorageAccountIdParamSet
Aceitar caracteres curinga?|false

**-StorageAccounts &lt;PSStorageAccount[]&gt;**

Especifica as contas de armazenamento associadas ao serviço de mídia.

- Nova conta de armazenamento (criada com a API do Gerenciador de Recursos) com suporte apenas.

- A conta de armazenamento deve existir e ter o mesmo local do serviço de mídia.

- Apenas uma conta de armazenamento pode ser especificada como principal.

Aliases |nenhum
---|---
Obrigatório? |verdadeiro
Position? |3
Valor padrão |nenhum
Aceitar entrada do Pipeline? |true(ByPropertyName)
Nome do conjunto de parâmetros |StorageAccountsParamSet
Aceitar caracteres curinga? |false

**-Tags &lt;Hashtable&gt;**

Especifica uma tabela de hash das marcações associadas ao serviço de mídia.

- Exemplo: @{"tag1"="value1";"tag2"=:value2"}

Aliases |nenhum
---|---
Obrigatório? |false
Position? |nomeado
Valor padrão |nenhum
Aceitar entrada do Pipeline? |false
Aceitar caracteres curinga? |false

**&lt;CommandParameters&gt;**

Este cmdlet oferece suporte aos parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.

### Entradas

O tipo de entrada é o tipo dos objetos que você pode enviar ao cmdlet.

### Saídas

O tipo de saída é o tipo dos objetos que o cmdlet transmite.

## Set-AzureRmMediaService

Atualiza um serviço de mídia.

### Sintaxe

	Set-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Tags <hashtable>] [-StorageAccounts <PSStorageAccount[]>]  [<CommonParameters>]

### Parâmetros

**-ResourceGroupName &lt;String&gt;**

Especifica o nome do grupo de recursos ao qual pertence este serviço de mídia.

Aliases |nenhum
---|---
Obrigatório? |verdadeiro
Position? |0
Valor Padrão |nenhum
Aceita entrada do Pipeline? |true(ByPropertyName)
Aceitar caracteres curinga? |false

**-AccountName &lt;String&gt;**

Especifica o nome do serviço de mídia.

Aliases |Name
---|---
Obrigatório? |Verdadeiro
Position? |1
Valor padrão |Nenhum
Aceitar entrada do Pipeline? |true(ByPropertyName)
Aceitar caracteres curinga? |Falso

**-StorageAccounts &lt;PSStorageAccount[]&gt;**

Especifica as contas de armazenamento associadas ao serviço de mídia.

- Nova conta de armazenamento (criada com a API do Gerenciador de Recursos) com suporte apenas.

- A conta de armazenamento deve existir e ter o mesmo local do serviço de mídia.

- Apenas uma conta de armazenamento pode ser especificada como principal.

Aliases |nenhum
---|---
Obrigatório? |false
Position? |Nomeado
Valor padrão |nenhum
Aceitar entrada do Pipeline? |true(ByPropertyName)
Nome do conjunto de parâmetros |StorageAccountsParamSet
Aceitar caracteres curinga? |false

**-Tags &lt;Hashtable&gt;**

Especifica uma tabela de hash das marcações associadas a este serviço de mídia.

- As marcações associadas ao serviço de mídia são substituídas pelo valor especificado pelo cliente.

Aliases |nenhum
---|---
Obrigatório? |Falso
Position? |Nomeado
Valor padrão |Nenhum
Aceitar entrada do Pipeline? |true(ByPropertyName)
Aceitar caracteres curinga? |false

**&lt;CommandParameters&gt;**

Este cmdlet oferece suporte aos parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.

### Entradas

O tipo de entrada é o tipo dos objetos que você pode enviar ao cmdlet.

### Saídas

O tipo de saída é o tipo dos objetos que o cmdlet transmite.

## Remove-AzureRmMediaService

Remove um serviço de mídia.

### Sintaxe

	Remove-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### Parâmetros

**-ResourceGroupName &lt;String&gt;**

Especifica o nome do grupo de recursos ao qual pertence este serviço de mídia.

Aliases |nenhum
---|---
Obrigatório? |verdadeiro
Position? |0
Valor padrão |nenhum
Aceitar entrada do Pipeline? |true(ByPropertyName)
Aceitar caracteres curinga? |false

**-AccountName &lt;String&gt;**

Especifica o nome do serviço de mídia.

Aliases |nenhum
---|---
Obrigatório? |verdadeiro
Position? |2
Valor padrão |Nenhum
Aceitar entrada do Pipeline? |true(ByPropertyName)
Aceitar caracteres curinga? |Falso

**&lt;CommandParameters&gt;**

Este cmdlet oferece suporte aos parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.

### Entradas

O tipo de entrada é o tipo dos objetos que você pode enviar ao cmdlet.

### Saídas

O tipo de saída é o tipo dos objetos que o cmdlet transmite.

## Get-AzureRmMediaService

Obtém todos os serviços de mídia em um grupo de recursos ou um serviço de mídia com um determinado nome.

### Sintaxe

ParameterSet: ResourceGroupParameterSet

	Get-AzureRmMediaService [-ResourceGroupName] <string>  [<CommonParameters>]	

ParameterSet: AccountNameParameterSet

	Get-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### Parâmetros

**-ResourceGroupName &lt;String&gt;**

Especifica o nome do grupo de recursos ao qual pertence este serviço de mídia.

Aliases |nenhum
---|---
Obrigatório? |verdadeiro
Position? |0
Valor padrão |nenhum
Aceitar entrada do Pipeline? |true(ByPropertyName)
Nome do conjunto de parâmetros |ResourceGroupParameterSet, AccountNameParameterSet
Aceitar caracteres curinga? false

**-AccountName &lt;String&gt;**

Especifica o nome do serviço de mídia.

Aliases |nenhum
---|---
Obrigatório? |verdadeiro
Position? |1
Valor padrão |nenhum
Aceitar entrada do Pipeline? |true(ByPropertyName)
Nome do conjunto de parâmetros |AccountNameParameterSet
Aceitar caracteres curinga? |false

**&lt;CommandParameters&gt;**

Este cmdlet oferece suporte aos parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.

### Entradas

O tipo de entrada é o tipo dos objetos que você pode enviar ao cmdlet.

### Saídas

O tipo de saída é o tipo dos objetos que o cmdlet transmite.

## Get-AzureRmMediaServiceKeys

Obtém as chaves de um serviço de mídia.

### Sintaxe

	Get-AzureRmMediaServiceKeys [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### Parâmetros

**-ResourceGroupName &lt;String&gt;**

Especifica o nome do grupo de recursos ao qual pertence este serviço de mídia.

Aliases |nenhum
---|---
Obrigatório? |verdadeiro
Position? |0
Valor padrão |nenhum
Aceitar entrada do Pipeline? |true(ByPropertyName)
Aceitar caracteres curinga? |false

**-AccountName &lt;String&gt;**

Especifica o nome do serviço de mídia.

Aliases |nenhum
---|---
Obrigatório? |verdadeiro
Position? |1
Valor padrão |nenhum
Aceitar entrada do Pipeline? |true(ByPropertyName)
Aceitar caracteres curinga? |false

**&lt;CommandParameters&gt;**

Este cmdlet oferece suporte aos parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.

### Entradas

O tipo de entrada é o tipo dos objetos que você pode enviar ao cmdlet.

### Saídas

O tipo de saída é o tipo dos objetos que o cmdlet transmite.

## Set-AzureRmMediaServiceKey

Regenera uma chave primária ou secundária de um serviço de mídia.

### Sintaxe

	Set-AzureRmMediaServiceKey [-ResourceGroupName] <string> [-AccountName] <string> [-KeyType] <KeyType> {Primary | Secondary}  [<CommonParameters>]

### Parâmetros

**-ResourceGroupName &lt;String&gt;**

Especifica o nome do grupo de recursos ao qual pertence este serviço de mídia.

Aliases |nenhum
---|---
Obrigatório? |verdadeiro
Position? |0
Valor padrão |nenhum
Aceitar entrada do Pipeline? |true(ByPropertyName)
Aceitar caracteres curinga? |false

**-AccountName &lt;String&gt;**

Especifica o nome do serviço de mídia.

Aliases |nenhum
---|---
Obrigatório? |verdadeiro
Position? |1
Valor padrão |nenhum
Aceitar entrada do Pipeline? |true(ByPropertyName)
Aceitar caracteres curinga? |false

**-KeyType &lt;KeyType&gt;**

Especifica o tipo de chave do serviço de mídia.

- Primária ou Secundária

Aliases |nenhum
---|---
Obrigatório? |verdadeiro
Position? |2
Valor padrão |nenhum
Aceitar entrada do Pipeline? |false
Aceitar caracteres curinga? |false

**&lt;CommandParameters&gt;**

Este cmdlet oferece suporte aos parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.

### Entradas

O tipo de entrada é o tipo dos objetos que você pode enviar ao cmdlet.

### Saídas

O tipo de saída é o tipo dos objetos que o cmdlet transmite.

## Sync-AzureRmMediaServiceStorageKeys

Sincroniza as chaves da conta de armazenamento para uma conta de armazenamento associada ao serviço de mídia.

### Sintaxe

	Sync-AzureRmMediaServiceStorageKeys [-ResourceGroupName] <string> [-MediaServiceAccountName] <string>    [-StorageAccountName] <string>  [<CommonParameters>]

### Parâmetros

**-ResourceGroupName &lt;String&gt;**

Especifica o nome do grupo de recursos ao qual pertence este serviço de mídia.

Aliases |nenhum
---|---
Obrigatório? |verdadeiro
Position? |0
Valor padrão |nenhum
Aceitar entrada do Pipeline? |true(ByPropertyName)
Aceitar caracteres curinga? |false

**-AccountName &lt;String&gt;**

Especifica o nome do serviço de mídia.

Aliases |nenhum
---|---
Obrigatório? |verdadeiro
Position? |1
Valor padrão |nenhum
Aceitar entrada do Pipeline? |true(ByPropertyName)
Aceitar caracteres curinga? |false

**-StorageAccountId &lt;String&gt;**

Especifica a conta de armazenamento associada ao serviço de mídia.

Aliases |ID
---|---
Obrigatório? |verdadeiro
Position? |2
Valor padrão |nenhum
Aceitar entrada do Pipeline? | true(ByPropertyName)
Aceitar caracteres curinga? |false

**&lt;CommandParameters&gt;**

Este cmdlet oferece suporte aos parâmetros comuns: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.

### Entradas

O tipo de entrada é o tipo dos objetos que você pode enviar ao cmdlet.

### Saídas

O tipo de saída é o tipo dos objetos que o cmdlet transmite.

## Próxima etapa 

Confira os roteiros de aprendizagem dos Serviços de Mídia.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

 

<!---HONumber=AcomDC_0907_2016-->