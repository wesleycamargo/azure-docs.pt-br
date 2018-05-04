---
title: Visualização de dependência em Migrações para Azure | Microsoft Docs
description: Fornece uma visão geral dos cálculos de avaliação no serviço Migrações para Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 2/21/2018
ms.author: raynew
ms.openlocfilehash: d075ce9ee124d373a1284577324a50338245a03f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
---
# <a name="dependency-visualization"></a>Visualização de dependência

Os serviços [Migrações para Azure](migrate-overview.md) avaliam grupos de computadores locais para a migração para o Azure. Para agrupar computadores, você pode usar a visualização de dependência. Este artigo fornece informações sobre este recurso.


## <a name="overview"></a>Visão geral

A visualização de dependência em Migrações para Azure permite criar grupos para avaliar a migração com maior confiança. Usando a visualização de dependência, você pode exibir as dependências de rede de computadores específicos ou em um grupo de computadores. Isso é útil para garantir que nenhuma funcionalidade seja perdida (ou computadores esquecidos) no processo de migração, quando os aplicativos e as cargas de trabalho são executados em vários computadores.  

## <a name="how-does-it-work"></a>Como ele funciona?

As Migrações para Azure usam a solução [Mapa do Serviço](../operations-management-suite/operations-management-suite-service-map.md) no [Log Analytics](../log-analytics/log-analytics-overview.md) para visualização de dependência.
- Ao criar um projeto de Migrações do Azure, um espaço de trabalho do Log Analytics é criado na sua assinatura.
- O nome do espaço de trabalho é o nome que você especificar para o projeto de migração, prefixado com **migrate-** e, opcionalmente, o sufixo com um número. 
- Navegue até o espaço de trabalho do Log Analytics da seção **Essentials** da página **Visão geral** do projeto.
- O espaço de trabalho criado é marcado com a chave **MigrateProject** e o valor **nome do projeto**. Você pode usá-los para pesquisar no portal do Azure.  

    ![Espaço de trabalho do Log Analytics](./media/concepts-dependency-visualization/oms-workspace.png)

Para usar a visualização de dependência, você precisa baixar e instalar agentes em cada computador local que você deseja analisar.  

## <a name="do-i-need-to-pay-for-it"></a>Eu preciso pagar por ele?

As Migrações para Azure estão disponíveis sem custo adicional. O uso dos recursos de visualização de dependência nas Migrações para Azure exigem o Mapa do Serviço. Ao criar um projeto de Migrações para Azure, ele criará automaticamente um novo espaço de trabalho do Log Analytics em seu nome.

> [!NOTE]
> O recurso de visualização de dependência usa o Mapa do Serviço por meio de um espaço de trabalho do Log Analytics. Desde de 28 de fevereiro de 2018, com o anúncio da disponibilidade geral das Migrações para Azure, o recurso agora está disponível sem custo adicional. Você precisará criar um novo projeto para usar o espaço de trabalho de uso gratuito. Os espaços de trabalho existentes antes da disponibilidade geral ainda são cobrados; portanto, recomendamos que você mova para um novo projeto.

1. O uso de qualquer solução que não o Mapa do Serviço dentro desse espaço de trabalho do Log Analytics incorrerá em cobranças padrão do Log Analytics. 
2. Para dar suporte a cenários de migração sem custo adicional, a solução de Mapa do Serviço não incorrerá em nenhum encargo nos primeiros 180 dias a partir da criação do projeto de Migrações para Azure, após o qual taxas padrão serão aplicadas.
3. Somente o espaço de trabalho criado como parte da criação do projeto será gratuito para uso.

Quando você registrar agentes para o espaço de trabalho, use a ID e a chave fornecida pelo projeto na página das etapas do agente de instalação. Você não pode usar um espaço de trabalho existente e associá-lo ao projeto de Migrações para Azure.

Quando o projeto de Migrações para Azure é excluído, o espaço de trabalho não é excluído junto com ele. Após a exclusão do projeto, o uso do Mapa do Serviço não será gratuito e cada nó será cobrado de acordo com a camada paga do espaço de trabalho do Log Analytics.

Saiba mais sobre os preços de Migrações para Azure [aqui](https://azure.microsoft.com/pricing/details/azure-migrate/). 

## <a name="how-do-i-manage-the-workspace"></a>Como faço para gerenciar o espaço de trabalho?

Você pode usar o espaço de trabalho do Log Analytics fora de Migrações para Azure. Ele não será excluído se você excluir o projeto de migração em que ele foi criado. Se você não precisar mais do espaço de trabalho, [exclua-o](../log-analytics/log-analytics-manage-access.md) manualmente.

Não exclua o espaço de trabalho criado pelas Migrações para Azure, a menos que você exclua o projeto de migração. Se você fizer isso, as dependências não funcionarão conforme o esperado.

## <a name="next-steps"></a>Próximas etapas

[Agrupar máquinas usando dependências da máquina](how-to-create-group-machine-dependencies.md)