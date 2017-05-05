---
title: Entender o limite de gastos do Azure | Microsoft Docs
description: "Descreve como funciona o limite de gastos do Azure e como removê-lo"
services: 
documentationcenter: 
author: genlin
manager: vikdesai
editor: 
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 39ae134d8927f5123667b64bbd0c659cd5f62ffc
ms.lasthandoff: 04/21/2017


---
# <a name="understand-azure-spending-limit-and-how-to-remove-it"></a>Entender o limite de gastos do Azure e como removê-lo

O limite de gastos do Azure representa o quanto sua assinatura do Azure pode gastar. Todos os novos clientes que se inscreverem para a oferta de avaliação, ou para ofertas que incluem créditos durante vários meses, terão um limite de gastos ativado por padrão. O limite de gastos é de $0. Isso não pode ser alterado. O limite de gastos não está disponível para tipos de assinatura como assinaturas pré-pagas e planos de comprometimento. Consulte a [lista completa de ofertas do Azure e a disponibilidade do limite de gastos](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="what-happens-when-i-reach-the-spending-limit"></a>O que acontece quando eu alcanço o limite de gastos?

Quando seu uso resultar em cobranças que consomem todas as quantias mensais incluídas em sua oferta, os serviços implantados serão desabilitados durante o restante do mês de cobrança. Por exemplo, os Serviços de Nuvem implantados são removidos do ambiente de produção, e suas máquinas virtuais do Azure são interrompidas e desalocadas. Para evitar que seus serviços sejam desabilitados, você pode escolher remover seu limite de gastos. Quando seus serviços forem desabilitados, os dados em suas contas de armazenamento e banco de dados estarão disponíveis como somente leitura para administradores. No início do próximo mês de cobrança, se sua oferta incluir créditos durante vários meses, sua assinatura será habilitada novamente. Depois, você pode reimplantar seus Serviços de Nuvem e ter acesso total às suas contas de armazenamento e bancos de dados.

Quando a assinatura de avaliação gratuita atingir o limite de gastos, você poderá reativar a assinatura e [atualizá-la automaticamente para nossa oferta pré-paga padrão](billing-upgrade-azure-subscription.md) em até 90 dias.

Você recebe notificações quando atinge o limite de gastos de sua oferta. Faça logon no [Centro de Contas do Azure](https://account.windowsazure.com), selecione **CONTA** e selecione **assinaturas**. Você recebe notificações sobre assinaturas que atingiram o limite de gastos.

## <a name="things-you-are-charged-for-even-if-you-have-a-spending-limit-enabled"></a>Coisas pelas quais você é cobrado mesmo que esteja com um limite de gastos habilitado

Alguns serviços do Azure e [compras do Marketplace](https://azure.microsoft.com/marketplace/) podem decorrer em cobranças no método de pagamento (CC) mesmo se um limite de gastos for definido. Os exemplos são licenças do Visual Studio e Azure Active Directory Premium que dão suporte a planos e a maior parte dos serviços de marcas de terceiros vendidos pelo Marketplace.


## <a name="when-not-to-use-the-spending-limit"></a>Quando não usar o limite de gastos

O limite de gastos pode evitar que você implante ou use determinados serviços da Microsoft e do Marketplace. Esses são os cenários em que você deve remover o limite de gastos na sua assinatura.

- Você planeja implantar imagens primárias como o Oracle e serviços como o Visual Studio Team Services. Esse cenário fará com que você exceda seu limite de gastos quase imediatamente e sua assinatura será desabilitada.

- Você tem serviços que não podem ser interrompidos.

- Você tem serviços e recursos com configurações como endereços IP virtuais e que não deseja perder. Essa configurações são perdidas quando os serviços e recursos forem desalocados.


## <a name="remove-the-spending-limit"></a>Remover o limite de gastos

Você pode remover o limite de gastos em qualquer momento, contanto que haja um método de pagamento válido associado à sua assinatura. Para ofertas que tem crédito em vários meses, você pode também reabilitar o limite de gastos no começo do seu próximo ciclo de cobrança.

Para remover seu limite de gastos, execute estas etapas:

1. Faça logon no [Centro de Contas do Azure](https://account.windowsazure.com).

2. Selecione uma assinatura.

3. Se a assinatura estiver desabilitada devido ao limite de gastos ter sido atingido, clique nessa notificação: "Assinatura alcançou o limite de gastos e foi desabilitada para evitar cobranças." Caso contrário, clique em **Remover limite de gastos** na área **STATUS DA ASSINATURA**.

4. Selecione uma opção que é adequada para você.

|Opção|Efeito|
|-------|-----|
|Remover o limite de gastos indefinidamente|Remove o limite de gastos sem ativá-lo automaticamente no início do próximo período de cobrança.|
|Remover limite de gastos para o período de cobrança atual|Remove o limite de gastos para que ele seja ativado automaticamente no início do próximo período de cobrança.|

## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.
Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
