<properties 
	pageTitle="Usar o portal do Azure para gerenciar os recursos do Azure | Microsoft Azure" 
	description="Use o portal do Azure e o Azure Resource Manager para implantar e gerenciar seus recursos. Mostra como marcar recursos e exibir logs de auditoria." 
	services="azure-resource-manager,azure-portal" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="03/29/2016" 
	ms.author="tomfitz"/>


# Usando o Portal do Azure para implantar e gerenciar os recursos do Azure

## Introdução

Este tópico mostra como usar o [portal do Azure](https://portal.azure.com) com o [Azure Resource Manager](../resource-group-overview.md) para implantar e gerenciar os recursos do Azure.

Atualmente, nem todo serviço dá suporte ao portal ou ao Gerenciador de Recursos. Para esses serviços, você precisará usar o [portal clássico](https://manage.windowsazure.com). Para conhecer o status de cada serviço, confira o [Gráfico de disponibilidade do portal do Azure](https://azure.microsoft.com/features/azure-portal/availability/).

Você também pode gerenciar recursos por meio do Azure PowerShell e da CLI do Azure. Para obter mais informações sobre como usar essas interfaces, confira [Como usar o Azure PowerShell com o Azure Resource Manager](../powershell-azure-resource-manager.md) e [Usar a CLI do Azure para Mac, Linux e Windows com o Azure Resource Manager](../xplat-cli-azure-resource-manager.md). Para saber mais sobre como implantar soluções com o Visual Studio, confira [Criar e implantar grupos de recursos do Azure com o Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## Criar e gerenciar grupo de recursos

Para criar um grupo de recursos vazio, selecione **Novo**, **Gerenciamento** e **Grupo de Recursos**.

![criar grupo de recursos vazio](./media/resource-group-portal/create-empty-group.png)

Você dá a ele um nome e um local e, se necessário, seleciona uma assinatura.

![definir valores de grupo](./media/resource-group-portal/set-group-properties.png)

Depois que seu grupo de recursos é criado, você pode implantar recursos nele. Para iniciar a implantação, basta selecionar **Novo** e o tipo de recurso que você deseja implantar.

![implantar recurso](./media/resource-group-portal/deploy-resource.png)

Se você não vir o tipo de recurso que deseja implantar, poderá pesquisá-lo no Marketplace.

![pesquisar no Marketplace](./media/resource-group-portal/search-resource.png)

Dependendo do tipo de recurso selecionado, você terá uma coleção de propriedades significativas para definir antes da implantação. Essas opções não são mostradas aqui, pois elas variam com base no tipo de recurso. Para todos os tipos, você deve selecionar um grupo de recursos de destino. A imagem a seguir mostra como criar um novo aplicativo Web e implantá-lo no grupo de recursos que você acabou de criar.

![criar grupo de recursos](./media/resource-group-portal/select-existing-group.png)

Como alternativa, você pode optar por criar um novo grupo de recursos ao implantar ois recursos. Em vez de selecionar um dos grupos de recursos existentes em sua assinatura, selecione **Novo** e dê um nome ao grupo de recursos.

![criar novo grupo de recursos](./media/resource-group-portal/select-new-group.png)

Sua implantação começará. Isso pode levar alguns minutos. Quando a implantação for concluída, você verá uma notificação.

![exibir notificação](./media/resource-group-portal/view-notification.png)

### Adicionar recursos a um grupo de recursos existente

Você pode adicionar recursos a um grupo de recursos usando o comando **Adicionar** na lâmina do grupo de recursos.

![adicionar recurso](./media/resource-group-portal/add-resource.png)

Você pode selecionar o recurso desejado na lista disponível.

### Procurar grupos de recursos

Você pode procurar entre todos os grupos de recursos clicando em **Grupos de recursos**.

![procurar grupos de recursos](./media/resource-group-portal/browse-groups.png)

Quando você seleciona um determinado grupo de recursos, você vê uma folha do grupo de recursos que fornece informações sobre esse grupo, incluindo uma lista de todos os recursos contidos no grupo.

![resumo do grupo de recursos](./media/resource-group-portal/group-summary.png)

Você pode adicionar mais tabelas e gráficos na folha do grupo de recursos selecionando **Adicionar uma seção** abaixo do resumo.

![adicionar seção](./media/resource-group-portal/add-section.png)

Você verá uma galeria de blocos para selecionar as informações que deseja incluir na folha. Os tipos de bloco exibidos são filtrados por tipo de recurso. A escolha de um recurso diferente altera os blocos disponíveis.

![adicionar seção](./media/resource-group-portal/tile-gallery.png)

Arraste o bloco que desejar até os espaços disponíveis.

![arrastar bloco](./media/resource-group-portal/drag-tile.png)

Depois de selecionar **Concluído** na parte superior do portal, o novo modo de exibição se tornará parte da folha.

![mostrar bloco](./media/resource-group-portal/show-lens.png)

Para obter acesso rápido a um grupo de recursos, você pode fixar a folha em seu painel.

![fixar grupo de recursos](./media/resource-group-portal/pin-group.png)

Como alternativa, você pode fixar uma seção da folha no painel selecionando as reticências (...) acima da seção. Você também pode personalizar o tamanho da seção na folha ou removê-los completamente. A imagem a seguir mostra como fixar, personalizar ou remover a seção CPU e Memória.

![fixar seção](./media/resource-group-portal/pin-cpu-section.png)

Depois de fixar a seção no painel, você verá o resumo nele.

![exibir painel](./media/resource-group-portal/view-startboard.png)

E selecioná-lo imediatamente conduz você a mais detalhes sobre os dados.

### Excluir grupo de recursos

Como os grupos de recursos permitem que você gerencie o ciclo de vida de todos os recursos contidos, a exclusão de um grupo de recursos resultará na exclusão de todos os recursos contidos no grupo. Você também pode excluir recursos individuais de um grupo de recursos. Tenha cuidado ao excluir um grupo de recursos pois pode haver outros recursos vinculados a ele. Você pode ver os recursos vinculados no mapa de recursos e tomar as medidas necessárias para evitar consequências não intencionais ao excluir grupos de recursos. Os recursos vinculados não serão excluídos, mas podem não funcionar conforme esperado.

![excluir grupo](./media/resource-group-portal/delete-group.png)


## Exibir implantações anteriores

Da folha do grupo de recursos, você pode ver a data e o status da última implantação para esse grupo de recursos. Selecionar o link, exibe um histórico das implantações para o grupo.

![última implantação](./media/resource-group-portal/last-deployment.png)

A seleção de qualquer implantação do histórico mostra detalhes sobre essa implantação.

![resumo da implantação](./media/resource-group-portal/deployment-summary.png)

Você pode ver as operações individuais que foram executadas durante a implantação. A imagem a seguir mostra uma operação bem-sucedida e uma que falhou.

![detalhes da operação](./media/resource-group-portal/operation-details.png)

Para saber mais sobre como solucionar problemas de uma implantação, confira [Solucionando problemas de implantações de grupo de recursos com o Portal do Azure](../resource-manager-troubleshoot-deployments-portal.md).

Você pode recuperar o modelo que foi usado para a implantação selecionando **Exportar modelo**.

![exportar modelo](./media/resource-group-portal/export-template.png)

Você verá o modelo exato usado para essa implantação.

![mostrar modelo](./media/resource-group-portal/show-template.png)

Não é uma representação completa do grupo de recursos. Se você adicionou ou excluiu recursos fora dessa implantação, essas ações não serão refletidas no modelo. A folha inclui o modelo, um arquivo de parâmetro para usar com o modelo e um script do PowerShell para implantar o modelo. Você pode baixar esses três arquivos selecionando **Salvar no arquivo**.

## Exibir logs de auditoria

O log de auditoria contém não apenas operações de implantação, mas todas as operações de gerenciamento obtidas em recursos em sua assinatura. Por exemplo, você pode ver nos logs de auditoria quando alguém na sua organização parou um aplicativo. Para ver os logs de auditoria, selecione **Procurar Tudo** e **Logs de Auditoria**.

![procurar logs de auditoria](./media/resource-group-portal/browse-audit-logs.png)

Na seção operações, você pode ver as operações individuais que foram executadas em sua assinatura.

![exibir log de auditoria](./media/resource-group-portal/view-audit-log.png)

Ao selecionar qualquer uma das operações, você pode ver mais detalhes, incluindo qual usuário executou a operação.

Para saber mais sobre como trabalhar com os logs de auditoria, confira [Operações de auditoria com o Gerenciador de Recursos](../resource-group-audit.md).

## Recursos de marca

Você pode aplicar marcas a recursos e grupos de recursos para organizar seus ativos de modo lógico. Para obter informações sobre como trabalhar com marcas usando o portal, confira [Usando marcas para organizar os recursos do Azure](../resource-group-using-tags.md).

## Implantar um modelo personalizado

Se você quiser executar uma implantação, mas não usa nenhum dos modelos no marketplace, você pode criar um modelo personalizado que define a infraestrutura para sua solução. Para saber mais sobre modelos, confira [Criando modelos do Gerenciador de Recursos do Azure](../resource-group-authoring-templates.md).

Para implantar um modelo personalizado usando o portal, selecione **Novo** e comece a procurar por **Implantação de Modelo** até poder selecioná-lo nas opções.

![procurar implantação de modelo](./media/resource-group-portal/search-template.png)

Selecione **Implantação de Modelo** nos recursos disponíveis.

![selecionar implantação de modelo](./media/resource-group-portal/select-template.png)

Depois de iniciar a implantação de modelo, você pode criar o modelo personalizado e definir valores para a implantação.

![criar modelo](./media/resource-group-portal/show-custom-template.png)

## Exibir sua assinatura e custos

Você pode exibir informações sobre sua assinatura e os custos acumulados para todos os recursos. Selecione **Assinaturas** e a assinatura que você deseja ver. Talvez você só tenha uma assinatura para selecionar.

![assinatura](./media/resource-group-portal/select-subscription.png)

Na folha da assinatura, você verá uma taxa de gravação.

![taxa de gravação](./media/resource-group-portal/burn-rate.png)

Haverá também uma divisão de custos por tipo de recurso.

![custo de recurso](./media/resource-group-portal/cost-by-resource.png)

## Controle de acesso para painéis do Azure

O acesso às informações exibidas pela maioria dos blocos no portal é controlado pelo [Controle de acesso baseado em função](../active-directory/role-based-access-control-configure.md) do Azure. Para integrar painéis ao ecossistema diretamente, todos os painéis publicados são implementados como recursos do Azure. Do ponto de vista do controle de acesso, os painéis não são diferentes de uma máquina virtual ou de uma conta de armazenamento.

Aqui está um exemplo. Digamos que você tem uma assinatura do Azure e vários membros da equipe receberam atribuições das funções de **proprietário**, **colaborador** ou **leitor** da assinatura. Os usuários que são proprietários ou colaboradores poderão listar, exibir, criar, modificar ou excluir painéis na assinatura. Os usuários que são os leitores poderão listar e exibir os painéis, mas não poderão modificá-los ou excluí-los. Os usuários com acesso de leitor poderão fazer edições locais em um painel publicado (por exemplo, ao solucionar um problema), mas não terão a opção de publicar essas alterações no servidor. Eles terão a opção de fazer uma cópia privada do painel para uso pessoal.

Observe que os blocos individuais no painel irão impor seus próprios requisitos de controle de acesso baseados nos recursos de que apresentam dados. Isso significa que você pode criar um painel que pode ser compartilhado mais amplamente e ainda proteger os dados em blocos individuais.

## Próximas etapas

- Para obter uma introdução aos conceitos no Gerenciador de Recursos, consulte [Visão Geral do Gerenciador de Recursos do Azure](../resource-group-overview.md).
- Para obter uma introdução ao uso do Azure PowerShell ao implantar recursos, confira [Usando o Azure PowerShell com o Gerenciador de Recursos do Azure](../powershell-azure-resource-manager.md).
- Para obter uma introdução ao uso da CLI do Azure ao implantar recursos, confira [Usando a CLI do Azure para Mac, Linux e Windows com o Gerenciamento de Recursos do Azure](../xplat-cli-azure-resource-manager.md).

<!---HONumber=AcomDC_0330_2016-->