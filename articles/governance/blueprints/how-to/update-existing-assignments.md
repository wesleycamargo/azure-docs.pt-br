---
title: Como atualizar uma atribuição do Azure Blueprint existente
description: Saiba mais sobre o mecanismo para atualizar uma atribuição existente em Azure Blueprints.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: ecac0fb21a6691874d5e8db49eadd7114d41845f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956193"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Como atualizar uma atribuição de blueprint existente

Quando um blueprint é atribuído, a atribuição pode ser atualizada. Há vários motivos para atualizar uma atribuição existente, como:

- Adicionar ou remover um [bloqueio de recurso](../concepts/resource-locking.md)
- Alterar o valor de [parâmetros dinâmicos](../concepts/parameters.md#dynamic-parameters)
- Atualizar a atribuição para uma versão **Publicada** mais recente do blueprint

## <a name="updating-assignments"></a>Atualizando atribuições

1. Inicie o serviço Azure Blueprint no portal do Azure clicando em **Todos os serviços**, procurando e selecionando **Política** no painel esquerdo. Na página **Política**, clique em **Blueprints**.

1. Selecione **Blueprints Atribuídos** na página à esquerda.

1. Na lista de blueprints, clique com o botão esquerdo do mouse na atribuição de blueprint e, em seguida, clique no botão **Atualizar Atribuição**, ou clique com o botão direito do mouse na atribuição de blueprint e selecione **Atualizar Atribuição**.

   ![Atualizar atribuição](../media/update-existing-assignments/update-assignment.png)

1. A página **Atribuir Blueprint** será carregada já preenchida com todos os valores da atribuição original. Você pode alterar a **versão de definição do blueprint**, o estado **Bloquear Atribuição** e qualquer um dos parâmetros dinâmicos que existem na definição do blueprint. Clique em **Atribuir** quando terminar de fazer alterações.

1. Na página de detalhes de atribuição atualizada, veja o novo status. Neste exemplo, adicionamos **Bloqueio** à atribuição.

   ![Atribuição atualizada – bloqueada](../media/update-existing-assignments/updated-assignment.png)

1. Explore detalhes sobre outras **Operações de Atribuição** usando a lista suspensa. A tabela **Recursos Gerenciados** é atualizada com a operação de atribuição selecionada.

   ![Operações de atribuição](../media/update-existing-assignments/assignment-operations.png)

## <a name="rules-for-updating-assignments"></a>Regras para atualizar atribuições

A implantação das atribuições atualizadas segue algumas regras importantes. Essas regras determinam o que acontece com um recurso existente, dependendo da alteração solicitada e do tipo de recurso de artefato que está sendo implantado ou atualizado.

- Atribuições de função
  - Se a função ou o destinatário da função (usuário, grupo ou aplicativo) for alterado, uma nova atribuição de função será criada. A atribuição de função já implantada permanece em vigor.
- Atribuições de Política
  - Se os parâmetros da atribuição de política forem alterados, a atribuição existente será atualizada.
  - Se a definição da atribuição de política for alterada, uma nova atribuição de política será criada. A atribuição de política já implantada permanece em vigor.
  - Se o artefato de atribuição de política for removido do blueprint, a atribuição de política já implantada permanecerá em vigor.
- Modelos do Gerenciador de Recursos do Azure
  - O modelo é processado por meio do Resource Manager como **PUT**. Como cada tipo de recurso manipula isso de uma forma diferente, examine a documentação de cada recurso incluído para determinar o impacto dessa ação quando executada por blueprints.

## <a name="possible-errors-on-updating-assignments"></a>Possíveis erros na atualização de atribuições

Ao atualizar atribuições, é possível fazer alterações que falham ao serem executadas. Um exemplo disso é alterar o local de um grupo de recursos depois que ele já foi implantado. É possível fazer qualquer alteração que seja compatível com o [Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md), mas qualquer alteração que resulte em erro por meio do Azure Resource Manager também resultará na falha da atribuição.

Não há nenhum limite para o número de vezes que uma atribuição pode ser atualizada. Assim, se ocorrer um erro, devido a um parâmetro inválido, a um objeto já existente ou a uma alteração não permitida pelo Azure Resource Manager, determine o erro e faça outra atualização da atribuição.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [ciclo de vida do blueprint](../concepts/lifecycle.md)
- Entenda como usar [parâmetros estáticos e dinâmicos](../concepts/parameters.md)
- Aprenda a personalizar a [ordem de sequenciamento de blueprint](../concepts/sequencing-order.md)
- Saiba como usar o [bloqueio de recurso de blueprint](../concepts/resource-locking.md)
- Resolver problemas durante a atribuição de um blueprint com a [solução de problemas gerais](../troubleshoot/general.md)
