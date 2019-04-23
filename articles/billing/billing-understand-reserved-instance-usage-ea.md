---
title: Entender o uso de reservas do Azure para Enterprise | Microsoft Docs
description: Aprenda a ler o seu uso para entender como a reserva do Azure para o seu registro Enterprise é aplicada.
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
ms.date: 03/13/2019
ms.author: banders
ms.openlocfilehash: daa7f6a116578fa8d1f2b5bf825a6f4cd48f7f64
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58648292"
---
# <a name="understand-azure-reservation-usage-for-your-enterprise-enrollment"></a>Entenda o uso de reserva do Azure para sua inscrição na empresa

Use o **ReservationId** de [página reservas](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) e o arquivo de uso de [portal EA](https://ea.azure.com) para avaliar o uso de reserva. Você também pode ver o uso de reserva na seção de resumo de uso de [portal de EA](https://ea.azure.com).

Se você comprou a reserva em um contexto de cobrança pagamento conforme o uso, consulte [entender o uso de reserva para a sua assinatura paga conforme o uso.](billing-understand-reserved-instance-usage.md)

## <a name="usage-for-reserved-virtual-machines-instances"></a>Uso para Instâncias de Máquinas Virtuais Reservadas

As seções a seguir, suponha que você esteja executando uma VM do Windows Standard_D1_v2 na região Leste dos EUA e suas informações de reserva, como a tabela a seguir:

| Campo | Valor |
|---| --- |
|ReservationId |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|Quantidade |1|
|SKU | Standard_D1|
|Região | eastus |

A parte de hardware da VM é coberta porque a VM implantada coincide com os atributos de reserva. Para ver quais softwares do Windows não são cobertos pela reserva, consulte [Instâncias de VM de Reserva do Azure Custos do software do Windows](billing-reserved-instance-windows-software-costs.md).

### <a name="usage-in-csv-file-for-reserved-vm-instances"></a>Uso no arquivo CSV para instâncias de VM reservadas

Você pode baixar o arquivo CSV de uso do Enterprise no Portal Empresarial. No arquivo CSV, filtre **Informações Adicionais** e digite **ReservationID**. A captura de tela a seguir mostra os campos relacionados à reserva:

![Csv do Enterprise Agreement (EA) para reserva do Azure](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. **ReservationId** em **campo Informações Adicionais** representa a reserva aplicada à VM.
2. **ConsumptionMeter** é a ID do medidor para a VM.
3. **Meter ID** é o medidor de reservas com custo de $ 0. O custo da VM em execução é pago pela instância da VM reservada.
4. Standard_D1 é uma VM vCPU e a VM é implantada sem o Benefício Híbrido do Azure. Portanto, esse medidor abrange o custo extra de software do Windows. Para localizar o medidor correspondente a série D 1 núcleo de VM, consulte [os custos de software do Windows de instâncias de VM do Azure reserva](billing-reserved-instance-windows-software-costs.md).  Se você tiver o benefício híbrido do Azure, esse custo extra não é aplicado.

## <a name="usage-for-sql-database--cosmos-db-reserved-capacity-reservations"></a>Uso para reservas de capacidade reservadas do Banco de Dados SQL e Cosmos DB

As seções a seguir usam o Banco de Dados SQL do Azure como exemplo para descrever o relatório de uso. Também é possível usar as mesmas etapas para obter o uso do Azure Cosmos DB.

Suponha que você esteja executando um Banco de Dados SQL Gen 4 na região leste dos EUA e as informações de reserva são semelhantes à tabela a seguir:

| Campo | Valor |
|---| --- |
|ReservationId |8244e673-83e9-45ad-B54B-3f5295d37cae|
|Quantidade |2|
|Produto| Banco de dados do SQL Gen 4 (2 núcleos)|
|Região | eastus |

### <a name="usage-in-csv-file"></a>Uso no arquivo CSV

Filtre em **Informações Adicionais**, digite a **ID de Reserva** e escolha a **Categoria do Medidor** necessária - Banco de Dados SQL do Azure ou Azure Cosmos DB. A captura de tela a seguir mostra os campos relacionados à reserva.

![Capacidade reservada do csv de EA (Enterprise Agreement) para o banco de dados SQL](./media/billing-understand-reserved-instance-usage-ea/billing-ea-sql-db-reserved-capacity-csv.png)

1. **ReservationId** no **informações adicionais** campo é a reserva é aplicada ao recurso de banco de dados SQL.
2. **ConsumptionMeter** é o ID do medidor para o recurso Banco de Dados SQL.
3. **ID do medidor** é o medidor de reserva com custo de US $0. Qualquer recurso de banco de dados SQL que se qualifica para a reserva mostra essa ID de medidor no arquivo CSV.

## <a name="usage-summary-page-in-enterprise-portal"></a>Página de resumo de uso no portal da empresa

Seu uso de reserva do Azure também é exibido na seção de resumo de uso no Portal Empresarial: ![Resumo de uso do Enterprise Agreement (EA)](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)

1. Você não é cobrado o componente de hardware da VM pois ele é coberto pela reserva. Para uma reserva de banco de dados SQL, você ver uma linha com **nome do serviço** banco de dados SQL do Azure reservadas a utilização da capacidade.
2. Neste exemplo, você não tem o benefício híbrido do Azure para que você será cobrado para o software do Windows usado com a VM.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de Ajuda, [criar uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:

- [O que são Reservas do Azure?](billing-save-compute-costs-reservations.md)
- [Pré-pagamento para máquinas virtuais com instâncias de VMs reservadas do Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Pagar antecipadamente por recursos de computação de banco de dados SQL com capacidade reservada do Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Gerenciar Reservas do Azure](billing-manage-reserved-vm-instance.md)
- [Entender como o desconto de reserva é aplicado](billing-understand-vm-reservation-charges.md)
- [Entender o uso de reserva para a sua assinatura paga conforme o uso](billing-understand-reserved-instance-usage.md)
- [Custos de software do Windows não estão incluídos nas reservas](billing-reserved-instance-windows-software-costs.md)

