---
title: Visualização de dependência em Migrações para Azure | Microsoft Docs
description: Fornece uma visão geral dos cálculos de avaliação no serviço Migrações para Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: raynew
ms.openlocfilehash: 8df587db7655e2aafd876d80581f3296c8c99fbf
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58004102"
---
# <a name="dependency-visualization"></a>Visualização de dependência

Os serviços [Migrações para Azure](migrate-overview.md) avaliam grupos de computadores locais para a migração para o Azure. Você pode usar a funcionalidade de visualização de dependência nas Migrações para Azure para criar grupos. Este artigo fornece informações sobre este recurso.

> [!NOTE]
> A funcionalidade de visualização de dependências não está disponível no Azure Governamental.

## <a name="overview"></a>Visão geral

A visualização de dependência nas Migrações para Azure permite criar grupos altamente confiáveis para avaliar a migração. Usando a visualização de dependência, você pode exibir as dependências de rede de computadores e identificar os computadores relacionados que precisam ser migrados em conjunto para o Azure. Essa funcionalidade é útil em cenários em que você não está completamente ciente dos computadores que constituem o aplicativo e precisam ser migrados em conjunto para o Azure.

## <a name="how-does-it-work"></a>Como ele funciona?

O Azure usa as migrações a [mapa do serviço](../operations-management-suite/operations-management-suite-service-map.md) solução em [registra em log do Azure Monitor](../log-analytics/log-analytics-overview.md) para visualização de dependência.
- Para aproveitar a visualização de dependência, você precisa associar um workspace novo ou existente do Log Analytics a um projeto das Migrações para Azure.
- Você só pode criar ou anexar um workspace na mesma assinatura em que o projeto de migração é criado.
- Para anexar a um workspace do Log Analytics a um projeto, acesse a seção **Essentials** da página **Visão geral** do projeto e clique em **Requer configuração**

    ![Associar o workspace do Log Analytics](./media/concepts-dependency-visualization/associate-workspace.png)

- Durante a associação de um workspace, haverá a opção de criar um workspace ou anexar um existente:
  - Ao criar um workspace, você precisará especificar um nome para ele. O workspace será criado em uma região na mesma [Geografia do Azure](https://azure.microsoft.com/global-infrastructure/geographies/) que o projeto de migração.
  - Ao anexar um workspace, você pode escolher entre todos os workspaces disponíveis na mesma assinatura que o projeto de migração. Observe que são listados somente os workspaces que foram criados em uma região [com suporte para o Mapa do Serviço](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-configure#supported-azure-regions). É necessário ser capaz de anexar a um workspace, verifique se você tem acesso de 'Leitura' no workspace.

  > [!NOTE]
  > Depois de anexar um workspace a um projeto, você não poderá mais alterá-lo.

- O workspace associado é marcado com a chave **Projeto de Migração** e o valor **Nome do projeto**, que pode ser usado para pesquisar no portal do Azure.
- Para navegar até o workspace associado ao projeto, acesse a seção **Essentials** na página **Visão geral** do projeto e acesse o workspace

    ![Navegar no workspace do Log Analytics](./media/concepts-dependency-visualization/oms-workspace.png)

Para usar a visualização de dependência, você precisa baixar e instalar agentes em cada computador local que você deseja analisar.  

- [O Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) precisa ser instalado em cada máquina.
- O [Agente de Dependência](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure) precisa ser instalado em cada máquina.
- Além disso, se você tiver máquinas sem conectividade com a Internet, será necessário fazer o download e instalar o gateway do Log Analytics nelas.

Você não precisa desses agentes nas máquinas que deseja avaliar, a menos que esteja usando a visualização de dependência.

## <a name="do-i-need-to-pay-for-it"></a>Eu preciso pagar por ele?

As Migrações para Azure estão disponíveis sem custo adicional. O uso do recurso de visualização de dependência nas Migrações para Azure requer o Mapa do Serviço e exige que você associe um workspace novo ou existente do Log Analytics ao projeto das Migrações para Azure. A funcionalidade de visualização de dependência nas Migrações para Azure é gratuita nos primeiros 180 dias nas Migrações para Azure.

1. O uso de qualquer solução que não seja o Mapa do Serviço dentro desse workspace do Log Analytics gerará cobranças do [Log Analytics Standard](https://azure.microsoft.com/pricing/details/log-analytics/).
2. Para dar suporte a cenários de migração sem custo adicional, a solução Mapa do Serviço não gerará nenhum encargo nos primeiros 180 dias desde o dia da associação do workspace do Log Analytics ao projeto de Migrações para Azure. Após 180 dias, os encargos do Log Analytics Standard serão aplicados.

Quando você registrar agentes para o workspace, use a ID e a chave fornecida pelo projeto na página das etapas do agente de instalação.

Quando o projeto de Migrações para Azure é excluído, o workspace não é excluído junto com ele. Após a exclusão do projeto, o uso do Mapa do Serviço não será mais gratuito e cada nó será cobrado de acordo com a camada paga do workspace do Log Analytics.

> [!NOTE]
> O recurso de visualização de dependência usa o Mapa do Serviço por meio de um workspace do Log Analytics. Desde de 28 de fevereiro de 2018, com o anúncio da disponibilidade geral das Migrações para Azure, o recurso agora está disponível sem custo adicional. Você precisará criar um novo projeto para usar o workspace de uso gratuito. Os workspaces existentes antes da disponibilidade geral ainda serão cobrados, portanto, recomendamos que você passe para um novo projeto.

Saiba mais sobre os preços de Migrações para Azure [aqui](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="how-do-i-manage-the-workspace"></a>Como faço para gerenciar o workspace?

Você pode usar o workspace do Log Analytics fora de Migrações para Azure. Ele não será excluído se você excluir o projeto de migração em que ele foi criado. Se você não precisar mais do workspace, [exclua-o](../azure-monitor/platform/manage-access.md) manualmente.

Não exclua o workspace criado pelas Migrações para Azure, a menos que você exclua o projeto de migração. Se você fizer isso, a funcionalidade de visualização de dependência não funcionará conforme o esperado.

## <a name="next-steps"></a>Próximas etapas
- [Agrupar máquinas usando dependências da máquina](how-to-create-group-machine-dependencies.md)
- [Saiba mais](https://docs.microsoft.com/azure/migrate/resources-faq#dependency-visualization) sobre as perguntas frequentes na visualização de dependência.
