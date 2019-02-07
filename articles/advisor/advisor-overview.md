---
title: Introdução ao Azure Advisor | Microsoft Docs
description: Use o Azure Advisor para otimizar as implantações do Azure.
services: advisor
documentationcenter: NA
author: kasparks
ms.service: advisor
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: kasparks
ms.openlocfilehash: 1a72225ce29b7a94f2fc402488f6b998cde0a0fb
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55494538"
---
# <a name="introduction-to-azure-advisor"></a>Introdução ao Azure Advisor

Saiba mais sobre as principais funcionalidades do Assistente do Azure e obtenha respostas para perguntas frequentes.

## <a name="what-is-advisor"></a>O que é o Assistente?
O Assistente é um consultor de nuvem personalizado que ajuda você a seguir as melhores práticas para otimizar suas implantações do Azure. Ele analisa a telemetria de uso e configuração do recurso e, depois, recomenda soluções que podem ajudar você a melhorar a economia, o desempenho, a alta disponibilidade e a segurança de seus recursos do Azure.

Com o Assistente, é possível:
* Obter recomendações de melhores práticas proativas, personalizadas e prontas para uso. 
* Melhorar o desempenho, a segurança e a alta disponibilidade de seus recursos, enquanto você identifica oportunidades para reduzir o gasto geral do Azure.
* Obter recomendações com ações propostas embutidas.

Você pode acessar o Advisor pelo [Portal do Azure](https://aka.ms/azureadvisordashboard). Entre no [Portal](https://portal.azure.com), localize o **Assistente** no menu de navegação ou pesquise por ele no menu **Todos os serviços**.

O painel do Assistente exibe recomendações personalizadas para todas as assinaturas.  Você pode aplicar filtros para exibir as recomendações para assinaturas e tipos de recursos específicos.  As recomendações são divididas em quatro categorias: 

* **Alta disponibilidade**: Para garantir e melhorar a continuidade dos aplicativos comercialmente críticos. Para saber mais, veja [Advisor High Availability recommendations](advisor-high-availability-recommendations.md) (Recomendações de alta disponibilidade do Advisor).
* **Segurança**: Para detectar ameaças e vulnerabilidades que podem levar a violações de segurança. Para saber mais, veja [Advisor Security recommendations](advisor-security-recommendations.md) (Recomendações de segurança do Advisor).
* **Desempenho**: Para melhorar a velocidade dos aplicativos. Para saber mais, veja [Advisor Performance recommendations](advisor-performance-recommendations.md) (Recomendações de desempenho do Advisor).
* **Custo**: Para otimizar e reduzir os gastos gerais do Azure. Para saber mais, veja [Advisor Cost recommendations](advisor-cost-recommendations.md) (Recomendações de custo do Advisor).

  ![Tipos de recomendação do Advisor](./media/advisor-overview/advisor-dashboard.png)

Você pode clicar em uma categoria para exibir a lista de recomendações nessa categoria e selecionar uma recomendação para saber mais sobre ela.  Saiba mais sobre as ações que você pode realizar para aproveitar uma oportunidade ou resolver um problema.

![Categoria de recomendação do Assistente](./media/advisor-overview/advisor-ha-category-example.png) 

Clique na ação recomendada para implementá-la.  Será aberta uma interface simples que permite que você implemente a recomendação ou fornece documentação que ajuda você com a implementação.  Quando você implementa uma recomendação, pode demorar até um dia para que o Assistente reconheça isso.

Se você não pretende tomar uma ação imediata em uma recomendação, você pode adiá-la por um período de tempo especificado ou ignorá-la.  Se você não deseja receber as recomendações para uma assinatura ou grupo de recursos específico, você poderá configurar o Assistente para gerar apenas recomendações para grupos de recursos e assinaturas especificados.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="how-do-i-access-advisor"></a>Como acesso o Advisor?
Você pode acessar o Advisor pelo [Portal do Azure](https://aka.ms/azureadvisordashboard). Entre no [Portal](https://portal.azure.com), localize o **Assistente** no menu de navegação ou pesquise por ele no menu **Todos os serviços**.

Você também pode exibir as recomendações do Assistente por meio interface de recursos da máquina virtual. Escolha uma máquina virtual e role até as recomendações do Advisor no menu. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>De quais permissões preciso para acessar o Advisor?
 
Você pode acessar as recomendações do Assistente como *Proprietário*, *Colaborador* ou *Leitor* de uma assinatura.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Para quais recursos o Advisor fornece recomendações?

O Assistente fornece recomendações para máquinas virtuais, conjuntos de disponibilidade, gateways de aplicativo, Serviços de Aplicativos, SQL Servers e Cache do Azure para Redis.

### <a name="can-i-postpone-or-dismiss-a-recommendation"></a>Posso adiar ou ignorar uma recomendação?

Para adiar ou ignorar uma recomendação, clique no link **Adiar**. Você pode especificar um período de adiamento ou selecione **Nunca** para ignorar a recomendação.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as recomendações do Assistente, consulte:

* [Introdução ao Advisor](advisor-get-started.md)
* [Recomendações de alta disponibilidade do Advisor](advisor-high-availability-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
* [Recomendações de desempenho do Advisor](advisor-performance-recommendations.md)
* [Recomendações de custo do Advisor](advisor-cost-recommendations.md)
