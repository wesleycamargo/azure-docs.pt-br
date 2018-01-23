---
title: Entender o limite de gastos do Azure | Microsoft Docs
description: "Descreve como funciona o limite de gastos do Azure e como removê-lo"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 12/22/2017
ms.author: genli
ms.openlocfilehash: d222300a591bcba556be5813ada2de555d25a1f0
ms.sourcegitcommit: 4256ebfe683b08fedd1a63937328931a5d35b157
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/23/2017
---
# <a name="understand-azure-spending-limit-and-how-to-remove-it"></a>Entender o limite de gastos do Azure e como removê-lo

O limite de gastos no Azure existe para evitar gastos sobre a quantidade de crédito. Todos os novos clientes que se inscreverem para a oferta ou avaliação gratuita, que inclui créditos durante vários meses, terão um limite de gastos ativado por padrão. O limite de gastos é de $0. Isso não pode ser alterado. O limite de gastos não está disponível para tipos de assinatura como assinaturas pré-pagas e planos de comprometimento. Consulte a [lista completa de ofertas do Azure e a disponibilidade do limite de gastos](https://azure.microsoft.com/support/legal/offer-details/).

**Pesquisando alertas de cobrança?** Consulte [Configurar alertas de cobrança ou de crédito para assinaturas do Azure](billing-set-up-alerts.md).

## <a name="what-happens-when-i-reach-the-spending-limit"></a>O que acontece quando eu alcanço o limite de gastos?

Quando seu uso resultar em encargos que consomem todas as quantias mensais incluídas em sua assinatura, os serviços implantados serão desabilitados durante o restante do período de cobrança. 

Por exemplo, quando gastar todo o crédito incluso na sua assinatura, os Serviços de Nuvem implantados serão removidos da produção e suas máquinas virtuais do Azure serão interrompidas e desalocadas. Os dados em seus bancos de dados e suas contas de armazenamento estarão disponíveis como somente leitura.

No início do próximo período de cobrança, se sua oferta de assinatura incluir créditos durante vários meses, sua assinatura será habilitada novamente automaticamente. Depois, você pode reimplantar seus Serviços de Nuvem e ter acesso total às suas contas de armazenamento e bancos de dados.

Enviaremos notificações por email quando você atingir o limite de gastos de sua oferta. Entre no [Centro de Contas](https://account.windowsazure.com/Subscriptions) e verá notificações sobre assinaturas que atingiram o limite de gastos.

Se você tiver uma avaliação gratuita e atingir o limite de gastos, você poderá [atualizar para o Pagamento Conforme o Uso](billing-upgrade-azure-subscription.md) para remover o limite de gastos e ter a assinatura automaticamente reabilitada.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>Remover o limite de gastos no Centro de Contas

Você pode remover o limite de gastos em qualquer momento, contanto que haja um método de pagamento válido associado à sua assinatura. Para ofertas que têm crédito em vários meses, você pode também reabilitar o limite de gastos no começo do seu próximo período de cobrança.

Para remover seu limite de gastos, execute estas etapas:

1. Entre no [Centro de Contas](https://account.windowsazure.com/Subscriptions).
1. Selecione uma assinatura.
. Se a assinatura estiver desabilitada devido ao limite de gastos ter sido atingido, clique nessa notificação: "A assinatura atingiu o limite de gastos e foi desabilitada para evitar encargos". Caso contrário, clique em **Remover limite de gastos** na área **STATUS DA ASSINATURA**.
1. Selecione uma opção que é adequada para você.

![Selecionando uma opção para remover o limite de gastos](./media/billing-spending-limit/remove-spending-limit.PNG)

|Opção|Efeito|
|-------|-----|
|Remover o limite de gastos indefinidamente|Remove o limite de gastos sem ativá-lo automaticamente no início do próximo período de cobrança.|
|Remover limite de gastos para o período de cobrança atual|Remove o limite de gastos para que ele seja ativado automaticamente no início do próximo período de cobrança.|

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="why-would-i-want-to-remove-the-spending-limit"></a>Por que desejaria remover o limite de gastos?

O limite de gastos pode evitar que você implante ou use determinados serviços da Microsoft e de terceiros. Esses são os cenários em que você deve remover o limite de gastos na sua assinatura.

* Você planeja implantar imagens primárias como o Oracle e serviços como o Visual Studio Team Services. Esse cenário fará com que você exceda seu limite de gastos quase imediatamente e sua assinatura será desabilitada.
* Você tem serviços que não podem ser interrompidos.
* Você tem serviços e recursos com configurações como endereços IP virtuais e que não deseja perder. Essas configurações são perdidas quando os serviços e os recursos são desalocados.

### <a name="how-do-i-turn-on-the-spending-limit-after-removing-it"></a>Como ativar o limite de gastos após removê-lo?

Esse recurso está disponível somente quando o limite de gastos foi removido indefinidamente. Altere-o para ativá-lo automaticamente no início do próximo período de cobrança.

1. Entre no [Centro de Contas](https://account.windowsazure.com/Subscriptions).
1. Clique na faixa amarela para alterar a opção de limite de gastos.
1. Escolha **Ativar o limite de gastos no próximo período de cobrança \<start date of billing period\>**

### <a name="how-do-i-set-a-custom-spending-limit"></a>Como definir o limite de gastos personalizado?

Atualmente, não temos limites de gastos personalizados. No entanto, você pode optar por [usar alertas de cobrança para controlar seus gastos](billing-set-up-alerts.md).

### <a name="does-the-spending-limit-prevent-all-charges-from-azure"></a>O limite de gastos impede todos os encargos do Azure?

[Alguns serviços externos publicados no Azure Marketplace](billing-understand-your-azure-marketplace-charges.md) não podem ser usados com seus créditos de assinatura e podem causar cobranças separadas, mesmo quando o limite de gastos estiver definido. Exemplos incluem licenças do Visual Studio, o Azure Active Directory Premium, planos de suporte e a maior parte dos serviços de terceiros. Quando você provisiona um novo serviço externo, é mostrado um aviso informando que os serviços são cobrados separadamente:

![Aviso de compra no Marketplace](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
