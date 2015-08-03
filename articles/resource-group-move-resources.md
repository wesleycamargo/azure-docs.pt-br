<properties 
	pageTitle="Mover recursos para um novo grupo de recursos" 
	description="Use o Azure PowerShell ou a API REST para mover recursos para um novo grupo de recursos do Gerenciador de Recursos do Azure." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/14/2015" 
	ms.author="tomfitz"/>

# Mover recursos para um novo grupo de recursos ou uma nova assinatura

Este tópico mostra como mover os recursos de um grupo de recursos para outro. Você também pode mover os recursos para uma nova assinatura. Você talvez precise mover recursos ao decidir que:

1. Para fins de cobrança, um recurso precisa residir em uma assinatura diferente.
2. Um recurso já não compartilha o mesmo ciclo de vida que os recursos com os quais estava agrupado anteriormente. Você deseja movê-lo para um novo grupo de recursos para gerenciar esse recurso separadamente dos outros.
3. Um recurso compartilha o mesmo ciclo de vida como outros recursos em um grupo de recursos diferente. Deseja movê-lo para o grupo de recursos com os outros recursos para gerenciá-los juntos.

Há algumas considerações importantes ao mover um recurso:

1. Você não pode alterar o local do recurso. Mover um recurso só o move para um novo grupo de recursos. O novo grupo de recursos pode ter um local diferente, mas que não altere o local do recurso.
2. O grupo de recursos de destino deve conter somente recursos que compartilham o mesmo ciclo de vida de aplicativo que os recursos que você está movendo.
3. Se você estiver usando o Azure PowerShell, verifique se está usando a versão mais recente. O comando **Move-AzureResource** é atualizado com frequência. Para atualizar sua versão, execute o Microsoft Web Platform Installer e verifique se uma nova versão está disponível. Para obter mais informações, consulte [Como instalar e configurar o Azure PowerShell](powershell-install-configure.md).
4. A operação de transferência pode levar algum tempo para ser concluída e, durante esse tempo, seu prompt do PowerShell aguardará até que a operação seja concluída.

## Serviços com suporte

Nem todos os serviços atualmente dão suporte à capacidade de mover recursos.

Por enquanto, os serviços que dão suporte à transferência para um novo grupo de recursos e uma nova assinatura são:

- Gerenciamento da API
- Pesquisa do Azure
- Data Factory
- Cofre da Chave
- Mobile Engagement
- Insights Operacionais
- Cache Redis

Os serviços que dão suporte à transferência para um novo grupo de recursos, mas não para uma nova assinatura são:

- Computação (clássico)
- Armazenamento (clássico)

Os serviços que atualmente não dão suporte à transferência de um recurso são:

- Redes Virtuais

## Usando o PowerShell para mover recursos

Para mover recursos existentes para outro grupo de recursos ou outra assinatura, use o comando **Move-AzureResource**.

O primeiro exemplo mostra como mover um recurso para um novo grupo de recursos.

    PS C:> Move-AzureResource -DestinationResourceGroupName TestRG -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OtherExample/providers/Microsoft.ClassicStorage/storageAccounts/examplestorage

O segundo exemplo mostra como mover vários recursos para um novo grupo de recursos.

    PS C:> $webapp = Get-AzureResource -ResourceGroupName OldRG -ResourceName ExampleSite -ResourceType Microsoft.Web/sites
    PS C:> $plan = Get-AzureResource -ResourceGroupName OldRG -ResourceName ExamplePlan -ResourceType Microsoft.Web/serverFarms
    PS C:> Move-AzureResource -DestinationResourceGroupName NewRG -ResourceId ($webapp.ResourceId, $plan.ResourceId)

Para mover para uma nova assinatura, inclua um valor para o parâmetro **DestinationSubscriptionId**.

## Usando a API REST para mover recursos

Para mover recursos existentes para outro grupo de recursos ou outra assinatura, execute:

    POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 

Substitua **{source-subscription-id}** e **{source-resource-group-name}** pela assinatura e pelo grupo de recursos que atualmente contêm os recursos que você deseja mover. Use **2015-01-01** para {api-version}.

Na solicitação, inclua um objeto JSON que define o grupo de recursos de destino e os recursos que você deseja mover.

    {
        "targetResourceGroup": "/subscriptions/{target-subscription-id}/resourceGroups/{target-resource-group-name}", "resources": [
            "/subscriptions/{source-id}/resourceGroups/{source-group-name}/providers/{provider-namespace}/{type}/{name}",
            "/subscriptions/{source-id}/resourceGroups/{source-group-name}/providers/{provider-namespace}/{type}/{name}",
            "/subscriptions/{source-id}/resourceGroups/{source-group-name}/providers/{provider-namespace}/{type}/{name}",
            "/subscriptions/{source-id}/resourceGroups/{source-group-name}/providers/{provider-namespace}/{type}/{name}"
        ]
    }

## Próximas etapas
- [Usando o PowerShell do Azure com o Gerenciador de Recursos](./powershell-azure-resource-manager.md)
- [Usando a CLI do Azure com o Gerenciador de Recursos](./virtual-machines/xplat-cli-azure-resource-manager.md)
- [Usando o Portal do Azure para gerenciar recursos](azure-portal/resource-group-portal.md)
- [Usando marcas para organizar os recursos](./resource-group-using-tags.md)

<!---HONumber=July15_HO4-->