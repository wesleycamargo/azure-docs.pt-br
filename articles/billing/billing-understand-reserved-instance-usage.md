---
title: Entender o uso de Reservas do Azure para assinatura Pagamento Conforme o Uso | Microsoft Docs
description: Aprenda a ler o seu uso para entender como a reserva do Azure para sua assinatura do Pré-pago é aplicada.
services: billing
documentationcenter: ''
author: manish-shukla01
manager: manshuk
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: manshuk
ms.openlocfilehash: 1226b2f73d556da2ff7d73f6f322e0bd1590f915
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307051"
---
# <a name="understand-azure-reservation-usage-for-your-pay-as-you-go-subscription"></a>Entenda o uso de reserva do Azure para sua assinatura do Pague conforme o uso

Use o ReservationId da [página Reservation](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) e o arquivo de uso do [portal Accounts do Azure](https://account.azure.com) para avaliar o uso de sua reserva.

Se você for um cliente com um Enterprise Agreement, consulte [entender o uso de reserva para seu registro de empresa.](billing-understand-reserved-instance-usage-ea.md).

Este artigo pressupõe que a reserva é aplicada a uma única assinatura. Se a reserva for aplicada a mais de uma assinatura, seu benefício de reserva poderá abranger vários arquivos CSV de uso.

## <a name="usage-for-reserved-virtual-machine-instances"></a>Uso para instâncias de máquina Virtual reservada

As seções a seguir, suponha que você está executando uma VM do Windows Standard_DS1_v2 na região Leste dos EUA e seus reservado VM instância informações como a tabela a seguir:

| Campo | Valor |
|---| :---: |
|ReservationId |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|Quantidade |1|
|SKU | Standard_DS1_v2|
|Região | eastus |

A parte de hardware da VM é coberta porque a VM implantada coincide com os atributos de reserva. Para ver qual software do Windows não é coberto pela instância de VM reservada, consulte [os custos de software do Windows de instâncias de VM de reserva do Azure](billing-reserved-instance-windows-software-costs.md)

### <a name="statement-section-of-csv-file-for-vms"></a>Seção de instrução do arquivo CSV para VMs

Esta seção do arquivo CSV mostra o uso total para sua reserva. Aplicar o filtro a **subcategoria de medidor** campo que contém **"Reserva-"**. Você vê algo como a seguinte captura de tela:

![Captura de tela dos detalhes e cobranças do uso da reserva filtrada](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

A linha **VM de Reserva-Base** tem o número total de horas cobertas pela reserva. Essa linha é $0,00 porque a reserva abrange-lo. O **Reservation-Windows Svr (1 núcleo)** linha abrange o custo de software do Windows.

### <a name="daily-usage-section-of-csv-file"></a>Seção uso diário do arquivo CSV

Filtrar **informações adicionais** e digite sua **ID de reserva**. A captura de tela a seguir mostra os campos relacionados à reserva.

![Captura de tela de detalhes de uso diário e cobranças](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. **ReservationId** no campo **Additional Info** é a reserva aplicada à VM.
2. **ConsumptionMeter** é a ID do medidor para a VM.
3. O **VM Reservation-Base** **subcategoria de medidor** linha representa o custo de US $0 na seção de instrução. O custo de execução desta VM já é pago pela reserva.
4. **ID do medidor** é a ID do medidor para a reserva. O custo deste medidor é $0. Esse ID do medidor aparece para qualquer VM que se qualifique para o desconto de reserva.
5. Standard_DS1_v2 é uma VM vCPU, e a VM é implantada sem o Benefício Híbrido do Azure. Portanto, esse medidor cobre a carga extra do software Windows. Para encontrar o medidor correspondente à VM core da série D 1, consulte [Instâncias de VM de Reserva do Azure Custos do software do Windows](billing-reserved-instance-windows-software-costs.md). Se você tiver o benefício híbrido do Azure, esse custo extra não será aplicado.

## <a name="usage-for-sql-database-reserved-capacity-reservations"></a>Uso do banco de dados do SQL reservadas reservas de capacidade

As seções a seguir, suponha que você esteja executando um banco de dados de SQL Gen 4 na região Leste dos EUA e suas informações de reserva, como a tabela a seguir:

| Campo | Valor |
|---| --- |
|ReservationId |446ec809-423D-467c-8c5c-bbd5d22906b1|
|Quantidade |2|
|Produto| Banco de dados do SQL Gen 4 (2 núcleos)|
|Região | eastus |

### <a name="statement-section-of-csv-file"></a>Seção de instrução do arquivo CSV

Filtrar **uso de instância reservado** nome do medidor. Você vê algo como a seguinte captura de tela:

![Capacidade reservada do arquivo CSV para o banco de dados SQL](./media/billing-understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-statements.png)

O **uso de instância reservado** linha tem o número total de horas de núcleo coberta pela reserva. A taxa é de $ 0 para esta linha, pois a reserva cobriu o custo.

### <a name="detail-section-of-csv-file"></a>Seção de detalhes do arquivo CSV

Filtrar **informações adicionais** e digite sua **ID de reserva**. A captura de tela a seguir mostra os campos relacionados à reserva de capacidade reservada do Banco de Dados SQL.

![Capacidade reservada do arquivo CSV para o banco de dados SQL](./media/billing-understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-details.png)

1. **ReservationId** no **informações adicionais** campo é a reserva de capacidade reservada do banco de dados do SQL que é aplicada ao recurso de banco de dados do SQL.
2. **ConsumptionMeter** é o ID do medidor para o recurso Banco de Dados SQL.
3. O **Id do medidor** é o medidor de reserva. O custo deste medidor é $0. Quaisquer recursos de banco de dados SQL que se qualificam para o desconto de reserva mostra essa ID de medidor no arquivo CSV.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:

- [O que são Reservas do Azure?](billing-save-compute-costs-reservations.md)
- [Pré-pagamento para máquinas virtuais com instâncias de VMs reservadas do Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Pagar antecipadamente por recursos de computação de banco de dados SQL com capacidade reservada do Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Gerenciar Reservas do Azure](billing-manage-reserved-vm-instance.md)
- [Entender como o desconto de reserva é aplicado](billing-understand-vm-reservation-charges.md)
- [Entender o uso de reserva para seu registro de empresa](billing-understand-reserved-instance-usage-ea.md)
- [Custos de software do Windows não estão incluídos nas Reservas](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
