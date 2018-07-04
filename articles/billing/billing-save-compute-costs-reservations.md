---
title: O que são instâncias reservadas do Azure? | Microsoft Docs
description: Saiba mais sobre as Instâncias de VM Reservadas do Azure e os preços de VM para economizar nas máquinas virtuais e obter o melhor preço efetivo.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: yashar
ms.openlocfilehash: 12fc88596b4283b6f809575328ab801704cc1c8d
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064197"
---
# <a name="what-are-azure-reserved-vm-instances"></a>O que são as Instâncias de VM Reservadas do Azure?
As [Instâncias de VM Reservadas do Azure](https://azure.microsoft.com/pricing/reserved-vm-instances) ajudam você a economizar dinheiro com o pagamento antecipado de um ou três anos de capacidade de computação, permitindo que você receba um desconto nas máquinas virtuais utilizadas. As instâncias reservadas do Azure podem reduzir significativamente os custos de sua máquina virtual - até 72% com base em preços pagos - com um compromisso inicial de um ou três anos. As instâncias reservadas fornecem um desconto de faturamento e não afetam o estado de tempo de execução de suas máquinas virtuais.

Você pode comprar uma instância reservada (RI) no [portal do Azure](https://aka.ms/reservations). Para obter mais informações, consulte [Pré-pagamento para máquinas virtuais e economize dinheiro com instâncias reservadas](https://go.microsoft.com/fwlink/?linkid=861721).

## <a name="why-should-i-buy-a-reserved-instance"></a>Por que devo comprar uma instância reservada?
Se você tiver máquinas virtuais que são executadas por longos períodos de tempo, a compra de uma instância reservada oferece a opção mais econômica. Por exemplo, se você executar continuamente quatro instâncias de uma VM D2 padrão na região Oeste dos EUA, sem uma instância reservada, será cobrada uma taxa de pagamento conforme o uso. Se você comprar uma instância reservada para essas quatro VMs, as VMs obterão imediatamente o benefício do faturamento. Elas não serão mais cobradas com base nas taxas pagas conforme o uso. 

## <a name="what-charges-does-a-reserved-instance-cover"></a>Quais cobranças uma instância reservada cobre?
Uma instância reservada cobre apenas as cobranças de infraestrutura da máquina virtual para suas máquinas virtuais Windows ou Linux. Uma instância reservada não cobre custos adicionais de software, rede ou armazenamento. Para máquinas virtuais do Windows, você pode cobrir os custos de licenciamento do Windows com o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reserved-instance"></a>Quem é elegível para comprar uma instância reservada?
Os clientes do Azure com esses tipos de assinatura podem comprar uma instância reservada:
-   Tipo de oferta de assinatura de Contrato Enterprise (MS-AZR-0017P).
-   Tipo de oferta de assinatura [paga conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/) (MS-AZR-003P). Você deve estar na função "Proprietário" da assinatura para comprar uma instância reservada. Para a compra de instâncias reservadas em um registro corporativo, o administrador corporativo deve habilitar as compras de instâncias reservadas no portal do EA. Por padrão, essa configuração está habilitada.
-   Os parceiros do Provedor de solução de nuvem (CSP) podem usar o Portal do Azure ou o [Centro de parceiros](https://docs.microsoft.com/partner-center/azure-reservations) para comprar instâncias reservadas.

## <a name="how-is-a-reserved-instance-purchase-billed"></a>Como uma compra de instância reservada é faturada?
The reserved instance purchase is charged to the payment method tied to the subscription. Se você tiver uma assinatura Enterprise, o custo da instância reservada será deduzido de seu saldo de compromisso monetário. Se seu saldo de compromisso monetário não cobrir o custo da instância reservada, você receberá a cobrança adicional.
Se você tiver uma assinatura paga conforme o uso, será cobrado imediatamente do seu cartão de crédito. Se você for cobrado por fatura, verá os encargos na sua próxima fatura.

## <a name="how-is-the-purchased-reserved-instance-discount-applied"></a>Como o desconto da Instância Reservada adquirida é aplicado?
O desconto de Instância Reservada aplica-se às máquinas virtuais que correspondem aos atributos selecionados no momento da compra da Instância Reservada. Os atributos incluem o escopo em que as VMs correspondentes são executadas. Por exemplo, caso deseje obter um desconto de Instância Reservada para quatro máquinas virtuais Standard D2 na região Oeste dos EUA, selecione a assinatura na qual as VMs estão em execução. Se as máquinas virtuais são executadas em diferentes assinaturas em sua conta ou registro, então, selecione o escopo como compartilhado. O escopo compartilhado permite que o desconto da instância reservada seja aplicado a todas as assinaturas. Você pode alterar o escopo depois de comprar uma instância reservada. Para alterar o escopo, consulte [Gerenciar instâncias reservadas no Azure](billing-manage-reserved-vm-instance.md).

O desconto de Instância Reservada só se aplica às máquinas virtuais associadas aos tipos de assinatura Enterprise ou Pagamento Conforme o Uso. As máquinas virtuais executadas em uma assinatura com outros tipos de oferta não recebem o desconto de Instância Reservada. Para registros do Enterprise, as assinaturas de desenvolvimento/teste do Enterprise não são qualificadas para os benefícios de Instância Reservada.

Para entender melhor como a instância reservada afeta o faturamento da sua máquina virtual, consulte [Entenda como o desconto de instância reservada é aplicado](billing-understand-vm-reservation-charges.md).

## <a name="what-happens-when-the-reserved-instance-term-expires"></a>O que acontece quando o prazo da Instância Reservada expira?
Ao final do prazo da Instância Reservada, o desconto na cobrança expira e a infraestrutura da máquina virtual é cobrada com base no preço pago conforme o uso. As instâncias reservadas do Azure não são renovadas automaticamente. Para continuar recebendo o desconto na cobrança, é necessário comprar uma nova Instância Reservada. 

## <a name="sizes-and-regional-availability"></a>Tamanhos e disponibilidade regional
As Instâncias Reservadas estão disponíveis para a maioria dos tamanhos de VM, com algumas exceções:
- VMs em Versão Prévia – qualquer série ou tamanho de VM que esteja em versão prévia não está disponível para compra de Instância Reservada.
- Nuvens – as Instâncias Reservadas não estão disponíveis para compra nas regiões Azure US Government, Alemanha ou China. 
- Cota insuficiente – uma Instância Reservada que tenha um escopo de uma assinatura única precisa ter uma cota de vCPU disponível na assinatura para a nova RI. Por exemplo, se a assinatura de destino tiver um limite de cota de 10 vCPUs para a Série D, você não poderá comprar uma Instância Reservada para 11 instâncias Standard_D1. A verificação de cota para as Instâncias Reservadas inclui as VMs já implantadas na assinatura. Por exemplo, se a assinatura tiver uma cota de 10 vCPUs para o D-Series e tiver duas instâncias standard_D1 implementadas, você poderá comprar uma instância reservada para 10 instâncias standard_D1 nessa assinatura. 
- Restrições de capacidade – em circunstâncias raras, o Azure limita a compra de novas Instâncias Reservadas para o subconjunto de tamanhos de VM, devido à baixa capacidade em uma região.

## <a name="next-steps"></a>Próximas etapas
Comece a economizar em máquinas virtuais comprando uma [Instância Reservada do Azure](https://go.microsoft.com/fwlink/?linkid=861721). 

Para saber mais sobre as Instâncias Reservadas, confira os seguintes artigos:

- [O que são as Instâncias de VM Reservadas do Azure?](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Gerenciar instâncias reservadas no Azure](billing-manage-reserved-vm-instance.md)
- [Entenda como o desconto de instância reservada é aplicado](billing-understand-vm-reservation-charges.md)
- [Entenda o uso de instâncias reservadas para sua assinatura do Pay-As-You-Go](billing-understand-reserved-instance-usage.md)
- [Entenda o uso reservado de instâncias para o seu registro Enterprise ](billing-understand-reserved-instance-usage-ea.md)
- [Custos de software do Windows não incluídos com instâncias reservadas](billing-reserved-instance-windows-software-costs.md)
- [Instâncias reservadas no programa do CSP (Provedor de Soluções na Nuvem CSP) do Partner Center](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
