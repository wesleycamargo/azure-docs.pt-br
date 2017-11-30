---
title: "Economizar dinheiro por pagar previamente para máquinas virtuais do Azure – Azure | Microsoft Docs"
description: "Saiba mais sobre a Instância de Máquina Virtual Reservada do Azure para economizar custos com suas máquinas virtuais."
services: billing
documentationcenter: 
author: vikramdesai01
manager: vikramdesai01
editor: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2017
ms.author: vikdesai
ms.openlocfilehash: 96e9cf2fed0b22fd7aa7b9ffeab0e94738ce510d
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2017
---
# <a name="save-money-on-virtual-machines-with-reserved-virtual-machine-instances"></a>Economizar dinheiro de máquinas virtuais com Instâncias de Máquinas Virtuais Reservadas 
As Instâncias de Máquina Virtual Reservada permitem que você pague previamente por um ou três anos de capacidade de computação para receber um desconto nas máquinas virtuais que você usa. Isso reduz de maneira significativa os custos com máquina virtual em até 72% nos preços pagos conforme o uso, com comprometimento antecipado de um ou de três anos. As Instâncias de Máquina Virtual Reservada são um desconto na cobrança e não afeta o estado de tempo de execução de máquinas virtuais.

Você pode comprar uma Instância de Máquina Virtual Reservada no [portal do Azure](https://aka.ms/reservations). Para obter mais informações, consulte [Pagar antecipadamente por máquinas virtuais e economizar dinheiro com Instâncias de Máquina Virtual Reservada](https://go.microsoft.com/fwlink/?linkid=861721).

## <a name="why-should-i-buy-a-reserved-virtual-machine-instance"></a>Por que devo comprar uma Instância de Máquina Virtual Reservada?
Se você tiver máquinas virtuais que são executadas por longos períodos de tempo, adquirir uma instância de máquina virtual reservada oferece a você o melhor custo-benefício. Por exemplo, se você executa continuamente quatro instâncias de D2 padrão na região Oeste dos EUA, sem uma reserva você é cobrado com taxas de pagamentos pré-pago. Se você adquirir uma Instância de Máquina Virtual Reservada para essas quatro VMs, elas receberão imediatamente o benefício de cobrança. Elas não serão mais cobradas com as taxas pré-pagas. 

## <a name="what-charges-does-a-reserved-virtual-machine-instance-cover"></a>Quais cobranças uma Instância de Máquina Virtual Reservada contempla?
Uma reserva apenas abrange os encargos de infraestrutura da máquina virtual para suas máquinas virtuais Windows ou Linux. Uma reserva não cobre encargos adicionais de software, rede e armazenamento. Para máquinas virtuais do Windows, você pode cobrir os custos de licenciamento do Windows com o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reserved-virtual-machine-instance"></a>Quem está qualificado para adquirir uma Instância de Máquina Virtual Reservada?
Clientes do Azure com estes tipos de assinaturas podem adquirir uma Instância de Máquina Virtual Reservada:
-   Tipo de oferta de assinatura de Contrato Enterprise (MS-AZR-0017P).
-   Tipo de oferta de assinatura [paga conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/) (MS-AZR-003P).
Você deve ter a função "Proprietário" na assinatura para comprar uma Instância Reservada. Para comprar reservas em um registro Enterprise, o administrador da empresa deve habilitar compras de reserva no portal EA; por padrão, a configuração está habilitada.

## <a name="how-is-a-reserved-virtual-machine-instances-purchase-billed"></a>Como uma compra de Instância de Máquina Virtual Reservada é cobrada?
A compra de reserva é cobrada no método de pagamento associado à assinatura. Se você tiver uma assinatura do Enterprise, o custo de reserva será deduzido do seu saldo de investimento. Se o saldo de investimento não cobrir o custo da reserva, você será cobrado pelo excedente.
Se você tiver uma assinatura paga conforme o uso, será cobrado imediatamente do seu cartão de crédito. Se você for cobrado por fatura, verá os encargos na sua próxima fatura.

## <a name="how-is-the-purchased-reserved-virtual-machine-instance-discount-applied"></a>Como o desconto da Instância de Máquina Virtual Reservada adquirida é aplicado?
O desconto de Instância de Máquina Virtual Reservada se aplica às máquinas virtuais que correspondem aos atributos que você selecionar ao adquirir a reserva. Os atributos incluem o escopo em que as VMs correspondentes são executadas. Por exemplo, se você quiser um desconto de Instância de VM Reservada para quatro máquinas virtuais de D2 padrão na região Oeste dos EUA, selecione a assinatura em que as máquinas virtuais estão em execução. Se as máquinas virtuais são executadas em diferentes assinaturas em sua conta ou registro, então, selecione o escopo como compartilhado. O escopo compartilhado permite que o desconto de reserva seja aplicado entre assinaturas.
Você pode alterar o escopo depois de comprar uma Instância de VM Reservada. Para alterar o escopo, consulte a documentação sobre como gerenciar reservas.

O desconto de reserva se aplica somente a máquinas virtuais em assinaturas com tipos de oferta Enterprise ou pagas conforme o uso. Máquinas virtuais em execução em uma assinatura com outros tipos de oferta não recebem o desconto de reserva. Para registros do Enterprise, as assinaturas de desenvolvimento/teste do Enterprise não são qualificadas para os benefícios de Instância Reservada.

Como a reserva afeta a cobrança de máquina virtual está explicado em [Noções básicas da aplicação do benefício de cobrança de reserva](https://go.microsoft.com/fwlink/?linkid=863405).

## <a name="what-happens-when-the-reservation-term-expires"></a>O que acontece quando o prazo de reserva expira?
No final do prazo da reserva, o desconto na cobrança expira e a infraestrutura de máquina virtual é cobrada com o preço pré-pago. As reservas não são renovadas automaticamente. Para continuar a receber o desconto de cobrança, é necessário comprar uma nova Instância de Máquina Virtual Reservada. 

## <a name="sizes-and-regional-availability"></a>Tamanhos e disponibilidade regional
As reservas de estão disponíveis para a maioria dos tamanhos de VM, com algumas exceções:
- Tamanhos de VM de versão prévia – qualquer tamanho que esteja na versão prévia não está disponível para compra de Instância de Máquina Virtual Reservada.
- Nuvens – Instâncias de Máquina Virtual Reservadas não estão disponíveis para compra nas regiões do Azure Governo dos EUA, Alemanha ou China. 
- Cota insuficiente – Uma Instância de VM Reservada que tenha um escopo para uma assinatura única deve ter uma cota de vCPU disponível na assinatura para a nova RI. Por exemplo, se a assinatura de destino tiver um limite de cota de 10 vCPUs para a família da série D, você não poderá adquirir uma Instância de VM Reservada para 11 instâncias Standard_D1. A verificação de cota para reservas inclui as VMs já implantadas na assinatura. Por exemplo, se a assinatura tiver uma cota de 10 vCPUs para a família da série D. Se esta assinatura tiver duas instâncias standard_D1 implantadas, você poderá comprar uma Instância de VM Reservada para 10 instâncias standard_D1 nesta assinatura. 
- Restrições de capacidade – em circunstâncias raras, o Azure limita a compra de novas reservas para o subconjunto de tamanhos de VM, devido à baixa capacidade em uma região.

## <a name="next-steps"></a>Próximas etapas
Comece a economizar em máquinas virtuais comprando uma [Instância de Máquina Virtual Reservada](https://go.microsoft.com/fwlink/?linkid=861721). 

Se precisar de ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
