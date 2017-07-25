---
title: Usar o Portal do Azure para implantar recursos do Azure | Microsoft Docs
description: Use o portal do Azure e o Azure Resource Manager para implantar seus recursos.
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 2c98a4aa-8d9f-4a0a-b764-214dbe8ed009
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: 85545ab2774097c7af9353fe8fc92c34e47354d9
ms.contentlocale: pt-br
ms.lasthandoff: 07/21/2017

---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Implantar recursos com modelos do Resource Manager e o portal do Azure
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-template-deploy.md)
> * [CLI do Azure](resource-group-template-deploy-cli.md)
> * [Portal](resource-group-template-deploy-portal.md)
> * [API REST](resource-group-template-deploy-rest.md)
> 
> 

Este tópico mostra como usar o [Portal do Azure](https://portal.azure.com) com o [Azure Resource Manager](resource-group-overview.md) para implantar seus recursos do Azure. Para saber sobre como gerenciar seus recursos, confira [Gerenciar recursos do Azure por meio do portal](resource-group-portal.md).

Atualmente, nem todo serviço dá suporte ao portal ou ao Gerenciador de Recursos. Para esses serviços, você precisa usar o [Portal Clássico](https://manage.windowsazure.com). Para obter o status de cada serviço, confira o [Gráfico de disponibilidade do Portal do Azure](https://azure.microsoft.com/features/azure-portal/availability/).

## <a name="create-resource-group"></a>Criar grupo de recursos
1. Para criar um grupo de recursos vazio, selecione **Novo** > **Gerenciamento** > **Grupo de Recursos**.
   
    ![criar grupo de recursos vazio](./media/resource-group-template-deploy-portal/create-empty-group.png)
2. Dê a ele um nome e uma localização e, se necessário, selecione uma assinatura. Você precisa fornecer um local para o grupo de recursos pois o grupo de recursos armazena metadados sobre os recursos. Por motivos de conformidade, talvez você queira especificar onde os metadados são armazenados. Em geral, é recomendável que você especifique um local em que a maioria de seus recursos residirá. Usar o mesmo local pode simplificar seu modelo.
   
    ![definir valores de grupo](./media/resource-group-template-deploy-portal/set-group-properties.png)

## <a name="deploy-resources-from-marketplace"></a>Implantar recursos do Marketplace
Após criar um grupo de recursos, você pode implantar recursos nele usando o Marketplace. O Marketplace oferece soluções predefinidas para cenários comuns.

1. Para iniciar uma implantação, selecione **Novo** e o tipo de recurso que você gostaria de implantar. Em seguida, procure a versão específica do recurso que deseja implantar.
   
    ![implantar recurso](./media/resource-group-template-deploy-portal/deploy-resource.png)
2. Se você não vir a solução específica que deseja implantar, você poderá pesquisá-lo no Marketplace.
   
    ![pesquisar no Marketplace](./media/resource-group-template-deploy-portal/search-resource.png)
3. Dependendo do tipo de recurso selecionado, você terá uma coleção de propriedades significativas para definir antes da implantação. Essas opções não são mostradas aqui, pois elas variam com base no tipo de recurso. Para todos os tipos, você deve selecionar um grupo de recursos de destino. A imagem a seguir mostra como criar um aplicativo Web e implantá-lo no grupo de recursos que você criou.
   
    ![Criar grupo de recursos](./media/resource-group-template-deploy-portal/select-existing-group.png)
   
    Como alternativa, você pode optar por criar um grupo de recursos ao implantar seus recursos. Selecione **Criar novo** e dê um nome ao grupo de recursos.
   
    ![criar novo grupo de recursos](./media/resource-group-template-deploy-portal/select-new-group.png)
4. Sua implantação será iniciada. Ela pode levar alguns minutos. Quando a implantação for concluída, você verá uma notificação.
   
    ![exibir notificação](./media/resource-group-template-deploy-portal/view-notification.png)
5. Após implantar seus recursos, você poderá adicionar mais deles ao grupo de recursos usando o comando **Adicionar** na folha do grupo de recursos.
   
    ![adicionar recurso](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Implantar recursos de um modelo personalizado
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

## <a name="deploy-resources-from-a-template-saved-to-your-account"></a>Implantar recursos de um modelo salvo em sua conta
O portal permite que você salve um modelo em sua conta do Azure e o reimplante mais tarde. Para obter mais informações sobre como trabalhar com esses modelos salvos, confira [Introdução aos modelos privados no Portal do Azure](../marketplace-consumer/mytemplates-getstarted.md).

1. Para localizar os modelos salvos, selecione **Procurar** > **Modelos**.
   
    ![procurar modelos](./media/resource-group-template-deploy-portal/browse-templates.png)
2. Na lista de modelos salvos em sua conta, selecione aquele no qual deseja trabalhar.
   
    ![modelos salvos](./media/resource-group-template-deploy-portal/saved-templates.png)
3. Selecione **Implantar** para reimplantar o modelo salvo.
   
    ![implantar modelo salvo](./media/resource-group-template-deploy-portal/deploy-saved-template.png)

## <a name="next-steps"></a>Próximas etapas
* Para exibir os logs de auditoria, confira [Operações de auditoria com o Gerenciador de Recursos](resource-group-audit.md).
* Para solucionar erros de implantação, confira [View deployment operations](resource-manager-deployment-operations.md) (Exibir operações de implantação).
* Para recuperar um modelo de uma implantação ou de um grupo de recursos, confira [Exportar um modelo do Azure Resource Manager a partir dos recursos existentes](resource-manager-export-template.md).
* Para obter orientação sobre como as empresas podem usar o Resource Manager para gerenciar assinaturas de forma eficaz, consulte [Azure enterprise scaffold – controle de assinatura prescritivas](resource-manager-subscription-governance.md).
* Para uma série de quatro partes sobre como automatizar a implantação, consulte [automatizar implantações de aplicativo para Azure Virtual Machines](../virtual-machines/windows/dotnet-core-1-landing.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Esta série aborda a arquitetura do aplicativo, acesso e segurança, disponibilidade e dimensionamento e implantação de aplicativos.


