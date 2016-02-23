<properties 
	pageTitle="Usar o portal do Azure para gerenciar os recursos do Azure | Microsoft Azure" 
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
	ms.topic="get-started-article" 
	ms.date="02/11/2016" 
	ms.author="tomfitz"/>


# Usando o Portal do Azure para gerenciar os recursos do Azure

## Introdução

O Gerenciador de Recursos do Azure permite implantar e gerenciar suas soluções por meio de grupos de recursos. Este tópico fornece uma visão geral de como usar grupos de recursos no portal do Azure. Normalmente, um grupo de recursos contém recursos relacionados a um aplicativo específico. Por exemplo, um grupo pode conter um aplicativo Web que hospeda seu site público, um Banco de Dados SQL que armazena dados relacionais usados pelo site e uma conta de Armazenamento que armazena ativos não relacionais. Todos os recursos em um grupo de recursos devem compartilhar o mesmo ciclo de vida. Para obter mais informações sobre o Gerenciador de Recursos, consulte [Visão geral do Gerenciador de Recursos](../resource-group-overview.md).

Atualmente, nem todo serviço dá suporte ao portal ou ao Gerenciador de Recursos. Para esses serviços, você precisará usar o [portal clássico](https://manage.windowsazure.com). Para conhecer o status de cada serviço, confira o [Gráfico de disponibilidade do portal do Azure](https://azure.microsoft.com/features/azure-portal/availability/).

Você também pode gerenciar recursos por meio do Azure PowerShell e da CLI do Azure. Para obter mais informações sobre como usar essas interfaces, confira [Como usar o Azure PowerShell com o Gerenciador de Recursos do Azure](../powershell-azure-resource-manager.md) e [Usar a CLI do Azure para Mac, Linux e Windows com o Gerenciador de Recursos do Azure](../xplat-cli-azure-resource-manager.md).


## Criar recursos e grupo de recursos

Se você precisa criar um grupo de recursos vazio, você pode selecionar **Novo**, **Gerenciamento** e **Grupo de Recursos**.

![criar grupo de recursos vazio](./media/resource-group-portal/create-empty-group.png)

Você dá a ele um nome e um local e, se necessário, seleciona uma assinatura.

![definir valores de grupo](./media/resource-group-portal/set-group-properties.png)

Ao implantar os recursos, você pode optar por implantá-los no grupo de recursos que criou. A imagem a seguir mostra como criar um novo aplicativo Web em um grupo de recursos existente.

![criar grupo de recursos](./media/resource-group-portal/select-existing-group.png)

Como alternativa, você pode optar por criar um novo grupo de recursos ao implantar ois recursos. Em vez de selecionar um dos grupos de recursos existentes em sua assinatura, selecione **Novo** e dê um nome ao grupo de recursos.

![criar novo grupo de recursos](./media/resource-group-portal/select-new-group.png)

## Procurar grupos de recursos

Você pode procurar entre todos os grupos de recursos clicando em **Grupos de recursos**.

![procurar grupos de recursos](./media/resource-group-portal/browse-groups.png)

Quando você seleciona um determinado grupo de recursos, você vê uma folha do grupo de recursos que fornece informações sobre esse grupo, incluindo uma lista de todos os recursos contidos no grupo.

![resumo do grupo de recursos](./media/resource-group-portal/group-summary.png)

A folha do grupo de recursos também fornece uma visão unificada de suas informações de cobrança e monitoramento de todos os recursos no grupo de recursos.

![monitoramento e cobrança](./media/resource-group-portal/monitoring-billing.png)

## Exibir sua assinatura e custos

Você pode exibir informações sobre sua assinatura e os custos acumulados para todos os recursos. Selecione **Assinaturas** e a assinatura que você deseja ver. Talvez você só tenha uma assinatura para selecionar.

![assinatura](./media/resource-group-portal/select-subscription.png)

Na folha da assinatura, você verá uma taxa de gravação.

![taxa de gravação](./media/resource-group-portal/burn-rate.png)

Haverá também uma divisão de custos por tipo de recurso.

![custo de recurso](./media/resource-group-portal/cost-by-resource.png)

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

Você pode aplicar marcas a recursos e grupos de recursos para organizar seus ativos de modo lógico. Para obter informações sobre como trabalhar com marcas usando o portal, confira [Usando marcas para organizar os recursos do Azure](../resource-group-using-tags.md).

## Implantando um modelo personalizado

Se você quiser executar uma implantação, mas não usa nenhum dos modelos no marketplace, você pode criar um modelo personalizado que define a infraestrutura para sua solução. Para saber mais sobre modelos, confira [Criando modelos do Gerenciador de Recursos do Azure](../resource-group-authoring-templates.md).

Para implantar um modelo personalizado por meio do portal, selecione **Novo** e comece a procurar **Implantação de Modelo** até poder selecioná-lo nas opções.

![procurar implantação de modelo](./media/resource-group-portal/search-template.png)

Selecione **Implantação de Modelo** dos recursos disponíveis.

![selecionar implantação de modelo](./media/resource-group-portal/select-template.png)

Depois de iniciar a implantação de modelo, você pode criar o modelo personalizado e definir valores para a implantação.

![criar modelo](./media/resource-group-portal/show-custom-template.png)

## Próximas etapas
Introdução

- Para obter uma introdução aos conceitos no Gerenciador de Recursos, consulte [Visão Geral do Gerenciador de Recursos do Azure](../resource-group-overview.md).
- Para obter uma introdução ao uso do Azure PowerShell ao implantar recursos, confira [Usando o Azure PowerShell com o Gerenciador de Recursos do Azure](../powershell-azure-resource-manager.md).
- Para obter uma introdução ao uso da CLI do Azure ao implantar recursos, confira [Usando a CLI do Azure para Mac, Linux e Windows com o Gerenciamento de Recursos do Azure](../xplat-cli-azure-resource-manager.md).

<!---HONumber=AcomDC_0218_2016-->