<properties
   pageTitle="Introdução aos cmdlets do PowerShell para o Azure Batch | Microsoft Azure"
   description="Introduz os cmdlets do PowerShell do Azure usados para gerenciar o serviço Azure Batch"
   services="batch"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="powershell"
   ms.workload="big-compute"
   ms.date="05/29/2015"
   ms.author="danlep"/>

# Introdução aos cmdlets do PowerShell para o Azure Batch
Este artigo é uma breve introdução aos cmdlets do PowerShell do Azure que você pode usar para gerenciar suas contas do Batch e obter informações sobre os itens de trabalho, trabalhos e tarefas do Batch.

Para obter a sintaxe detalhada do cmdlet, digite `get-help <Cmdlet_name>` ou consulte a [Referência de cmdlet do Azure Batch](https://msdn.microsoft.com/library/azure/mt125957.aspx).


## Pré-requisitos

* **Visualização do Batch** - inscreva-se para obter a [Visualização do Batch](https://account.windowsazure.com/PreviewFeatures), se ainda não fez isso, para trabalhar com o serviço.
* **PowerShell do Azure** - consulte [Como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md) para conhecer os pré-requisitos e as instruções de download e de instalação. Os cmdlets do Batch foram introduzidos na versão 0.8.10 e em versões posteriores.

## Usar os cmdlets do Batch

Use os procedimentos padrão para iniciar o PowerShell do Azure e para [conectar-se às suas assinaturas do Azure](../powershell-install-configure.md#Connect). Além disso:

* **Selecionar a assinatura do Azure** - se você tiver mais de uma assinatura, selecione a assinatura onde o recurso Visualização do Batch foi adicionado:

    ```
    Select-AzureSubscription -SubscriptionName <SubscriptionName>
    ```

* **Alterne para o modo do Gerenciador de Recursos do Azure** - os cmdlets do Batch são fornecidos no módulo do Gerenciador de Recursos do Azure. Consulte [Usando o Windows PowerShell com o Gerenciador de Recursos](../powershell-azure-resource-manager.md) para obter detalhes. Para usar esse módulo, execute o cmdlet [Switch-AzureMode](https://msdn.microsoft.com/library/dn722470.aspx):

    ```
    Switch-AzureMode -Name AzureResourceManager
    ```

## Gerenciar contas e chaves do Batch

Você pode usar os cmdlets do PowerShell do Azure para criar e gerenciar contas e chaves do Batch.

### Criar uma conta do Batch

**New-AzureBatchAccount** cria uma nova conta do Batch em um grupo de recursos especificado. Se você ainda não tiver um grupo de recursos, crie um executando o cmdlet [New-AzureResourceGroup](https://msdn.microsoft.com/library/dn654594.aspx), especificando uma das regiões do Azure no parâmetro **Location**. (Você pode encontrar as regiões disponíveis para diferentes recursos do Azure ao executar [Get-AzureLocation](https://msdn.microsoft.com/library/dn654582.aspx)). Por exemplo:

```
New-AzureResourceGroup –Name MyBatchResourceGroup –location "Central US"
```

Em seguida, crie uma nova conta do Batch no grupo de recursos, também especificando um nome de conta para <*nome_da_conta*> e um local onde o serviço Batch esteja disponível. A criação da conta pode levar vários minutos para ser concluída. Por exemplo:

```
New-AzureBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName MyBatchResourceGroup
```

> [AZURE.NOTE]O nome da conta do Batch deve ser exclusivo no Azure, conter entre 3 e 24 caracteres e usar somente números e letras minúsculas.

### Obter chaves de acesso da conta
**Get-AzureBatchAccountKeys** mostra as chaves de acesso associadas a uma conta do Azure Batch. Por exemplo, execute o seguinte para obter as chaves primária e secundária da conta criada por você.

```
$Account = Get-AzureBatchAccountKeys –AccountName <accountname>
$Account.PrimaryAccountKey
$Account.SecondaryAccountKey
```

### Gerar uma nova chave de acesso
**AzureBatchAccountKey novo** gera uma nova chave de conta primária ou secundária para uma conta do Azure Batch. Por exemplo, para gerar uma nova chave primária para a sua conta do Batch, digite:

```
New-AzureBatchAccountKey -AccountName <account_name> -KeyType Primary
```

> [AZURE.NOTE]Para gerar uma nova chave secundária, especifique "Secondary" para o parâmetro **KeyType**. É necessário gerar novamente as chaves primária e secundária separadamente.

### Excluir uma conta do Batch
**AzureBatchAccount remover** exclui uma conta do Batch. Por exemplo:

```
Remove-AzureBatchAccount -AccountName <account_name>
```

Quando solicitado, confirme que você deseja remover a conta. A remoção de conta pode levar algum tempo para ser concluída.

## Consultar itens de trabalho, trabalhos e tarefas

Use cmdlets como **Get-AzureBatchWorkItem**, **Get-AzureBatchJob**, **Get-AzureBatchTask** e **Get-AzureBatchPool** para consultar entidades criadas em uma conta do Batch.

Para usar esses cmdlets, você precisa primeiro criar um objeto AzureBatchContext para armazenar o nome e as chaves da sua conta:

```
$context = Get-AzureBatchAccountKeys "<account_name>"
```

Você passa esse contexto para cmdlets que interagem com o serviço Batch usando o parâmetro **BatchContext**.

> [AZURE.NOTE]Por padrão, a chave primária da conta é usada para autenticação, mas você pode selecionar a chave a ser usada alterando a propriedade **KeyInUse** do seu objeto de BatchAccountContext: ```$context.KeyInUse = "Secondary"```.


### Consultar dados

Por exemplo, use **Get-AzureBatchWorkItem** para localizar seus itens de trabalho. Por padrão, isso consulta todos os itens de trabalho em sua conta, supondo que você já armazenou o objeto BatchAccountContext em *$context*:

```
Get-AzureBatchWorkItem -BatchContext $context
```

O mesmo pode ser feito com outras entidades, como pools:

```
Get-AzureBatchPool -BatchContext $context
```
### Usar um filtro OData

Você pode fornecer um filtro OData usando o parâmetro **Filter** para localizar apenas os objetos de seu interesse. Por exemplo, você pode localizar todos os itens de trabalho com nomes que começam com "myWork":

```
$filter = "startswith(name,'myWork') and state eq 'active'"
Get-AzureBatchWorkItem -Filter $filter -BatchContext $context
```

Esse método não é tão flexível quanto usar "Where-Object" em um pipeline local. No entanto, a consulta é enviada para o serviço Batch diretamente para que toda a filtragem ocorra no servidor, poupando largura de banda de Internet.

### Usar o parâmetro Name

Uma alternativa a um filtro OData é usar o parâmetro **Name**. Para consultar um item de trabalho específico chamado "myWorkItem":

```
Get-AzureBatchWorkItem -Name "myWorkItem" -BatchContext $context

```
O parâmetro **Name** dá suporte apenas à pesquisa de nome completo, não a curingas ou filtros de estilo OData.

### Usar o pipeline

Os cmdlets do Batch podem aproveitar o pipeline do PowerShell para enviar dados entre cmdlets. Isso tem o mesmo efeito que especificar um parâmetro, mas facilita a listagem de várias entidades. Por exemplo, você pode encontrar todas as tarefas na sua conta:

```
Get-AzureBatchWorkItem -BatchContext $context | Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context
```

### Usar o parâmetro MaxCount

Por padrão, cada cmdlet retorna no máximo 1.000 objetos. Se atingir esse limite, você poderá refinar seu filtro para retornar menos objetos ou definir explicitamente um máximo usando o parâmetro **MaxCount**. Por exemplo:

```
Get-AzureBatchWorkItem -MaxCount 2500 -BatchContext $context

```

Para remover o limite superior, defina **MaxCount** como 0 ou menos.

## Tópicos relacionados
* [Visão geral técnica do Batch](batch-technical-overview.md)
* [Baixar o PowerShell do Azure](http://go.microsoft.com/p/?linkid=9811175)
* [Referência de cmdlet do Azure Batch](https://msdn.microsoft.com/library/azure/mt125957.aspx)
* [Consultas de lista eficientes](batch-efficient-list-queries.md)

<!---HONumber=62-->