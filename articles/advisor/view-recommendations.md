---
title: Exibir recomendações do Assistente do Azure que são importantes para você
description: Exibir e filtrar recomendações do Assistente do Azure para reduzir o ruído.
services: advisor
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 04/03/2019
ms.author: kasparks
ms.openlocfilehash: 9f599a63fd5f52420f1b79e769d4f7bca9683b32
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59052831"
---
# <a name="view-azure-advisor-recommendations-that-matter-to-you"></a>Exibir recomendações do Assistente do Azure que são importantes para você

O Azure Advisor fornece recomendações para ajudá-lo a otimizar suas implantações do Azure. Dentro do Advisor, você tem acesso a alguns recursos que ajudam você a refinar suas recomendações somente àquelas que são importantes para você.

## <a name="configure-subscriptions-and-resource-groups"></a>Configurar grupos de recursos e assinaturas

Advisor proporciona a capacidade de selecionar as assinaturas e grupos de recursos que são importantes para você e sua organização. Você só verá as recomendações para as assinaturas e grupos de recursos que você selecionar. Por padrão, todos são selecionados. As definições de configuração se aplicam ao grupo de recursos ou assinatura, portanto, as mesmas configurações se aplicam a todas as pessoas que tem acesso a esse grupo de recursos ou assinatura. As definições de configuração podem ser alteradas no portal do Azure ou programaticamente.

Para fazer alterações no portal do Azure:

1. Abra [Azure Advisor](https://aka.ms/azureadvisordashboard) no portal do Azure.

1. Selecione **configuração** no menu.

   ![Menu de configuração do Advisor](./media/view-recommendations/configuration.png)

1. A caixa de seleção **Include** coluna para assinaturas ou grupos de recursos para receber recomendações do assistente. Se a caixa estiver desabilitada, você pode não ter permissão para alterar uma configuração nesse grupo de recursos ou assinatura. Saiba mais sobre [permissões no Azure Advisor](permissions.md).

1. Clique em **aplicar** na parte inferior, depois de fazer uma alteração.

## <a name="filtering-your-view-in-the-azure-portal"></a>Filtragem de seu modo de exibição no portal do Azure

As definições de configuração permanecem ativos até que seja alterado. Se você quiser limitar a exibição de recomendações para uma única exibição, você pode usar as listas suspensas fornecidas na parte superior do painel Advisor. De painéis de visão geral, alta disponibilidade, segurança, desempenho, custo e todas as recomendações, você pode selecionar as assinaturas, tipos de recursos e status de recomendação que você deseja ver.

   ![Menu de filtragem do Advisor](./media/view-recommendations/filtering.png)

## <a name="dismissing-and-postponing-recommendations"></a>Ignorando e adiar recomendações

O Assistente do Azure permite que você descartar ou adiar recomendações em um único recurso. Se você ignorar uma recomendação, você não vê-la novamente, a menos que você ativá-la manualmente. No entanto, adiando a uma recomendação permite que você especificar uma duração após a qual a recomendação é automaticamente ativada novamente. Adiando a pode ser feito no portal do Azure ou programaticamente.

### <a name="postpone-a-single-recommendation-in-the-azure-portal"></a>Adiar uma recomendação única no portal do Azure 

1. Abra [Azure Advisor](https://aka.ms/azureadvisordashboard) no portal do Azure.
1. Selecione uma categoria de recomendação para exibir suas recomendações
1. Selecione uma recomendação na lista de recomendações
1. Selecione adiar ou ignorar a recomendação que você deseja adiar ou ignorar

     ![Menu de filtragem do Advisor](./media/view-recommendations/postpone-dismiss.png)

### <a name="postpone-or-dismiss-a-multiple-recommendations-in-the-azure-portal"></a>Adiar ou ignorar um várias recomendações no portal do Azure

1. Abra [Azure Advisor](https://aka.ms/azureadvisordashboard) no portal do Azure.
1. Selecione uma categoria de recomendação para exibir suas recomendações.
1. Selecione uma recomendação na lista de recomendações.
1. Marque a caixa de seleção à esquerda da linha para todos os recursos que você deseja adiar ou ignorar a recomendação.
1. Selecione **adiar** ou **ignorar** na parte superior esquerda da tabela.

     ![Menu de filtragem do Advisor](./media/view-recommendations/postpone-dismiss-multiple.png)

> [!NOTE]
> Você precisa de permissão de Colaborador ou proprietário para descartar ou adiar uma recomendação. Saiba mais sobre permissões no Assistente do Azure.

> [!NOTE]
> Se as caixas de seleção estiverem desabilitadas, recomendações podem ainda ser sendo carregado. Aguarde até que todas as recomendações carregar antes de tentar adiar ou ignorar.

### <a name="reactivate-a-postponed-or-dismissed-recommendation"></a>Reativar uma recomendação adiada ou ignorada

Você pode ativar uma recomendação que foi adiada ou descartada. Essa ação pode ser feita no portal do Azure ou programaticamente. No Portal do Azure:

1. Abra [Azure Advisor](https://aka.ms/azureadvisordashboard) no portal do Azure.

1. Altere o filtro no painel de visão geral para **adiado**. Advisor exibe recomendações adiadas ou ignoradas.

    ![Menu de filtragem do Advisor](./media/view-recommendations/activate-postponed.png)

1. Selecione uma categoria para ver **adiado** e **descartado** recomendações.

1. Selecione uma recomendação na lista de recomendações. Isso abre as recomendações com o **adiada & ignorados** guia já selecionada para mostrar os recursos para o qual esta recomendação foi adiada ou descartada.

1. Clique em **ativar** no final da linha. Quando clicado, a recomendação é Active Directory para esse recurso e então removidas desta tabela. A recomendação agora está visível na **Active** guia.
 
     ![Menu de filtragem do Advisor](./media/view-recommendations/activate-postponed-2.png)

## <a name="next-steps"></a>Próximas etapas

Este artigo explica como você pode exibir as recomendações que são importantes para você no Assistente do Azure. Para saber mais sobre o Assistente, consulte: 

- [O que é Azure Advisor?](advisor-overview.md)
- [Introdução ao Advisor](advisor-get-started.md)
- [Permissões no Assistente do Azure](permissions.md)



