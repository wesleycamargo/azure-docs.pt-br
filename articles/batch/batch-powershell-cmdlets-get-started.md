<properties
   pageTitle="Introdução aos cmdlets do PowerShell do Lote do Azure | Microsoft Azure"
   description="Obter uma rápida introdução aos cmdlets do Azure PowerShell que você pode usar para gerenciar o serviço Lote do Azure"
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
   ms.date="10/13/2015"
   ms.author="danlep"/>

# Introdução aos cmdlets do PowerShell para o Azure Batch
Este artigo é uma breve introdução aos cmdlets do PowerShell do Azure que você pode usar para gerenciar suas contas do Lote e obter informações sobre os trabalhos e tarefas do Lote e outros detalhes.

Para obter a sintaxe detalhada do cmdlet, digite `get-help <Cmdlet_name>` ou consulte a [Referência de cmdlet do Azure Batch](https://msdn.microsoft.com/library/azure/mt125957.aspx).

[AZURE.INCLUDE [powershell-preview-include](../../includes/powershell-preview-include.md)]

## Pré-requisitos

* **Azure PowerShell** - Os cmdlets do Lote são fornecidos no módulo do Gerenciador de Recursos do Azure. Consulte os [cmdlets do Gerenciador de recursos do Azure](https://msdn.microsoft.com/library/azure/mt125356.aspx) para obter os pré-requisitos, as instruções de instalação e o uso básico.



* **Registre-se no namespace do provedor do Lote (operação única)** - Antes de trabalhar com suas contas do Lote, você deverá registrar-se no namespace do provedor do Lote. Essa operação só precisa ser executada uma vez por assinatura.

    ```
    Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch
    ```

## Gerenciar contas e chaves do Batch


### Criar uma conta do Batch

**New-AzureRmBatchAccount** cria uma nova conta do Lote em um grupo de recursos especificado. Se você ainda não tiver um grupo de recursos, crie um executando o cmdlet [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt603739.aspx), especificando uma das regiões do Azure no parâmetro **Location**, como “EUA Central”. Por exemplo:

```
New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"
```

Em seguida, crie uma nova conta do Lote no grupo de recursos, especificando também um nome de conta para <*nome\_da\_conta*> e um local onde o serviço em Lotes esteja disponível. A criação da conta pode levar vários minutos para ser concluída. Por exemplo:

```
New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName MyBatchResourceGroup
```

> [AZURE.NOTE]O nome da conta do Batch deve ser exclusivo no Azure, conter entre 3 e 24 caracteres e usar somente números e letras minúsculas.

### Obter chaves de acesso da conta
**Get-AzureRmBatchAccountKeys** mostra as chaves de acesso associadas a uma conta do Lote do Azure. Por exemplo, execute o seguinte para obter as chaves primária e secundária da conta criada por você.

```
$Account = Get-AzureBatchAccountKeys –AccountName <accountname>

$Account.PrimaryAccountKey

$Account.SecondaryAccountKey
```

### Gerar uma nova chave de acesso
**New-AzureRmBatchAccountKey** gera uma nova chave de conta primária ou secundária para uma conta do Lote do Azure. Por exemplo, para gerar uma nova chave primária para a sua conta do Batch, digite:

```
New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary
```

> [AZURE.NOTE]Para gerar uma nova chave secundária, especifique "Secondary" para o parâmetro **KeyType**. É necessário gerar novamente as chaves primária e secundária separadamente.

### Excluir uma conta do Batch
**Remove-AzureRmBatchAccount** exclui uma conta do Lote. Por exemplo:

```
Remove-AzureRmBatchAccount -AccountName <account_name>
```

Quando solicitado, confirme que você deseja remover a conta. A remoção de conta pode levar algum tempo para ser concluída.

## Consultar trabalhos, tarefas e outros detalhes

Use cmdlets como **Get-AzureBatchJob**, **Get-AzureBatchTask** e **Get-AzureBatchPool** para consultar as entidades criadas em uma conta do Lote.

Para usar esses cmdlets, você precisa primeiro criar um objeto AzureBatchContext para armazenar o nome e as chaves da sua conta:

```
$context = Get-AzureRmBatchAccountKeys -AccountName <account_name>
```

Você passa esse contexto para cmdlets que interagem com o serviço Batch usando o parâmetro **BatchContext**.

> [AZURE.NOTE]Por padrão, a chave primária da conta é usada para autenticação, mas você pode selecionar a chave a ser usada alterando a propriedade **KeyInUse** do seu objeto de BatchAccountContext: `$context.KeyInUse = "Secondary"`.


### Consultar dados

Por exemplo, use **Get-AzureBatchPools** para encontrar os pools. Por padrão, isso consulta todos os pools em sua conta, supondo que você já tenha armazenado o objeto BatchAccountContext em *$context*:

```
Get-AzureBatchPool -BatchContext $context
```
### Usar um filtro OData

Você pode fornecer um filtro OData usando o parâmetro **Filter** para localizar apenas os objetos de seu interesse. Por exemplo, você pode localizar todos os pools com ids que começam com "myPool":

```
$filter = "startswith(id,'myPool')"

Get-AzureBatchPool -Filter $filter -BatchContext $context
```

Esse método não é tão flexível quanto usar "Where-Object" em um pipeline local. No entanto, a consulta é enviada para o serviço Batch diretamente para que toda a filtragem ocorra no servidor, poupando largura de banda de Internet.

### Use o parâmetro de Id

Uma alternativa a um filtro OData é usar o parâmetro **ID**. Para consultar um pool específico com id "myPool":

```
Get-AzureBatchPool -Id "myPool" -BatchContext $context

```
O parâmetro **ID** só dá suporte à pesquisa de ID completo, não a curingas ou a filtros de estilo OData.

### Usar o pipeline

Os cmdlets do Batch podem aproveitar o pipeline do PowerShell para enviar dados entre cmdlets. Isso tem o mesmo efeito que especificar um parâmetro, mas facilita a listagem de várias entidades. Por exemplo, usar o seguinte localiza todas as tarefas em sua conta:

```
Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context
```

### Usar o parâmetro MaxCount

Por padrão, cada cmdlet retorna no máximo 1.000 objetos. Se você atingir esse limite, refine seu filtro para retornar menos objetos ou defina explicitamente um máximo usando o parâmetro **MaxCount**. Por exemplo:

```
Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

```

Para remover o limite superior, defina **MaxCount** como 0 ou menos.

## Tópicos relacionados
* [Baixar o PowerShell do Azure](http://go.microsoft.com/?linkid=9811175)
* [Como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md)
* [Referência de cmdlet do Azure Batch](https://msdn.microsoft.com/library/azure/mt125957.aspx)
* [Consultar o serviço em Lotes com eficiência](batch-efficient-list-queries.md)

<!---HONumber=Oct15_HO4-->