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
	ms.date="06/10/2016" 
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

Este tópico mostra como usar o [portal do Azure](https://portal.azure.com) com o [Azure Resource Manager](../resource-group-overview.md) para gerenciar seus recursos do Azure. Para saber mais sobre a implantação de recursos por meio do portal, consulte [Implantar recursos com os modelos do Resource Manager e o portal do Azure](../resource-group-template-deploy-portal.md).

Atualmente, nem todo serviço dá suporte ao portal ou ao Gerenciador de Recursos. Para esses serviços, você precisará usar o [portal clássico](https://manage.windowsazure.com). Para obter o status de cada serviço, confira o [Gráfico de disponibilidade do portal do Azure](https://azure.microsoft.com/features/azure-portal/availability/).

<a id="access-control-for-azure-dashboards" />
## Personalizar o painel para monitorar recursos

O portal fornece um painel que você pode usar para monitorar e gerenciar seus recursos. O painel é totalmente personalizável e você pode criar vários painéis para fornecer diferentes modos de exibição da assinatura.

![painel Transações da Web](./media/resource-group-portal/dashboard.png)

> [AZURE.TIP] A melhor maneira de aprender a trabalhar com painéis é assistir ao vídeo [Criar Painéis Personalizados no Portal do Microsoft Azure](https://channel9.msdn.com/Blogs/trevor-cloud/azure-portal-dashboards).

### Compartilhamento de painéis do Azure e controle de acesso
Depois de configurar um painel, você pode publicá-lo e compartilhá-lo com outros usuários na sua organização. O [Controle de Acesso Baseado em Funções](../active-directory/role-based-access-control-configure.md) do Azure controla o acesso às informações exibidas pelos blocos no portal. Todos os painéis publicados são implementados como recursos do Azure. Do ponto de vista do controle de acesso, os painéis não são diferentes de uma máquina virtual ou de uma conta de armazenamento.

Aqui está um exemplo. Digamos que você tenha uma assinatura do Azure e vários membros de sua equipe receberam as funções de **proprietário**, **colaborador** ou **leitor** da assinatura. Os usuários que são proprietários ou colaboradores podem listar, exibir, criar, modificar ou excluir painéis na assinatura. Os usuários que são os leitores podem listar e exibir os painéis, mas não podem modificá-los ou excluí-los. Os usuários com acesso de leitor podem fazer edições locais em um painel publicado (por exemplo, ao solucionar um problema), mas não podem publicar essas alterações no servidor. Eles terão a opção de fazer uma cópia privada do painel para uso pessoal.

Blocos individuais no painel impõem seus próprios requisitos de controle de acesso com base nos recursos que exibem. Portanto, você pode criar um painel que é compartilhado amplamente e ainda proteger os dados em blocos individuais.

## Gerenciar grupos de recursos

1. Para ver todos os grupos de recursos em sua assinatura, selecione **Grupos de recursos**.

    ![procurar grupos de recursos](./media/resource-group-portal/browse-groups.png)

2. Selecione o grupo de recursos específico que você deseja gerenciar. Você verá uma folha do grupo de recursos que fornece informações sobre esse grupo, incluindo uma lista de todos os recursos contidos nele.

    ![resumo do grupo de recursos](./media/resource-group-portal/group-summary.png)

    Selecionar qualquer um dos recursos mostra detalhes sobre o recurso em questão.

3. Na folha de um recurso, você pode adicionar mais gráficos e tabelas selecionando **Adicionar uma seção** abaixo do resumo.

    ![adicionar seção](./media/resource-group-portal/add-section.png)

4. Você verá uma galeria de blocos para selecionar as informações que deseja incluir na folha. Os tipos de bloco exibidos são filtrados por tipo de recurso. A escolha de um recurso diferente altera os blocos disponíveis.

    ![adicionar seção](./media/resource-group-portal/tile-gallery.png)

5. Arraste o bloco que desejar até os espaços disponíveis.

    ![arrastar bloco](./media/resource-group-portal/drag-tile.png)

6. Após você selecionar **Concluído** na parte superior do portal, a nova exibição fará parte da folha.

    ![mostrar bloco](./media/resource-group-portal/show-lens.png)

7. Para obter acesso rápido a um grupo de recursos, você pode fixar a folha em seu painel.

    ![fixar grupo de recursos](./media/resource-group-portal/pin-group.png)

    Como alternativa, você pode fixar uma seção da folha no painel selecionando as reticências (...) acima da seção. Você também pode personalizar o tamanho da seção na folha ou removê-los completamente. A imagem a seguir mostra como fixar, personalizar ou remover a seção CPU e Memória.

    ![fixar seção](./media/resource-group-portal/pin-cpu-section.png)

8. Depois de fixar a seção no painel, você verá o resumo nele.

    ![exibir painel](./media/resource-group-portal/view-startboard.png)

E selecioná-lo imediatamente conduz você a mais detalhes sobre os dados.

## Recursos de marca

Você pode aplicar marcas a recursos e grupos de recursos para organizar seus ativos de modo lógico. Para obter informações sobre como trabalhar com marcas usando o portal, confira [Usando marcas para organizar os recursos do Azure](../resource-group-using-tags.md).

## Exibir sua assinatura e custos

Você pode exibir informações sobre sua assinatura e os custos acumulados para todos os recursos. Selecione **Assinaturas** e a assinatura que você deseja ver. Talvez você só tenha uma assinatura para selecionar.

![assinatura](./media/resource-group-portal/select-subscription.png)

Na folha da assinatura, você verá uma taxa de gravação.

![taxa de gravação](./media/resource-group-portal/burn-rate.png)

Haverá também uma divisão de custos por tipo de recurso.

![custo de recurso](./media/resource-group-portal/cost-by-resource.png)

## Exportar modelo

Depois de configurar o grupo de recursos, convém exibir o modelo do Resource Manager para o grupo de recursos. A exportação do modelo oferece dois benefícios:

1. Você pode automatizar com facilidade as implantações futuras da solução, pois toda a infraestrutura está definida no modelo.

2. Você pode se familiarizar com a sintaxe do modelo analisando o JSON (JavaScript Object Notation) que representa sua solução.

Para obter diretrizes passo a passo, consulte [Exportar um modelo do Azure Resource Manager a partir dos recursos existentes](../resource-manager-export-template.md).

## Excluir grupo de recursos ou recursos

Excluir um grupo de recursos exclui todos os recursos contidos nele. Você também pode excluir recursos individuais de um grupo de recursos. Tenha cuidado ao excluir um grupo de recursos, pois pode haver recursos em outros grupos de recursos vinculados a ele. Os recursos vinculados não serão excluídos, mas podem não funcionar conforme esperado.

![excluir grupo](./media/resource-group-portal/delete-group.png)


## Próximas etapas

- Para saber sobre as ações de auditoria, consulte [Auditar operações com o Gerenciador de Recursos](../resource-group-audit.md).
- Para solucionar os erros de implantação, consulte [Solução de problemas das implantações dos grupos de recursos com o Portal do Azure](../resource-manager-troubleshoot-deployments-portal.md).
- Para implantar recursos por meio do portal, consulte [Implantar recursos com os modelos do Resource Manager e o portal do Azure](../resource-group-template-deploy-portal.md).

<!---HONumber=AcomDC_0720_2016-->