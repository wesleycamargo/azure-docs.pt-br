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
	ms.date="11/11/2015"
	ms.author="tomfitz"/>


# Usando marcas para organizar os recursos do Azure

O Gerenciador de Recursos permite que você organize os recursos logicamente por meio da aplicação de marcas. As marcas consistem em pares de chave/valor que identificam recursos com propriedades definidas por você. Para marcar recursos como pertencentes à mesma categoria, aplique a mesma marca a esses recursos.

Quando você exibir os recursos com uma determinada marca, verá os recursos de todos os grupos de recursos. Você não está limitado aos recursos no mesmo grupo de recursos, o que permite que você organize seus recursos de forma independente das relações de implantação. As marcas podem ser particularmente úteis quando você precisar organizar os recursos de gerenciamento ou de cobrança.

Cada marca que você adiciona a um recurso ou grupo de recursos é adicionada automaticamente à taxonomia de toda a assinatura. Você também pode pré-popular a taxonomia para sua assinatura com nomes de marca, enquanto os valores que você deseja usar como recursos serão marcados no futuro.

Cada recurso ou grupo de recursos pode ter no máximo 15 marcas. O nome da marca é limitado a 512 caracteres e o valor da marca é limitado a 256 caracteres.

> [AZURE.NOTE]Você só pode aplicar marcas em recursos com suporte a operações do Gerenciador de Recursos. Se você tiver criado uma Máquina Virtual, uma Rede Virtual ou um Armazenamento por meio do modelo de implantação clássica (como por meio do Portal do Azure ou da [API de Gerenciamento de Serviço](../services/api-management/)), você não poderá aplicar uma marca a esse recurso. Será necessário implantar esses recursos novamente por meio do Gerenciador de Recursos para dar suporte à marcação. Todos os outros recursos oferecem suporte à marcação.

## Marcas em modelos

Adicionar uma marca a um recurso durante a implantação é muito fácil. Basta adicionar o elemento **marcas** ao recurso que você está implantando e fornecer o nome e o valor da marca. O nome e o valor da marca não precisam existir previamente em sua assinatura. Você pode fornecer até 15 marcas para cada recurso.

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

## Marcas no portal de visualização

Marcar recursos e grupos de recursos no portal de visualização é fácil. Use o hub Procurar para navegar até o recurso ou o grupo de recursos que você gostaria de marcar e clique na parte de Marcas na seção Visão geral, na parte superior da lâmina.

![Parte de marcas nas folhas de recurso e grupo de recursos](./media/resource-group-using-tags/tag-icon.png)

Isso abrirá uma lâmina com a lista de marcas que já foram aplicadas. Se esta for sua primeira marca, a lista estará vazia. Para adicionar uma marca, especifique um nome e valor e pressione Enter. Depois de adicionar algumas marcas, você verá opções de preenchimento automático com base em valores e nomes de marca pré-existentes, para melhor assegurar uma taxonomia consistente entre os recursos e evitar erros comuns, como erros de ortografia.

![Marcar recursos com pares de nome/valor](./media/resource-group-using-tags/tag-resources.png)

Para exibir sua taxonomia de marcas no portal, use o hub Procurar para exibir Tudo e, em seguida, selecionar Marcas.

![Localizar marcas pelo hub Procurar](./media/resource-group-using-tags/browse-tags.png)

Fixe as marcas mais importantes no seu quadro inicial para acesso rápido e você estará pronto para começar. Divirta-se!

![Fixar marcas no Quadro Inicial](./media/resource-group-using-tags/pin-tags.png)

## Marcação com o PowerShell

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]

As marcas existem diretamente em recursos e grupos de recursos, portanto, para ver quais marcas já estão aplicadas, podemos simplesmente obter um recurso ou grupo de recursos com **Get-AzureRmResource** ou **Get-AzureRmResourceGroup**. Vamos começar com um grupo de recursos.

    PS C:\> Get-AzureRmResourceGroup tag-demo

    ResourceGroupName : tag-demo
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
    Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *

    Resources         :
                    Name                             Type                                  Location
                    ===============================  ====================================  ==============
                    CPUHigh ExamplePlan              microsoft.insights/alertrules         eastus
                    ForbiddenRequests tag-demo-site  microsoft.insights/alertrules         eastus
                    LongHttpQueue ExamplePlan        microsoft.insights/alertrules         eastus
                    ServerErrors tag-demo-site       microsoft.insights/alertrules         eastus
                    ExamplePlan-tag-demo             microsoft.insights/autoscalesettings  eastus
                    tag-demo-site                    microsoft.insights/components         centralus
                    ExamplePlan                      Microsoft.Web/serverFarms             southcentralus
                    tag-demo-site                    Microsoft.Web/sites                   southcentralus


Esse cmdlet retorna vários bits de metadados sobre o grupo de recursos, incluindo quais marcas foram aplicadas, se houver. Para marcar um grupo de recursos, basta usar o comando **Set-AzureRmResourceGroup** e especificar um valor e nome de marca.

    PS C:\> Set-AzureRmResourceGroup tag-demo -Tag @( @{ Name="project"; Value="tags" }, @{ Name="env"; Value="demo"} )

    ResourceGroupName : tag-demo
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
                    Name     Value
                    =======  =====
                    project  tags
                    env      demo

As marcas são atualizadas como um todo, portanto, se você estiver adicionando uma marca a um recurso que já foi marcado, você precisará usar uma matriz com todas as marcas que você deseja manter. Para fazer isso, você pode primeiro selecionar as marcas existentes e adicionar uma nova.

    PS C:\> $tags = (Get-AzureRmResourceGroup -Name tag-demo).Tags
    PS C:\> $tags += @{Name="status";Value="approved"}
    PS C:\> Set-AzureRmResourceGroup tag-demo -Tag $tags

    ResourceGroupName : tag-demo
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
                    Name     Value
                    =======  ========
                    project  tags
                    env      demo
                    status   approved


Para remover uma ou mais marcas, apenas salve a matriz sem aquela(s) que deseja remover.

O processo é o mesmo para os recursos, exceto se você usar os cmdlets **Get-AzureRmResource** e **Set-AzureRmResource**.

Para obter os grupos de recursos com uma marca específica, use o cmdlet **Find-AzureRmResourceGroup** com o parâmetro **-Tag**.

    PS C:\> Find-AzureRmResourceGroup -Tag @{ Name="env"; Value="demo" } | %{ $_.ResourceGroupName }
    rbacdemo-group
    tag-demo

Para versões do Azure PowerShell anteriores à visualização 1.0, use os seguintes comandos para obter recursos com uma marca específica.

    PS C:\> Get-AzureResourceGroup -Tag @{ Name="env"; Value="demo" } | %{ $_.ResourceGroupName }
    rbacdemo-group
    tag-demo
    PS C:\> Get-AzureResource -Tag @{ Name="env"; Value="demo" } | %{ $_.Name }
    rbacdemo-web
    rbacdemo-docdb
    ...    

Para obter uma lista de todas as marcas dentro de uma assinatura usando o PowerShell, use o cmdlet **Get-AzureRmTag**.

    PS C:/> Get-AzureRmTag
    Name                      Count
    ----                      ------
    env                       8
    project                   1

Você pode ver as marcas que começam com "hidden-" e "link:". Elas são marcas internas, que você deve ignorar e evitar alterar.

Use o cmdlet **New-AzureRmTag** para adicionar novas marcas à taxonomia. Essas marcas serão incluídas no preenchimento automático, mesmo que elas ainda não tenham sido aplicadas a nenhum recurso ou grupo de recursos. Para remover um nome/valor de uma marca, primeiramente remova a marca de todos os recursos com os quais ela pode ser usada e, em seguida, use o cmdlet **Remove-AzureRmTag** para removê-la da taxonomia.

## Marcação com a API REST

O portal e o PowerShell usam a [API REST do Gerenciador de Recursos](https://msdn.microsoft.com/library/azure/dn848368.aspx) em segundo plano. Se você precisar integrar a marcação a outro ambiente, você pode obter marcas com um GET na ID do recurso e atualizar o conjunto de marcas com uma chamada de PATCH.


## Marcação e cobrança

Para serviços com suporte, você pode usar marcas para agrupar os dados de cobrança. Por exemplo, [Máquinas Virtuais integrado ao Gerenciador de Recursos do Azure](/virtual-machines/virtual-machines-azurerm-versus-azuresm.md) permitem definir e aplicar marcas para organizar o uso de cobrança para máquinas virtuais. Se você estiver executando várias VMs para organizações diferentes, poderá usar as marcas para o uso do grupo por centro de custo. Você também pode usar marcas para categorizar os custos pelo ambiente de tempo de execução, como por exemplo, o uso de cobrança para VMs em execução no ambiente de produção.

Você pode recuperar as informações sobre marcas por meio das [APIs RateCard e Uso de Recursos do Azure](billing-usage-rate-card-overview.md) ou do arquivo de uso (CSV) com valores separados por vírgula, que pode ser baixado no [Portal de contas do Azure](https://account.windowsazure.com/) ou no [portal de EA](https://ea.azure.com). Para obter mais informações sobre o acesso programático às informações de cobrança, veja [Obter informações sobre o consumo de recursos do Microsoft Azure](billing-usage-rate-card-overview.md). Para operações da API REST, veja a [Referência da API REST de cobrança do Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Quando você baixa o CSV de uso para serviços que dão suporte a marcas de cobrança, as marcas aparecerão na coluna **Marcas**. Para obter mais detalhes, consulte [Entenda sua fatura do Microsoft Azure](billing-understand-your-bill.md).

![Ver as marcas de cobranças](./media/resource-group-using-tags/billing_csv.png)

## Próximas etapas

- É possível aplicar restrições e convenções em sua assinatura com políticas personalizadas. A política que você definir pode exigir que uma determinada marca seja definida para todos os recursos. Para saber mais, confira [Usar a Política para gerenciar recursos e controlar o acesso](resource-manager-policy.md).
- Para obter uma introdução ao uso do Azure PowerShell ao implantar recursos, consulte [Usando o Azure PowerShell com o Gerenciador de Recursos do Azure](./powershell-azure-resource-manager.md).
- Para obter uma introdução ao uso da CLI do Azure ao implantar recursos, veja a seção [Usando a CLI do Azure para Mac, Linux e Windows com o Gerenciamento de Recursos do Azure](./xplat-cli-azure-resource-manager.md).
- Para obter uma introdução ao uso do portal de visualização, veja [Usando o portal de visualização do Azure para gerenciar os recursos do Azure](./resource-group-portal.md)  

<!---HONumber=Nov15_HO3-->