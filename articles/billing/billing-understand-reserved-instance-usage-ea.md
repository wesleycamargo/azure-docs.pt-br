---
title: Entender o uso da Instância Reservada do Azure para Empresas - Azure Billing | Microsoft Docs
description: Saiba como interpretar seu uso para entender como a Instância de VM Reservada do Azure para a sua assinatura de empresa é aplicada.
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
ms.date: 05/09/2018
ms.author: manshuk
ms.openlocfilehash: a92fce33b194c5cb7b763930e7fd11135f9fbd4f
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301307"
---
# <a name="understand-azure-reserved-instance-usage-for-your-enterprise-enrollment"></a>Entender o uso da Instância Reservada do Azure no seu registro de Empresa
Entender a utilização da Instância Reservada com o uso de ReservationId da **página Reserva** e o [arquivo de utilização do](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)portal [EA](https://ea.azure.com). Você também pode ver o uso de Instância Reservada na seção de resumo de uso do [portal EA](https://ea.azure.com).

>[!NOTE]
>Se você comprou a Instância Reservada em um contexto de cobrança Pago pelo Uso, consulte [Entender o uso da Instância Reservada para sua assinatura Paga pelo Uso.](billing-understand-reserved-instance-usage.md)

Na seção a seguir, suponha que você esteja executando uma VM do Windows Standard_D1_v2 na região Leste dos EUA e que suas informações de Instância Reservadas sejam como a tabela a seguir:

| Campo | Valor |
|---| --- |
|ReservationId |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|Quantidade |1|
|SKU | Standard_D1|
|Região | eastus |

## <a name="reserved-instance-application"></a>Aplicação de Instância Reservada

A parte de hardware da VM é coberta porque a VM implantada coincide com os atributos de Instância Reservada. Para ver qual software do Windows não é coberto pela Instância Reservada, vá até [Custos de software do Windows para Instâncias de VM Reservadas do Azure.](billing-reserved-instance-windows-software-costs.md)


### <a name="reserved-instance-usage-in-csv"></a>Uso de instância reservada em csv
Você pode baixar o csv de uso de EA do portal EA. No arquivo csv baixado, filtre por informações adicionais e digite sua **ReservationID**. A captura de tela a seguir mostra os campos relacionados à Instância Reservada:

![Csv de EA (Enterprise Agreement) por Instância Reservada do Azure](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. **ReservationId** no campo Informações Adicionais representa a Instância Reservada usada para aplicar o benefício à VM.
2. ConsumptionMeter é a MeterId da VM.
3. Trata-se de Reservation Meter com o custo $0, pois o custo da execução da VM já é pago pela Instância Reservada. 
4. Standard_D1 é uma VM vCPU, e a VM é implantada sem o Benefício Híbrido do Azure. Portanto, esse medidor abrange o custo extra de software do Windows. Consulte [Custos de software do Windows para Instâncias de VM de Reserva do Azure.](billing-reserved-instance-windows-software-costs.md) para localizar o medidor correspondente a 1 núcleo de VM, série D. Se for usado o Benefício Híbrido do Azure, esse custo extra não será aplicado.

### <a name="reserved-instance-usage-in-usage-summary-page-in-ea-portal"></a>Uso de Instância Reservada na página de resumo de uso no portal EA

O uso da Instância Reservada também é mostrada na seção de resumo do portal EA: ![Resumo do uso de EA](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)

1. Você não é cobrado pelo componente de hardware da VM, pois ele é coberto pela Instância Reservada. 
2. Você é cobrado pelo software do Windows, pois o Benefício Híbrido do Azure não é usado. 

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as Instâncias Reservadas do Azure, confira os seguintes artigos:

- [Economizar dinheiro de máquinas virtuais com Instâncias Reservadas do Microsoft Azure](billing-save-compute-costs-reservations.md)
- [Pagar antecipadamente por Máquinas Virtuais com Instâncias Reservadas](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Gerenciar as Instâncias Reservadas](billing-manage-reserved-vm-instance.md)
- [Entender como é aplicado o desconto de Instância Reservada](billing-understand-vm-reservation-charges.md)
- [Entender o uso da Instância Reservada na sua assinatura Paga pelo Uso](billing-understand-reserved-instance-usage.md)
- [Os custos de software do Windows não estão incluídos nas Instâncias Reservadas](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.