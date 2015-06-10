<properties
	pageTitle="Introdução aos cmdlets do PowerShell do Azure em lotes"
	description="Introduz os cmdlets do PowerShell do Azure usados para gerenciar o serviço de lote do Azure"
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor="yidingz"/>

<tags
	ms.service="batch"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="powershell"
	ms.workload="big-compute"
	ms.date="04/15/2015"
	ms.author="danlep"/>

# Introdução aos cmdlets do PowerShell do Azure em lotes
Este artigo é uma breve introdução aos cmdlets do PowerShell do Azure que você pode usar para gerenciar suas contas de lote e obter informações sobre seus itens de trabalho em lotes, trabalhos e tarefas.

Para obter a sintaxe detalhada do cmdlet, digite ```get-help <Cmdlet_name>```.


## Pré-requisitos

* **Visualização de lote** -Inscreva-se para o [visualização de lote](https://account.windowsazure.com/PreviewFeatures), se você ainda não fez isso, trabalhar com o serviço.
* **PowerShell do azure** -consulte [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para pré-requisitos e as instruções de download e instalação. Cmdlets de lote foram introduzidas na versão 0.8.10 e versões posteriores.

## Usar os cmdlets do lote

Use os procedimentos padrão para iniciar o PowerShell do Azure e [suas assinaturas do Azure se conectem](../powershell-install-configure.md#Connect). Além disso:

* **Assinatura do Azure selecione** - se você tiver mais de assinatura, selecione a assinatura onde você adicionou o recurso de visualização de lote:

    ```
    Select-AzureSubscription -SubscriptionName <SubscriptionName>
    ```

* **Alternar para o modo de AzureResourceManage** -enviar o lote cmdlets no módulo do Gerenciador de recursos do Azure. Consulte [usando o Windows PowerShell com o Gerenciador de recursos](../powershell-azure-resource-manager.md) para obter detalhes. Para usar este módulo, execute o [Switch-AzureMode](https://msdn.microsoft.com/library/dn722470.aspx) cmdlet:

    ```
    Switch-AzureMode -Name AzureResourceManager
    ```

## Gerenciar contas de lote e chaves

Você pode usar os cmdlets do PowerShell do Azure para criar e gerenciar contas de lote e chaves.

### Criar uma conta de lote

**AzureBatchAccount novo** cria uma nova conta de lote em um grupo de recursos especificado. Se você ainda não tiver um grupo de recursos, crie um executando o [New-AzureResourceGroup](https://msdn.microsoft.com/library/dn654594.aspx) cmdlet, especificando uma das regiões do Azure no **local** parâmetro. (Você pode encontrar regiões disponíveis para os diferentes recursos do Azure executando [Get-AzureLocation](https://msdn.microsoft.com/library/dn654582.aspx).) Por exemplo:

```
New-AzureResourceGroup –Name MyBatchResourceGroup –location "Central US"
```

Em seguida, crie uma nova conta de lote no grupo de recursos, também, especificando um nome de conta para < * * nome_da_conta > e um local onde o serviço de lote está disponível. Criação da conta pode levar vários minutos para ser concluída. Por exemplo:

```
New-AzureBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName MyBatchResourceGroup
```

> [AZURE.NOTE]O nome da conta de lote deve ser exclusivo no Azure, conter entre 3 e 24 caracteres e usar somente números e letras minúsculas.

### Obter chaves de acesso da conta
**Get-AzureBatchAccountKeys** mostra as chaves de acesso associadas a uma conta de lote do Azure. Por exemplo, execute o seguinte para obter as chaves primárias e secundárias da conta criada por você.

```
$Account = Get-AzureBatchAccountKeys –AccountName <accountname>
$Account.PrimaryAccountKey
$Account.SecondaryAccountKey
```

### Gerar uma nova chave de acesso
**AzureBatchAccountKey novo** gera uma nova chave de conta primária ou secundária de uma conta de lote do Azure. Por exemplo, para gerar uma nova chave primária para a sua conta em lotes, digite:

```
New-AzureBatchAccountKey -AccountName <account_name> -KeyType Primary
```

> [AZURE.NOTE]Para gerar uma nova chave secundária, especifique "Secondary" para o **KeyType** parâmetro. É necessário regenerar as chaves primárias e secundárias separadamente.

### Excluir uma conta de lote
**AzureBatchAccount remover** exclui uma conta de lote. Por exemplo:

```
Remove-AzureBatchAccount -AccountName <account_name>
```

Quando solicitado, confirme que deseja remover a conta. Remoção de conta pode levar algum tempo para ser concluída.

## Consultar itens de trabalho, trabalhos e tarefas

Como usar os cmdlets **Get-AzureBatchWorkItem**, **Get-AzureBatchJob**, **Get-AzureBatchTask**, e **Get-AzureBatchPool** para consultar entidades criadas em uma conta de lote.

Para usar esses cmdlets, você precisa primeiro criar um objeto AzureBatchContext para armazenar o nome de conta e chaves:

```
$context = Get-AzureBatchAccountKeys "<account_name>"
```

Você passar esse contexto para cmdlets que interagem com o serviço de lote usando o **BatchContext** parâmetro.

> [AZURE.NOTE]Por padrão, a chave primária da conta é usado para autenticação, mas você pode selecionar a chave para usar alterando seu objeto de BatchAccountContext **KeyInUse** propriedade: ```$context.KeyInUse = "Secondary"```.


### Consulta de dados

Por exemplo, use **Get-AzureBatchWorkItem** para localizar seus itens de trabalho. Por padrão essa consulta para todos os itens de trabalho em sua conta, supondo que você já armazenado o objeto BatchAccountContext *$context*:

```
Get-AzureBatchWorkItem -BatchContext $context
```

O mesmo pode ser feito com outras entidades, como pools de:

```
Get-AzureBatchPool -BatchContext $context
```
### Usar um filtro OData

Você pode fornecer um filtro de OData usando o **filtro** parâmetro para localizar apenas os objetos que você está interessado. Por exemplo, você pode localizar todos os itens de trabalho com nomes que começam com "myWork":

```
$filter = "startswith(name,'myWork') and state eq 'active'" 
Get-AzureBatchWorkItem -Filter $filter -BatchContext $context
``` 

Esse método não é tão flexível quanto usar "Where-Object" em um pipeline de local. No entanto, a consulta é enviada para o serviço de lote diretamente para que toda a filtragem ocorre no lado do servidor, poupando largura de banda de Internet.

### Use o parâmetro Name

Uma alternativa para um filtro OData é usar o **nome** parâmetro. Para consultar um item de trabalho específico chamado "myWorkItem":

``` 
Get-AzureBatchWorkItem -Name "myWorkItem" -BatchContext $context 

```
O **nome** parâmetro oferece suporte apenas a pesquisa de nome completo, curingas não ou filtros de estilo do OData.

### Use o pipleline

Cmdlets de lote podem aproveitar o pipeline do PowerShell para enviar dados entre cmdlets. Isso tem o mesmo efeito que especificar um parâmetro, mas torna listando várias entidades mais fácil. Por exemplo, você pode encontrar todas as tarefas na sua conta:

```
Get-AzureBatchWorkItem -BatchContext $context | Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context 
```

### Use o parâmetro MaxCount

Por padrão, cada cmdlet retorna no máximo 1000 objetos. Se você atingir esse limite, você pode refinar o filtro para trazer de volta menos objetos ou definir explicitamente um máximo usando o **MaxCount** parâmetro. Por exemplo:

```
Get-AzureBatchWorkItem -MaxCount 2500 -BatchContext $context 

```

Para remover o limite superior, defina **MaxCount** como 0 ou menos.

## Tópicos relacionados
* [Visão geral técnica do lote](batch-technical-overview.md)
* [Download do PowerShell do Azure](http://go.microsoft.com/p/?linkid=9811175)
* [Referência de cmdlet do Azure](https://msdn.microsoft.com/library/jj554330.aspx)

<!---HONumber=GIT-SubDir-->