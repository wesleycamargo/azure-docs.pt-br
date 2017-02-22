---
title: "Introdução ao Azure Advisor | Microsoft Docs"
description: "Use o Azure Advisor para otimizar as implantações do Azure."
services: advisor
documentationcenter: NA
author: kumudd
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: f67ca5996fdde0f0939ac46c2f6280f9d89af3d0
ms.openlocfilehash: a39a5555ce3af59b509a0522725addbb382ff7af

---

# <a name="introduction-to-azure-advisor"></a>Introdução ao Azure Advisor

Saiba mais sobre o Azure Advisor, seus principais recursos e as perguntas frequentes.

## <a name="what-is-azure-advisor"></a>O que é o Azure Advisor?
O Azure Advisor é um consultor de nuvem personalizado que ajuda a seguir as práticas recomendadas para otimizar as implantações do Azure. Ele analisa a telemetria de uso e configuração do recurso e recomenda soluções que podem ajudar você a aprimorar o custo-benefício, o desempenho, a alta disponibilidade e a segurança dos seus recursos do Azure.

Com o Azure Advisor, você pode:
-   Obter práticas recomendadas proativas, acionáveis e personalizadas 
-   Melhorar o desempenho, a segurança e a alta disponibilidade de seus recursos enquanto procura oportunidades para reduzir a despesa geral do Azure
-   Obter recomendações com ações embutidas

Você pode acessar o Advisor pelo [Portal do Azure](https://aka.ms/azureadvisordashboard). Entre no [portal](https://portal.azure.com), selecione **Procurar** e role até o **Azure Advisor**. O painel do Advisor exibe recomendações personalizadas para uma assinatura selecionada. As recomendações são divididas em quatro categorias. 

-   **Alta Disponibilidade**: para garantir e melhorar a continuidade de aplicativos críticos aos negócios. Para saber mais, veja [Advisor High Availability recommendations](advisor-high-availability-recommendations.md) (Recomendações de alta disponibilidade do Advisor).

-   **Segurança**: para detectar ameaças e vulnerabilidades que podem levar a possíveis violações de segurança. Para saber mais, veja [Advisor Security recommendations](advisor-security-recommendations.md) (Recomendações de segurança do Advisor).

-   **Desempenho**: para melhorar a velocidade de seus aplicativos. Para saber mais, veja [Advisor Performance recommendations](advisor-performance-recommendations.md) (Recomendações de desempenho do Advisor).

-   **Custo**: para otimizar e reduzir a despesa geral do Azure. Para saber mais, veja [Advisor Cost recommendations](advisor-cost-recommendations.md) (Recomendações de custo do Advisor).

  ![Tipos de recomendação do Advisor](./media/advisor-overview/advisor-all-tab-examples.png)

> [!NOTE]
> O Azure Advisor gera recomendações para assinaturas em que você recebeu a função de **Proprietário**, **Colaborador** ou **Leitor**.

Você pode clicar em uma recomendação para obter informações adicionais sobre ela. Também é possível saber mais sobre ações que você pode executar para aproveitar uma oportunidade ou resolver um problema. O Advisor oferece recomendações com ações embutidas ou links de documentação. Ao clicar em uma ação embutida, você é levado para uma "jornada interativa de usuário" para implementá-la. Clicar em um link de documentação aponta para a documentação que descreve como você pode implementar manualmente a ação. 

O Advisor atualiza as recomendações de hora em hora. Se você não pretende tomar uma ação imediata de acordo com a recomendação, é possível adiá-la por um período ou descartá-la. 

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="how-do-i-access-advisor"></a>Como acesso o Advisor?
Você pode acessar o Advisor pelo Portal do Azure. Entre no portal, selecione **Procurar** e role até o **Azure Advisor**. Você também pode exibir as recomendações do Advisor por meio da folha de recursos de máquina virtual. Escolha uma máquina virtual e role até as recomendações do Advisor no menu. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>De quais permissões preciso para acessar o Advisor?

Para exibir as recomendações do Advisor, você deve ter acesso a pelo menos uma assinatura. Sua função deve ser **Proprietário**, **Colaborador** ou **Leitor** da assinatura. Os proprietários e colaboradores da assinatura podem computar/gerar recomendações. No entanto, com uma função de **Leitor**, você só pode exibir as recomendações do Advisor. 

### <a name="how-often-are-advisor-recommendations-updated"></a>Com que frequência as recomendações do Advisor são atualizadas?

As recomendações do Advisor são atualizadas de hora em hora.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Para quais recursos o Advisor fornece recomendações?

O Advisor fornece recomendações para máquinas virtuais, conjuntos de disponibilidade, Serviços de Aplicativos, servidores SQL, bancos de dados SQL e cache Redis.

### <a name="can-i-snooze-or-dismiss-a-recommendation"></a>Posso adiar ou descartar uma recomendação?

Para adiar ou descartar uma recomendação, clique no botão ou link **Adiar**. Você pode especificar uma hora de adiamento ou selecionar **Nunca** para descartar a recomendação.

## <a name="next-steps"></a>Próximas etapas

Veja estes recursos para saber mais sobre as recomendações do Advisor:

-  [Introdução ao Advisor](advisor-get-started.md)
-  [Recomendações de alta disponibilidade do Advisor](advisor-high-availability-recommendations.md)
-  [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
-  [Recomendações de desempenho do Advisor](advisor-performance-recommendations.md)
-  [Recomendações de custo do Advisor](advisor-cost-recommendations.md)



<!--HONumber=Nov16_HO4-->


