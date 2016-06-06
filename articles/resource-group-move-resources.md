<properties 
	pageTitle="Mover recursos para um novo grupo de recursos | Microsoft Azure" 
	description="Use o Azure Resource Manager para mover recursos para um novo grupo de recursos ou uma nova assinatura." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/12/2016" 
	ms.author="tomfitz"/>

# Mover recursos para um novo grupo de recursos ou uma nova assinatura

Este tópico mostra como mover os recursos de um grupo de recursos para outro. Você também pode mover recursos para uma nova assinatura (no entanto, a assinatura deve existir no mesmo [locatário](./active-directory/active-directory-howto-tenant.md)). Você talvez precise mover recursos ao decidir que:

1. Para fins de cobrança, um recurso precisa residir em uma assinatura diferente.
2. Um recurso já não compartilha o mesmo ciclo de vida que os recursos com os quais estava agrupado anteriormente. Você deseja movê-lo para um novo grupo de recursos para gerenciar esse recurso separadamente dos outros.

Ao mover recursos, ambos o grupo de origem e o grupo de destino estão bloqueados pela duração da operação. As operações de gravação e exclusão são bloqueadas nos grupos até que a migração seja concluída.

Você não pode alterar o local do recurso. Mover um recurso só o move para um novo grupo de recursos. O novo grupo de recursos pode ter um local diferente, mas que não altere o local do recurso.

## Lista de verificação antes de mover recursos

Há algumas etapas importantes a serem realizadas antes de mover um recurso. Ao verificar essas condições, é possível evitar erros.

1. O serviço deve dar suporte à capacidade de mover recursos. Veja a lista abaixo para obter informações sobre quais [serviços dão suporte à movimentação de recursos](#services-that-support-move).
2. A assinatura de destino deve estar registrada para que o provedor de recursos do recurso seja movido. Se não estiver, você receberá um erro afirmando que a **assinatura não foi registrada para um tipo de recurso**. Você pode encontrar esse problema ao mover um recurso para uma nova assinatura que nunca tenha sido usada com esse tipo de recurso. Para saber como verificar o status do registro e registrar provedores de recursos, consulte [Provedores e tipos de recursos](../resource-manager-supported-services.md#resource-providers-and-types).
3. Se estiver usando o Azure PowerShell ou a CLI do Azure, use a versão mais recente. Para atualizar sua versão, execute o Microsoft Web Platform Installer e verifique se uma nova versão está disponível. Para saber mais, confira [Como instalar e configurar o Azure PowerShell](powershell-install-configure.md) e [Instalar a CLI do Azure](xplat-cli-install.md).
4. Se estiver movendo um aplicativo do Serviço de Aplicativo, você examinou as [Limitações do Serviço de Aplicativo](#app-service-limitations).
5. Se estiver movendo recursos implantados por meio do modelo clássico, você examinou as [Limitações da implantação clássica](#classic-deployment-limitations).

## Serviços que dão suporte à movimentação

Por enquanto, os serviços que dão suporte à transferência para um novo grupo de recursos e uma nova assinatura são:

- Gerenciamento da API
- Aplicativos do Serviço de Aplicativo - veja [Limitações do Serviço de Aplicativo](#app-service-limitations)
- Automação
- Batch
- CDN
- Serviços de Nuvem - veja [Limitações da implantação clássica](#classic-deployment-limitations)
- Data Factory
- DNS
- Banco de Dados de Documentos
- Clusters do HDInsight
- Cofre da Chave
- Mobile Engagement
- Hubs de Notificação
- Insights Operacionais
- Cache Redis
- Agendador
- Pesquisar
- Armazenamento (clássico) - consulte [Limitações da implantação clássica](#classic-deployment-limitations)
- Servidor do Banco de Dados SQL - O banco de dados e o servidor devem residir no mesmo grupo de recursos. Quando você move um SQL Server, todos os seus bancos de dados também são movidos.
- Máquinas virtuais (clássicas) - consulte [Limitações da implantação clássica](#classic-deployment-limitations)

## Serviços dão suporte parcial à movimentação

Os serviços que dão suporte à transferência para um novo grupo de recursos, mas não para uma nova assinatura são:

- Redes Virtuais

## Serviços não dão suporte à movimentação

Os serviços que atualmente não dão suporte à transferência de um recurso são:

- Application Insights
- Rota Expressa
- Armazenamento
- Máquinas Virtuais
- Redes Virtuais (clássicas) - consulte [Limitações da implantação clássica](#classic-deployment-limitations)

## Limitações do Serviço de Aplicativo

Ao trabalhar com aplicativos do Serviço de Aplicativo, você não pode mover um plano de Serviço de Aplicativo. Para mover os Aplicativos do Serviço de Aplicativo, as opções são:

- Mova o plano do Serviço de Aplicativo e todos os outros recursos do Serviço de Aplicativo nesse grupo de recursos para um novo grupo de recursos que ainda não têm os recursos do Serviço de Aplicativo. Isso significa mover até mesmo os recursos do Serviço de Aplicativo que não estão associados ao plano do Serviço de Aplicativo. 
- Mova os aplicativos para um grupo de recursos diferente, mas mantenha todos os planos do Serviço de Aplicativo no grupo de recursos original.

Se o grupo de recursos original também incluir um recurso do Application Insights, não será possível mover esse recurso, pois, atualmente, o Application Insights não dá suporte à operação de movimentação. Se você incluir o recurso do Application Insights ao mover aplicativos do Serviço de Aplicativo, toda a operação de movimentação falhará. No entanto, para que o aplicativo funcione corretamente, o Application Insights e o plano do Serviço de Aplicativo não precisam residir no mesmo grupo de recursos que o aplicativo.

Por exemplo, se o grupo de recursos contém:

- **web-a**, que está associado a **plan-a** e a **app-insights-a**
- **web-b**, que está associado a **plan-b** e a **app-insights-b**

Suas opções são:

- Mover **web-a**, **plan-a**, **web-b** e **plan-b**
- Mover **web-a** e **web-b**
- Mover **web-a**
- Mover **web-b**

Todas as outras combinações envolvem a movimentação de um tipo de recurso que não pode ser movido (Application Insights) ou deixar para trás um tipo de recurso que não pode ser deixado para trás ao mover um plano do Serviço de Aplicativo (qualquer tipo de recurso do Serviço de Aplicativo).

Caso o aplicativo Web resida em um grupo de recursos diferente de seu plano do Serviço de Aplicativo, mas você desejar mover ambos para um novo grupo de recursos, será necessário realizar a movimentação em duas etapas. Por exemplo:

- **web-a** reside em **web-group**
- **plan-a** reside em **plan-group**
- Você quer que **web-a** e **plan-a** residam em **combined-group**

Para realizar essa movimentação, execute duas operações de movimentação separadas na sequência a seguir:

1. Mover **web-a** para **plan-group**
2. Mover **web-a** e **plan-a** para **combined-group**.

## Limitações da implantação clássica

As opções de movimentação dos recursos implantados por meio do modelo clássico apesentam diferenças, dependendo se você estiver movendo os recursos em uma assinatura ou para uma nova assinatura.

Ao mover recursos de um grupo de recursos para outro **na mesma assinatura**, as seguintes restrições se aplicarão:

- Redes virtuais (clássicas) não podem ser movidas.
- Máquinas virtuais (clássicas) devem ser movidas com o serviço de nuvem. 
- Um serviço de nuvem pode ser movido apenas quando a movimentação inclui todas as suas máquinas virtuais.
- Apenas um serviço de nuvem pode ser movido por vez.
- Apenas uma conta de armazenamento (clássica) pode ser movida por vez.
- Uma conta de armazenamento (clássica) não pode ser movida na mesma operação com uma máquina virtual ou um serviço de nuvem.

Ao mover recursos para uma **nova assinatura**, as seguintes restrições se aplicarão:

- Todos os recursos clássicos na assinatura devem ser movidos na mesma operação.
- A movimentação pode ser solicitada apenas por meio do portal ou de uma API REST separada para movimentações clássicas. Os comandos de movimentação padrão do Gerenciador de Recursos não funcionam quando há uma movimentação dos recursos clássicos para uma nova assinatura. As etapas para usar o portal ou a API REST são mostradas nas seções abaixo.

## Usando o portal para mover recursos

Você pode mover alguns recursos por meio do portal; no entanto, nem todos os provedores de recursos que dão suporte à operação de movimentação fornecem essa funcionalidade no portal.

Para mover um recurso, selecione o recurso e, em seguida, selecione o botão **Mover**.

![mover recursos](./media/resource-group-move-resources/move-resources.png)

Você especifica a assinatura e o grupo de recursos de destino ao mover o recurso. Se outros recursos tiverem de ser movidos com o recurso, eles serão listados.

![selecionar destino](./media/resource-group-move-resources/select-destination.png)

Em **Notificações**, você verá que a operação de movimentação está em execução.

![mostrar status da movimentação](./media/resource-group-move-resources/show-status.png)

Quando for concluída, você será notificado sobre o resultado.

![mostrar resultado da movimentação](./media/resource-group-move-resources/show-result.png)

Para ver outra opção para mover recursos para um novo grupo de recursos (mas não a assinatura), selecione o recurso que você deseja mover.

![selecionar recurso a ser movido](./media/resource-group-move-resources/select-resource.png)

Selecione suas **Propriedades**.

![selecionar propriedades](./media/resource-group-move-resources/select-properties.png)

Se esta opção estiver disponível para este tipo de recurso, selecione **Alterar grupo de recursos**.

![alterar grupo de recursos](./media/resource-group-move-resources/change-resource-group.png)

É possível selecionar quais recursos serão movidos e o grupo de recursos para o qual eles serão movidos.

![mover recursos](./media/resource-group-move-resources/select-group.png)

Ao mover recursos implantados por meio do modelo clássico para um novo grupo de recursos, é possível usar o ícone de edição ao lado do nome do grupo de recursos.

![mover recursos clássicos](./media/resource-group-move-resources/edit-rg-icon.png)

Selecione os recursos que serão movidos tendo em mente as [Limitações da implantação clássica](#classic-deployment-limitations). Selecione **OK** para iniciar a movimentação.

 ![selecionar recursos clássicos](./media/resource-group-move-resources/select-classic-resources.png)
 
 Ao mover recursos implantados por meio do modelo clássico para uma nova assinatura, use o ícone de edição ao lado da assinatura.
 
 ![mover para uma nova assinatura](./media/resource-group-move-resources/edit-subscription-icon.png)
 
 Todos os recursos clássicos são selecionados automaticamente para a movimentação.

## Usando o PowerShell para mover recursos

Para mover os recursos existentes para outro grupo de recursos ou assinatura, use o comando **Move-AzureRmResource**.

O primeiro exemplo mostra como mover um recurso para um novo grupo de recursos.

    $resource = Get-AzureRmResource -ResourceName ExampleApp -ResourceGroupName OldRG
    Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $resource.ResourceId

O segundo exemplo mostra como mover vários recursos para um novo grupo de recursos.

    $webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
    $plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
    Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId

Para mover para uma nova assinatura, inclua um valor para o parâmetro **DestinationSubscriptionId**.

Será solicitado que você confirme se deseja mover os recursos especificados.

    Confirm
    Are you sure you want to move these resources to the resource group
    '/subscriptions/{guid}/resourceGroups/newRG' the resources:

    /subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/serverFarms/exampleplan
    /subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/sites/examplesite
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

## Usar a CLI do Azure para mover recursos

Para mover os recursos existentes para outro grupo de recursos ou assinatura, use o comando **azure resource move**. O exemplo a seguir mostra como mover um Cache Redis para um novo grupo de recursos. No parâmetro **-i**, forneça uma lista separada por vírgulas de ids do recurso a serem movidas.

    azure resource move -i "/subscriptions/{guid}/resourceGroups/OldRG/providers/Microsoft.Cache/Redis/examplecache" -d "NewRG"
	
Será solicitado que você confirme se deseja mover o recurso especificado.
	
    info:    Executing command resource move
    Move selected resources in OldRG to NewRG? [y/n] y
    + Moving selected resources to NewRG
    info:    resource move command OK

## Usando a API REST para mover recursos

Para mover recursos existentes para outro grupo de recursos ou outra assinatura, execute:

    POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 

No corpo da solicitação, especifique o grupo de recursos de destino e os recursos para mover. Para obter mais informações sobre a operação de movimentação REST, consulte [Mover recursos](https://msdn.microsoft.com/library/azure/mt218710.aspx).

No entanto, para mover **recursos clássicos para uma nova assinatura**, é necessário usar operações REST diferentes. Para verificar se uma assinatura pode participar como a assinatura de origem ou de destino em uma movimentação de assinatura cruzada de recursos clássicos, use a seguinte operação:

    POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
    
Para a assinatura de origem, use o corpo da solicitação:

    {
        "role": "source"
    }

Para a assinatura de destino, use o corpo da solicitação:

    {
        "role": "target"
    }

A resposta de qualquer uma dessas operações de validação é:

    {
        "status": "{status}",
        "reasons": [
            "reason1",
            "reason2"
        ]
    }

Para mover todos os recursos clássicos de uma assinatura para outra, use a seguinte operação:

    POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01

Com o corpo da solicitação:

    {
        "target": "/subscriptions/{target-subscription-id}"
    }



## Próximas etapas
- Para saber mais sobre os cmdlets do PowerShell para gerenciar sua assinatura, veja [Usando o Azure PowerShell com o Resource Manager](powershell-azure-resource-manager.md).
- Para saber mais sobre os comandos da CLI do Azure para gerenciar sua assinatura, veja [Usando a CLI do Azure com o Resource Manager](xplat-cli-azure-resource-manager.md).
- Para saber mais sobre os recursos do portal para gerenciar sua assinatura, veja [Usando o Portal do Azure para gerenciar recursos](./azure-portal/resource-group-portal.md).
- Para saber mais sobre como aplicar uma organização lógica aos seus recursos, veja [Usando marcações para organizar seus recursos](resource-group-using-tags.md).

<!---HONumber=AcomDC_0525_2016-->