---
title: O que são as Instâncias Reservadas do Azure? – Cobrança do Azure | Microsoft Docs
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
ms.openlocfilehash: 93be4bb037af400599b88bb71f34143ee65a5deb
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301695"
---
# <a name="what-are-azure-reserved-vm-instances"></a>O que são as Instâncias de VM Reservadas do Azure?
As [Instâncias de VM Reservadas do Azure](https://azure.microsoft.com/pricing/reserved-vm-instances) ajudam você a economizar dinheiro com o pagamento antecipado de um ou três anos de capacidade de computação, permitindo que você receba um desconto nas máquinas virtuais utilizadas. As Instâncias Reservadas do Azure podem reduzir de maneira significativa os custos com máquina virtual em até 72% nos preços pagos conforme o uso, com compromisso antecipado de um ou três anos. As Instâncias Reservadas oferecem um desconto na cobrança e não afetam o estado de tempo de execução das máquinas virtuais.

Compre uma RI (Instância Reservada) no [portal do Azure](https://aka.ms/reservations). Para obter mais informações, confira [Pagar antecipadamente por máquinas virtuais e economizar dinheiro com Instâncias Reservadas](https://go.microsoft.com/fwlink/?linkid=861721).

## <a name="why-should-i-buy-a-reserved-instance"></a>Por que deveria comprar uma Instância Reservada?
Caso você tenha máquinas virtuais que são executadas por longos períodos, a compra de uma Instância Reservada oferece a você a opção de melhor custo-benefício. Por exemplo, se você executar continuamente quatro instâncias de uma VM Standard D2 na região Oeste dos EUA, sem uma Instância Reservada, você será cobrado com base em taxas pagas conforme o uso. Se você comprar uma Instância Reservada para essas quatro VMs, elas receberão imediatamente o benefício de cobrança. Elas não serão mais cobradas com base nas taxas pagas conforme o uso. 

## <a name="what-charges-does-a-reserved-instance-cover"></a>Quais encargos são cobertos por uma Instância Reservada?
Uma Instância Reservada cobre somente os encargos de infraestrutura da máquina virtual para as máquinas virtuais do Windows ou do Linux. Uma Instância Reservada não cobre encargos adicionais de software, rede ou armazenamento. Para máquinas virtuais do Windows, você pode cobrir os custos de licenciamento do Windows com o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reserved-instance"></a>Quem está qualificado para comprar uma Instância Reservada?
Os clientes do Azure com estes tipos de assinaturas podem comprar uma Instância Reservada:
-   Tipo de oferta de assinatura de Contrato Enterprise (MS-AZR-0017P).
-   Tipo de oferta de assinatura [paga conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/) (MS-AZR-003P). Você deve ter a função "Proprietário" na assinatura para comprar uma Instância Reservada. Para comprar Instâncias Reservadas em um Registro Enterprise, o administrador corporativo precisa habilitar as compras de Instância Reservada no portal do EA. Por padrão, essa configuração está habilitada.
-   Os parceiros do CSP (Provedor de Soluções na Nuvem) podem usar o portal do Azure ou o [Partner Center](https://docs.microsoft.com/partner-center/azure-reservations) para comprar Instâncias Reservadas.

## <a name="how-is-a-reserved-instance-purchase-billed"></a>Como uma compra de Instância Reservada é cobrada?
A compra de Instância Reservada é cobrada na forma de pagamento vinculada à assinatura. Caso você tenha uma assinatura do Enterprise, o custo da Instância Reservada será deduzido de seu saldo de compromisso monetário. Se o saldo de compromisso monetário não cobrir o custo da Instância Reservada, você será cobrado pelo excedente.
Se você tiver uma assinatura paga conforme o uso, será cobrado imediatamente do seu cartão de crédito. Se você for cobrado por fatura, verá os encargos na sua próxima fatura.

## <a name="how-is-the-purchased-reserved-instance-discount-applied"></a>Como o desconto da Instância Reservada adquirida é aplicado?
O desconto de Instância Reservada aplica-se às máquinas virtuais que correspondem aos atributos selecionados no momento da compra da Instância Reservada. Os atributos incluem o escopo em que as VMs correspondentes são executadas. Por exemplo, caso deseje obter um desconto de Instância Reservada para quatro máquinas virtuais Standard D2 na região Oeste dos EUA, selecione a assinatura na qual as VMs estão em execução. Se as máquinas virtuais são executadas em diferentes assinaturas em sua conta ou registro, então, selecione o escopo como compartilhado. O escopo compartilhado permite que o desconto de Instância Reservada seja aplicado entre assinaturas. Você pode alterar o escopo depois de comprar uma Instância Reservada. Para alterar o escopo, confira a documentação sobre como gerenciar Instâncias Reservadas.

O desconto de Instância Reservada só se aplica às máquinas virtuais associadas aos tipos de assinatura Enterprise ou Pagamento Conforme o Uso. As máquinas virtuais executadas em uma assinatura com outros tipos de oferta não recebem o desconto de Instância Reservada. Para registros do Enterprise, as assinaturas de desenvolvimento/teste do Enterprise não são qualificadas para os benefícios de Instância Reservada.

Para entender melhor como a Instância Reservada afeta a cobrança de sua máquina virtual, confira [Noções básicas sobre a aplicação do benefício de cobrança às Instâncias Reservadas](https://go.microsoft.com/fwlink/?linkid=863405).

## <a name="what-happens-when-the-reserved-instance-term-expires"></a>O que acontece quando o prazo da Instância Reservada expira?
Ao final do prazo da Instância Reservada, o desconto na cobrança expira e a infraestrutura da máquina virtual é cobrada com base no preço pago conforme o uso. As Instâncias Reservadas não são renovadas automaticamente. Para continuar recebendo o desconto na cobrança, é necessário comprar uma nova Instância Reservada. 

## <a name="sizes-and-regional-availability"></a>Tamanhos e disponibilidade regional
As Instâncias Reservadas estão disponíveis para a maioria dos tamanhos de VM, com algumas exceções:
- VMs em Versão Prévia – qualquer série ou tamanho de VM que esteja em versão prévia não está disponível para compra de Instância Reservada.
- Nuvens – as Instâncias Reservadas não estão disponíveis para compra nas regiões Azure US Government, Alemanha ou China. 
- Cota insuficiente – uma Instância Reservada que tenha um escopo de uma assinatura única precisa ter uma cota de vCPU disponível na assinatura para a nova RI. Por exemplo, se a assinatura de destino tiver um limite de cota de 10 vCPUs para a Série D, você não poderá comprar uma Instância Reservada para 11 instâncias Standard_D1. A verificação de cota para as Instâncias Reservadas inclui as VMs já implantadas na assinatura. Por exemplo, se a assinatura tiver uma cota de 10 vCPUs para a Série D e duas instâncias standard_D1 implantadas, você poderá comprar uma Instância Reservada para 10 instâncias standard_D1 nessa assinatura. 
- Restrições de capacidade – em circunstâncias raras, o Azure limita a compra de novas Instâncias Reservadas para o subconjunto de tamanhos de VM, devido à baixa capacidade em uma região.

## <a name="next-steps"></a>Próximas etapas
Comece a economizar em máquinas virtuais comprando uma [Instância Reservada do Azure](https://go.microsoft.com/fwlink/?linkid=861721). 

Para saber mais sobre as Instâncias Reservadas, confira os seguintes artigos:

- [Pagar antecipadamente por Máquinas Virtuais com Instâncias Reservadas](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Gerenciar as Instâncias Reservadas](billing-manage-reserved-vm-instance.md)
- [Entender como é aplicado o desconto de Instância Reservada](billing-understand-vm-reservation-charges.md)
- [Entender o uso da Instância Reservada na sua assinatura Paga pelo Uso](billing-understand-reserved-instance-usage.md)
- [Entender o uso da Instância Reservada no seu registro de Empresa](billing-understand-reserved-instance-usage-ea.md)
- [Os custos de software do Windows não estão incluídos nas Instâncias Reservadas](billing-reserved-instance-windows-software-costs.md)
- [Instâncias reservadas no programa do CSP (Provedor de Soluções na Nuvem CSP) do Partner Center](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
