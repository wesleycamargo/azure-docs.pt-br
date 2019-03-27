---
title: Criar um blueprint no portal
description: Use o Azure Blueprints para criar, definir e implantar artefatos por meio do portal do Azure.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/11/2019
ms.topic: quickstart
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: fdf87bff026dee4969b3995b37c31de3ead7714b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58004911"
---
# <a name="define-and-assign-an-azure-blueprint-in-the-portal"></a>Definir e atribuir um Azure Blueprint no portal

Aprender a criar e atribuir blueprints permite definir padrões comuns para desenvolver configurações reutilizáveis e que podem ser implantadas rapidamente com base em modelos do Resource Manager, políticas, segurança e muito mais. Neste tutorial, você aprenderá a usar o Blueprint do Azure para executar algumas das tarefas comuns relacionadas à criação, publicação e atribuição de um blueprint dentro de sua organização, como:

> [!div class="checklist"]
> - Criar um novo projeto e adicionar vários artefatos com suporte
> - Faça alterações em um blueprint existente ainda em **Rascunho**
> - Marcar um plano gráfico como pronto para atribuir com **publicado**
> - Atribuir um blueprint a uma assinatura existente
> - Verificar o status e o progresso de um blueprint atribuído
> - Remover um blueprint que tenha sido atribuído a uma assinatura

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-a-blueprint"></a>Criar um plano gráfico

A primeira etapa na definição de um padrão padrão para conformidade é compor um blueprint a partir dos recursos disponíveis. Neste exemplo, crie um novo blueprint chamado 'MyBlueprint' para configurar atribuições de função e política para a assinatura, adicione um novo grupo de recursos e crie um modelo de Gerenciador de Recursos e uma atribuição de função no novo grupo de recursos.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione **Definições de blueprint** da página à esquerda e clique no **+ Criar blueprint** botão na parte superior da página.

   - Alternativamente, clique em **Criar** na página **Introdução** para ir direto para a criação de um blueprint.

   ![Criar plano gráfico](./media/create-blueprint-portal/create-blueprint-button.png)

1. Forneça um **Nome do blueprint** como "MyBlueprint" (letras e números – até 48 caracteres, mas sem espaços nem caracteres especiais) para o blueprint, mas deixe **Descrição do blueprint** em branco por enquanto. Na caixa **Definir local**, clique nas reticências à direita, selecione o [grupo de gerenciamento](../management-groups/overview.md) ou a assinatura em que deseja salvar o blueprint e clique em **Selecione**.

1. Verifique se as informações estão corretas (os campos **Nome do blueprint** e **Local da definição** não podem ser alterados mais tarde) e clique em **Avançar: Artefatos** na parte inferior da página ou na guia **Artefatos** na parte superior da página.

1. Adicionar atribuição de função na assinatura: Clique com o botão esquerdo na linha **+ Adicionar artefato...** em **Assinatura** e a janela "Adicionar artefato" é aberta no lado direito do navegador. Selecione 'Atribuição de função' para _tipo de Artefato_. Em _Papel_, selecione 'Colaborador' e deixe o campo _Adicionar usuário, aplicativo ou grupo_ com a caixa de seleção indicando um **parâmetro dinâmico**. Clique em **adicionar** para adicionar este artefato para o blueprint.

   ![Artefato - atribuição de função](./media/create-blueprint-portal/add-role-assignment.png)

   > [!NOTE]
   > A maioria dos artefatos _suporta_ parâmetros. Um parâmetro atribuído a um valor durante a criação do blueprint é um **parâmetro estático**. Se o parâmetro for atribuído durante a atribuição de blueprint, ele será um **parâmetro dinâmico**. Para obter mais informações, consulte [parâmetros de blueprint](./concepts/parameters.md).

1. Adicionar designação de política na assinatura: Clique na linha **+ Adicionar artefato…** sob o artefato de atribuição de função. Selecione 'Atribuição de política' para _Tipo de artefato_. Altere _tipo_ para "Incorporado" e em _Pesquise_ insira "tag". Clique fora da _pesquisa_ para a filtragem ocorra. Selecione "Aplicar tag e seu valor padrão aos grupos de recursos" clicando nele. Clique em **adicionar** para adicionar este artefato para o plano gráfico.

1. Clique na linha da designação de política 'Aplicar tag e seu valor padrão aos grupos de recursos'. A janela para fornecer parâmetros para o artefato como parte da definição do blueprint é aberta e permite configurar os parâmetros para todas as atribuições (**parâmetros estáticos**) com base nesse blueprint em vez de durante a atribuição (**parâmetros dinâmicos**). Este exemplo usa **parâmetros dinâmicos** durante a atribuição do blueprint e, portanto, deixe os padrões e clique em **Cancelar**.

1. Adicionar o grupo de recursos na assinatura: Clique com o botão esquerdo na linha **+ Adicionar artefato...** sob a **Assinatura**. Selecione 'Grupo de recursos' para _Tipo de artefato_. Deixe os campos _Nome de exibição do artefato_, _Nome do grupo de recursos_ e _Local_ em branco, mas garanta que a caixa de seleção esteja marcada para cada propriedade de parâmetro para torná-los **parâmetros dinâmicos**. Clique em **adicionar** para adicionar este artefato para o blueprint.

1. Adicionar modelo sob o grupo de recursos: Clique com o botão esquerdo na linha **+ Adicionar Artefato...** linha sob a entrada **ResourceGroup**. Selecione 'Modelo do Azure Resource Manager' para _Tipo de artefato_, defina _Nome de exibição do artefato_ como 'StorageAccount' e deixe _Descrição_ em branco. Na guia **Template** na caixa do editor, cole o seguinte modelo do Resource Manager. Depois de colar o modelo, selecione a guia **Parâmetros** e observe que os parâmetros de modelo **storageAccountType** e **local** tenha sido detectados. Cada parâmetro foi automaticamente detectado e preenchido, mas configurado como um **parâmetro dinâmico**. Remova a marca da caixa de seleção **storageAccountType** e observe que a lista suspensa contém apenas valores incluídos no modelo do Resource Manager em **allowedValues**. Verifique a caixa para defini-lo de volta para um **parâmetro dinâmico**. Clique em **adicionar** para adicionar este artefato para o plano gráfico.

   > [!IMPORTANT]
   > Se estiver importando o modelo, verifique se o arquivo é apenas JSON e não inclui HTML. Ao apontar para uma URL no GitHub, assegure-se de ter clicado em **RAW** para obter o arquivo JSON puro e não aquele envolvido com HTML para exibição no GitHub. Um erro ocorrerá se o modelo importado não for puramente JSON.

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "storageAccountType": {
               "type": "string",
               "defaultValue": "Standard_LRS",
               "allowedValues": [
                   "Standard_LRS",
                   "Standard_GRS",
                   "Standard_ZRS",
                   "Premium_LRS"
               ],
               "metadata": {
                   "description": "Storage Account type"
               }
           },
           "location": {
               "type": "string",
               "defaultValue": "[resourceGroups('ResourceGroup').location]",
               "metadata": {
                   "description": "Location for all resources."
               }
           }
       },
       "variables": {
           "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
       },
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts",
           "name": "[variables('storageAccountName')]",
           "location": "[parameters('location')]",
           "apiVersion": "2018-07-01",
           "sku": {
               "name": "[parameters('storageAccountType')]"
           },
           "kind": "StorageV2",
           "properties": {}
       }],
       "outputs": {
           "storageAccountName": {
               "type": "string",
               "value": "[variables('storageAccountName')]"
           }
       }
   }
   ```

   ![Artefato - modelo do Resource Manager](./media/create-blueprint-portal/add-resource-manager-template.png)

1. Seu blueprint concluído deve ser semelhante ao seguinte. Observe que cada artefato tem '_x_ de _y_ parâmetros preenchidos' sob a coluna _Parâmetros_. Os **parâmetros dinâmicos** são definidos durante cada atribuição do blueprint.

   ![Projeto concluído](./media/create-blueprint-portal/completed-blueprint.png)

1. Agora que todos os artefatos planejados foram adicionados, clique em **Salvar rascunho** na parte inferior da página.

## <a name="edit-a-blueprint"></a>Editar um blueprint

Em [Criar um blueprint](#create-a-blueprint), uma Descrição não foi fornecida nem a atribuição de função foi adicionada ao novo grupo de recursos. Isso pode ser corrigido seguindo estas etapas:

1. Selecione **Definições do blueprint** na página à esquerda.

1. Na lista de planos gráficos, clique com o botão direito do mouse naquele que você criou anteriormente e selecione **Editar Blueprint**.

1. Em **Descrição do Blueprint**, forneça algumas informações sobre o blueprint e os artefatos que o compõem. Nesse caso, insira algo como: "Este blueprint define a atribuição de política e de atribuição de tag na assinatura, cria um ResourceGroup e implementa um modelo de recurso e uma atribuição de função para esse ResourceGroup".

1. Clique em **Avançar: Artefatos** na parte inferior da página ou na guia **Artefatos** na parte superior da página.

1. Adicionar atribuição de função sob o grupo de recursos: Clique com o botão esquerdo na linha **+ Adicionar artefato...**, diretamente sob a entrada **ResourceGroup**. Selecione 'Atribuição de função' para _tipo de Artefato_. Em _Função_, selecione 'Proprietário' e remova a marca de seleção do campo _Adicionar usuário, aplicativo ou grupo_ e pesquise e selecione um usuário, aplicativo ou grupo para adicionar. Esse artefato usa um **parâmetro static** definido igualmente em todas as atribuições deste projeto. Clique em **adicionar** para adicionar este artefato para o blueprint.

   ![Artifact - Role Assignment #2](./media/create-blueprint-portal/add-role-assignment-2.png)

1. Seu blueprint concluído deve ser semelhante ao seguinte. Observe que a designação de função adicionada recentemente mostra **1 de 1 parâmetros preenchidos**, o que significa que é um **parâmetro estático**.

   ![Modelo concluído #2](./media/create-blueprint-portal/completed-blueprint-2.png)

1. Clique em **Salvar rascunho** agora que foi atualizado.

## <a name="publish-a-blueprint"></a>Publicar um modelo

Agora que todos os artefatos planejados foram adicionados ao blueprint, é hora de publicá-lo.
A publicação torna disponível para ser atribuído a uma assinatura.

1. Selecione **Definições do blueprint** na página à esquerda.

1. Na lista de blueprints, clique com o botão direito do mouse no que você criou anteriormente e selecione **Publicar blueprint**.

1. Na caixa de diálogo aberta, forneça uma **Versão** (letras, números e hifens com um comprimento máximo de 20 caracteres), como 'v1', e **Alterar notas** (opcional), como 'Primeira publicação'.

1. Clique em **Publicar** na parte inferior da página.

## <a name="assign-a-blueprint"></a>Atribuir um modelo

Depois que um blueprint for publicado, ele poderá ser atribuído a uma assinatura. Atribua o blueprint que você criou a uma das assinaturas em sua hierarquia do grupo de gerenciamento. Se o blueprint for salvo em uma assinatura, ele só poderá ser atribuído a essa assinatura.

1. Selecione **Definições do blueprint** na página à esquerda.

1. Na lista de blueprints, clique com o botão direito do mouse no que você criou anteriormente (ou selecione as reticências) e selecione **Atribuir blueprint**.

1. Na página **Atribuir blueprint**, selecione as assinaturas para as quais deseja implantar esse blueprint na lista suspensa **Assinatura**.

   - Se as ofertas do Enterprise com suporte estiverem disponíveis em [Cobrança do Azure](../../billing/index.md), um link **Criar novo** será ativado na caixa **Assinatura**.

     1. Selecione o link **Criar novo** para criar uma nova assinatura, em vez de selecionar os existentes.

        ![Atribuição de blueprint – criar assinatura](./media/create-blueprint-portal/assignment-create-subscription.png)

     1. Forneça um **Nome de exibição** para a nova assinatura.

     1. Selecione a **Oferta** disponível na lista suspensa.

     1. Use o botão de reticências para selecionar o [grupo de gerenciamento](../management-groups/index.md) do qual a assinatura será um filho.

     1. Selecione **Criar** na parte inferior da página.

     > [!IMPORTANT]
     > A nova assinatura é criada imediatamente quando **Criar** for selecionado.

   > [!NOTE]
   > Uma atribuição é criada para cada assinatura selecionada, permitindo alterações em uma única designação de assinatura posteriormente, sem forçar alterações no restante das assinaturas selecionadas.

1. Para **Nome da atribuição**, forneça um nome exclusivo para essa atribuição.

1. Em **Localização**, selecione uma região na qual criar a identidade gerenciada e o objeto de implantação de assinatura. O Blueprint do Azure usa essa identidade gerenciada para implantar todos os artefatos no blueprint atribuído. Para saber mais, veja [identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

1. Deixe o menu suspenso **Versão de definição de blueprint** de versões **Publicadas** na entrada 'v1' (o padrão é a versão **Publicada** mais recentemente).

1. Para **Atribuição de Bloqueio**, deixe o padrão de **Não Bloquear**. Para obter mais informações, consulte [bloqueio de recursos de projetos](./concepts/resource-locking.md).

   ![Atribuição – Bloqueio e identidades gerenciadas](./media/create-blueprint-portal/assignment-locking-mi.png)

1. Em **Identidade gerenciada**, deixe o padrão de **Atribuído pelo sistema**.

1. Para a atribuição de função de nível de assinatura **[Grupo de usuários ou nome do aplicativo]: Colaborador**, procure e selecione um usuário, aplicativo ou grupo.

1. Para a atribuição de política em nível de assinatura, defina o **Nome da Tag** como 'CostCenter' e o **Tag Value** como 'ContosoIT'.

1. Para o 'ResourceGroup', forneça um **Nome** de 'StorageAccount' e um **Local** de 'East US 2' no menu suspenso.

   > [!NOTE]
   > Para cada artefato que foi incluído no grupo de recursos durante a definição do blueprint, esse artefato é recuado para alinhar com o grupo de recursos ou objeto com o qual será implementado. Os artefatos que não receberem parâmetros ou não tiverem parâmetros a serem definidos na atribuição serão listados apenas para informações contextuais.

1. No modelo 'StorageAccount' do Azure Resource Manager, selecione 'Standard_GRS' para o parâmetro **storageAccountType**.

1. Leia a caixa de informações na parte inferior da página e clique em **Atribuir**.

## <a name="track-deployment-of-a-blueprint"></a>Controlar a implantação de um plano gráfico

Quando um blueprint foi atribuído a uma ou mais assinaturas, duas coisas acontecem:

- O blueprint é incluído na página **Blueprints atribuídos** por assinatura atribuída a
- Inicia o processo de implantação de todos os artefatos definidos pelo blueprint

Agora que o blueprint foi atribuído a uma assinatura, verifique o progresso da implantação.

1. Selecione **Blueprints atribuídos** na página à esquerda.

1. Na lista de blueprints, clique com o botão direito do mouse no que você atribuiu anteriormente e selecione **Exibir detalhes da atribuição**.

   ![Exibir detalhes de atribuição](./media/create-blueprint-portal/view-assignment-details.png)

1. Na página **Atribuição de blueprint**, valide se todos os artefatos foram implantados com sucesso e se não houve erros durante a implantação. Em caso de erro, confira a [solução de problemas de blueprint](./troubleshoot/general.md) a fim de obter as etapas para determinar o que deu errado.

## <a name="unassign-a-blueprint"></a>Cancelar a atribuição de um blueprint

Se não for mais necessária, remova uma atribuição de blueprint de uma assinatura. O blueprint pode ter sido substituído por um mais recente com padrões, políticas e designs atualizados. Quando um blueprint é removido, os artefatos atribuídos como parte desse blueprint são deixados para trás. Para remover uma atribuição de blueprint, siga estas etapas:

1. Selecione **Blueprints atribuídos** na página à esquerda.

1. Na lista de blueprints, selecione o blueprint que terá a atribuição cancelada e clique no botão **Cancelar atribuição de blueprint** na parte superior da página.

1. Ler a mensagem de confirmação e, em seguida, clique em **Ok**.

## <a name="delete-a-blueprint"></a>Excluir um blueprint

1. Selecione **Definições do blueprint** na página à esquerda.

1. Clique com o botão direito do mouse no blueprint que deseja excluir e selecione **Excluir blueprint** e clique em **Sim** na caixa de diálogo de confirmação.

> [!NOTE]
> A exclusão de um blueprint nesse método também exclui todas as **Versões publicadas** do blueprint selecionado. Para excluir uma única versão, abra o blueprint, clique na guia **Published versions**, selecione e clique na versão que você deseja excluir e, em seguida, clique em **Delete This Version**. Além disso, um blueprint com atribuições não pode ser excluído até que todas as atribuições de blueprints tenham sido excluídas.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [ciclo de vida do blueprint](./concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](./concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](./concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](./concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](./how-to/update-existing-assignments.md).
- Resolver problemas durante a atribuição de blueprint com [solução de problemas gerais](./troubleshoot/general.md).