<properties 
	pageTitle="Usar o Portal de Visualização do Azure para gerenciar os recursos do Azure | Microsoft Azure" 
	description="Agrupe vários recursos como um grupo lógico que se torna o limite do ciclo de vida para recursos contidos nele." 
	services="azure-resource-manager,azure-portal" 
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
	ms.date="09/16/2015" 
	ms.author="tomfitz"/>


# Usando o Portal de Visualização do Azure para gerenciar os recursos do Azure

## Introdução

Historicamente, para gerenciar um recurso (como um servidor de banco de dados, um banco de dados ou um aplicativo Web) no Microsoft Azure era necessário executar operações em um recurso de cada vez. Se você tivesse um aplicativo complexo composto de vários recursos, seria necessário coordenar manualmente alterações na infraestrutura do aplicativo. No Portal de Visualização do Azure, você pode usar o Gerenciador de Recursos do Azure para criar grupos de recursos para implantar e gerenciar todos os recursos em um aplicativo coletivamente.

Normalmente, um grupo de recursos contém recursos relacionados a um aplicativo específico. Por exemplo, um grupo pode conter um aplicativo Web que hospeda seu site público, um Banco de Dados SQL que armazena dados relacionais usados pelo site e uma conta de Armazenamento que armazena ativos não relacionais. Todos os recursos em um grupo de recursos devem compartilhar o mesmo ciclo de vida. Para obter mais informações sobre o Gerenciador de Recursos, consulte [Visão geral do Gerenciador de Recursos](../resource-group-overview.md).

Este tópico fornece uma visão geral de como usar grupos de recursos no portal de visualização do Azure.

## Criar recursos e grupo de recursos

Se você precisa criar um grupo de recursos vazio, você pode selecionar **Novo**, **Gerenciamento** e **Grupo de Recursos**.

![criar grupo de recursos vazio](./media/resource-group-portal/create-empty-group.png)

Você dá a ele um nome e um local e, se necessário, seleciona uma assinatura.

![definir valores de grupo](./media/resource-group-portal/set-group-properties.png)

No entanto, você não precisa criar explicitamente um grupo de recursos vazio. Ao criar um novo recurso, você pode optar por criar um novo grupo de recursos ou usar um grupo de recursos existente. A imagem a seguir mostra como criar um novo aplicativo Web com a opção de selecionar um grupo de recursos existente ou criar um novo.

![criar grupo de recursos](./media/resource-group-portal/select-existing-group.png)

## Procurar grupos de recursos

Você pode procurar entre todos os grupos de recursos clicando em **Procurar Tudo** e **Grupos de recursos**.

![procurar grupos de recursos](./media/resource-group-portal/browse-groups.png)

Quando você seleciona um determinado grupo de recursos, você vê uma folha do grupo de recursos que fornece informações sobre esse grupo, incluindo uma lista de todos os recursos contidos no grupo.

![resumo do grupo de recursos](./media/resource-group-portal/group-summary.png)

A folha do grupo de recursos também fornece uma visão unificada de suas informações de cobrança e monitoramento de todos os recursos no grupo de recursos.

![monitoramento e cobrança](./media/resource-group-portal/monitoring-billing.png)

## Personalizar a interface

Para obter acesso rápido ao resumo do grupo de recursos, você pode fixar a folha no seu Quadro Inicial.

![fixar grupo de recursos](./media/resource-group-portal/pin-group.png)

Ou então, você pode fixar uma seção da folha no Quadro Inicial selecionando as reticências (...) acima da seção. Você também pode personalizar o tamanho da seção na folha ou removê-los completamente. A imagem a seguir mostra como fixar, personalizar ou remover a seção Eventos.

![fixar seção](./media/resource-group-portal/pin-section.png)

Depois de fixar a seção de Eventos para o Quadro Inicial, você verá um resumo dos eventos no Quadro Inicial.

![quadro inicial de eventos](./media/resource-group-portal/events-startboard.png)

E selecioná-lo imediatamente conduz você a mais detalhes sobre os eventos.

## Exibição após implantações

Da folha do grupo de recursos, você pode ver a data e o status da última implantação para esse grupo de recursos. Selecionar o link, exibe um histórico das implantações para o grupo.

![última implantação](./media/resource-group-portal/last-deployment.png)

A seleção de qualquer implantação do histórico mostra detalhes sobre essa implantação.

![resumo da implantação](./media/resource-group-portal/deployment-summary.png)

Você pode ver as operações individuais que foram executadas durante a implantação. A imagem a seguir mostra uma operação bem-sucedida e uma que falhou.

![detalhes da operação](./media/resource-group-portal/operation-details.png)

Selecionar qualquer uma das operações mostra mais detalhes sobre a operação. Isso pode ser especialmente útil quando uma operação tiver falhado, conforme mostrado abaixo. Isso pode ajudar a solucionar o motivo pelo qual uma implantação falhou. Na imagem a seguir, você pode ver que o site não foi implantado porque seu nome não era exclusivo.

![mensagem da operação](./media/resource-group-portal/operation-message.png)

## Exibindo logs de auditoria

O log de auditoria contém não apenas operações de implantação, mas todas as operações de gerenciamento obtidas em recursos em sua assinatura. Por exemplo, você pode ver nos logs de auditoria quando alguém na sua organização parou um aplicativo. Para ver os logs de auditoria, selecione **Procurar Tudo** e **Logs de Auditoria**.

![procurar logs de auditoria](./media/resource-group-portal/browse-audit-logs.png)

Na seção operações, você pode ver as operações individuais que foram executadas em sua assinatura.

![exibir log de auditoria](./media/resource-group-portal/view-audit-log.png)

Ao selecionar qualquer uma das operações, você pode ver mais detalhes, incluindo qual usuário executou a operação.

Você pode filtrar o que é mostrado no log de auditoria, selecionando **Filtro**.

![filtrar log](./media/resource-group-portal/filter-logs.png)

Você pode selecionar o tipo das operações a mostrar, assim como as pertencentes a um grupo de recursos ou recurso, as que estão em um período de tempo especificado, são iniciadas por um chamador específico ou têm determinados níveis de operação.

![opções de filtragem](./media/resource-group-portal/filter-options.png)

## Adicionando recursos a grupos de recursos

Você pode adicionar recursos a um grupo de recursos usando o comando **Adicionar** na lâmina do grupo de recursos.

![adicionar recurso](./media/resource-group-portal/add-resource.png)

Você pode selecionar o recurso desejado na lista disponível.

## Excluindo grupos de recursos

Como os grupos de recursos permitem que você gerencie o ciclo de vida de todos os recursos contidos, a exclusão de um grupo de recursos resultará na exclusão de todos os recursos contidos no grupo. Você também pode excluir recursos individuais de um grupo de recursos. Tenha cuidado ao excluir um grupo de recursos pois pode haver outros recursos vinculados a ele. Você pode ver os recursos vinculados no mapa de recursos e tomar as medidas necessárias para evitar consequências não intencionais ao excluir grupos de recursos. Os recursos vinculados não serão excluídos, mas podem não funcionar conforme esperado.

![excluir grupo](./media/resource-group-portal/delete-group.png)

## Marcação de recursos

Você pode aplicar marcas a recursos e grupos de recursos para organizar seus ativos de modo lógico. Para obter informações sobre como trabalhar com marcas por meio do portal de visualização, consulte [Usando marcas para organizar os recursos do Azure](../resource-group-using-tags.md).

## Implantando um modelo personalizado

Se você quiser executar uma implantação, mas não usa nenhum dos modelos no marketplace, você pode criar um modelo personalizado que define a infraestrutura para sua solução. Para obter mais informações sobre modelos, consulte [Criação de modelos do Gerenciador de Recursos do Azure](../resource-group-authoring-templates.md).

Para implantar um modelo personalizado por meio do portal de visualização, selecione **Novo**, **Marketplace** e **Tudo**.

![localizar implantação de modelo](./media/resource-group-portal/launch-template.png)

Procure **Implantação de Modelo** e selecione esse item na lista retornada.

![procurar implantação de modelo](./media/resource-group-portal/search-template.png)

Depois de iniciar a implantação de modelo, você pode criar o modelo personalizado e definir valores para a implantação.

![criar modelo](./media/resource-group-portal/show-custom-template.png)

## Próximas etapas
Introdução

- Para obter uma introdução aos conceitos no Gerenciador de Recursos, consulte [Visão Geral do Gerenciador de Recursos do Azure](../resource-group-overview.md).  
- Para obter uma introdução ao uso do Azure PowerShell ao implantar recursos, consulte [Usando o Azure PowerShell com o Gerenciador de Recursos do Azure](../powershell-azure-resource-manager.md).
- Para obter uma introdução ao uso da CLI do Azure ao implantar recursos, consulte [Usando a CLI do Azure para Mac, Linux e Windows com o Gerenciamento de Recursos do Azure](../xplat-cli-azure-resource-manager.md). 
  


 

<!---HONumber=Oct15_HO3-->