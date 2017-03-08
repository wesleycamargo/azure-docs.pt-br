A versão 3.0 do módulo AzureRm.Resources inclui alterações significativas sobre como você trabalha com marcas. Antes de prosseguir, verifique sua versão:

```powershell
Get-Module -ListAvailable -Name AzureRm.Resources | Select Version
```

Se os resultados mostram a versão 3.0 ou superior, os exemplos neste tópico funcionam com seu ambiente. Se você não tiver a versão 3.0 ou posterior, [atualize sua versão](/powershell/azureps-cmdlets-docs/) usando a Galeria do PowerShell ou o Web Platform Installer antes de avançar com este tópico.

```powershell
Version
-------
3.5.0
```

Ao aplicar marcas a um recurso ou grupo de recursos, você pode substituir as marcas existentes nesse recurso ou grupo de recursos. Portanto, você deve usar uma abordagem diferente com base em se o recurso ou o grupo de recursos tem marcas existentes que você deseja preservar. Para adicionar marcas à:

* grupo de recursos sem marcas existentes.

  ```powershell
  Set-AzureRmResourceGroup -Name TagTestGroup -Tag @{ Dept="IT"; Environment="Test" }
  ```

* grupo de recursos com marcas existentes.

  ```powershell
  $tags = (Get-AzureRmResourceGroup -Name TagTestGroup).Tags
  $tags += @{Status="Approved"}
  Set-AzureRmResourceGroup -Tag $tags -Name TagTestGroup
  ```

* recurso sem marcas existentes.

  ```powershell
  Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceName storageexample -ResourceGroupName TagTestGroup -ResourceType Microsoft.Storage/storageAccounts
  ```

* recurso com marcas existentes.

  ```powershell
  $tags = (Get-AzureRmResource -ResourceName storageexample -ResourceGroupName TagTestGroup).Tags
  $tags += @{Status="Approved"}
  Set-AzureRmResource -Tag $tags -ResourceName storageexample -ResourceGroupName TagTestGroup -ResourceType Microsoft.Storage/storageAccounts
  ```

Para aplicar todas as marcas de um grupo de recursos em seus recursos, e **não manter as marcas existentes nos recursos**, use o seguinte script:

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups) 
{
    Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName | ForEach-Object {Set-AzureRmResource -ResourceId $_.ResourceId -Tag $g.Tags -Force } 
}
```

Para aplicar todas as marcas de um grupo de recursos em seus recursos, e **manter as marcas existentes nos recursos que não são duplicados**, use o seguinte script:

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups) 
{
    if ($g.Tags -ne $null) {
        $resources = Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName 
        foreach ($r in $resources)
        {
            $resourcetags = (Get-AzureRmResource -ResourceId $r.ResourceId).Tags
            foreach ($key in $g.Tags.Keys)
            {
                if ($resourcetags.ContainsKey($key)) { $resourcetags.Remove($key) }
            }
            $resourcetags += $g.Tags
            Set-AzureRmResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
    }
}
```

Para remover todas as marcas, passe uma tabela de hash vazio.

```powershell
Set-AzureRmResourceGroup -Tag @{} -Name TagTestGgroup
```

Para obter os grupos de recursos com uma marca específica, use o cmdlet `Find-AzureRmResourceGroup`.

```powershell
(Find-AzureRmResourceGroup -Tag @{ Dept="Finance" }).Name 
```

Para obter todos os recursos com marca e valor específicos, use o cmdlet `Find-AzureRmResource`.

```powershell
(Find-AzureRmResource -TagName Dept -TagValue Finance).Name
```

