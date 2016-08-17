<properties 
	pageTitle="Usar o portal do Azure para gerenciar os recursos do Azure | Microsoft Azure" 
	description="Use o portal do Azure e o Azure Resource Manager para gerenciar seus recursos. Mostra como trabalhar com painéis e blocos para monitorar recursos." 
	services="azure-resource-manager,azure-portal" 
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
	ms.date="08/03/2016" 
	ms.author="tomfitz"/>


# Gerenciar recursos do Azure por meio do portal

> [AZURE.SELECTOR]
- [Portal](azure-portal/resource-group-portal.md)
- [CLI do Azure](xplat-cli-azure-resource-manager.md)
- [PowerShell do Azure](powershell-azure-resource-manager.md)
- [.NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)
- [Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group/)
- [Nó](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)
- [Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)
- [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

Este tópico mostra como usar o [portal do Azure](https://portal.azure.com) com o [Azure Resource Manager](../resource-group-overview.md) para gerenciar seus recursos do Azure. Atualmente, nem todo serviço dá suporte ao portal ou ao Gerenciador de Recursos. Para esses serviços, você precisa usar o [portal clássico](https://manage.windowsazure.com). Para obter o status de cada serviço, confira o [Gráfico de disponibilidade do portal do Azure](https://azure.microsoft.com/features/azure-portal/availability/).

## Gerenciar grupos de recursos

1. Para ver todos os grupos de recursos em sua assinatura, selecione **Grupos de recursos**.

    ![procurar grupos de recursos](./media/resource-group-portal/browse-groups.png)

1. Para criar um grupo de recursos vazio, escolha **Adicionar**.

    ![adicionar grupo de recursos](./media/resource-group-portal/add-resource-group.png)

1. Forneça um nome e local para o novo grupo de recursos. Selecione **Criar**.

    ![criar grupo de recursos](./media/resource-group-portal/create-empty-group.png)

1. Talvez seja necessário selecionar **Atualizar** para ver o grupo de recursos recém-criado.

    ![atualizar grupo de recursos](./media/resource-group-portal/refresh-resource-groups.png)

1. Para personalizar as informações exibidas para os grupos de recursos, escolha **Colunas**.

    ![personalizar colunas](./media/resource-group-portal/select-columns.png)

1. Selecione as colunas para adicionar e, em seguida, selecione **Atualizar**.

    ![adicionar colunas](./media/resource-group-portal/add-columns.png)

1. Para saber mais sobre a implantação de recursos em seu novo grupo de recursos, confira [Implantar recursos com os modelos do Resource Manager e o Portal do Azure](../resource-group-template-deploy-portal.md).

1. Para obter acesso rápido a um grupo de recursos, você pode fixar a folha em seu painel.

    ![fixar grupo de recursos](./media/resource-group-portal/pin-group.png)

1. Este painel exibe o grupo de recursos e seus recursos. Você pode selecionar os grupos de recursos, ou qualquer um dos seus recursos, para navegar até o item.

    ![fixar grupo de recursos](./media/resource-group-portal/show-resource-group-dashboard.png)

## Recursos de marca

Você pode aplicar marcas a recursos e grupos de recursos para organizar seus ativos de modo lógico. Para obter informações sobre como trabalhar com marcas, confira [Usando marcas para organizar os recursos do Azure](../resource-group-using-tags.md).

[AZURE.INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## Monitorar recursos

Quando você seleciona um recurso, a folha do recurso apresenta gráficos e tabelas padrão para esse tipo de recurso de monitoramento. Você pode personalizar o modo de monitorar o recurso alterando as tabelas e gráficos exibidos.

1. Na folha de um recurso, adicione mais gráficos e tabelas selecionando **Adicionar uma seção** abaixo do resumo.

    ![adicionar seção](./media/resource-group-portal/add-section.png)

1. Na galeria de blocos, selecione as informações que você deseja apresentar na folha. O editor filtra os blocos por tipo de recurso. A escolha de um recurso diferente altera os blocos disponíveis.

    ![adicionar seção](./media/resource-group-portal/tile-gallery.png)

1. Arraste o bloco que desejar até os espaços disponíveis.

    ![arrastar bloco](./media/resource-group-portal/drag-tile.png)

1. Após você selecionar **Concluído** na parte superior do portal, a nova exibição fará parte da folha.

    ![mostrar bloco](./media/resource-group-portal/show-lens.png)

1. Você pode fixar uma seção da folha no painel selecionando as reticências (...) acima da seção. Você também pode personalizar o tamanho da seção na folha ou removê-los completamente. A imagem a seguir mostra como fixar, personalizar ou remover a seção CPU e Memória.

    ![fixar seção](./media/resource-group-portal/pin-cpu-section.png)

1. Depois de fixar a seção no painel, você verá o resumo nele. E selecioná-lo imediatamente conduz você a mais detalhes sobre os dados.

    ![exibir painel](./media/resource-group-portal/view-startboard.png)

1. Você pode também criar vários painéis para monitorar e gerenciar seus recursos, e compartilhar esses painéis com os outros em sua organização. Selecione **Novo painel**.

    ![painel Transações da Web](./media/resource-group-portal/dashboard.png)

     Para aprender a trabalhar com painéis assista ao vídeo [Criar Painéis Personalizados no Portal do Microsoft Azure](https://channel9.msdn.com/Blogs/trevor-cloud/azure-portal-dashboards). Para saber mais sobre como compartilhar o acesso a um painel publicado, confira [Compartilhar painéis do Azure](azure-portal-dashboard-share-access.md).

## Mover recursos

Se você precisar mover um recurso para outro grupo de recursos ou outra assinatura, consulte [Mover recursos para um novo grupo de recursos ou uma assinatura](../resource-group-move-resources.md).

## Bloquear recursos

Você pode bloquear uma assinatura, um recurso ou um grupo de recursos para impedir que outros usuários em sua organização excluam ou modifiquem acidentalmente recursos críticos. Para saber mais, confira [Bloquear recursos com o Gerenciador de Recursos do Azure](../resource-group-lock-resources.md).

[AZURE.INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## Exibir sua assinatura e custos

Você pode exibir informações sobre sua assinatura e os custos acumulados para todos os seus recursos. Selecione **Assinaturas** e a assinatura que você deseja ver. Talvez você só tenha uma assinatura para selecionar.

![assinatura](./media/resource-group-portal/select-subscription.png)

Na folha da assinatura, você verá uma taxa de gravação.

![taxa de gravação](./media/resource-group-portal/burn-rate.png)

Haverá também uma divisão de custos por tipo de recurso.

![custo de recurso](./media/resource-group-portal/cost-by-resource.png)

## Exportar modelo

Depois de configurar o grupo de recursos, convém exibir o modelo do Resource Manager para o grupo de recursos. A exportação do modelo oferece dois benefícios:

1. Você pode automatizar com facilidade as implantações futuras da solução, pois o modelo contém a infraestrutura completa.

2. Você pode se familiarizar com a sintaxe do modelo analisando o JSON (JavaScript Object Notation) que representa sua solução.

Para obter diretrizes passo a passo, consulte [Exportar um modelo do Azure Resource Manager a partir dos recursos existentes](../resource-manager-export-template.md).

## Excluir grupo de recursos ou recursos

Excluir um grupo de recursos exclui todos os recursos contidos nele. Você também pode excluir recursos individuais de um grupo de recursos. Tenha cuidado ao excluir um grupo de recursos, pois pode haver recursos em outros grupos de recursos vinculados a ele. O Resource Manager não exclui os recursos vinculados, mas talvez eles não funcionem corretamente sem os recursos esperados.

![excluir grupo](./media/resource-group-portal/delete-group.png)

## Próximas etapas

- Para saber sobre as ações de auditoria, consulte [Auditar operações com o Gerenciador de Recursos](../resource-group-audit.md).
- Para solucionar os erros de implantação, consulte [Solução de problemas das implantações dos grupos de recursos com o Portal do Azure](../resource-manager-troubleshoot-deployments-portal.md).
- Para implantar recursos por meio do portal, consulte [Implantar recursos com os modelos do Resource Manager e o portal do Azure](../resource-group-template-deploy-portal.md).
- Para gerenciar o acesso aos recursos, confira [Usar atribuições de função para gerenciar o acesso aos recursos da assinatura do Azure](../active-directory/role-based-access-control-configure.md).

<!---HONumber=AcomDC_0803_2016-->