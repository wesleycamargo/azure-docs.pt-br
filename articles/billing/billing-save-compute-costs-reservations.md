---
title: O que são Reservas do Azure? | Microsoft Docs
description: Saiba mais sobre as Reservas do Azure e os preços para economizar nos custos de máquinas virtuais, de bancos de dados SQL, do Azure Cosmos DB e de outros recursos.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/06/2019
ms.author: banders
ms.openlocfilehash: a93bfd8f71c515bdd5112170f27336a0df62c6e5
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55818848"
---
# <a name="what-are-azure-reservations"></a>O que são Reservas do Azure?

As Reservas do Azure ajudam a economizar pagando previamente por um ano ou três anos de máquina virtual, de capacidade de computação do Banco de Dados SQL, de produtividade do Azure Cosmos DB ou de outros recursos do Azure. Pagar previamente permite que você obtenha um desconto nos recursos que você usar. As reservas podem reduzir significativamente os custos com máquinas virtuais, computação do Banco de Dados SQL, Azure Cosmos DB ou outros recursos em até 72% nos preços pagos conforme o uso. As reservas fornecem um desconto de cobrança e não afetam o estado de tempo de execução dos recursos.

Você pode comprar uma instância reservada no [portal do Azure](https://aka.ms/reservations). Para saber mais, consulte os tópicos a seguir:

- [Pré-pagamento para máquinas virtuais com instâncias de VMs reservadas do Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Pagar antecipadamente por recursos de computação de banco de dados SQL com capacidade reservada do Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Pagar antecipadamente por recursos do Azure Cosmos DB com capacidade reservada do Azure Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md)

## <a name="why-should-i-buy-a-reservation"></a>Por que devo comprar uma reserva?

Se você tem máquinas virtuais, bancos de dados SQL ou do Azure Cosmos DB executados por períodos de tempo longos, a compra de uma instância reservada oferece a opção mais econômica. Por exemplo, se você executa continuamente quatro instâncias de um serviço sem a reserva, você será cobrado com taxas pré-pagas. Se você comprar uma reserva para esses recursos, você obtém imediatamente o desconto de reserva. Os recursos não serão mais cobrados com base nas taxas pagas conforme o uso.

## <a name="what-charges-does-a-reservation-cover"></a>Quais cobranças uma reserva cobre?

- Instância de Máquina Virtual Reservada: Uma reserva cobre apenas os custos de computação da máquina virtual. Não cobre encargos adicionais de software, rede e armazenamento.
- vCore reservado do Banco de Dados SQL: Apenas os custos de computação são incluídos em uma reserva. A licença é cobrada separadamente.
- Capacidade reservada do Azure Cosmos DB: Uma reserva cobre a produtividade provisionada para seus recursos, mas não cobre encargos de rede e armazenamento.

Para as máquinas virtuais do Windows e banco de Dados SQL, você pode cobrir os custos de licenciamento com o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Quem é elegível para comprar uma reserva?

Os clientes do Azure com os seguintes tipos de assinatura podem comprar uma reserva:

- Tipo de oferta de assinatura de Contrato Enterprise (MS-AZR-0017P ou MS-AZR-0148P).
- Tipo de oferta de assinatura paga conforme o uso (MS-AZR-003P ou MS-AZR-0023P).
- Os parceiros do Provedor de Soluções na Nuvem (CSP) podem usar o portal do Azure ou o [Partner Center](https://docs.microsoft.com/partner-center/azure-reservations) para comprar Reservas do Azure.

Um desconto de reserva aplica-se apenas aos recursos associados aos tipos de assinatura Enterprise, Pagamento Conforme o Uso ou CSP.

 Comprar um plano:

- Você deve estar em uma função de Proprietário em pelo menos uma assinatura Enterprise ou Pagamento Conforme o Uso.

## <a name="how-is-a-reservation-billed"></a>Como uma reserva é cobrada?

A reserva é cobrada no método de pagamento associado à assinatura. Se você tiver uma assinatura do Enterprise, o custo de reserva será deduzido do seu saldo de investimento. Se o saldo de investimento não cobrir o custo da reserva, você será cobrado pelo excedente. Se você tiver uma assinatura paga conforme o uso, será cobrado imediatamente do seu cartão de crédito. Se você for cobrado por fatura, verá os encargos na sua próxima fatura.

## <a name="how-is-the-reservation-discount-applied"></a>Como o desconto de reserva é aplicado?

O desconto de reserva se aplica para o uso de recursos que correspondem aos atributos que você seleciona ao comprar a reserva. Os atributos incluem o escopo em que as VMs, os banco de dados SQL, o Azure Cosmos DB ou outros recursos correspondentes são executados. Por exemplo, caso deseje obter um desconto de reserva para quatro máquinas virtuais Standard D2 na região Oeste dos EUA, selecione a assinatura na qual as VMs estão em execução. Se as máquinas virtuais são executadas em diferentes assinaturas em sua conta ou registro, então, selecione o escopo como compartilhado. O escopo compartilhado permite que o desconto de reserva seja aplicado entre assinaturas. Você pode alterar o escopo depois de comprar uma reserva. Para obter mais informações, consulte [Gerenciar Reservas do Azure](billing-manage-reserved-vm-instance.md).

Um desconto de reserva aplica-se apenas aos recursos associados aos tipos de assinatura Enterprise, Pagamento Conforme o Uso ou CSP. Os recursos executados em uma assinatura com outros tipos de oferta não recebem o desconto de reserva.

Para compreender melhor como as reservas afetam a cobrança, consulte os tópicos a seguir:

- [Compreender o desconto de Instâncias de VM Reservadas do Azure](billing-understand-vm-reservation-charges.md)
- [Compreender o desconto de reserva do Azure](billing-understand-vm-reservation-charges.md)
- [Entender o desconto de reserva do Azure Cosmos DB](billing-understand-cosmosdb-reservation-charges.md)
- [Entender o desconto de reserva do Azure e o uso do SUSE](billing-understand-suse-reservation-charges.md)

## <a name="what-happens-when-the-reservation-term-expires"></a>O que acontece quando o prazo de reserva expira?

No final do prazo de reserva, o desconto de cobrança expira e a máquina virtual, o Banco de Dados SQL, o Azure Cosmos DB ou outro recurso é cobrado pelo preço pago conforme o uso. As Reservas do Azure não são renovadas automaticamente. Para continuar recebendo o desconto de cobrança, você deve comprar uma nova reserva para serviços e softwares elegíveis.

## <a name="discount-applies-to-different-sizes-with-instance-size-flexibility"></a>O desconto aplica-se a diferentes tamanhos com flexibilidade de tamanho da instância

Quando você compra uma reserva, o desconto pode ser aplicado a outras instâncias com atributos que estão dentro do mesmo grupo de tamanho. A flexibilidade da cobertura de desconto depende do tipo de reserva e dos atributos que você escolhe quando compra a reserva.

- Instâncias de VM reservadas: Ao comprar a reserva, se você selecionar **Otimizado para**: **flexibilidade de tamanho da instância**, a cobertura de desconto dependerá do tamanho da VM que você selecionar. A reserva pode ser aplicada aos tamanhos de VMs (máquinas virtuais) no mesmo grupo de série de tamanho. Para obter mais informações, confira [Flexibilidade de tamanho de máquina virtual com Instâncias de VM Reservadas](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).
- Plano de software SUSE Linux Enterprise: A cobertura de desconto depende das vCPUs das VMs em que você executa o software SUSE. Para obter mais informações, confira [Entender como o desconto do plano de software do SUSE Linux Enterprise é aplicado](billing-understand-suse-reservation-charges.md).
- Capacidade reservada do Banco de Dados SQL: A cobertura de desconto depende do nível de desempenho escolhido. Para obter mais informações, confira [Entender como um desconto de reserva do Azure é aplicado](billing-understand-reservation-charges.md).
- Capacidade reservada do Azure Cosmos DB: A cobertura de desconto depende da taxa de transferência provisionada. Para obter mais informações, confira [Entender como um desconto de reserva do Azure Cosmos DB é aplicado](billing-understand-cosmosdb-reservation-charges.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximas etapas

- Comece a economizar em suas máquinas virtuais comprando uma [Instância de VM Reservada](../virtual-machines/windows/prepay-reserved-vm-instances.md), uma [capacidade reservada do Banco de Dados SQL](../sql-database/sql-database-reserved-capacity.md) ou uma [capacidade reservada do Azure Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md).
- Saiba mais sobre as Reservas do Azure com os seguintes artigos:
    - [Gerenciar Reservas do Azure](billing-manage-reserved-vm-instance.md)
    - [Entender o uso de reserva para a sua assinatura paga conforme o uso](billing-understand-reserved-instance-usage.md)
    - [Entender o uso de reserva para seu registro de empresa](billing-understand-reserved-instance-usage-ea.md)
    - [Custos de software do Windows não estão incluídos nas reservas](billing-reserved-instance-windows-software-costs.md)
    - [Reservas do Azure no programa de CSP (Provedor de Soluções na Nuvem) do Partner Center](https://docs.microsoft.com/partner-center/azure-reservations)
