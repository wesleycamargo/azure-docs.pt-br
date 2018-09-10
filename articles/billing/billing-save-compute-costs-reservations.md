---
title: O que são as reservas do Azure? | Microsoft Docs
description: Saiba mais sobre reservas do Azure e preços para economizar em suas máquinas virtuais, bancos de dados SQL e outros custos de recursos.
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
ms.date: 08/08/2018
ms.author: yashar
ms.openlocfilehash: 93c11852a11e0bb57a0b92090368298fc14b8c2a
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39626297"
---
# <a name="what-are-azure-reservations"></a>O que são as reservas do Azure?

As reservas do Azure ajudam você a economizar dinheiro fazendo o pré-pagamento por um ou três anos de máquina virtual ou capacidade de computação de Banco de Dados SQL. Pagar previamente permite que você obtenha um desconto nos recursos que você usar. As instâncias do Azure podem reduzir significativamente os custos de sua máquina virtual ou custos de computação de banco de dados SQL - até 72% com base em preços pagos - com um compromisso inicial de um ou três anos. as reservas fornecem um desconto de faturamento e não afetam o estado de tempo de execução de suas máquinas virtuais banco de dados SQL.

Você pode comprar uma instância reservada no [portal do Azure](https://aka.ms/reservations). Para saber mais, consulte os tópicos a seguir:

- [Pré-pagamento para máquinas virtuais com instâncias de VMs reservadas do Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Pagar antecipadamente por recursos de computação de banco de dados SQL com capacidade reservada do Banco de Dados SQL do Microsoft Azure](../sql-database/sql-database-reserved-capacity.md)

## <a name="why-should-i-buy-a-reservation"></a>Por que devo comprar uma reserva?

Se você tiver máquinas virtuais ou banco de dados SQL que são executados por longos períodos de tempo, a compra de uma instância reservada oferece a opção mais econômica. Por exemplo, se você executa continuamente quatro instâncias de um serviço sem a reserva, você será cobrado com taxas pré-pagas. Se você comprar uma reserva para esses recursos, você obtém imediatamente o desconto de reserva. Os recursos não serão mais cobrados com base nas taxas pagas conforme o uso.

## <a name="what-charges-does-a-reservation-cover"></a>Quais cobranças uma reserva cobre?

- Instância de Máquina Virtual reservada: uma reserva apenas abrange os custos de computação de máquina virtual. Não cobre encargos adicionais de software, rede e armazenamento.
- Vcore reservado de Banco de Dados SQL: apenas os custos de computação são incluídos com uma reserva. A licença é cobrada separadamente.

Para as máquinas virtuais do Windows e banco de Dados SQL, você pode cobrir os custos de licenciamento com o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Quem é elegível para comprar uma reserva?

Os clientes do Azure com esses tipos de assinatura podem comprar uma reserva:

- Tipo de oferta de assinatura de Contrato Enterprise (MS-AZR-0017P).
- Tipo de oferta de assinatura [paga conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/) (MS-AZR-003P). Você deve ter a função "Proprietário" na assinatura para comprar uma reserva. Para comprar reservas em um registro de empresa, o administrador da empresa deve habilitar compras de reserva no portal EA. Por padrão, essa configuração está habilitada.
- Os parceiros do Provedor de solução de nuvem (CSP) podem usar o portal do Microsoft Azure ou o [Centro de parceiros](https://docs.microsoft.com/partner-center/azure-reservations) para comprar reservas. 

Um desconto de reserva no Azure apenas é aplicado a máquinas virtuais ou banco de Dados SQL associados a tipos de assinatura de empresa, pagamento Conforme o Uso ou CSP.

## <a name="how-is-a-reservation-billed"></a>Como uma reserva é cobrada?

A reserva é cobrada no método de pagamento associado à assinatura. Se você tiver uma assinatura do Enterprise, o custo de reserva será deduzido do seu saldo de investimento. Se o saldo de investimento não cobrir o custo da reserva, você será cobrado pelo excedente. Se você tiver uma assinatura paga conforme o uso, será cobrado imediatamente do seu cartão de crédito. Se você for cobrado por fatura, verá os encargos na sua próxima fatura.

## <a name="how-is-the-reservation-discount-applied"></a>Como o desconto de reserva é aplicado?

O desconto de reserva se aplica para o uso de recursos que correspondem aos atributos que você seleciona ao comprar a reserva. Os atributos incluem o escopo em que as VMs, banco de dados SQL correspondentes são executados. Por exemplo, caso deseje obter um desconto de reserva para quatro máquinas virtuais Standard D2 na região Oeste dos EUA, selecione a assinatura na qual as VMs estão em execução. Se as máquinas virtuais são executadas em diferentes assinaturas em sua conta ou registro, então, selecione o escopo como compartilhado. O escopo compartilhado permite que o desconto de reserva seja aplicado entre assinaturas. Você pode alterar o escopo depois de comprar uma reserva. Para obter mais informações, consulte [Gerenciar reservas no Azure](billing-manage-reserved-vm-instance.md).

O desconto de reserva no Azure apenas é aplicado a máquinas virtuais ou banco de Dados SQL associados a tipos de assinatura de empresa ou pagamento Conforme o Uso. Máquinas virtuais ou banco de dados SQL em execução em uma assinatura com outros tipos de oferta não recebem o desconto de reserva. Para registros do Enterprise, as assinaturas de Desenvolvimento/Teste do Enterprise não são qualificadas para os benefícios de reserva.

Para entender melhor como as reservas afetam sua máquina virtual ou faturamento de banco de dados SQL, consulte [Entenda como o desconto de reserva é aplicado](billing-understand-vm-reservation-charges.md).

## <a name="what-happens-when-the-reservation-term-expires"></a>O que acontece quando o prazo de reserva expira?

No final do prazo da reserva, o desconto na cobrança expira e a máquina virtual, banco de dados SQL ou outros recursos são faturados de acordo com o preço pré-pago. As reservas do Azure não têm renovação automática. Para continuar recebendo o desconto na cobrança, é necessário comprar uma nova reserva para os serviços em que a reserva é aplicável.

## <a name="next-steps"></a>Próximas etapas

Comece a economizar em máquinas virtuais comprando uma [Instância de Máquina Virtual Reservada](../virtual-machines/windows/prepay-reserved-vm-instances.md) ou [capacidade reservada de Banco de Dados SQL do Microsoft Azure](../sql-database/sql-database-reserved-capacity.md).

Para saber mais sobre reservas, consulte os seguintes artigos:

- [Gerenciar reservas do Azure](billing-manage-reserved-vm-instance.md)
- [Entenda como o desconto de reserva é aplicado](billing-understand-vm-reservation-charges.md)
- [Entenda o uso de instâncias reservadas para sua assinatura de Pagamento Conforme o Uso](billing-understand-reserved-instance-usage.md)
- [Entenda o uso de reserva para o seu registro Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Os custos de software do Windows não incluídos com instâncias reservadas](billing-reserved-instance-windows-software-costs.md)
- [Reservas do Azure no programa do Provedor de Solução do Partner Center (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
