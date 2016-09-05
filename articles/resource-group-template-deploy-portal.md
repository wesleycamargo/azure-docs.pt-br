<properties 
	pageTitle="Usar o portal do Azure para implantar recursos do Azure | Microsoft Azure" 
	description="Use o portal do Azure e o Azure Resource Manager para implantar seus recursos." 
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
	ms.date="06/30/2016" 
	ms.author="tomfitz"/>

# Implantar recursos com modelos do Resource Manager e o portal do Azure

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [CLI do Azure](resource-group-template-deploy-cli.md)
- [Portal](resource-group-template-deploy-portal.md)
- [API REST](resource-group-template-deploy-rest.md)

Este tópico mostra como usar o [Portal do Azure](https://portal.azure.com) com o [Azure Resource Manager](resource-group-overview.md) para implantar seus recursos do Azure. Para saber sobre como gerenciar seus recursos, confira [Gerenciar recursos do Azure por meio do portal](./azure-portal/resource-group-portal.md).

Atualmente, nem todo serviço dá suporte ao portal ou ao Gerenciador de Recursos. Para esses serviços, você precisa usar o [Portal Clássico](https://manage.windowsazure.com). Para obter o status de cada serviço, confira o [Gráfico de disponibilidade do Portal do Azure](https://azure.microsoft.com/features/azure-portal/availability/).

## Criar grupo de recursos

1. Para criar um grupo de recursos vazio, selecione **Novo** > **Gerenciamento** > **Grupo de Recursos**.

    ![criar grupo de recursos vazio](./media/resource-group-template-deploy-portal/create-empty-group.png)

2. Dê a ele um nome e uma localização e, se necessário, selecione uma assinatura.

    ![definir valores de grupo](./media/resource-group-template-deploy-portal/set-group-properties.png)

## Implantar recursos do Marketplace

Após criar um grupo de recursos, você pode implantar recursos nele usando o Marketplace. O Marketplace oferece soluções predefinidas para cenários comuns.

1. Para iniciar uma implantação, selecione **Novo** e o tipo de recurso que você gostaria de implantar. Em seguida, procure a versão específica do recurso que deseja implantar.

    ![implantar recurso](./media/resource-group-template-deploy-portal/deploy-resource.png)

2. Se você não vir a solução específica que deseja implantar, você poderá pesquisá-lo no Marketplace.

    ![pesquisar no Marketplace](./media/resource-group-template-deploy-portal/search-resource.png)

3. Dependendo do tipo de recurso selecionado, você terá uma coleção de propriedades significativas para definir antes da implantação. Essas opções não são mostradas aqui, pois elas variam com base no tipo de recurso. Para todos os tipos, você deve selecionar um grupo de recursos de destino. A imagem a seguir mostra como criar um aplicativo Web e implantá-lo no grupo de recursos que você criou.

    ![criar grupo de recursos](./media/resource-group-template-deploy-portal/select-existing-group.png)

    Como alternativa, você pode optar por criar um grupo de recursos ao implantar seus recursos. Selecione **Criar novo** e dê um nome ao grupo de recursos.

    ![criar novo grupo de recursos](./media/resource-group-template-deploy-portal/select-new-group.png)

4. Sua implantação será iniciada. Ela pode levar alguns minutos. Quando a implantação for concluída, você verá uma notificação.

    ![exibir notificação](./media/resource-group-template-deploy-portal/view-notification.png)

5. Após implantar seus recursos, você poderá adicionar mais deles ao grupo de recursos usando o comando **Adicionar** na folha do grupo de recursos.

    ![adicionar recurso](./media/resource-group-template-deploy-portal/add-resource.png)

## Implantar recursos de um modelo personalizado

Se quiser executar uma implantação, mas não usar nenhum dos modelos no Marketplace, você poderá criar um modelo personalizado que define a infraestrutura para sua solução. Para saber mais sobre a criação de modelos, veja [Criando modelos do Gerenciador de Recursos do Azure](resource-group-authoring-templates.md).

1. Para implantar um modelo personalizado usando o portal, selecione **Novo** e comece a procurar pela **Implantação do Modelo** até conseguir selecioná-la nas opções.

    ![procurar implantação de modelo](./media/resource-group-template-deploy-portal/search-template.png)

2. Selecione a **Implantação do Modelo** entre os recursos disponíveis.

    ![selecionar implantação de modelo](./media/resource-group-template-deploy-portal/select-template.png)

3. Depois de iniciar a implantação do modelo, abra o modelo em branco que está disponível para personalização.

    ![criar modelo](./media/resource-group-template-deploy-portal/show-custom-template.png)

    No editor, adicione a sintaxe JSON que define os recursos que você deseja implantar. Selecione **Salvar** quando terminar. Para obter as diretrizes sobre como escrever a sintaxe JSON, confira o [Passo a passo do modelo do Resource Manager](resource-manager-template-walkthrough.md).

    ![editar modelo](./media/resource-group-template-deploy-portal/edit-template.png)

4. Ou você pode selecionar um modelo já existente entre os [modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/). Esses modelos são fornecidos como contribuição pela comunidade. Eles abrangem vários cenários comuns, e alguém pode ter adicionado um modelo parecido com o que você está tentando implantar. Você pode pesquisar nos modelos para encontrar algo que corresponda ao seu cenário.

    ![selecionar modelo de início rápido](./media/resource-group-template-deploy-portal/select-quickstart-template.png)

    Você pode exibir o modelo selecionado no editor.

5. Depois de fornecer todos os outros valores, selecione **Criar** para implantar o modelo.

    ![implantar modelo](./media/resource-group-template-deploy-portal/create-custom-deploy.png)

## Implantar recursos de um modelo salvo em sua conta

O portal permite que você salve um modelo em sua conta do Azure e o reimplante mais tarde. Para obter mais informações sobre como trabalhar com esses modelos salvos, confira [Introdução aos modelos privados no Portal do Azure](./marketplace-consumer/mytemplates-getstarted.md).

1. Para localizar os modelos salvos, selecione **Procurar** > **Modelos**.

    ![procurar modelos](./media/resource-group-template-deploy-portal/browse-templates.png)

2. Na lista de modelos salvos em sua conta, selecione aquele no qual deseja trabalhar.

    ![modelos salvos](./media/resource-group-template-deploy-portal/saved-templates.png)

3. Selecione **Implantar** para reimplantar o modelo salvo.

    ![implantar modelo salvo](./media/resource-group-template-deploy-portal/deploy-saved-template.png)

## Próximas etapas

- Para exibir os logs de auditoria, confira [Operações de auditoria com o Gerenciador de Recursos](resource-group-audit.md).
- Para solucionar os erros de implantação, confira [Solução de problemas das implantações dos grupos de recursos com o Portal do Azure](resource-manager-troubleshoot-deployments-portal.md).
- Para recuperar um modelo de uma implantação ou de um grupo de recursos, confira [Exportar um modelo do Azure Resource Manager a partir dos recursos existentes](resource-manager-export-template.md).

<!---HONumber=AcomDC_0824_2016-->