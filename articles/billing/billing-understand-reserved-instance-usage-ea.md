---
title: Entenda o uso de instância reservada do Azure para Enterprise | Microsoft Docs
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
ms.openlocfilehash: d6e8b2544f919abeb7fde0e37fc12bd29f0171ef
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064233"
---
# <a name="understand-azure-reserved-instance-usage-for-your-enterprise-enrollment"></a>Entenda o uso de instância reservada do Azure para Enterprise | Microsoft Docs...
Entenda a utilização de uma instância reservada usando o **ReservationId** da [Página de Reservas](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) e o arquivo de uso do [portal da EA](https://ea.azure.com). Você também pode ver o uso da instância reservada na seção de resumo de uso do [portal da EA](https://ea.azure.com).

>[!NOTE]
>Se você comprou a instância reservada em um contexto de faturamento pré-pago, consulte [Entenda o uso de instâncias reservadas para sua assinatura pré-paga.](billing-understand-reserved-instance-usage.md)

Para a seção a seguir, suponha que você esteja executando uma VM do Windows Standard_D1_v2 na região leste dos EUA e suas informações de instância reservadas se parecem com a tabela a seguir:

| Campo | Valor |
|---| --- |
|ReservationId |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|Quantidade |1|
|SKU | Standard_D1|
|Região | eastus |

## <a name="reserved-instance-application"></a>Aplicativo de instância reservada

A parte de hardware da VM é coberta porque a VM implementada corresponde aos atributos da instância reservada. Para ver qual software do Windows não é coberto pela instância reservada, vá para os custos de software de Instâncias de VMs Reservadas do Azure, vá para [Custos de software do Windows das Instâncias de VM do Azure Reserve.](billing-reserved-instance-windows-software-costs.md)


### <a name="reserved-instance-usage-in-csv"></a>Uso de instância reservada no csv
Você pode baixar o csv de uso de EA do portal EA. No arquivo csv baixado, filtre por informações adicionais e digite sua **ReservationID**. A captura de tela a seguir mostra os campos relacionados à instância reservada:

![Csv do Enterprise Agreement (EA) para instância reservada do Azure](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. **ReservationId** no campo Informações Adicionais representa a instância reservada usada para aplicar benefícios à VM.
2. ConsumptionMeter é a MeterId da VM.
3. Este é o medidor de reserva com custo de $ 0, pois o custo de execução da VM já é pago pela instância reservada. 
4. Standard_D1 é uma VM vCPU, e a VM é implantada sem o Benefício Híbrido do Azure. Portanto, esse medidor abrange o custo extra de software do Windows. Consulte [Custos de software do Windows para Instâncias de VM de Reserva do Azure.](billing-reserved-instance-windows-software-costs.md) para localizar o medidor correspondente a 1 núcleo de VM, série D. Se for usado o Benefício Híbrido do Azure, esse custo extra não será aplicado.

### <a name="reserved-instance-usage-in-usage-summary-page-in-ea-portal"></a>Uso de instância reservada na página de resumo de uso no portal da EA

O uso de instâncias reservadas também é exibido na seção de resumo de uso do portal da EA: ![Resumo do uso do Enterprise Agreement (EA)](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)

1. Você não é cobrado pelo componente de hardware da VM, pois ela é coberta pela instância reservada. 
2. Você é cobrado pelo software do Windows, pois o Benefício Híbrido do Azure não é usado. 

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as instâncias reservadas do Azure, consulte os seguintes artigos:

- [O que são instâncias de VM reservadas do Azure?](billing-save-compute-costs-reservations.md)
- [Pré-pagamento para máquinas virtuais com instâncias de VMs reservadas do Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Gerenciar instâncias reservadas no Azure](billing-manage-reserved-vm-instance.md)
- [Entenda como o desconto de instância reservada é aplicado](billing-understand-vm-reservation-charges.md)
- [Entenda o uso de instâncias reservadas para sua assinatura do Pay-As-You-Go](billing-understand-reserved-instance-usage.md)
- [Custos de software do Windows não incluídos com instâncias reservadas](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.