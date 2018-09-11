---
title: Compre planos do SUSE Linux - Reservas do Azure | Microsoft Docs
description: Saiba como você pode pagar antecipadamente pelo uso do SUSE e economizar dinheiro com seus custos de pagamento conforme o uso.
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
ms.date: 08/29/2018
ms.author: yashar
ms.openlocfilehash: 9c3976a5fa98049de03f2a65b71f1fc927947142
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43311168"
---
# <a name="prepay-for-suse-software-plans-from-azure-reservations"></a>Pré-pagamento para planos de software SUSE das reservas do Azure

Pague antecipadamente seu uso do SUSE e economize dinheiro com seus custos de pagamento conforme o uso. Os descontos aplicam-se apenas aos medidores do SUSE e não ao uso da máquina virtual. Você pode comprar reservas para máquinas virtuais separadamente para economizar ainda mais.

Você pode comprar planos de software do SUSE no portal do Azure. Comprar um plano:

- Você deve estar em uma função de Proprietário em pelo menos uma assinatura Enterprise ou Pagamento Conforme o Uso.
- Para as assinaturas Enterprise, as compras de reserva devem estar habilitadas no [portal EA](https://ea.azure.com).
- Para o programa Provedor de Solução de Nuvem (CSP), os agentes admin ou agentes de vendas podem comprar os planos do SUSE.

## <a name="buy-a-suse-software-plan"></a>Compre um plano de software do SUSE

1. Vá para [reservas](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) no portal do Azure.
1. Selecione **adicionar** e selecione o SUSE Linux.
1. Preencha os campos obrigatórios. Qualquer VM do SUSE Linux que corresponda aos atributos do que você compra recebe o desconto. O número real de implantações que obtêm o desconto depende do escopo e da quantidade selecionada.

    | Campo      | DESCRIÇÃO|
    |:------------|:--------------|
    |NOME        |O nome desta compra.|
    |Assinatura|A assinatura usada para pagar por este plano. São cobrados os custos iniciais para a reserva à forma de pagamento na assinatura. O tipo de assinatura deve ser um contrato enterprise (número de oferta: MS-AZR-0017P) ou de Pagamento Conforme o Uso (número da oferta: MS-AZR-0003P). Para uma assinatura de empresa, os encargos são deduzidos do saldo do compromisso monetário do registro ou cobrados como média. Para a assinatura de Pagamento Conforme o Uso, as cobranças são feitas ao cartão de crédito ou à forma de pagamento de faturas na assinatura.|
    |Escopo       |O escopo pode cobrir uma assinatura ou várias assinaturas (escopo compartilhado). Se você selecionar: <ul><li>Assinatura única - O desconto do plano é aplicado ao uso do SUSE Linux nesta assinatura. </li><li>Compartilhado - o desconto de plano é aplicado ao uso do SUSE Linux em qualquer assinatura dentro de seu contexto de cobrança. Para clientes empresariais, o escopo compartilhado é o registro e inclui todas as assinaturas (exceto as assinaturas de desenvolvimento/teste) no registro. Para clientes de Pagamento Conforme o Uso, o escopo compartilhado consiste em todas as assinaturas de Pagamento Conforme o Uso criadas pelo administrador da conta.</li></ul>|
    |Plano de software     |Selecione o plano do SUSE Linux. Para obter ajuda sobre como identificar o que comprar, consulte [entender como o desconto de reserva de software do SUSE Linux Enterprise é aplicado](../../billing/billing-understand-suse-reservation-charges.md).|
    |Tamanho da VM     |O preço do SUSE Linux depende do número de vCPUs na VM. Selecione a opção que representa o número de vCPUs em suas VMs do SUSE Linux.|
    |Termo        |Um ano ou três anos.|
    |Quantidade    |O número de VMs para as quais você está comprando este plano do SUSE Linux. A quantidade é o número de instâncias do SUSE Linux em execução que podem receber o desconto do faturamento.|
1. Selecione **Comprar**.
1. Selecione **Exibir essa Reserva** para ver o status de sua compra.

O desconto de reserva é aplicado automaticamente a qualquer máquina virtual do SUSE em execução que corresponda à reserva. O desconto aplica-se apenas ao medidor SUSE. As cobranças de computação da VM não são cobertas por este plano.

## <a name="discount-applies-to-different-vm-sizes-with-instance-size-flexibility"></a>O desconto se aplica a diferentes tamanhos de VM com flexibilidade de tamanho de instância

Como as instâncias de VMs reservadas, os planos do SUSE Linux oferecem flexibilidade no tamanho da instância. Isso significa que seu desconto se aplica mesmo quando você implanta uma VM com um tamanho diferente do plano do SUSE que você comprou. Para obter mais informações, consulte [Entenda como o desconto de reserva do software SUSE Linux Enterprise é aplicado](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="cancellation-and-exchanges-not-allowed"></a>Cancelamento e trocas não permitidas

Você não pode cancelar ou um plano do SUSE que você comprou do exchange. Verifique seu uso para garantir que você compre o plano certo. Para obter ajuda sobre como identificar o que comprar, consulte [entender como o desconto de reserva de software do SUSE Linux Enterprise é aplicado](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="next-steps"></a>Próximas etapas

Para aprender a gerenciar uma reserva, confira [Gerenciar reservas do Azure](../../billing/billing-manage-reserved-vm-instance.md).

Para saber mais, consulte os seguintes artigos:

- [Quais são as reservas do Azure?](../../billing/billing-save-compute-costs-reservations.md)
- [Gerenciar reservas no Azure](../../billing/billing-manage-reserved-vm-instance.md)
- [Entender como o desconto de reserva do SUSE é aplicado](../../billing/billing-understand-suse-reservation-charges.md)
- [Entender o uso de reserva para a sua assinatura paga conforme o uso](../../billing/billing-understand-reserved-instance-usage.md)
- [Entender o uso de reserva para seu registro de empresa](../../billing/billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.