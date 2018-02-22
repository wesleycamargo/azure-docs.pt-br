---
title: "Entender o uso da Instância Reservada do Azure para Empresas | Microsoft Docs"
description: "Saiba como interpretar seu uso para entender a aplicação da Instância Reservada no seu registro de Empresa."
services: billing
documentationcenter: 
author: manish-shukla01
manager: manshuk
editor: 
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: manshuk
ms.openlocfilehash: 515eae3c9a84a171bebc5213f5824e1b50336e34
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="understand--reserved-instance-usage-for-your-enterprise-enrollment"></a>Entender o uso da Instância Reservada no seu registro de Empresa
Entender a utilização da Instância Reservada com o uso de ReservationId da [página Reserva](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade ) e o arquivo de utilização do [portal EA.](https://ea.azure.com) Você também pode ver o uso de reserva na seção de resumo de uso do [portal EA.](https://ea.azure.com)

>[!NOTE]
>Se você comprou a reserva em um contexto de cobrança Pago pelo Uso, consulte [Entender o uso da Instância Reservada para sua assinatura Paga pelo Uso.](billing-understand-reserved-instance-usage.md)

Na seção a seguir, suponha que você esteja executando uma VM do Windows Standard_D1_v2 na região Leste dos EUA e que suas informações de reserva sejam como a tabela a seguir:

| Campo | Valor |
|---| --- |
|ReservationId |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|Quantidade |1|
|SKU | Standard_D1|
|Região | eastus |

## <a name="reservation-application"></a>Aplicação de reserva

A parte de hardware da VM é coberta porque a VM implantada coincide com os atributos de reserva. Para ver qual software do Windows não é coberto pela Instância Reservada, vá até [Custos de software do Windows para Instâncias de VM Reservadas do Azure.](billing-reserved-instance-windows-software-costs.md)


### <a name="reservation-usage-in-csv"></a>Uso de reserva em arquivo csv
Você pode baixar o csv de uso de EA do portal EA. No arquivo csv baixado, filtre por informações adicionais e digite sua ID de Reserva. A captura de tela a seguir mostra os campos relacionados à reserva:

![Csv de EA por Instância Reservada](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. ReservationId no campo Informações Adicionais representa a reserva usada para aplicar o benefício à VM.
2. ConsumptionMeter é a MeterId da VM.
3. Trata-se de ReservationMeter com o custo $0, pois o custo da execução da VM já é pago pela reserva. 
4. Standard_D1 é uma VM vCPU, e a VM é implantada sem o Benefício Híbrido do Azure. Portanto, esse medidor abrange o custo extra de software do Windows. Consulte [Custos de software do Windows para Instâncias de VM de Reserva do Azure.](billing-reserved-instance-windows-software-costs.md) para localizar o medidor correspondente a 1 núcleo de VM, série D. Se for usado o Benefício Híbrido do Azure, esse custo extra não será aplicado.

### <a name="reservation-usage-in-usage-summary-page-in-ea-portal"></a>Uso de reserva na página de resumo de uso no portal EA

O uso da Instância Reservada também é mostrado na seção sobre resumo de uso do portal EA: ![Resumo do uso de EA](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)

1. Você não é cobrado pelo componente de hardware da VM, pois ele é coberto pela Instância Reservada. 
2. Você é cobrado pelo software do Windows, pois o Benefício Híbrido do Azure não é usado. 

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as Instâncias de Máquina Virtual Reservadas, confira os artigos a seguir.

- [Pagar antecipadamente pelas Máquinas Virtuais com Instâncias de VM Reservadas](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Gerenciar Instâncias de Máquina Virtual Reservadas](billing-manage-reserved-vm-instance.md)
- [Economizar dinheiro em máquinas virtuais com Instâncias de Máquinas Virtuais Reservadas](billing-save-compute-costs-reservations.md)
- [Entender como é aplicado o desconto em Instâncias de Máquina Virtual Reservadas](billing-understand-vm-reservation-charges.md)
- [Entender o uso da Instância Reservada na sua assinatura de Pagamento Conforme o Uso](billing-understand-reserved-instance-usage.md)
- [Custos de software do Windows não inclusos nas Instâncias Reservadas](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.