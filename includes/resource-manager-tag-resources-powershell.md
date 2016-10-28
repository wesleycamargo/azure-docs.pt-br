### Alterações do cmdlet de marcação na versão mais recente do PowerShell

A versão de agosto de 2016 do [Azure PowerShell 2.0][powershell] inclui alterações significativas no trabalho com marcações. Antes de continuar, verifique a versão do módulo AzureRm.Resources.

    Get-Module -ListAvailable -Name AzureRm.Resources | Select Version

Se a última atualização do Azure PowerShell foi feita antes de agosto de 2016, os resultados deverão mostrar uma versão inferior a 3.0.

    Version
    -------
    2.0.2

Se você atualizou o Azure PowerShell desde agosto de 2016, os resultados deverão mostrar uma versão 3.0.

    Version
    -------
    3.0.1
    
Se sua versão do módulo é 3.0.1 ou posterior, você tem os cmdlets mais recentes para trabalhar com marcações. Esta versão do módulo de recursos do Azure é instalada automaticamente quando você instala ou atualiza o Azure PowerShell usando a Galeria do PowerShell, o Web Platform Installer ou o PowerShellGet. Se sua versão é anterior a 3.0.1, você pode continuar usando essa versão, mas pode considerar atualizá-la para a versão mais recente. A versão mais recente inclui alterações que facilitam o trabalho com marcações. As duas abordagens são mostradas neste tópico.

### Atualizando o script para as alterações na versão mais recente 

Na versão mais recente, o nome de parâmetro **Tags** foi alterado para **Tag** e o tipo foi alterado de **Hashtable** para **Hashtable**. Não é mais necessário fornecer o **Nome** nem o **Valor** de cada entrada. Em vez disso, você pode fornecer pares chave-valor no formato **Chave = "Valor"**.

Para atualizar o script existente, altere o parâmetro **Tags** para **Tag** e altere o formato de marcação, conforme mostrado no exemplo a seguir.

    # Old
    New-AzureRmResourceGroup -Tags @{ Name = "testtag"; Value = "testval" } -Name $resourceGroupName -Location $location

    # New
    New-AzureRmResourceGroup -Tag @{ testtag = "testval" } -Name $resourceGroupName -Location $location 

No entanto, é necessário observar que os grupos de recursos e os recursos ainda retornarão uma propriedade **Tags** em seus metadados. Essa propriedade não é alterada.

### Versão 3.0.1 ou posterior

Marcações existem diretamente em recursos e grupos de recursos. Para ver as marcações existentes, exiba um recurso com **Get-AzureRmResource** ou um ou grupo de recursos com **Get-AzureRmResourceGroup**.

Vamos começar com um grupo de recursos.

    Get-AzureRmResourceGroup -Name testrg1

Esse cmdlet retorna vários bits de metadados sobre o grupo de recursos, incluindo quais marcas foram aplicadas, se houver.

    ResourceGroupName : testrg1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
                    Name         Value
                    ===========  ==========
                    Dept         Finance
                    Environment  Production

Para recuperar os metadados de recursos, incluindo marcações, use o exemplo a seguir.

    Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName testrg1

Você vê os nomes de marcação nos resultados.

    Name              : tfsqlserver
    ResourceId        : /subscriptions/{guid}/resourceGroups/tag-demo-group/providers/Microsoft.Sql/servers/tfsqlserver
    ResourceName      : tfsqlserver
    ResourceType      : Microsoft.Sql/servers
    Kind              : v12.0
    ResourceGroupName : testrg1
    Location          : westus
    SubscriptionId    : {guid}
    Tags              : {Dept, Environment}

Use a propriedade **Tags** para obter nomes de marcação e valores.

    (Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName testrg1).Tags

Isso retorna os seguintes resultados:

    Name                   Value
    ----                   -----
    Dept                   Finance
    Environment            Production

Em vez de exibir as marcações de um determinado recurso ou grupo de recursos, você geralmente vai preferir recuperar todos os recursos ou grupos de recursos com uma determinada marcação e valor. Para obter os grupos de recursos com uma marca específica, use o cmdlet **Find-AzureRmResourceGroup** com o parâmetro **-Tag**.

Para recuperar grupos de recursos com um valor de marcação, use o formato a seguir.

    (Find-AzureRmResourceGroup -Tag @{ Dept="Finance" }).Name 

Para obter todos os recursos com uma determinada marcação e valor, use o cmdlet **Find-AzureRmResource**.

    (Find-AzureRmResource -TagName Dept -TagValue Finance).Name
    
Para adicionar uma marcação a um grupo de recursos sem marcações existentes, use o comando **Set-AzureRmResourceGroup** e especifique um objeto de marcação.

    Set-AzureRmResourceGroup -Name test-group -Tag @{ Dept="IT"; Environment="Test" }

Ele retorna o grupo de recursos com seus novos valores de marcação.

    ResourceGroupName : test-group
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
                    Name          Value
                    =======       =====
                    Dept          IT
                    Environment   Test
                    
Você pode adicionar marcações a um recurso sem marcações existentes usando o comando **Set-AzureRmResource**

    Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceId /subscriptions/{guid}/resourceGroups/test-group/providers/Microsoft.Web/sites/examplemobileapp

Marcações são atualizadas como um todo. Para adicionar uma marcação a um recurso que tem outras marcações, use uma matriz com todas as marcações que você deseja manter. Primeiro, selecione as marcações existentes, adicione uma a esse conjunto e reaplique todas as marcações.

    $tags = (Get-AzureRmResourceGroup -Name tag-demo).Tags
    $tags += @{Status="approved"}
    Set-AzureRmResourceGroup -Name test-group -Tag $tags

Para remover uma ou mais marcas, apenas salve a matriz sem aquela(s) que deseja remover.

O processo é o mesmo para os recursos, exceto que você usa os cmdlets **Get-AzureRmResource** e **Set-AzureRmResource**.

Para obter uma lista de todas as marcas dentro de uma assinatura usando o PowerShell, use o cmdlet **Get-AzureRmTag**.

    Get-AzureRmTag
    
Isso retorna os nomes de marcação e uma contagem do número de recursos e grupos de recursos com a marcação

    Name                      Count
    ----                      ------
    Dept                       8
    Environment                8

Você pode ver as marcas que começam com "hidden-" e "link:". Elas são marcações internas, que você deve ignorar e evitar alterar.

Use o cmdlet **New-AzureRmTag** para adicionar novas marcas à taxonomia. Essas marcações estão incluídas no preenchimento automático, mesmo que elas ainda não tenham sido aplicadas a nenhum recurso ou grupo de recursos. Para remover um nome/valor de uma marca, primeiramente remova a marca de todos os recursos com os quais ela pode ser usada e, em seguida, use o cmdlet **Remove-AzureRmTag** para removê-la da taxonomia.

### Versões anteriores à 3.0.1

Marcações existem diretamente em recursos e grupos de recursos. Para ver as marcações existentes, exiba um recurso com **Get-AzureRmResource** ou um ou grupo de recursos com **Get-AzureRmResourceGroup**.

Vamos começar com um grupo de recursos.

    Get-AzureRmResourceGroup -Name testrg1

Esse cmdlet retorna vários bits de metadados sobre o grupo de recursos, incluindo quais marcas foram aplicadas, se houver.

    ResourceGroupName : testrg1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
                    Name         Value
                    ===========  ==========
                    Dept         Finance
                    Environment  Production
                    
Para recuperar os metadados de recursos, use o exemplo a seguir. Os metadados de recursos não exibem marcações diretamente.

    Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName testrg1

Você verá nos resultados que as marcações são exibidas apenas como objeto Hashtable.

    Name              : tfsqlserver
    ResourceId        : /subscriptions/{guid}/resourceGroups/tag-demo-group/providers/Microsoft.Sql/servers/tfsqlserver
    ResourceName      : tfsqlserver
    ResourceType      : Microsoft.Sql/servers
    Kind              : v12.0
    ResourceGroupName : tag-demo-group
    Location          : westus
    SubscriptionId    : {guid}
    Tags              : {System.Collections.Hashtable}

Você pode exibir as marcações reais recuperando a propriedade **Tags**.

    (Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName tag-demo-group).Tags | %{ $_.Name + ": " + $_.Value }
   
Ela retorna resultados formatados:
    
    Dept: Finance
    Environment: Production
    
Em vez de exibir as marcações de um determinado recurso ou grupo de recursos, você geralmente vai preferir recuperar todos os recursos ou grupos de recursos com uma determinada marcação e valor. Para obter os grupos de recursos com uma marca específica, use o cmdlet **Find-AzureRmResourceGroup** com o parâmetro **-Tag**.

Para recuperar grupos de recursos com um valor de marcação, use o formato a seguir.

    Find-AzureRmResourceGroup -Tag @{ Name="Dept"; Value="Finance" } | %{ $_.Name }
    
Para obter todos os recursos com marcação e valor específicos, use o cmdlet Find-AzureRmResource.

    Find-AzureRmResource -TagName Dept -TagValue Finance | %{ $_.ResourceName }

Para adicionar uma marcação a um grupo de recursos sem marcações existentes, basta usar o comando Set-AzureRmResourceGroup e especificar um objeto de marcação.

    Set-AzureRmResourceGroup -Name test-group -Tag @( @{ Name="Dept"; Value="IT" }, @{ Name="Environment"; Value="Test"} )
    
Ele retorna o grupo de recursos com seus novos valores de marcação.

    ResourceGroupName : test-group
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
                Name          Value
                =======       =====
                Dept          IT
                Environment   Test

Você pode adicionar marcações a um recurso sem marcações existentes usando o comando Set-AzureRmResource.

    Set-AzureRmResource -Tag @( @{ Name="Dept"; Value="IT" }, @{ Name="Environment"; Value="Test"} ) -ResourceId /subscriptions/{guid}/resourceGroups/test-group/providers/Microsoft.Web/sites/examplemobileapp

Marcações são atualizadas como um todo. Para adicionar uma marcação a um recurso que tem outras marcações, use uma matriz com todas as marcações que você deseja manter. Primeiro, selecione as marcações existentes, adicione uma a esse conjunto e reaplique todas as marcações.

    $tags = (Get-AzureRmResourceGroup -Name tag-demo).Tags
    $tags += @{Name="status";Value="approved"}
    Set-AzureRmResourceGroup -Name test-group -Tag $tags

Para remover uma ou mais marcas, apenas salve a matriz sem aquela(s) que deseja remover.

O processo é o mesmo para os recursos, exceto que são utilizados os cmdlets Get-AzureRmResource e Set-AzureRmResource.

Para obter uma lista de todas as marcas dentro de uma assinatura usando o PowerShell, use o cmdlet **Get-AzureRmTag**.

    Get-AzureRmTag
    
Isso retorna os nomes de marcação e uma contagem do número de recursos e grupos de recursos com a marcação

    Name                      Count
    ----                      ------
    Dept                       8
    Environment                8

Você pode ver as marcas que começam com "hidden-" e "link:". Elas são marcações internas, que você deve ignorar e evitar alterar.

Use o cmdlet **New-AzureRmTag** para adicionar novas marcas à taxonomia. Essas marcações estão incluídas no preenchimento automático, mesmo que elas ainda não tenham sido aplicadas a nenhum recurso ou grupo de recursos. Para remover um nome/valor de uma marca, primeiramente remova a marca de todos os recursos com os quais ela pode ser usada e, em seguida, use o cmdlet **Remove-AzureRmTag** para removê-la da taxonomia.


[powershell]: https://msdn.microsoft.com/library/mt619274(v=azure.200).aspx

<!---HONumber=AcomDC_0907_2016-->