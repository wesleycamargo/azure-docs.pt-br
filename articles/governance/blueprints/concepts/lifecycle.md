---
title: Entender o ciclo de vida de um Azure Blueprint
description: Aprenda sobre o ciclo de vida de um blueprint e os detalhes de cada estágio.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: e0790168a8b9590aaa440a04cd99f26c2ece2818
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991531"
---
# <a name="understand-the-life-cycle-of-an-azure-blueprint"></a>Entender o ciclo de vida de um Azure Blueprint

Como muitos recursos no Azure, um blueprint do Azure Blueprint tem um ciclo de vida típico e natural. Eles são criados, implantados e finalmente excluídos quando não forem mais necessários ou relevantes.
Os blueprints são compatíveis com operações de ciclo de vida CRUD (criar/ler/atualizar/excluir) tradicionais, mas também vão além para oferecer mais níveis de status compatíveis com pipelines comuns de CI/CD (integração contínua /implantação contínua) para uso por aqueles que gerenciam a infraestrutura por meio de código – um elemento fundamental em DevOps conhecido como IaC (Infraestrutura como Código).

Para entender completamente um blueprint e seus estágios, vamos abordar um ciclo de vida padrão:

> [!div class="checklist"]
> - Criação e edição de um blueprint
> - Publicar o blueprint
> - Criação e edição de uma nova versão do blueprint
> - Publicar uma nova versão do blueprint
> - Exclusão de uma versão específica do blueprint
> - Exclusão do blueprint

## <a name="creating-and-editing-a-blueprint"></a>Criação e edição de um blueprint

Ao criar um blueprint, adicione artefatos, salve-o em um grupo de gerenciamento e forneça um nome e uma versão exclusivos. Neste ponto, o blueprint está em um modo **Rascunho** e ainda não pode ser atribuído. No entanto, enquanto está em modo **Rascunho**, ele pode ser atualizado e alterado.

Um blueprint em modo **Rascunho** que nunca foi publicado exibirá na página **Definições de Blueprint** um ícone diferente daqueles que foram **Publicados**. A **Última Versão** também será exibida como **Rascunho** para os blueprints nunca publicados.

Crie e edite um blueprint com o [portal do Azure](../create-blueprint-portal.md#create-a-blueprint) ou a [API REST](../create-blueprint-rest-api.md#create-a-blueprint).

## <a name="publishing-a-blueprint"></a>Publicar um blueprint

Depois que todas as alterações desejadas tiverem sido feitas no blueprint em modo **Rascunho**, ele poderá ser **Publicado** e disponibilizado para atribuição. A versão **Publicada** do blueprint não pode ser alterada.
Uma vez **Publicado**, o blueprint é exibido com um ícone diferente dos blueprints em **Rascunho** e exibirá o número de versão fornecido na coluna **Última Versão**.

Publique um blueprint com o [portal do Azure](../create-blueprint-portal.md#publish-a-blueprint) ou a [API REST](../create-blueprint-rest-api.md#publish-a-blueprint).

## <a name="creating-and-editing-a-new-version-of-the-blueprint"></a>Criação e edição de uma nova versão do blueprint

Embora uma versão **Publicada** de um blueprint não possa ser alterada, uma nova versão do blueprint poderá ser adicionada ao blueprint existente e modificada conforme necessário. Isso é feito por meio de alterações em um blueprint existente. Se o blueprint já estava **Publicado**, quando essas alterações forem salvas, serão mostradas como **Alterações Não Publicadas** na lista de definições de blueprints. Salvar as alterações salva uma versão de **Rascunho** do blueprint.

Edite um blueprint com o [portal do Azure](../create-blueprint-portal.md#edit-a-blueprint).

## <a name="publishing-a-new-version-of-the-blueprint"></a>Publicar uma nova versão do blueprint

Assim como a primeira versão de um blueprint foi **Publicada** para poder ser atribuída, cada versão subsequente do mesmo blueprint deve ser **Publicada** antes de ser atribuída. Quando **Alterações Não Publicadas** são feitas a um blueprint e ainda não foram **Publicadas**, o botão **Publicar Blueprint** fica disponível na página Editar Blueprint. Se o botão não estiver visível, o blueprint já foi **Publicado** e não tem **Alterações Não Publicadas**.

> [!NOTE]
> Um único blueprint pode ter várias versões **Publicadas** que podem ser atribuídas a assinaturas.

As etapas para publicar um blueprint com **Alterações Não Publicadas** como uma nova versão de um blueprint existente são as mesmas que as etapas para publicar um novo blueprint.

## <a name="deleting-a-specific-version-of-the-blueprint"></a>Exclusão de uma versão específica do blueprint

Cada versão de um blueprint é um objeto exclusivo que pode ser individualmente **Publicado**. Isso também significa que cada versão de um blueprint pode ser excluída. Excluir uma versão de um blueprint não tem nenhum impacto nas outras versões desse blueprint.

> [!NOTE]
> Não é possível excluir um blueprint com atribuições ativas. Exclua as atribuições primeiro e, em seguida, exclua a versão que você deseja remover.

1. Inicie o serviço Azure Blueprints no portal do Azure clicando no **todos os serviços** e procurando e selecionando **Política** no painel esquerdo. Sobre a **política** página, clique em **plantas**.

1. Selecione **Definições de Blueprint** na página à esquerda e use as opções de filtro para localizar o blueprint do qual você deseja excluir uma versão. Clique nele para abrir a página de edição.

1. Clique na guia **Versões publicadas** e localize a versão que você deseja excluir.

1. Clique com o botão direito do mouse na versão a ser excluída e selecione **Excluir Esta Versão**.

## <a name="deleting-the-blueprint"></a>Exclusão do blueprint

O blueprint principal também pode ser excluído. A exclusão do blueprint principal também excluirá todas as versões desse blueprint, independentemente do status **Rascunho** ou **Publicado**. Assim como na exclusão de uma versão de um blueprint, a exclusão do blueprint principal não remove as atribuições existentes das versões dos blueprints.

> [!NOTE]
> Não é possível excluir um blueprint com atribuições ativas. Exclua as atribuições primeiro e, em seguida, exclua a versão que você deseja remover.

Exclua um blueprint com o [portal do Azure](../create-blueprint-portal.md#delete-a-blueprint) ou a [API REST](../create-blueprint-rest-api.md#delete-a-blueprint).

## <a name="assignments"></a>Atribuições

Há vários momentos no ciclo de vida de um blueprint em que é possível atribuí-lo a uma assinatura.
Sempre que o modo de uma versão do blueprint for **Publicado**, essa versão poderá ser atribuída a uma assinatura. Mesmo se houver uma versão **Rascunho** do blueprint, se houver uma ou mais versões desse blueprint no modo **Publicado**, cada uma dessas versões **Publicadas** ficará disponível para ser atribuída. Isso permite que as versões de um blueprint sejam usadas e ativamente atribuídas enquanto uma versão mais recente estiver sendo desenvolvida.

Como as versões de blueprints são atribuídas, é importante entender o local em que elas estão atribuídas e com quais parâmetros elas foram atribuídas. Os parâmetros podem ser estáticos ou dinâmicos. Para obter mais informações, confira [parâmetros estáticos e dinâmicos](parameters.md).

### <a name="updating-assignments"></a>Atualizando atribuições

Quando um blueprint é atribuído, a atribuição pode ser atualizada. Há vários motivos para atualizar uma atribuição existente, como:

- Adicionar ou remover um [bloqueio de recurso](resource-locking.md)
- Alterar o valor de [parâmetros dinâmicos](parameters.md#dynamic-parameters)
- Atualizar a atribuição para uma versão **Publicada** mais recente do blueprint

Para saber como, confira [atualizar atribuições existentes](../how-to/update-existing-assignments.md).

## <a name="next-steps"></a>Próximas etapas

- Entenda como usar [parâmetros estáticos e dinâmicos](parameters.md)
- Aprenda a personalizar o [especificações técnicas de ordem de sequenciamento](sequencing-order.md)
- Saiba como fazer uso de [especificações técnicas de recurso de bloqueio](resource-locking.md)
- Saiba como [atualizar atribuições existentes](../how-to/update-existing-assignments.md)
- Resolver problemas durante a atribuição de um plano gráfico com [gerais de solução de problemas](../troubleshoot/general.md)