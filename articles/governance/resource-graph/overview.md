---
title: Visão geral do Azure Resource Graph
description: O Azure Resource Graph é um serviço do Azure que habilita a consulta complexa de recursos em escala.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: bcfee71af25ccb996f9467922aa38cabe2b252ac
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46959012"
---
# <a name="what-is-azure-resource-graph"></a>O que é o Azure Resource Graph

O Azure Resource Graph é um serviço no Azure que foi desenvolvido para ampliar o Azure Resource Manager, fornecendo exploração de recursos eficiente e de alto desempenho, com a capacidade de consultar em escala em todas as assinaturas e grupos de gerenciamento, permitindo a você controlar o seu ambiente de forma efetiva. Essas consultas fornecem as seguintes funcionalidades:

- Capacidade de consultar recursos com filtragem, agrupamento e classificação complexos por propriedades de recurso.
- Capacidade de explorar de forma iterativa os recursos com base nos requisitos de governança e de converter a expressão resultante em uma definição de política.
- Capacidade de avaliar o impacto da aplicação de políticas em um ambiente de nuvem de grande escala.

Nesta documentação, você estudará cada funcionalidade em detalhes.

> [!NOTE]
> O Azure Resource Graph é usado pela nova experiência de navegação de 'Todos os recursos' do portal do Azure. Ele é projetado para ajudar os clientes com uma necessidade de gerenciar ambientes de grande escala.

## <a name="how-does-azure-resource-graph-complement-azure-resource-manager"></a>Como o Azure Resource Graph complementa o Azure Resource Manager

O Azure Resource Manager atualmente envia dados para um cache de recursos limitados que expõe vários campos de recursos, mais especificamente: Nome do recurso, ID, Tipo, Grupo de recursos, Assinaturas e Local. Hoje em dia, se você quiser trabalhar com mais propriedades de recurso, você precisará fazer chamadas para cada provedor de recursos individuais e solicitar detalhes de propriedades para cada recurso.

Com o Azure Resource Graph, você pode acessar essas propriedades retornadas pelos provedores de recursos sem a necessidade de fazer chamadas individuais para cada um deles.

## <a name="the-query-language"></a>A linguagem da consulta

Agora que você tem uma compreensão melhor do que é o Azure Resource Graph, nos aprofundaremos em como construir consultas.

É importante entender a que a linguagem da consulta do Azure Resource Graph é semelhante mas não igual à KQL ([linguagem de consulta do Kusto](https://docs.loganalytics.io/docs/Language-Reference/)).

Primeiro, para obter detalhes sobre operações e funções que podem ser usadas com o Azure Resource Graph, consulte [linguagem de consulta do Resource Graph](./concepts/query-language.md). Para procurar recursos, veja [explorar recursos](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Permissões no Azure Resource Graph

Para usar o Resource Graph, você deve ser autorizado por meio do RBAC ([controle de acesso baseado em função](../../role-based-access-control/overview.md)) com, no mínimo, acesso de leitura para os recursos que você deseja consultar. Se você não tiver permissões `read` no grupo de gerenciamento, assinatura, grupo de recursos ou recurso individual, ele não será retornado nos resultados de uma consulta ao Resource Graph.

## <a name="running-your-first-query"></a>Realizando sua primeira consulta

O Resource Graph dá suporte à CLI do Azure e ao Azure PowerShell. O componente de consulta é estruturado do mesmo modo, independentemente da linguagem usada. O suporte ao Azure Resource Graph ainda não está disponível por padrão em qualquer um dos SDKs, então é preciso que uma extensão ou módulo seja carregado para fornecer os comandos necessários.
Saiba como habilitar o Resource Graph na [CLI do Azure](first-query-azurecli.md#add-the-resource-graph-extension) e no [Azure PowerShell](first-query-powershell.md#add-the-resource-graph-module).

## <a name="next-steps"></a>Próximas etapas

- Executar sua primeira consulta com a [CLI do Azure](first-query-azurecli.md)
- Executar sua primeira consulta com o [Azure PowerShell](first-query-powershell.md)
- Começar com [Consultas iniciais](./samples/starter.md)
- Melhore sua compreensão com [Consultas avançadas](./samples/advanced.md)