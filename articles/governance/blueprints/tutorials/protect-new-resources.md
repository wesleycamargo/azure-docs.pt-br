---
title: Proteger recursos novos com bloqueios de recursos de blueprint
description: Saiba como usar os bloqueios de recursos do Azure Blueprints Somente Leitura e Não Excluir para proteger recursos recém-implantados.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/28/2019
ms.topic: tutorial
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: d315fb5fe3ce7844946e6a9405a9a5f6a0be8b9d
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59272267"
---
# <a name="protect-new-resources-with-azure-blueprints-resource-locks"></a>Proteger recursos novos com bloqueios de recursos do Azure Blueprints

Os [bloqueios de recursos](../concepts/resource-locking.md) do Azure Blueprints possibilitam proteger recursos recém-implantados contra interferências, até mesmo por uma conta com a função _Proprietário_. Essa proteção pode ser adicionada a recursos criados por um artefato de modelo do Resource Manager na definição de blueprint.

As seguintes etapas serão abordadas:

> [!div class="checklist"]
> - Criar uma nova definição de blueprint
> - Marcar a definição de blueprint como **Publicada**
> - Atribuir a definição de blueprint a uma assinatura existente
> - Inspecionar o novo grupo de recursos
> - Cancelar a atribuição do blueprint para remover os bloqueios

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, será necessária uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-new-blueprint-definition"></a>Criar uma nova definição de blueprint

Primeiro, crie uma nova definição de blueprint.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Na página **Introdução** à esquerda, selecione o botão **Criar** em _Criar um blueprint_.

1. Localize o exemplo de blueprint **Blueprint em Branco** na parte superior da página e selecione **Começar com blueprint em branco**.

1. Insira as informações _Básicas_ do exemplo de blueprint:

   - **Nome do blueprint**: Forneça um nome para a sua cópia do exemplo de blueprint. Para este tutorial, usaremos o nome _locked-storageaccount_.
   - **Descrição do blueprint**: Descreve a definição do blueprint. Use "Para testar o bloqueio em recursos de blueprint implantados."
   - **Localização da definição**: Use o botão de reticências e selecione o grupo de gerenciamento ou a assinatura em que deseja salvar sua definição de blueprint.

1. Selecione a guia _Artefatos_ na parte superior da página ou selecione **Avançar: Artefatos** na parte inferior da página.

1. Adicionar o grupo de recursos na assinatura: Selecione a linha **+ Adicionar artefato...** sob a **Assinatura**.
   Selecione 'Grupo de recursos' _tipo de artefato_. Defina o _Nome de exibição do artefato_ como **RGtoLock**.
   Deixe os campos _Nome do grupo de recursos_ e _Local_ em branco, mas verifique se a caixa de seleção está marcada em cada propriedade para torná-los **parâmetros dinâmicos**. Clique em **adicionar** para adicionar este artefato para o blueprint.

1. Adicionar modelo sob o grupo de recursos: Selecione a linha **+ Adicionar artefato...** na entrada **RGtoLock**. Selecione 'Modelo do Azure Resource Manager' para _Tipo de artefato_, defina _Nome de exibição do artefato_ como 'StorageAccount' e deixe _Descrição_ em branco. Na guia **Template** na caixa do editor, cole o seguinte modelo do Resource Manager. Depois de colar o modelo, selecione **Adicionar** para adicionar o artefato ao blueprint.

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
           }
       },
       "variables": {
           "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
       },
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts",
           "name": "[variables('storageAccountName')]",
           "location": "[resourceGroup().location]",
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

1. Na parte inferior da página, selecione **Salvar Rascunho**.

Esta etapa cria a definição do blueprint de exemplo na assinatura ou no grupo de gerenciamento selecionado.

Quando a notificação **Êxito ao salvar a definição de blueprint** do portal for exibida, avance para a próxima etapa.

## <a name="publish-the-blueprint-definition"></a>Publique a definição do blueprint

Agora, sua definição de blueprint foi criada em seu ambiente. Ela é criada no modo **Rascunho** e deve ser **Publicada** antes de ser atribuída e implantada.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione a página **Definições de Blueprint** à esquerda. Use os filtros para localizar a definição de blueprint _locked-storageaccount_ e selecione-a.

1. Selecione **Publicar blueprint** na parte superior da página. No novo painel à direita, informe a **Versão** como _1.0_. Essa propriedade será útil se você fizer uma modificação mais tarde. Forneça **Notas de alteração**, como "Primeira versão publicada para bloquear recursos implantados de blueprint." Em seguida, selecione **Publicar** na parte inferior da página.

Esta etapa possibilita atribuir o blueprint a uma assinatura. Depois de publicado, as alterações ainda poderão ser feitas. As alterações adicionais exigem a publicação com uma novo valor de **Versão** a fim de rastrear as diferenças entre as versões da mesma definição de blueprint.

Quando a notificação **Êxito ao publicar a definição de blueprint** do portal for exibida, avance para a próxima etapa.

## <a name="assign-the-blueprint-definition"></a>Atribuir a definição de blueprint

Quando a definição blueprint for **Publicada** com êxito, ele poderá ser atribuído a uma assinatura do grupo de gerenciamento em que ele foi salvo. Esta é a etapa em que os parâmetros são fornecidos para tornar exclusiva cada implantação da definição de blueprint.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione a página **Definições de Blueprint** à esquerda. Use os filtros para localizar a definição de blueprint _locked-storageaccount_ e selecione-a.

1. Selecione **Atribuir blueprint** na parte superior da página de definição de blueprint.

1. Forneça os valores de parâmetro para a atribuição de blueprint:

   - Noções básicas

     - **Assinaturas**: Selecione uma ou mais das assinaturas que estão no grupo de gerenciamento em que você salvou a definição de blueprint. Se você selecionar mais de uma assinatura, será criada uma atribuição para cada uma, usando os parâmetros inseridos.
     - **Nome da atribuição**: O nome é pré-preenchido para você com base no nome da definição de blueprint. Queremos que essa atribuição represente o bloqueio de novo grupo de recursos, portanto, altere o nome da atribuição para _ssignment-locked-storageaccount-TestingBPLocks_.
     - **Localização**: Selecione uma região para a identidade gerenciada a ser criada. O Blueprint do Azure usa essa identidade gerenciada para implantar todos os artefatos no blueprint atribuído. Para saber mais, veja [identidades gerenciadas para recursos do Azure](../../../active-directory/managed-identities-azure-resources/overview.md).
       Para este tutorial, selecione _Leste dos EUA 2_.
     - **Versão de definição de blueprint**: Escolha a versão **Publicada** _1.0_ da definição de blueprint de exemplo.

   - Bloquear atribuição

     Selecione o modo de bloqueio de blueprint _Somente Leitura_. Para obter mais informações, consulte [bloqueio de recursos de projetos](../concepts/resource-locking.md).

   - Identidade Gerenciada

     Deixe a opção padrão _Atribuída pelo sistema_. Para obter mais informações, confira [identidades gerenciadas](../../../active-directory/managed-identities-azure-resources/overview.md).

   - Parâmetros do artefato

     Os parâmetros definidos nesta seção se aplicam ao artefato sob o qual ele está definido. Esses são [parâmetros dinâmicos](../concepts/parameters.md#dynamic-parameters), pois são definidos durante a atribuição do blueprint. Para cada artefato, defina o valor de parâmetro para o que está definido na coluna **Valor**.

     |Nome do artefato|Tipo de artefato|Nome do parâmetro|Valor|DESCRIÇÃO|
     |-|-|-|-|-|
     |Grupo de recursos RGtoLock|Grupo de recursos|NOME|TestingBPLocks|Define o nome do novo grupo de recursos ao qual os bloqueios de blueprint serão aplicados.|
     |Grupo de recursos RGtoLock|Grupo de recursos|Local padrão|Oeste dos EUA 2|Define a localização do novo grupo de recursos ao qual os bloqueios de blueprint serão aplicados.|
     |StorageAccount|Modelo do Resource Manager|storageAccountType (StorageAccount)|Standard_GRS|Selecionar SKU de armazenamento. O valor padrão é _Standard_LRS_.|

1. Depois que todos os parâmetros forem inseridos, selecione **Atribuir** na parte inferior da página.

Esta etapa implanta os recursos definidos e configura a **Atribuição de Bloqueio** selecionada. Os bloqueios de blueprint podem levar até 30 minutos para serem aplicados.

Quando a notificação **Êxito ao atribuir a definição de blueprint** do portal for exibida, avance para a próxima etapa.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Inspecionar recursos implantados pela atribuição

A atribuição criou o grupo de recursos _TestingBPLocks_ e a conta de armazenamento implantados pelo artefato de modelo do Resource Manager. O novo grupo de recursos e o estado de bloqueio selecionado são exibidos na página de detalhes de atribuição.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione a página **Blueprints atribuídos** à esquerda. Use os filtros para localizar a atribuição de blueprint _assignment-locked-storageaccount-TestingBPLocks_ e, em seguida, selecione-a.

   Nessa página, podemos ver que a atribuição foi bem-sucedida e os recursos foram implantados com o respectivo estado de bloqueio do blueprint. Se a atribuição for atualizada, a lista suspensa **Operação de atribuição** mostrará detalhes sobre a implantação de cada versão de definição. É possível clicar no grupo de recursos para abrir diretamente a página de propriedades.

1. Selecione o grupo de recursos **TestingBPLocks**.

1. Selecione a página **Controle de acesso (IAM)** à esquerda e, em seguida, a guia **Atribuições de função**.

   Aqui, podemos ver que a atribuição de blueprint _assignment-locked-storageaccount-TestingBPLocks_ tem a função _Proprietário_, pois foi usada para implantar e bloquear o grupo de recursos.

1. Selecione a guia **Negar atribuições**.

   A atribuição de blueprint criou uma [atribuição de negação](../../../role-based-access-control/deny-assignments.md) no grupo de recursos implantado para impor o modo de bloqueio _Somente Leitura_ do blueprint. A atribuição de negação impede que alguém com os direitos adequados execute ações específicas na guia _Atribuições de função_. A atribuição de negação afeta _Todas as entidades_.

   Para obter informações sobre a exclusão de uma entidade de segurança de uma atribuição de negação, veja [bloqueio de recursos de blueprint](../concepts/resource-locking.md#exclude-a-principal-from-a-deny-assignment).

1. Selecione a atribuição de negação e, em seguida, selecione a página **Permissões Negadas** à esquerda.

   A atribuição de negação está impedindo todas as operações com **\*** e a configuração **Ação**, mas permite o acesso de leitura excluindo **\*/read** por meio de **NotActions**.

1. Na trilha do portal do Azure, selecione **TestingBPLocks – Controle de acesso (IAM)**. Depois, selecione a página **Visão geral** à esquerda e, em seguida, o botão **Excluir grupo de recursos**. Insira o nome _TestingBPLocks_ para confirmar a exclusão e selecione **Excluir** na parte inferior do painel.

   A notificação do portal, **Falha ao excluir grupo de recursos TestingBPLocks**, é exibida. O erro informa que, embora sua conta tenha permissão para excluir o grupo de recursos, o acesso é negado pela atribuição de blueprint. Lembre-se de que selecionamos o modo de bloqueio de blueprint _Somente Leitura_ durante a atribuição de blueprint. O bloqueio de blueprint impede que uma conta com permissão, até mesmo _Proprietário_, exclua o recurso. Para obter mais informações, consulte [bloqueio de recursos de projetos](../concepts/resource-locking.md).

Estas etapas mostram que nossos recursos implantados agora estão protegidos com bloqueios de blueprint que impediram a exclusão indesejada, até mesmo usando uma conta com permissão.

## <a name="unassign-the-blueprint"></a>Cancelar a atribuição do blueprint

A última etapa é remover a atribuição da definição de blueprint. A remoção da atribuição não removerá os artefatos tocados.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **Blueprints**.

1. Selecione a página **Blueprints atribuídos** à esquerda. Use os filtros para localizar a atribuição de blueprint _assignment-locked-storageaccount-TestingBPLocks_ e, em seguida, selecione-a.

1. Selecione o botão **Cancelar atribuição do blueprint** na parte superior da página. Leia o aviso na caixa de diálogo de confirmação e, em seguida, selecione **OK**.

   Com a atribuição de blueprint removida, os bloqueios de blueprint também são removidos. Os recursos criados já podem ser excluídos por uma conta com permissões.

1. Selecione **Grupos de recursos** no menu do Azure e, em seguida, selecione **TestingBPLocks**.

1. Selecione a página **Controle de acesso (IAM)** à esquerda e, em seguida, a guia **Atribuições de função**.

A segurança do grupo de recursos mostra que a atribuição de blueprint não tem mais acesso de _Proprietário_.

Quando a notificação **A remoção da atribuição do blueprint foi bem-sucedida** do portal for exibida, avance para a próxima etapa.

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar este tutorial, exclua os seguintes recursos:

- Grupo de recursos _TestingBPLocks_
- Definição de blueprint _locked-storageaccount_

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [ciclo de vida do blueprint](../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../concepts/resource-locking.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../concepts/sequencing-order.md).
- Saiba como [atualizar atribuições existentes](../how-to/update-existing-assignments.md).
- Resolver problemas durante a atribuição de blueprint com [solução de problemas gerais](../troubleshoot/general.md).