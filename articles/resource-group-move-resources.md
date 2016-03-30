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
	ms.date="03/04/2016" 
	ms.author="tomfitz"/>

# Mover recursos para um novo grupo de recursos ou uma nova assinatura

Este tópico mostra como mover os recursos de um grupo de recursos para outro. Você também pode mover recursos para uma nova assinatura (no entanto, a assinatura deve existir no mesmo [locatário](./active-directory/active-directory-howto-tenant.md)). Você talvez precise mover recursos ao decidir que:

1. Para fins de cobrança, um recurso precisa residir em uma assinatura diferente.
2. Um recurso já não compartilha o mesmo ciclo de vida que os recursos com os quais estava agrupado anteriormente. Você deseja movê-lo para um novo grupo de recursos para gerenciar esse recurso separadamente dos outros.
3. Um recurso compartilha o mesmo ciclo de vida como outros recursos em um grupo de recursos diferente. Deseja movê-lo para o grupo de recursos com os outros recursos para gerenciá-los juntos.

## Considerações antes de mover os recursos

Há algumas questões importantes a considerar antes de mover um recurso:

1. Você não pode alterar o local do recurso. Mover um recurso só o move para um novo grupo de recursos. O novo grupo de recursos pode ter um local diferente, mas que não altere o local do recurso.
2. Nem todos os serviços atualmente dão suporte à capacidade de mover recursos. Consulte na lista abaixo informações sobre quais serviços dão suporte à movimentação de recursos.
3. O provedor de recursos do recurso que está sendo movido deve ser registrado na assinatura de destino. Você pode encontrar esse problema ao mover um recurso para uma nova assinatura que nunca tenha sido usada com esse tipo de recurso. Por exemplo, se você estiver movendo uma instância do serviço Gerenciamento de API para uma assinatura que não tenha registrado o provedor de recursos **Microsoft.ApiManagement**, a movimentação não terá êxito. Para saber como verificar o status do registro e registrar provedores de recursos, confira [Provedores e tipos de recursos](../resource-manager-supported-services/#resource-providers-and-types).
4. O grupo de recursos de destino deve conter somente recursos que compartilham o mesmo ciclo de vida de aplicativo que os recursos que você está movendo.
5. Se você estiver usando o Azure PowerShell ou a CLI do Azure, verifique se está usando a versão mais recente. Para atualizar sua versão, execute o Microsoft Web Platform Installer e verifique se uma nova versão está disponível. Para saber mais, confira [Como instalar e configurar o Azure PowerShell](powershell-install-configure.md) e [Instalar a CLI do Azure](xplat-cli-install.md).
6. A operação de transferência pode levar algum tempo para ser concluída e, durante esse tempo, seu prompt aguardará até que a operação seja concluída.
7. Ao mover recursos, ambos o grupo de origem e o grupo de destino estão bloqueados pela duração da operação. As operações de gravação e exclusão são bloqueadas nos grupos até que a migração seja concluída.

## Serviços que dão suporte à movimentação

Por enquanto, os serviços que dão suporte à transferência para um novo grupo de recursos e uma nova assinatura são:

- Gerenciamento da API
- Aplicativos do Serviço de Aplicativo (confira as [Limitações do Serviço de Aplicativo](#app-service-limitations) abaixo)
- Automação
- Batch
- CDN
- Data Factory
- Banco de Dados de Documentos
- Clusters do HDInsight
- Cofre da Chave
- Mobile Engagement
- Hubs de Notificação
- Insights Operacionais
- Cache Redis
- Pesquisar
- Servidor de Banco de Dados SQL (consulte as [Limitações do Banco de dados SQL](#sql-database-limitations) abaixo)

## Serviços dão suporte parcial à movimentação

Os serviços que dão suporte à transferência para um novo grupo de recursos, mas não para uma nova assinatura são:

- Máquinas virtuais (clássico)
- Armazenamento (clássico)
- Redes Virtuais
- Serviços de Nuvem

## Serviços não dão suporte à movimentação

Os serviços que atualmente não dão suporte à transferência de um recurso são:

- Máquinas Virtuais
- Armazenamento
- Rota Expressa

## Limitações do Serviço de Aplicativo

Ao trabalhar com aplicativos do Serviço de Aplicativo, você não pode mover um plano de Serviço de Aplicativo. Para mover os Aplicativos do Serviço de Aplicativo, as opções são:

- Mova todos os recursos de um grupo de recursos para um grupo de recursos diferente, se o grupo de recursos de destino ainda não tiver recursos Microsoft.Web.
- Mova os aplicativos Web para um grupo de recursos diferente, mas mantenha o plano de Serviço de Aplicativo no grupo de recursos original.

## Limitações do Banco de dados SQL

Você não pode mover um banco de dados SQL separadamente do seu servidor. O banco de dados e o servidor devem residir no mesmo grupo de recursos. Quando você move um SQL Server, todos os seus bancos de dados também são movidos.

## Usando o PowerShell para mover recursos

Para mover os recursos existentes para outro grupo de recursos ou assinatura, use o comando **Move-AzureRmResource**.

O primeiro exemplo mostra como mover um recurso para um novo grupo de recursos.

    PS C:\> $resource = Get-AzureRmResource -ResourceName ExampleApp -ResourceGroupName OldRG
    PS C:\> Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $resource.ResourceId

O segundo exemplo mostra como mover vários recursos para um novo grupo de recursos.

    PS C:\> $webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
    PS C:\> $plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
    PS C:\> Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId

Para mover para uma nova assinatura, inclua um valor para o parâmetro **DestinationSubscriptionId**.

## Usar a CLI do Azure para mover recursos

Para mover os recursos existentes para outro grupo de recursos ou assinatura, use o comando **azure resource move**. O exemplo a seguir mostra como mover um Cache Redis para um novo grupo de recursos. No parâmetro **-i**, forneça uma lista separada por vírgulas de ids do recurso a serem movidas.

    azure resource move -i "/subscriptions/{guid}/resourceGroups/OldRG/providers/Microsoft.Cache/Redis/examplecache" -d "NewRG"
    info:    Executing command resource move
    Move selected resources in OldRG to NewRG? [y/n] y
    + Moving selected resources to NewRG
    info:    resource move command OK

## Usando a API REST para mover recursos

Para mover recursos existentes para outro grupo de recursos ou outra assinatura, execute:

    POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 

No corpo da solicitação, especifique o grupo de recursos de destino e os recursos para mover. Para obter mais informações sobre a operação de movimentação REST, consulte [Mover recursos](https://msdn.microsoft.com/library/azure/mt218710.aspx).

## Usando o portal para mover recursos

Você pode mover alguns recursos por meio do portal; no entanto, nem todos os provedores de recursos que dão suporte à operação de movimentação fornecem essa funcionalidade no portal.

Para mover um recurso, selecione o recurso e selecione o botão **Mover**.

![mover recursos](./media/resource-group-move-resources/move-resources.png)

Você especifica para onde deseja mover o recurso. Se outros recursos tiverem de ser movidos com o recurso, eles serão listados.

![selecionar destino](./media/resource-group-move-resources/select-destination.png)

## Próximas etapas
- [Usando o PowerShell do Azure com o Gerenciador de Recursos](./powershell-azure-resource-manager.md)
- [Usando a CLI do Azure com o Gerenciador de Recursos](./xplat-cli-azure-resource-manager.md)
- [Usando o Portal do Azure para gerenciar recursos](azure-portal/resource-group-portal.md)
- [Usando marcas para organizar os recursos](./resource-group-using-tags.md)

<!---HONumber=AcomDC_0316_2016-->