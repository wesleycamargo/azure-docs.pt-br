---
title: Entender o desconto de reservas do Azure | Microsoft Docs
description: Saiba como um desconto de reserva é aplicado à execução de bancos de dados SQL.
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
ms.date: 08/08/2018
ms.author: cwatson
ms.openlocfilehash: ee73cb3164ce59136dd268853b8caa967a6f42e9
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393376"
---
# <a name="understand-how-an-azure-reservation-discount-is-applied-to-sql-databases"></a>Entenda como um desconto de reserva do Azure é aplicado a bancos de dados SQL

Depois de comprar uma capacidade reservada do Banco de Dados SQL do Azure, o desconto de reserva é aplicado automaticamente aos Bancos de Dados SQL que correspondem aos atributos e à quantidade da reserva. Uma reserva abrange os custos de computação de banco de dados SQL. Você é cobrado por software, armazenamento e rede às taxas normais. Você pode cobrir os custos de licenciamento para bancos de dados SQL com [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

Para instâncias de máquina Virtual reservadas, consulte [entender instâncias reservadas do Azure VM desconto](billing-understand-vm-reservation-charges.md).

## <a name="reservation-discount-applied-to-sql-databases"></a>Desconto de reserva aplicado a bancos de dados SQL

 O desconto de capacidade reservada do Banco de Dados SQL é aplicado à execução de Bancos de Dados SQL a cada hora. A reserva que você compra é correspondida ao uso de computação emitido pelos Bancos de Dados SQL em execução. Para Bancos de Dados SQL que não são executados a hora inteira, a reserva é aplicada automaticamente a outros Bancos de Dados SQL que correspondem aos atributos de reserva. O desconto pode ser aplicado a bancos de dados SQL que estão sendo executados simultaneamente. Se você não tiver bancos de dados SQL que sejam executados durante a hora inteira que corresponda aos atributos de reserva, você não obterá o benefício total do desconto de reserva para essa hora.

Os exemplos a seguir mostram como o desconto de capacidade reservada do Banco de Dados SQL se aplica, dependendo do número de núcleos que você comprou e quando está sendo executado.

- Cenário 1: Você compra uma capacidade reservada do Banco de Dados SQL para um Banco de Dados SQL de 8 núcleos. Você executa um banco de dados SQL de 16 núcleos que corresponde ao restante dos atributos da reserva. Você é cobrado pelo preço pré-pago de 8 núcleos de uso de computação do Banco de Dados SQL. Você recebe o desconto de reserva para uma hora de uso de computação do Banco de Dados SQL principal de 8.

Para o restante desses exemplos, suponha que a capacidade de banco de dados do SQL reservadas que comprar é para um banco de dados SQL de 16 núcleos e o restante dos atributos de reserva corresponder os bancos de dados SQL em execução.

- Cenário 2: Você executa dois bancos de dados SQL com 8 núcleos por uma hora. O desconto de reserva de 16 núcleos é aplicado para computação de uso para as duas de 8 núcleos de bancos de dados SQL.
- Cenário 3: Executar um 16 núcleos, banco de dados SQL do pm 1 para 1:30 pm. Você pode executar outro núcleo 16 banco de dados SQL de 1:30 às 2 horas. Ambos são cobertas pelo desconto de reserva.
- Cenário 4: você executa um banco de dados SQL de 16 núcleos das 13h às 13h45. Você pode executar outro núcleo 16 banco de dados SQL de 1:30 às 2 horas. Você será cobrado o preço pago conforme o uso para a sobreposição de 15 minutos. O desconto de reserva se aplica ao uso de computação para o restante do tempo.

Para entender e visualizar o aplicativo de suas reservas do Azure nos relatórios de uso de faturamento, consulte [Entender o uso de reserva do Azure](https://go.microsoft.com/fwlink/?linkid=862757).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:

- [O que são Reservas do Azure?](billing-save-compute-costs-reservations.md)
- [Pré-pagamento para máquinas virtuais com instâncias de VMs reservadas do Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Pagar antecipadamente por recursos de computação de banco de dados SQL com capacidade reservada do Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Gerenciar Reservas do Azure](billing-manage-reserved-vm-instance.md)
- [Entender o uso de reserva para a sua assinatura paga conforme o uso](billing-understand-reserved-instance-usage.md)
- [Entenda o uso de reservas para o seu registro Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Entender o uso de reserva para assinaturas de CSP](https://docs.microsoft.com/partner-center/azure-reservations)


## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
