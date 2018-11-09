---
title: Entenda o bloqueio de recursos nos Blueprints do Azure
description: Aprenda sobre as opções de bloqueio para proteger recursos ao atribuir um blueprint.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 4e71797837927fe5f5233bcf88d35fef98f504e9
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50139434"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Entenda o bloqueio de recursos nos Blueprints do Azure

A criação de ambientes consistentes em escala só é realmente valiosa se houver um mecanismo para manter essa consistência. Este artigo explica como o bloqueio de recursos funciona em Blueprints do Azure.

## <a name="locking-modes-and-states"></a>Estados e modos de bloqueio

O modo de bloqueio se aplica à atribuição de blueprint e tem apenas duas opções: **nenhuma** ou **All Resources**. O modo de bloqueio é configurado durante a atribuição de blueprint e não pode ser alterado depois que a atribuição é aplicada com êxito à assinatura.

Recursos criados por artefatos em uma atribuição de blueprint possuem três estados: **Not Locked**, **Read Only** ou **Cannot Edit / Delete**. Cada artefato pode estar na **não bloqueado** estado. No entanto, os artefatos do grupo sem recurso possuem **Somente leitura** e os grupos de recursos têm **Não é possível editar / excluir** estados. Essa diferença é uma distinção importante em como esses recursos são gerenciados.

O estado **Somente Leitura** é exatamente como definido: o recurso não pode ser alterado de forma alguma - sem alterações e não pode ser excluído. O **Não é possível editar/excluir** tem mais nuances devido à natureza de "contêiner" dos grupos de recursos. O objeto de grupo de recursos é somente leitura, mas é possível fazer alterações em recursos não bloqueados dentro do grupo de recursos.

## <a name="overriding-locking-states"></a>Substituindo os estados de bloqueio

Normalmente, é possível que alguém com o [controle de acesso baseado em função](../../../role-based-access-control/overview.md) (RBAC) apropriado na assinatura, como a função de 'Proprietário', possa alterar ou excluir qualquer recurso. Esse acesso não é o caso quando o Blueprints aplica o bloqueio como parte de uma atribuição implantada. Se a atribuição foi definida com o **bloqueio** opção, nem mesmo a assinatura proprietário pode alterar os recursos incluídos.

Isso protege a consistência do plano gráfico em definido e o ambiente em que ele foi projetado para criar a partir de exclusão acidental ou através de programação ou de alteração.

## <a name="removing-locking-states"></a>Removendo os estados de bloqueio

Esse acesso não é o caso quando o Blueprints aplica o bloqueio como parte de uma atribuição implantada. Quando a atribuição é removida, os bloqueios criados por planos gráficos são removidos. No entanto, o recurso é deixado para trás e precisaria ser excluído por meios normais.

## <a name="how-blueprint-locks-work"></a>Como o plano gráfico bloqueios trabalho

Uma função RBAC `denyAssignments` é aplicada aos recursos de artefatos durante a atribuição de um blueprint se a atribuição selecionou a opção **Lock**. A função é adicionada pela identidade gerenciada da atribuição do blueprint e só pode ser removida dos recursos do artefato pela mesma identidade gerenciada. Essa medida de segurança impõe o mecanismo de bloqueio e impede a remoção do bloqueio do blueprint fora do Blueprints. Remoção da função e o bloqueio é possível apenas removendo a atribuição de planta, que pode ser feita somente por pessoas com direitos apropriados.

> [!IMPORTANT]
> O Gerenciador de Recursos do Azure armazena em cache os detalhes da atribuição de função por até 30 minutos. Como resultado, `denyAssignments` em recursos de blueprint pode não estar imediatamente em pleno vigor. Durante esse período, talvez seja possível excluir um recurso destinado a ser protegido por bloqueios de blueprint.

## <a name="next-steps"></a>Próximas etapas

- Aprenda sobre o [ciclo de vida do blueprint](lifecycle.md)
- Entenda como usar [parâmetros estáticos e dinâmicos](parameters.md)
- Aprenda a personalizar o [especificações técnicas de ordem de sequenciamento](sequencing-order.md)
- Saiba como [atualizar atribuições existentes](../how-to/update-existing-assignments.md)
- Resolver problemas durante a atribuição de blueprint com [solução de problemas gerais](../troubleshoot/general.md)