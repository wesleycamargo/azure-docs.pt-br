---
title: Entender o limite de gastos do Azure | Microsoft Docs
description: Descreve como funciona o limite de gastos do Azure e como removê-lo
services: ''
documentationcenter: ''
author: genlin
manager: alherz
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.openlocfilehash: bfc6bf8cab1358c47e66660303f3303f4e9c763a
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2019
ms.locfileid: "54900996"
---
# <a name="understand-azure-spending-limit-and-how-to-remove-it"></a>Entender o limite de gastos do Azure e como removê-lo

O limite de gastos no Azure existe para evitar gastos sobre a quantidade de crédito. Todos os novos clientes que se inscreverem para a oferta ou avaliação gratuita, que inclui créditos durante vários meses, terão um limite de gastos ativado por padrão. O limite de gastos é de $0. Isso não pode ser alterado. O limite de gastos não está disponível para tipos de assinatura como assinaturas pré-pagas e planos de comprometimento. Consulte a [lista completa de ofertas do Azure e a disponibilidade do limite de gastos](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="what-happens-when-i-reach-the-spending-limit"></a>O que acontece quando eu alcanço o limite de gastos?

Quando seu uso resultar em encargos que consomem todas as quantias mensais incluídas em sua assinatura, os serviços implantados serão desabilitados durante o restante do período de cobrança. 

Por exemplo, quando gastar todo o crédito incluso na sua assinatura, os Serviços de Nuvem implantados serão removidos da produção e suas máquinas virtuais do Azure serão interrompidas e desalocadas. Os dados em suas contas de armazenamento estão disponíveis como somente leitura.

No início do próximo período de cobrança, se sua oferta de assinatura incluir créditos durante vários meses, sua assinatura será habilitada novamente automaticamente. Depois, você pode reimplantar seus Serviços de Nuvem e ter acesso total às suas contas de armazenamento e bancos de dados.

Enviaremos notificações por email quando você atingir o limite de gastos de sua oferta. Entre no [Centro de Contas](https://account.windowsazure.com/Subscriptions) e verá notificações sobre assinaturas que atingiram o limite de gastos.

Se você tiver uma avaliação gratuita e atingir o limite de gastos, você poderá [atualizar para o Pagamento Conforme o Uso](billing-upgrade-azure-subscription.md) para remover o limite de gastos e ter a assinatura automaticamente reabilitada.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>Remover o limite de gastos no Centro de Contas

Você pode remover o limite de gastos em qualquer momento, contanto que haja um método de pagamento válido associado à sua assinatura. Para ofertas que têm crédito em vários meses, você pode também reabilitar o limite de gastos no começo do seu próximo período de cobrança.

Para remover seu limite de gastos, execute estas etapas:

1. Entre no [Centro de Contas](https://account.windowsazure.com/Subscriptions).
1. Selecione uma assinatura. Se a assinatura estiver desabilitada porque o limite de gastos foi atingido, clique na notificação: “A assinatura alcançou o Limite de Gastos e foi desabilitada para evitar encargos.” Caso contrário, clique em **Remover limite de gastos** na área **STATUS DA ASSINATURA**.
1. Selecione uma opção que é adequada para você.

![Selecionando uma opção para remover o limite de gastos](./media/billing-spending-limit/remove-spending-limit.PNG)

|Opção|Efeito|
|-------|-----|
|Remover o limite de gastos indefinidamente|Remove o limite de gastos sem ativá-lo automaticamente no início do próximo período de cobrança.|
|Remover limite de gastos para o período de cobrança atual|Remove o limite de gastos para que ele seja ativado automaticamente no início do próximo período de cobrança.|

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="why-would-i-want-to-remove-the-spending-limit"></a>Por que desejaria remover o limite de gastos?

O limite de gastos pode evitar que você implante ou use determinados serviços da Microsoft e de terceiros. Esses são os cenários em que você deve remover o limite de gastos na sua assinatura.

* Você planeja implantar imagens primárias como o Oracle e serviços como o Azure DevOps Services. Esse cenário fará com que você exceda seu limite de gastos quase imediatamente e sua assinatura será desabilitada.
* Você tem serviços que não podem ser interrompidos.
* Você tem serviços e recursos com configurações como endereços IP virtuais e que não deseja perder. Essas configurações são perdidas quando os serviços e os recursos são desalocados.

### <a name="how-do-i-turn-on-the-spending-limit-after-removing-it"></a>Como ativar o limite de gastos após removê-lo?

Esse recurso está disponível somente quando o limite de gastos foi removido indefinidamente. Altere-o para ativá-lo automaticamente no início do próximo período de cobrança.

1. Entre no [Centro de Contas](https://account.windowsazure.com/Subscriptions).
1. Clique na faixa amarela para alterar a opção de limite de gastos.
1. Escolha **Ativar o limite de gastos no próximo período de cobrança \<start date of billing period\>**

### <a name="how-do-i-set-a-custom-spending-limit"></a>Como definir o limite de gastos personalizado?

Limites de gastos personalizados não estão disponíveis.

### <a name="does-the-spending-limit-prevent-all-charges-from-azure"></a>O limite de gastos impede todos os encargos do Azure?

[Alguns serviços externos publicados no Azure Marketplace](billing-understand-your-azure-marketplace-charges.md) não podem ser usados com seus créditos de assinatura e podem causar cobranças separadas, mesmo quando o limite de gastos estiver definido. Exemplos incluem licenças do Visual Studio, o Azure Active Directory Premium, planos de suporte e a maior parte dos serviços de terceiros. Quando você provisiona um novo serviço externo, é mostrado um aviso informando que os serviços são cobrados separadamente:

![Aviso de compra no Marketplace](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
