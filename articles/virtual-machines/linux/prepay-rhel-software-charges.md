---
title: Comprar planos do Red Hat Enterprise Linux - Azure reservas | Microsoft Docs
description: Saiba como você pode pagar antecipadamente por seu uso do Red Hat e economizar dinheiro ao longo de seus custos de pagamento conforme o uso.
services: virtual-machines-linux
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/18/2019
ms.author: yashar
ms.openlocfilehash: e90241d9da140ca33d1aae8ffc5636f8eb2fbc74
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652890"
---
# <a name="prepay-for-red-hat-enterprise-linux-plans-from-azure-reservations"></a>Pagar antecipadamente por planos de Red Hat Enterprise Linux do Azure reservas

Pagar antecipadamente por seu uso do Red Hat e economizar dinheiro ao longo de seus custos de pagamento conforme o uso. Os descontos se aplicam somente aos medidores de Red Hat e não sobre o uso de máquina virtual. Você pode comprar reservas para máquinas virtuais separadamente para economizar ainda mais.

Você pode comprar planos de software do Red Hat no portal do Azure. Comprar um plano:

- Você deve estar em uma função de Proprietário em pelo menos uma assinatura Enterprise ou Pagamento Conforme o Uso.
- Para as assinaturas Enterprise, a opção **Adicionar Instâncias Reservadas** deve estar habilitada no [Portal EA](https://ea.azure.com). Ou, se essa configuração estiver desabilitada, você deve ser um administrador de EA na assinatura.
- Para o programa de provedor de solução de nuvem (CSP), os agentes administradores ou agentes de vendas podem comprar os planos do Red Hat.

## <a name="buy-a-red-hat-software-plan"></a>Compre um plano de software do Red Hat

1. Vá para [reservas](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) no portal do Azure.
1. Selecione **adicionar** e selecione o Red Hat Linux.
1. Preencha os campos obrigatórios. Qualquer VM Red Hat Linux que corresponde aos atributos daquilo que você compra obtém o desconto. O número real de implantações que obtêm o desconto depende do escopo e da quantidade selecionada.

    | Campo      | DESCRIÇÃO|
    |:------------|:--------------|
    |NOME        |O nome desta compra.|
    |Assinatura|A assinatura usada para pagar por este plano. São cobrados os custos iniciais para a reserva à forma de pagamento na assinatura. O tipo de assinatura deve ser um contrato empresarial (números da oferta: MS-AZR-0017P ou MS-AZR-0148P) ou Pagamento Conforme o Uso (números da oferta: MS-AZR-0003P ou MS-AZR-0023P). Para uma assinatura de empresa, os encargos são deduzidos do saldo do compromisso monetário do registro ou cobrados como média. Para a assinatura de Pagamento Conforme o Uso, as cobranças são feitas ao cartão de crédito ou à forma de pagamento de faturas na assinatura.|
    |Escopo       |O escopo pode cobrir uma assinatura ou várias assinaturas (escopo compartilhado). Se você selecionar: <ul><li>Assinatura única - o desconto de plano é aplicada ao uso do Red Hat Linux nesta assinatura. </li><li>Compartilhado - o desconto de plano é aplicado ao uso do Red Hat Linux em qualquer assinatura dentro de seu contexto de cobrança. Para clientes empresariais, o escopo compartilhado é o registro e inclui todas as assinaturas no registro. Para clientes de Pagamento Conforme o Uso, o escopo compartilhado consiste em todas as assinaturas de Pagamento Conforme o Uso criadas pelo administrador da conta.</li></ul>|
    |Plano     |Selecione o plano do Red Hat Linux. Para obter ajuda sobre como identificar o que comprar, consulte a entender como o desconto de reserva do Red Hat Linux Enterprise software é aplicado.|
    |Tamanho da VM     |Preços do Red Hat Linux depende do número de vCPUs na VM. Selecione a opção que representa o número de vCPUs em suas VMs do Red Hat Linux.|
    |Termo        |Um ano ou três anos.|
    |Quantidade    |Planejar o número de VMs que você está comprando o esse Red Hat Linux. A quantidade é o número de instâncias do Red Hat Linux que podem obter o desconto de cobrança em execução.|
1. Selecione **Comprar**.
1. Selecione **Exibir essa Reserva** para ver o status de sua compra.

O desconto de reserva é aplicado automaticamente a quaisquer máquinas virtuais Red Hat em execução que corresponda a reserva. O desconto se aplica somente ao medidor Red Hat. As cobranças de computação da VM não são cobertas por este plano.

## <a name="discount-applies-to-different-vm-sizes"></a>Desconto aplica-se aos diferentes tamanhos de VM

Como as instâncias de VM reservadas, planos de Red Hat Linux oferecem flexibilidade de tamanho de instância. Isso significa que seu desconto se aplica até mesmo quando você implanta uma VM que tenha um tamanho diferente do plano do Red Hat que você comprou. Para obter mais informações, consulte a entender como o desconto de reserva do Red Hat Linux Enterprise software é aplicado.

## <a name="cancellation-and-exchanges-not-allowed"></a>Cancelamento e trocas não permitidas

Você não pode cancelar ou um plano do Red Hat que você comprou do exchange. Verifique seu uso para garantir que você compre o plano certo. Para obter ajuda sobre como identificar o que comprar, consulte a entender como o desconto de reserva do Red Hat Linux Enterprise software é aplicado.

## <a name="next-steps"></a>Próximas etapas

Para aprender a gerenciar uma reserva, confira [Gerenciar reservas do Azure](../../billing/billing-manage-reserved-vm-instance.md).

Para saber mais, consulte os seguintes artigos:

- [O que são Reservas do Azure?](../../billing/billing-save-compute-costs-reservations.md)
- [Gerenciar Reservas no Azure](../../billing/billing-manage-reserved-vm-instance.md)
- Entender como o desconto de reserva do Red Hat é aplicado
- [Entender o uso de reserva para a sua assinatura paga conforme o uso](../../billing/billing-understand-reserved-instance-usage.md)
- [Entender o uso de reserva para seu registro de empresa](../../billing/billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.