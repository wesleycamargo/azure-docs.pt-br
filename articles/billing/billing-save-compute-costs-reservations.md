---
title: O que são Reservas do Azure? | Microsoft Docs
description: Saiba mais sobre Reservas do Azure e preços para economizar em suas máquinas virtuais, bancos de dados SQL e outros custos do recurso.
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
ms.openlocfilehash: 82b23f46acc94fefccc871583657200b90a33f05
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43303555"
---
# <a name="what-are-azure-reservations"></a>O que são Reservas do Azure?

As Reservas do Azure ajudam-lhe a economizar dinheiro, pagando previamente por um ano ou três anos de máquina virtual, capacidade de computação do Banco de Dados SQL ou outros recursos do Azure. Pagar previamente permite que você obtenha um desconto nos recursos que você usar. As reservas podem reduzir significativamente a máquina virtual, computação do Banco de Dados SQL ou outros custos de recursos em até 72% nos preços pagos conforme o uso. As reservas fornecem um desconto de cobrança e não afetam o estado de tempo de execução dos recursos.

Você pode comprar uma instância reservada no [portal do Azure](https://aka.ms/reservations). Para saber mais, consulte os tópicos a seguir:

- [Pré-pagamento para máquinas virtuais com instâncias de VMs reservadas do Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Pagar antecipadamente por recursos de computação de banco de dados SQL com capacidade reservada do Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)

## <a name="why-should-i-buy-a-reservation"></a>Por que devo comprar uma reserva?

Se você tiver máquinas virtuais ou banco de dados SQL que são executados por longos períodos de tempo, a compra de uma instância reservada oferece a opção mais econômica. Por exemplo, se você executa continuamente quatro instâncias de um serviço sem a reserva, você será cobrado com taxas pré-pagas. Se você comprar uma reserva para esses recursos, você obtém imediatamente o desconto de reserva. Os recursos não serão mais cobrados com base nas taxas pagas conforme o uso.

## <a name="what-charges-does-a-reservation-cover"></a>Quais cobranças uma reserva cobre?

- Instância de Máquina Virtual reservada: uma reserva apenas abrange os custos de computação de máquina virtual. Não cobre encargos adicionais de software, rede e armazenamento.
- Vcore reservado de Banco de Dados SQL: apenas os custos de computação são incluídos com uma reserva. A licença é cobrada separadamente.

Para as máquinas virtuais do Windows e banco de Dados SQL, você pode cobrir os custos de licenciamento com o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Quem é elegível para comprar uma reserva?

Os clientes do Azure com esses tipos de assinatura podem comprar uma reserva:

- Tipo de oferta de assinatura de Contrato Enterprise (MS-AZR-0017P).
- Tipo de oferta de assinatura [paga conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/) (MS-AZR-003P). Você deve ter a função "Proprietário" na assinatura para comprar uma reserva.
- Os parceiros de CSP (Provedor de Soluções na Nuvem) podem usar o portal do Azure ou o [Partner Center](https://docs.microsoft.com/partner-center/azure-reservations) para comprar Reservas do Azure.

Um desconto de reserva aplica-se apenas aos recursos associados aos tipos de assinatura Enterprise, Pagamento Conforme o Uso ou CSP.

## <a name="how-is-a-reservation-billed"></a>Como uma reserva é cobrada?

A reserva é cobrada no método de pagamento associado à assinatura. Se você tiver uma assinatura do Enterprise, o custo de reserva será deduzido do seu saldo de investimento. Se o saldo de investimento não cobrir o custo da reserva, você será cobrado pelo excedente. Se você tiver uma assinatura paga conforme o uso, será cobrado imediatamente do seu cartão de crédito. Se você for cobrado por fatura, verá os encargos na sua próxima fatura.

## <a name="how-is-the-reservation-discount-applied"></a>Como o desconto de reserva é aplicado?

O desconto de reserva se aplica para o uso de recursos que correspondem aos atributos que você seleciona ao comprar a reserva. Os atributos incluem o escopo em que as VMs, banco de dados SQL correspondentes são executados. Por exemplo, caso deseje obter um desconto de reserva para quatro máquinas virtuais Standard D2 na região Oeste dos EUA, selecione a assinatura na qual as VMs estão em execução. Se as máquinas virtuais são executadas em diferentes assinaturas em sua conta ou registro, então, selecione o escopo como compartilhado. O escopo compartilhado permite que o desconto de reserva seja aplicado entre assinaturas. Você pode alterar o escopo depois de comprar uma reserva. Para obter mais informações, consulte [Gerenciar Reservas do Azure](billing-manage-reserved-vm-instance.md).

Um desconto de reserva aplica-se apenas aos recursos associados aos tipos de assinatura Enterprise, Pagamento Conforme o Uso ou CSP. Os recursos executados em uma assinatura com outros tipos de oferta não recebem o desconto de reserva. Para registros do Enterprise, as assinaturas de Desenvolvimento/Teste do Enterprise não são qualificadas para os benefícios de reserva.

Para compreender melhor como as reservas afetam a cobrança, consulte os tópicos a seguir:

-  [Compreender o desconto de Instâncias de VM Reservadas do Azure](billing-understand-vm-reservation-charges.md)
- [Compreender o desconto de reserva do Azure](billing-understand-vm-reservation-charges.md)
- Compreender o desconto e uso de reservas do Azure para SUSE

## <a name="what-happens-when-the-reservation-term-expires"></a>O que acontece quando o prazo de reserva expira?

No final do prazo de reserva, o desconto de cobrança expira e a máquina virtual, o Banco de Dados SQL ou outro recurso é cobrado pelo preço de pago conforme o uso. As Reservas do Azure não são renovadas automaticamente. Para continuar recebendo o desconto de cobrança, você deve comprar uma nova reserva para serviços e softwares elegíveis.

## <a name="next-steps"></a>Próximas etapas

Comece a economizar em máquinas virtuais comprando uma [Instância de Máquina Virtual Reservada](../virtual-machines/windows/prepay-reserved-vm-instances.md) ou [capacidade reservada de Banco de Dados SQL do Microsoft Azure](../sql-database/sql-database-reserved-capacity.md).

Para saber mais sobre Reservas do Azure, consulte os artigos a seguir:

- [Gerenciar Reservas do Azure](billing-manage-reserved-vm-instance.md)
- [Entender o uso de reserva para a sua assinatura paga conforme o uso](billing-understand-reserved-instance-usage.md)
- [Entender o uso de reserva para seu registro de empresa](billing-understand-reserved-instance-usage-ea.md)
- [Custos de software do Windows não estão incluídos nas reservas](billing-reserved-instance-windows-software-costs.md)
- [Reservas do Azure no programa de CSP (Provedor de Soluções na Nuvem) do Partner Center.](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
