---
title: Entenda o bloqueio de recursos nos Blueprints do Azure
description: Aprenda sobre as opções de bloqueio para proteger recursos ao atribuir um blueprint.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 718c23b806da5058c042b961077e51ba0d4b6245
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973242"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Entenda o bloqueio de recursos nos Blueprints do Azure

A criação de ambientes consistentes em escala só é verdadeiramente valiosa se houver um mecanismo para garantir que a consistência persista. Este artigo explica como o bloqueio de recursos funciona em Blueprints do Azure.

## <a name="locking-modes-and-states"></a>Estados e modos de bloqueio

O modo de bloqueio se aplica à atribuição de blueprint e tem apenas duas opções: **nenhuma** ou **All Resources**. Isso é configurado durante a atribuição de blueprint e não pode ser alterado depois que a atribuição é aplicada com êxito à assinatura.

Os recursos criados pelas definições de artefato no blueprint atribuído à assinatura têm três estados: **Not Locked**, **Read Only** ou **Cannot Edit / Delete**. Qualquer tipo de artefato pode ter o estado **Não Bloqueado**. No entanto, os artefatos do grupo sem recursos são como **Somente leitura** e os grupos de recursos são como **Não é possível editar/excluir** . Essa é uma distinção importante em como esses recursos são gerenciados.

O estado **Somente Leitura** é exatamente como definido: o recurso não pode ser alterado de forma alguma - sem alterações e não pode ser excluído. O **Não é possível editar/excluir** tem mais nuances devido à natureza de "contêiner" dos grupos de recursos. O objeto de grupo de recursos é somente leitura, mas é possível criar, atualizar e excluir recursos dentro do grupo de recursos - desde que eles não façam parte de qualquer atribuição de blueprint com o estado de bloqueio **Somente Leitura**.

## <a name="overriding-locking-states"></a>Substituindo os estados de bloqueio

Embora normalmente seja possível que alguém com o [controle de acesso baseado em função](../../../role-based-access-control/overview.md) apropriado (RBAC) na assinatura, como a função de 'Proprietário', seja capaz de alterar ou excluir qualquer recurso, esse não é o caso. quando o Blueprints aplica o bloqueio como parte de uma atribuição implantada. Se a atribuição foi definida com o **bloqueio** opção, nem mesmo a assinatura proprietário pode alterar os recursos incluídos.

Isso protege a consistência do plano gráfico em definido e o ambiente em que ele foi projetado para criar a partir de exclusão acidental ou através de programação ou de alteração.

## <a name="removing-locking-states"></a>Removendo os estados de bloqueio

Se for necessário excluir os recursos criados por uma atribuição, a única maneira de excluí-los é primeiro remover a atribuição. Quando a atribuição é removida, os bloqueios criados por planos gráficos são removidos. O recurso, no entanto, é deixado para trás e precisaria ser excluído por meios normais por alguém com as permissões apropriadas.

## <a name="how-blueprint-locks-work"></a>Como o plano gráfico bloqueios trabalho

Uma função RBAC `denyAssignments` é aplicada aos recursos de artefatos durante a atribuição de um blueprint se a atribuição selecionou a opção **Lock**. A função é adicionada pela identidade gerenciada da atribuição do blueprint e só pode ser removida dos recursos do artefato pela mesma identidade gerenciada. Isso impõe o mecanismo de bloqueio e evita tentativas de remover o bloqueio do blueprint fora do Blueprints. Remoção da função e o bloqueio é possível apenas removendo a atribuição de planta, que pode ser feita somente por pessoas com direitos apropriados.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [ciclo de vida do plano gráfico](lifecycle.md)
- Entenda como usar [parâmetros estáticos e dinâmicos](parameters.md)
- Aprenda a personalizar o [especificações técnicas de ordem de sequenciamento](sequencing-order.md)
- Saiba como [atualizar atribuições existentes](../how-to/update-existing-assignments.md)
- Resolver problemas durante a atribuição de um plano gráfico com [gerais de solução de problemas](../troubleshoot/general.md)