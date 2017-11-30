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
ms.openlocfilehash: 7ef601033b36ee968cb766d40a0a6b05afa9a1a4
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2017
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

A parte de hardware da VM é coberta porque a VM implantada coincide com os atributos de reserva. Para ver qual software do Windows não é coberto pela Instância Reservada, vá até [Custos de software do Windows para Instâncias de VM de Reserva do Azure.](billing-reserved-instance-windows-software-costs.md)


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

## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.