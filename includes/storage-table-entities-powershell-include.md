<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>Gerenciando entidades de tabela

Agora que você tem uma tabela, vamos examinar como gerenciar entidades ou linhas, na tabela. 

Uma entidade pode ter até 255 propriedades, incluindo três propriedades do sistema: **PartitionKey**, **RowKey** e **Timestamp**. Você é responsável por inserir e atualizar os valores de **PartitionKey** e **RowKey**. O servidor gerencia o valor de **Timestamp**, que não pode ser modificado. Juntas, **PartitionKey** e **RowKey** identificam exclusivamente cada entidade dentro de uma tabela.

* **PartitionKey**: determina a partição em que a entidade está armazenada.
* **RowKey**: identifica exclusivamente a entidade dentro da partição.

Você pode definir até 252 propriedades personalizadas para uma entidade. 

### <a name="add-table-entities"></a>Adicionar entidades de tabela

Adicione entidades a uma tabela usando **Add-StorageTableRow**. Esses exemplos usam chaves de partição com valores “partition1” e “partition2” e chaves de linha iguais para abreviações de estado. As propriedades de cada entidade são username e userid. 

```powershell
$partitionKey1 = "partition1"
$partitionKey2 = "partition2"

# add four rows 
Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey1 `
    -rowKey ("CA") -property @{"username"="Chris";"userid"=1}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey2 `
    -rowKey ("NM") -property @{"username"="Jessie";"userid"=2}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey1 `
    -rowKey ("WA") -property @{"username"="Christine";"userid"=3}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey2 `
    -rowKey ("TX") -property @{"username"="Steven";"userid"=4}
```

### <a name="query-the-table-entities"></a>Consultar as entidades de tabela

Há várias maneiras diferentes para consultar as entidades em uma tabela.

#### <a name="retrieve-all-entities"></a>Recuperar todas as entidades

Para recuperar todas as entidades, use **Get-AzureStorageTableRowAll**.

```powershell
Get-AzureStorageTableRowAll -table $storageTable | ft
```

Este comando gera resultados semelhantes à seguinte tabela:

| userid | Nome de Usuário | partition | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |
| 2 | Jessie | partition2 | NM |
| 4 | Steven | partition2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>Recuperar entidades de uma partição específica

Para recuperar todas as entidades em uma partição específica, use **Get-AzureStorageTableRowByPartitionKey**.

```powershell
Get-AzureStorageTableRowByPartitionKey -table $storageTable -partitionKey $partitionKey1 | ft
```
Os resultados são parecidos com a seguinte tabela:

| userid | Nome de Usuário | partition | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>Recuperar entidades de um valor específico em uma coluna específica

Para recuperar entidades em que o valor em uma coluna específica é igual a um valor específico, use **Get-AzureStorageTableRowByColumnName**.

```powershell
Get-AzureStorageTableRowByColumnName -table $storageTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

Essa consulta recupera um registro.

|field|valor|
|----|----|
| userid | 1 |
| Nome de Usuário | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

#### <a name="retrieve-entities-using-a-custom-filter"></a>Recuperar entidades usando um filtro personalizado 

Para recuperar entidades usando um filtro personalizado, use **Get-AzureStorageTableRowByCustomFilter**.

```powershell
Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter "(userid eq '1')"
```

Essa consulta recupera um registro.

|field|valor|
|----|----|
| userid | 1 |
| Nome de Usuário | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

### <a name="updating-entities"></a>Atualizando entidades 

Há três etapas para a atualização de entidades. Primeiro, recupere a entidade a ser alterada. Em segundo lugar, faça a alteração. Em terceiro lugar, confirme a alteração usando **Update-AzureStorageTableRow**.

Atualize a entidade com o nome de usuário = “Jessie” para ter o nome de usuário = “Jessie2”. Este exemplo também mostra outra maneira de criar um filtro personalizado usando tipos .NET. 

```powershell
# Create a filter and get the entity to be updated.
[string]$filter = `
    [Microsoft.WindowsAzure.Storage.Table.TableQuery]::GenerateFilterCondition("username",`
    [Microsoft.WindowsAzure.Storage.Table.QueryComparisons]::Equal,"Jessie")
$user = Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter $filter

# Change the entity.
$user.username = "Jessie2" 

# To commit the change, pipe the updated record into the update cmdlet.
$user | Update-AzureStorageTableRow -table $storageTable 

# To see the new record, query the table.
Get-AzureStorageTableRowByCustomFilter -table $storageTable `
    -customFilter "(username eq 'Jessie2')"
```

Os resultados mostram o registro Jessie2.

|field|valor|
|----|----|
| userid | 2 |
| Nome de Usuário | Jessie2 |
| PartitionKey | partition2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>Excluindo entidades de tabela

Você pode excluir uma entidade ou todas as entidades da tabela.

#### <a name="deleting-one-entity"></a>Excluindo uma entidade

Para excluir uma única entidade, obtenha uma referência a essa entidade e redirecione-a em **Remove-AzureStorageTableRow**.

```powershell
# Set filter.
[string]$filter = `
  [Microsoft.WindowsAzure.Storage.Table.TableQuery]::GenerateFilterCondition("username",`
  [Microsoft.WindowsAzure.Storage.Table.QueryComparisons]::Equal,"Jessie2")

# Retrieve entity to be deleted, then pipe it into the remove cmdlet.
$userToDelete = Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter 
$userToDelete | Remove-AzureStorageTableRow -table $storageTable 

# Retrieve entities from table and see that Jessie2 has been deleted.
Get-AzureStorageTableRowAll -table $storageTable | ft
```

#### <a name="delete-all-entities-in-the-table"></a>Excluir todas as entidades da tabela 

Para excluir todas as entidades da tabela, recupere-as e redirecione os resultados para o cmdlet remove. 

```powershell
# Get all rows and pipe it into the remove cmdlet.
Get-AzureStorageTableRowAll `
    -table $storageTable | Remove-AzureStorageTableRow -table $storageTable 

# List entities in the table (there won't be any).
Get-AzureStorageTableRowAll -table $storageTable | ft
```