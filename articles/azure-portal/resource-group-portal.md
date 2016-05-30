<properties 
	pageTitle="Usar o portal do Azure para gerenciar os recursos do Azure | Microsoft Azure" 
	description="Use o portal do Azure e o Azure Resource Manager para implantar e gerenciar seus recursos. Mostra como marcar recursos e exibir logs de auditoria." 
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
	ms.date="05/16/2016" 
	ms.author="tomfitz"/>


# Usando o Portal do Azure para implantar e gerenciar os recursos do Azure

## Introdução

Este tópico mostra como usar o [portal do Azure](https://portal.azure.com) com o [Azure Resource Manager](../resource-group-overview.md) para implantar e gerenciar os recursos do Azure.

Atualmente, nem todo serviço dá suporte ao portal ou ao Gerenciador de Recursos. Para esses serviços, você precisará usar o [portal clássico](https://manage.windowsazure.com). Para conhecer o status de cada serviço, confira o [Gráfico de disponibilidade do portal do Azure](https://azure.microsoft.com/features/azure-portal/availability/).

Você também pode gerenciar recursos por meio do Azure PowerShell e da CLI do Azure. Para obter mais informações sobre como usar essas interfaces, confira [Como usar o Azure PowerShell com o Azure Resource Manager](../powershell-azure-resource-manager.md) e [Usar a CLI do Azure para Mac, Linux e Windows com o Azure Resource Manager](../xplat-cli-azure-resource-manager.md). Para saber mais sobre como implantar soluções com o Visual Studio, confira [Criar e implantar grupos de recursos do Azure com o Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## Criar grupo de recursos

Para criar um grupo de recursos vazio, selecione **Novo**, **Gerenciamento** e **Grupo de Recursos**.

![criar grupo de recursos vazio](./media/resource-group-portal/create-empty-group.png)

Forneça a ele um nome e um local e, se for necessário, selecione uma assinatura.

![definir valores de grupo](./media/resource-group-portal/set-group-properties.png)

## Implantar recursos

Após a criação de um grupo de recursos, você pode implantar recursos nele. Para iniciar uma implantação, basta selecionar **Novo** e o tipo de recurso que você quer implantar.

![implantar recurso](./media/resource-group-portal/deploy-resource.png)

Se você não vir o tipo de recurso que deseja implantar, poderá pesquisá-lo no Marketplace.

![pesquisar no Marketplace](./media/resource-group-portal/search-resource.png)

Dependendo do tipo de recurso selecionado, você terá uma coleção de propriedades significativas para definir antes da implantação. Essas opções não são mostradas aqui, pois elas variam com base no tipo de recurso. Para todos os tipos, você deve selecionar um grupo de recursos de destino. A imagem a seguir mostra como criar um novo aplicativo Web e implantá-lo no grupo de recursos que você acabou de criar.

![criar grupo de recursos](./media/resource-group-portal/select-existing-group.png)

Como alternativa, você pode optar por criar um novo grupo de recursos ao implantar ois recursos. Em vez de selecionar um dos grupos de recursos existentes em sua assinatura, selecione **Novo** e dê um nome ao grupo de recursos.

![criar novo grupo de recursos](./media/resource-group-portal/select-new-group.png)

Sua implantação começará. Isso pode levar alguns minutos. Quando a implantação for concluída, você verá uma notificação.

![exibir notificação](./media/resource-group-portal/view-notification.png)

Depois de implantar os recursos, talvez você decida que precisa adicionar mais recursos ao grupo. Você pode adicionar recursos a um grupo de recursos usando o comando **Adicionar** na folha do grupo de recursos.

![adicionar recurso](./media/resource-group-portal/add-resource.png)

## Exportar modelo

Depois de configurar o grupo de recursos, convém exibir o modelo do Resource Manager para o grupo de recursos. A exportação do modelo oferece dois benefícios:

1. Você pode automatizar com facilidade as implantações futuras da solução, pois toda a infraestrutura está definida no modelo.

2. Você pode se familiarizar com a sintaxe do modelo analisando o JSON (JavaScript Object Notation) que representa sua solução.

> [AZURE.NOTE] O recurso de exportação do modelo está em versão de visualização, e nem todos os tipos de recursos oferecem suporte à exportação de um modelo. Ao tentar exportar um modelo, talvez você veja um erro afirmando que alguns recursos não foram exportados. Se for necessário, defina manualmente esses recursos em seu modelo depois de baixá-lo.

Para obter diretrizes passo a passo, veja [Exportar modelo do Azure Resource Manager com base em recursos existentes](../resource-manager-export-template/).

## Gerenciar um grupo de recursos

É possível procurar entre todos os grupos de recursos clicando em **Grupos de recursos**.

![procurar grupos de recursos](./media/resource-group-portal/browse-groups.png)

Quando você seleciona um determinado grupo de recursos, você vê uma folha do grupo de recursos que fornece informações sobre esse grupo, incluindo uma lista de todos os recursos contidos no grupo.

![resumo do grupo de recursos](./media/resource-group-portal/group-summary.png)

É possível adicionar mais gráficos e tabelas à folha do grupo de recursos selecionando **Adicionar uma seção** abaixo do resumo.

![adicionar seção](./media/resource-group-portal/add-section.png)

Você verá uma galeria de blocos para selecionar as informações que deseja incluir na folha. Os tipos de bloco exibidos são filtrados por tipo de recurso. A escolha de um recurso diferente altera os blocos disponíveis.

![adicionar seção](./media/resource-group-portal/tile-gallery.png)

Arraste o bloco que desejar até os espaços disponíveis.

![arrastar bloco](./media/resource-group-portal/drag-tile.png)

Depois de selecionar **Concluído** na parte superior do portal, a nova exibição se tornará parte da folha.

![mostrar bloco](./media/resource-group-portal/show-lens.png)

Para obter acesso rápido a um grupo de recursos, você pode fixar a folha em seu painel.

![fixar grupo de recursos](./media/resource-group-portal/pin-group.png)

Como alternativa, você pode fixar uma seção da folha no painel selecionando as reticências (...) acima da seção. Você também pode personalizar o tamanho da seção na folha ou removê-los completamente. A imagem a seguir mostra como fixar, personalizar ou remover a seção CPU e Memória.

![fixar seção](./media/resource-group-portal/pin-cpu-section.png)

Depois de fixar a seção no painel, você verá o resumo nele.

![exibir painel](./media/resource-group-portal/view-startboard.png)

E selecioná-lo imediatamente conduz você a mais detalhes sobre os dados.

Como os grupos de recursos permitem que você gerencie o ciclo de vida de todos os recursos contidos, a exclusão de um grupo de recursos resultará na exclusão de todos os recursos contidos no grupo. Você também pode excluir recursos individuais de um grupo de recursos. Tenha cuidado ao excluir um grupo de recursos, pois pode haver recursos em outros grupos de recursos vinculados a ele. Os recursos vinculados não serão excluídos, mas podem não funcionar conforme esperado.

![excluir grupo](./media/resource-group-portal/delete-group.png)

## Recursos de marca

Você pode aplicar marcas a recursos e grupos de recursos para organizar seus ativos de modo lógico. Para obter informações sobre como trabalhar com marcas usando o portal, confira [Usando marcas para organizar os recursos do Azure](../resource-group-using-tags.md).

## Implantar o modelo salvo

Se você salvou um modelo em sua conta, poderá exibi-lo posteriormente selecionando **Procurar** e **Modelos**.

![procurar modelos](./media/resource-group-portal/browse-templates.png)

Você verá sua própria coleção de modelos.

![mostrar coleção de modelos](./media/resource-group-portal/show-template-collection.png)


## Implantar modelo personalizado

Se você quiser executar uma implantação, mas não usa nenhum dos modelos no marketplace, você pode criar um modelo personalizado que define a infraestrutura para sua solução. Para saber mais sobre modelos, confira [Criando modelos do Gerenciador de Recursos do Azure](../resource-group-authoring-templates.md).

Para implantar um modelo personalizado usando o portal, selecione **Novo** e comece a procurar por **Implantação de Modelo** até conseguir selecioná-la nas opções.

![procurar implantação de modelo](./media/resource-group-portal/search-template.png)

Selecione **Implantação de Modelo** nos recursos disponíveis.

![selecionar implantação de modelo](./media/resource-group-portal/select-template.png)

Depois de iniciar a implantação de modelo, você pode criar o modelo personalizado e definir valores para a implantação.

![criar modelo](./media/resource-group-portal/show-custom-template.png)

Outra opção é selecionar um modelo já existente nos [Modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/). Esses modelos são fornecidos como contribuição pela comunidade. Eles abrangem vários cenários comuns, e alguém pode ter adicionado um modelo parecido com o que você está tentando implantar. Você pode pesquisar nos modelos para encontrar algo que corresponda ao seu cenário.

![selecionar modelo de início rápido](./media/resource-group-portal/select-quickstart-template.png)

Depois de selecionar um modelo, ele é carregado no editor.

## Exibir sua assinatura e custos

Você pode exibir informações sobre sua assinatura e os custos acumulados para todos os recursos. Selecione **Assinaturas** e a assinatura que você deseja ver. Talvez você só tenha uma assinatura para selecionar.

![assinatura](./media/resource-group-portal/select-subscription.png)

Na folha da assinatura, você verá uma taxa de gravação.

![taxa de gravação](./media/resource-group-portal/burn-rate.png)

Haverá também uma divisão de custos por tipo de recurso.

![custo de recurso](./media/resource-group-portal/cost-by-resource.png)

## Controle de acesso para painéis do Azure

O acesso às informações exibidas pela maioria dos blocos no portal é controlado pelo [Controle de Acesso Baseado em Função](../active-directory/role-based-access-control-configure.md) do Azure. Para integrar painéis ao ecossistema diretamente, todos os painéis publicados são implementados como recursos do Azure. Do ponto de vista do controle de acesso, os painéis não são diferentes de uma máquina virtual ou de uma conta de armazenamento.

Aqui está um exemplo. Digamos que você tenha uma assinatura do Azure e vários membros de sua equipe receberam atribuições para as funções de **proprietário**, **colaborador** ou **leitor** da assinatura. Os usuários que são proprietários ou colaboradores poderão listar, exibir, criar, modificar ou excluir painéis na assinatura. Os usuários que são os leitores poderão listar e exibir os painéis, mas não poderão modificá-los ou excluí-los. Os usuários com acesso de leitor poderão fazer edições locais em um painel publicado (por exemplo, ao solucionar um problema), mas não terão a opção de publicar essas alterações no servidor. Eles terão a opção de fazer uma cópia privada do painel para uso pessoal.

Observe que os blocos individuais no painel irão impor seus próprios requisitos de controle de acesso baseados nos recursos de que apresentam dados. Isso significa que você pode criar um painel que pode ser compartilhado mais amplamente e ainda proteger os dados em blocos individuais.

## Próximas etapas

- Para exibir os logs de auditoria, confira [Auditar operações com o Gerenciador de Recursos](../resource-group-audit.md).
- Para solucionar erros de implantação, confira [Solucionando problemas de implantações do grupo de recursos com o Portal do Azure](../resource-manager-troubleshoot-deployments-portal.md).

<!---HONumber=AcomDC_0518_2016-->