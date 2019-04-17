---
title: Visão geral do Azure Resource Graph
description: Entenda como o serviço do Azure Resource Graph habilita a consulta complexa de recursos em grande escala.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/29/2019
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 28efdabc024fd32c83ba966b15284ec6ff368d4d
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59269275"
---
# <a name="overview-of-the-azure-resource-graph-service"></a>Visão geral do serviço Azure Resource Graph

O Azure Resource Graph é um serviço no Azure que foi desenvolvido para ampliar o Azure Resource Manager, fornecendo exploração de recursos eficiente e de alto desempenho, com a capacidade de consultar em escala em todas as assinaturas e grupos de gerenciamento, permitindo a você controlar o seu ambiente de forma efetiva. Essas consultas fornecem os seguintes recursos:

- Capacidade de consultar recursos com filtragem, agrupamento e classificação complexos por propriedades de recurso.
- Capacidade de explorar de forma iterativa os recursos com base nos requisitos de governança e de converter a expressão resultante em uma definição de política.
- Capacidade de avaliar o impacto da aplicação de políticas em um ambiente de nuvem de grande escala.

Nesta documentação, você estudará cada recurso em detalhes.

> [!NOTE]
> O Azure Resource Graph é usado pela nova experiência de navegação de 'Todos os recursos' do portal do Azure. Ele é projetado para ajudar os clientes que precisam gerenciar ambientes de grande escala.

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Como o Azure Resource Graph complementa o Azure Resource Manager

O Azure Resource Manager atualmente envia dados para um cache de recursos limitados que disponibiliza vários campos de recursos, mais especificamente: Nome do recurso, ID, Tipo, Grupo de recursos, Assinaturas e Local. Anteriormente, trabalhar com propriedades de recurso diversas exigia chamadas a cada provedor de recursos individual e detalhes de propriedade de solicitação para cada recurso.

Com o Azure Resource Graph, você pode acessar essas propriedades retornadas pelos provedores de recursos sem a necessidade de fazer chamadas individuais para cada um deles. Para obter uma lista de tipos de recursos com suporte, procure um **Sim** na tabela [Recursos para implantações do modo completo](../../azure-resource-manager/complete-mode-deletion.md).

## <a name="the-query-language"></a>A linguagem da consulta

Agora que você tem uma compreensão melhor do que é o Azure Resource Graph, nos aprofundaremos em como construir consultas.

É importante entender que a linguagem da consulta do Azure Resource Graph se baseia na [linguagem de consulta do Kusto](../../data-explorer/data-explorer-overview.md) usada pelo Azure Data Explorer.

Primeiro, para obter detalhes sobre operações e funções que podem ser usadas com o Azure Resource Graph, consulte [linguagem de consulta do Resource Graph](./concepts/query-language.md). Para procurar recursos, veja [explorar recursos](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Permissões no Azure Resource Graph

Para usar o Resource Graph, você deve ter os direitos apropriados no RBAC ([controle de acesso baseado em função](../../role-based-access-control/overview.md)) com, no mínimo, acesso de leitura para os recursos que você deseja consultar. Sem pelo menos `read` permissões para o objeto ou o grupo de objetos do Azure, os resultados não serão retornados.

> [!NOTE]
> O Resource Graph usa as assinaturas disponíveis para uma entidade de segurança durante o logon. Para ver os recursos de uma nova assinatura adicionada durante uma sessão ativa, a entidade de segurança deve atualizar o contexto. Essa ação ocorre automaticamente ao fazer logoff e logon.

## <a name="throttling"></a>Limitação

As consultas ao Resource Graph são limitadas para fornecer a melhor experiência e tempo de resposta para todos os clientes. Caso sua organização deseje usar a API do Resource Graph para consultas frequentes e em grande escala, use o portal 'Comentários' da página do Resource Graph. Forneça seu caso comercial e marque a caixa de seleção 'A Microsoft pode enviar um email para você sobre seus comentários' para que equipe entre em contato com você.

## <a name="running-your-first-query"></a>Realizando sua primeira consulta

O Resource Graph dá suporte à CLI do Azure, ao Azure PowerShell e ao SDK do Azure para .NET. A consulta é estruturada da mesma maneira para ambas as linguagens. Saiba como habilitar o Resource Graph na [CLI do Azure](first-query-azurecli.md#add-the-resource-graph-extension) e no [Azure PowerShell](first-query-powershell.md#add-the-resource-graph-module).

## <a name="next-steps"></a>Próximas etapas

- Executar sua primeira consulta com a [CLI do Azure](first-query-azurecli.md)
- Executar sua primeira consulta com o [Azure PowerShell](first-query-powershell.md)
- Começar com [Consultas iniciais](./samples/starter.md)
- Melhore sua compreensão com [Consultas avançadas](./samples/advanced.md)