<properties
    pageTitle="Usando marcas para organizar os recursos do Azure | Microsoft Azure"
    description="Mostra como aplicar marcas para organizar os recursos para cobrança e gerenciamento."
    services="azure-resource-manager"
    documentationCenter=""
    authors="tfitzmac"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="AzurePortal"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/08/2016"
    ms.author="tomfitz"/>



# <a name="using-tags-to-organize-your-azure-resources"></a>Usando marcas para organizar os recursos do Azure

O Gerenciador de Recursos permite que você organize os recursos logicamente por meio da aplicação de marcas. As marcas consistem em pares de chave/valor que identificam recursos com propriedades definidas por você. Para marcar recursos como pertencentes à mesma categoria, aplique a mesma marca a esses recursos.

Quando você exibir os recursos com uma determinada marcação, verá os recursos de todos os grupos de recursos. Você não está limitado aos recursos no mesmo grupo de recursos, o que permite que você organize seus recursos de forma independente das relações de implantação. As marcações podem ser úteis quando você precisar organizar os recursos de gerenciamento ou de cobrança.

Cada marca que você adiciona a um recurso ou grupo de recursos é adicionada automaticamente à taxonomia de toda a assinatura. Você também pode pré-popular a taxonomia para sua assinatura com nomes de marca, enquanto os valores que você deseja usar como recursos serão marcados no futuro.

Cada recurso ou grupo de recursos pode ter no máximo 15 marcas. O nome da marca é limitado a 512 caracteres e o valor da marca é limitado a 256 caracteres.

> [AZURE.NOTE] Você só pode aplicar marcas em recursos com suporte a operações do Gerenciador de Recursos. Se você tiver criado uma máquina virtual, uma rede virtual ou um armazenamento por meio do modelo de implantação clássica (como por meio do portal clássico do Azure), não poderá aplicar uma marcação a esse recurso. Para dar suporte à marcação, implante esses recursos novamente por meio do Resource Manager. Todos os outros recursos oferecem suporte à marcação.

## <a name="templates"></a>Modelos

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

Atualmente, o Gerenciador de Recursos não dá suporte ao processamento de um objeto para os valores e nomes de marca. Em vez disso, transmita um objeto para os valores de marcação, porém ainda será necessário especificar os nomes de marcação conforme mostrado no exemplo a seguir.

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


## <a name="portal"></a>Portal

[AZURE.INCLUDE [resource-manager-tag-resource](../includes/resource-manager-tag-resources.md)]

## <a name="powershell"></a>PowerShell

[AZURE.INCLUDE [resource-manager-tag-resources](../includes/resource-manager-tag-resources-powershell.md)]

## <a name="azure-cli"></a>CLI do Azure

[AZURE.INCLUDE [resource-manager-tag-resources-cli](../includes/resource-manager-tag-resources-cli.md)]

## <a name="rest-api"></a>API REST

O portal e o PowerShell usam a [API REST do Gerenciador de Recursos](https://msdn.microsoft.com/library/azure/dn848368.aspx) em segundo plano. Se você precisar integrar a marcação a outro ambiente, você pode obter marcas com um GET na ID do recurso e atualizar o conjunto de marcas com uma chamada de PATCH.


## <a name="tags-and-billing"></a>Marcas e cobrança

Para serviços com suporte, você pode usar marcas para agrupar os dados de cobrança. Por exemplo, [Máquinas Virtuais integrado ao Gerenciador de Recursos do Azure](./virtual-machines/virtual-machines-windows-compare-deployment-models.md) permitem definir e aplicar marcas para organizar o uso de cobrança para máquinas virtuais. Se você estiver executando várias VMs para organizações diferentes, poderá usar as marcas para o uso do grupo por centro de custo.  
Você também pode usar marcas para categorizar os custos pelo ambiente de tempo de execução, como por exemplo, o uso de cobrança para VMs em execução no ambiente de produção.

Você pode recuperar as informações sobre as marcações por meio das [APIs RateCard e Uso de Recursos do Azure](billing-usage-rate-card-overview.md) ou do arquivo de uso CSV (com valores separados por vírgula). Baixe o arquivo de uso no [portal de contas do Azure](https://account.windowsazure.com/) ou no [portal de EA](https://ea.azure.com). Para saber mais sobre o acesso programático às informações de cobrança, confira [Obter informações sobre o consumo de recursos do Microsoft Azure](billing-usage-rate-card-overview.md). Para operações de API REST, confira [Referência da API REST de cobrança do Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Quando você baixa o CSV de uso para serviços que dão suporte a marcações com cobrança, as marcações aparecerão na coluna **Marcações** . Para obter mais detalhes, consulte [Entenda sua fatura do Microsoft Azure](billing-understand-your-bill.md).

![Ver as marcas de cobranças](./media/resource-group-using-tags/billing_csv.png)

## <a name="next-steps"></a>Próximas etapas

- É possível aplicar restrições e convenções em sua assinatura com políticas personalizadas. A política que você definir pode exigir que todos os recursos tenham uma valor para uma marcação específica. Para saber mais, confira [Usar a Política para gerenciar recursos e controlar o acesso](resource-manager-policy.md).
- Para obter uma introdução ao uso do Azure PowerShell ao implantar recursos, confira [Usando o Azure PowerShell com o Gerenciador de Recursos do Azure](./powershell-azure-resource-manager.md).
- Para obter uma introdução ao uso da CLI do Azure ao implantar recursos, confira [Usando a CLI do Azure para Mac, Linux e Windows com o Gerenciamento de Recursos do Azure](./xplat-cli-azure-resource-manager.md).
- Para obter uma introdução ao uso do portal, confira [Usando o portal do Azure para gerenciar os recursos do Azure](./azure-portal/resource-group-portal.md)  



<!--HONumber=Oct16_HO2-->


