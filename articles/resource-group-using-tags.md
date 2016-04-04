<properties
	pageTitle="Usando marcas para organizar os recursos do Azure | Microsoft Azure"
	description="Mostra como aplicar marcas para organizar os recursos para cobrança e gerenciamento."
	services="azure-resource-manager"
	documentationCenter=""
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="AzurePortal"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/07/2016"
	ms.author="tomfitz"/>


# Usando marcas para organizar os recursos do Azure

O Gerenciador de Recursos permite que você organize os recursos logicamente por meio da aplicação de marcas. As marcas consistem em pares de chave/valor que identificam recursos com propriedades definidas por você. Para marcar recursos como pertencentes à mesma categoria, aplique a mesma marca a esses recursos.

Quando você exibir os recursos com uma determinada marca, verá os recursos de todos os grupos de recursos. Você não está limitado aos recursos no mesmo grupo de recursos, o que permite que você organize seus recursos de forma independente das relações de implantação. As marcas podem ser particularmente úteis quando você precisar organizar os recursos de gerenciamento ou de cobrança.

Cada marca que você adiciona a um recurso ou grupo de recursos é adicionada automaticamente à taxonomia de toda a assinatura. Você também pode pré-popular a taxonomia para sua assinatura com nomes de marca, enquanto os valores que você deseja usar como recursos serão marcados no futuro.

Cada recurso ou grupo de recursos pode ter no máximo 15 marcas. O nome da marca é limitado a 512 caracteres e o valor da marca é limitado a 256 caracteres.

> [AZURE.NOTE] Você só pode aplicar marcas em recursos com suporte a operações do Gerenciador de Recursos. Se você tiver criado uma máquina virtual, uma rede virtual ou um armazenamento por meio do modelo de implantação clássica (como por meio do portal do Azure ou da API de Gerenciamento de Serviços), não poderá aplicar uma marca a esse recurso. Será necessário implantar esses recursos novamente por meio do Gerenciador de Recursos para dar suporte à marcação. Todos os outros recursos oferecem suporte à marcação.

## Marcas em modelos

Para marcar um recurso durante a implantação, basta adicionar o elemento **marcas** ao recurso que você está implantando e fornecer o nome e o valor da marca. O nome e o valor da marca não precisam existir previamente em sua assinatura. Você pode fornecer até 15 marcas para cada recurso.

O exemplo a seguir mostra uma conta de armazenamento com uma marca.

    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2015-06-15",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "tags": {
                "dept": "Finance"
            },
            "properties": 
            {
                "accountType": "Standard_LRS"
            }
        }
    ]

Atualmente, o Gerenciador de Recursos não dá suporte ao processamento de um objeto para os valores e nomes de marca. Em vez disso, você pode transmitir um objeto para os valores de marca, porém ainda é necessário especificar os nomes de marca conforme mostrado abaixo.

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "tagvalues": {
          "type": "object",
          "defaultValue": {
            "dept": "Finance",
            "project": "Test"
          }
        }
      },
      "resources": [
      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Storage/storageAccounts",
        "name": "examplestorage",
        "tags": {
          "dept": "[parameters('tagvalues').dept]",
          "project": "[parameters('tagvalues').project]"
        },
        "location": "[resourceGroup().location]",
        "properties": {
          "accountType": "Standard_LRS"
        }
      }]
    }


## Marcas no portal

Você pode adicionar marcas aos recursos e grupos de recursos existentes no portal. Use o hub Procurar para navegar até o recurso ou o grupo de recursos que você gostaria de marcar e clique na parte de Marcas na seção Visão geral, na parte superior da lâmina.

![Parte de marcas nas folhas de recurso e grupo de recursos](./media/resource-group-using-tags/tag-icon.png)

Isso abrirá uma lâmina com a lista de marcas que já foram aplicadas. Se esta for sua primeira marca, a lista estará vazia. Para adicionar uma marca, especifique um nome e valor e pressione Enter. Depois de adicionar algumas marcas, você verá opções de preenchimento automático com base em valores e nomes de marca pré-existentes, para melhor assegurar uma taxonomia consistente entre os recursos e evitar erros comuns, como erros de ortografia.

![Marcar recursos com pares de nome/valor](./media/resource-group-using-tags/tag-resources.png)

Para exibir sua taxonomia de marcas no portal, use o hub Procurar para exibir Tudo e, em seguida, selecionar Marcas.

![Localizar marcas pelo hub Procurar](./media/resource-group-using-tags/browse-tags.png)

Fixe as marcas mais importantes no seu quadro inicial para acesso rápido e você estará pronto para começar. Divirta-se!

![Fixar marcas no Quadro Inicial](./media/resource-group-using-tags/pin-tags.png)

## Marcas e PowerShell

As marcas existem diretamente em recursos e grupos de recursos; portanto, para ver quais marcas já estão aplicadas, podemos simplesmente obter um recurso ou grupo de recursos com **Get-AzureRmResource** ou **Get-AzureRmResourceGroup**. Vamos começar com um grupo de recursos.

    PS C:\> Get-AzureRmResourceGroup -Name tag-demo-group

    ResourceGroupName : tag-demo-group
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
                    Name         Value
                    ===========  ==========
                    Dept         Finance
                    Environment  Production


Esse cmdlet retorna vários bits de metadados sobre o grupo de recursos, incluindo quais marcas foram aplicadas, se houver.

Ao obter metadados para um recurso, as marcas não serão exibidas diretamente. Você verá abaixo que as marcas são exibidas apenas como objeto Hashtable.

    PS C:\> Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName tag-demo-group

    Name              : tfsqlserver
    ResourceId        : /subscriptions/{guid}/resourceGroups/tag-demo-group/providers/Microsoft.Sql/servers/tfsqlserver
    ResourceName      : tfsqlserver
    ResourceType      : Microsoft.Sql/servers
    Kind              : v12.0
    ResourceGroupName : tag-demo-group
    Location          : westus
    SubscriptionId    : {guid}
    Tags              : {System.Collections.Hashtable}

Você pode exibir as marcas reais recuperando a propriedade **Tags**.

    PS C:\> (Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName tag-demo-group).Tags | %{ $_.Name + ": " + $_.Value }
    Dept: Finance
    Environment: Production

Em vez de exibir as marcas de um determinado recurso ou grupo de recursos, você geralmente vai preferir recuperar todos os recursos ou grupos de recursos que tenham uma determinada marca e valor. Para obter os grupos de recursos com uma marca específica, use o cmdlet **Find-AzureRmResourceGroup** com o parâmetro **-Tag**.

    PS C:\> Find-AzureRmResourceGroup -Tag @{ Name="Dept"; Value="Finance" } | %{ $_.Name }
    tag-demo-group
    web-demo-group

Para obter todos os recursos com uma determinada marca e valor, use o cmdlet **Find-AzureRmResource**.

    PS C:\> Find-AzureRmResource -TagName Dept -TagValue Finance | %{ $_.ResourceName }
    tfsqlserver
    tfsqldatabase

Para adicionar uma marca a um grupo de recursos que não tem marcas existentes, basta usar o comando **Set-AzureRmResourceGroup** e especificar um objeto de marca.

    PS C:\> Set-AzureRmResourceGroup -Name test-group -Tag @( @{ Name="Dept"; Value="IT" }, @{ Name="Environment"; Value="Test"} )

    ResourceGroupName : test-group
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
                    Name          Value
                    =======       =====
                    Dept          IT
                    Environment   Test
                    
Você pode adicionar marcas a um recurso que não tem marcas existentes usando o comando **SetAzureRmResource**

    PS C:\> Set-AzureRmResource -Tag @( @{ Name="Dept"; Value="IT" }, @{ Name="Environment"; Value="Test"} ) -ResourceId /subscriptions/{guid}/resourceGroups/test-group/providers/Microsoft.Web/sites/examplemobileapp

As marcas são atualizadas como um todo, portanto, se você estiver adicionando uma marca a um recurso que já foi marcado, você precisará usar uma matriz com todas as marcas que você deseja manter. Para fazer isso, primeiro selecione as marcas existentes, adicione uma nova ao conjunto e reaplique todas as marcas.

    PS C:\> $tags = (Get-AzureRmResourceGroup -Name tag-demo).Tags
    PS C:\> $tags += @{Name="status";Value="approved"}
    PS C:\> Set-AzureRmResourceGroup -Name test-group -Tag $tags

Para remover uma ou mais marcas, apenas salve a matriz sem aquela(s) que deseja remover.

O processo é o mesmo para os recursos, exceto se você usar os cmdlets **Get-AzureRmResource** e **Set-AzureRmResource**.

Para obter uma lista de todas as marcas dentro de uma assinatura usando o PowerShell, use o cmdlet **Get-AzureRmTag**.

    PS C:/> Get-AzureRmTag
    Name                      Count
    ----                      ------
    env                       8
    project                   1

Você pode ver as marcas que começam com "hidden-" e "link:". Elas são marcas internas, que você deve ignorar e evitar alterar.

Use o cmdlet **New-AzureRmTag** para adicionar novas marcas à taxonomia. Essas marcas serão incluídas no preenchimento automático, mesmo que elas ainda não tenham sido aplicadas a nenhum recurso ou grupo de recursos. Para remover um nome/valor de uma marca, primeiramente remova a marca de todos os recursos com os quais ela pode ser usada e, em seguida, use o cmdlet **Remove-AzureRmTag** para removê-la da taxonomia.

## Marcas e CLI do Azure

As marcas existem diretamente em recursos e grupos de recursos, portanto, para ver quais marcas já estão aplicadas, podemos simplesmente obter um grupo de recursos com **azure group show**.

    azure group show -n tag-demo-group
    info:    Executing command group show
    + Listing resource groups
    + Listing resources for the group
    data:    Id:                  /subscriptions/{guid}/resourceGroups/tag-demo-group
    data:    Name:                tag-demo-group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: Dept=Finance;Environment=Production
    data:    Resources:
    data:
    data:      Id      : /subscriptions/{guid}/resourceGroups/tag-demo-group/providers/Microsoft.Sql/servers/tfsqlserver
    data:      Name    : tfsqlserver
    data:      Type    : servers
    data:      Location: eastus2
    data:      Tags    : Dept=Finance;Environment=Production
    ...

Para obter as marcas somente para o grupo de recursos, use um utilitário JSON, como [jq](http://stedolan.github.io/jq/download/).

    azure group show -n tag-demo-group --json | jq ".tags"
    {
      "Dept": "Finance",
      "Environment": "Production" 
    }

Você pode exibir as marcas de um recurso específico usando **azure resource show**.

    azure resource show -g tag-demo-group -n tfsqlserver -r Microsoft.Sql/servers -o 2014-04-01-preview --json | jq ".tags"
    {
      "Dept": "Finance",
      "Environment": "Production"
    }
    
Você pode recuperar todos os recursos com determinada marca e valor, conforme mostrado abaixo.

    azure resource list --json | jq ".[] | select(.tags.Dept == "Finance") | .name"
    "tfsqlserver"
    "tfsqlserver/tfsqldata"

As marcas são atualizadas como um todo, portanto, se você estiver adicionando uma marca a um recurso que já foi marcado, você precisará recuperar todas as marcas existentes que desejar manter. A fim de definir valores de marca para um grupo de recursos, use **azure group set** e forneça todas as marcas para o grupo de recursos.

    azure group set -n tag-demo-group -t Dept=Finance;Environment=Production;Project=Upgrade
    info:    Executing command group set
    ...
    data:    Name:                tag-demo-group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: Dept=Finance;Environment=Production;Project=Upgrade
    ...
    
Você pode listar as marcas existentes em sua assinatura com **azure tag list** e adicionar uma nova marca com **azure tag create**. Para remover uma marca de taxonomia de sua assinatura, primeiro remova a marca dos recursos que podem ser usados com ele e remova a marca com **azure tag delete**.

## Marcas e API REST

O portal e o PowerShell usam a [API REST do Gerenciador de Recursos](https://msdn.microsoft.com/library/azure/dn848368.aspx) em segundo plano. Se você precisar integrar a marcação a outro ambiente, você pode obter marcas com um GET na ID do recurso e atualizar o conjunto de marcas com uma chamada de PATCH.


## Marcas e cobrança

Para serviços com suporte, você pode usar marcas para agrupar os dados de cobrança. Por exemplo, [Máquinas Virtuais integrado ao Gerenciador de Recursos do Azure](./virtual-machines/virtual-machines-windows-compare-deployment-models.md) permitem definir e aplicar marcas para organizar o uso de cobrança para máquinas virtuais. Se você estiver executando várias VMs para organizações diferentes, poderá usar as marcas para o uso do grupo por centro de custo. Você também pode usar marcas para categorizar os custos pelo ambiente de tempo de execução, como por exemplo, o uso de cobrança para VMs em execução no ambiente de produção.

Você pode recuperar as informações sobre marcas por meio das [APIs RateCard e Uso de Recursos do Azure](billing-usage-rate-card-overview.md) ou do arquivo de uso (CSV) com valores separados por vírgula, que pode ser baixado no [Portal de contas do Azure](https://account.windowsazure.com/) ou no [portal de EA](https://ea.azure.com). Para saber mais sobre o acesso programático às informações de cobrança, confira [Obter informações sobre o consumo de recursos do Microsoft Azure](billing-usage-rate-card-overview.md). Para operações de API REST, confira [Referência da API REST de cobrança do Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Quando você baixa o CSV de uso para serviços que dão suporte a marcas de cobrança, as marcas aparecerão na coluna **Marcas**. Para obter mais detalhes, consulte [Entenda sua fatura do Microsoft Azure](billing-understand-your-bill.md).

![Ver as marcas de cobranças](./media/resource-group-using-tags/billing_csv.png)

## Próximas etapas

- É possível aplicar restrições e convenções em sua assinatura com políticas personalizadas. A política que você definir pode exigir que uma determinada marca seja definida para todos os recursos. Para saber mais, confira [Usar a Política para gerenciar recursos e controlar o acesso](resource-manager-policy.md).
- Para obter uma introdução ao uso do Azure PowerShell ao implantar recursos, confira [Usando o Azure PowerShell com o Gerenciador de Recursos do Azure](./powershell-azure-resource-manager.md).
- Para obter uma introdução ao uso da CLI do Azure ao implantar recursos, confira [Usando a CLI do Azure para Mac, Linux e Windows com o Gerenciamento de Recursos do Azure](./xplat-cli-azure-resource-manager.md).
- Para obter uma introdução ao uso do portal, confira [Usando o portal do Azure para gerenciar os recursos do Azure](./azure-portal/resource-group-portal.md)  

<!---HONumber=AcomDC_0323_2016-->