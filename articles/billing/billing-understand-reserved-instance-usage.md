---
title: "Entender o uso da Instância Reservada do Azure para sua assinatura Paga pelo Uso | Microsoft Docs"
description: "Saiba como interpretar seu uso para entender a aplicação da Instância Reservada na sua assinatura Paga pelo Uso."
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
ms.openlocfilehash: 29f153803d5eb74e2d287d97cf9436e81b2a3e20
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="understand-reserved-instance-usage-for-your-pay-as-you-go-subscription"></a>Entender o uso da Instância Reservada na sua assinatura Paga pelo Uso

Entender a utilização da Instância Reservada com o uso de ReservationId da [página de Reserva](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade ) e o arquivo de utilização do [portal de Contas do Azure.](https://account.azure.com)


>[!NOTE]
>Este artigo não se aplica aos clientes do EA. Se você for cliente do EA, consulte [Entender o uso da Instância Reservada no seu registro de Empresa.](billing-understand-reserved-instance-usage-ea.md) Este artigo também pressupõe que a reserva seja aplicada a uma única assinatura. Se a reserva for aplicada a mais de uma assinatura, o benefício de reserva pode abranger vários arquivos csv de uso. 

Na seção a seguir, suponha que você esteja executando uma VM do Windows Standard_DS1_v2 na região Leste dos EUA e que suas informações de reserva sejam como a tabela a seguir:

| Campo | Valor |
|---| :---: |
|ReservationId |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|Quantidade |1|
|SKU | Standard_DS1_v2|
|Região | eastus |

## <a name="reservation-application"></a>Aplicação de reserva

A parte de hardware da VM é coberta porque a VM implantada coincide com os atributos de reserva. Para ver qual software do Windows não é coberto pela Instância Reservada, vá até [Custos de software do Windows para Instâncias de VM de Reserva do Azure.](billing-reserved-instance-windows-software-costs.md)

### <a name="statement-section-of-csv"></a>Seção de instrução do csv
Esta seção do seu csv mostra o uso total para da sua reserva. Aplique o filtro no campo Medir Subcategoria que contém "Reserva-" e seus dados se parecerão com a captura de tela a seguir: ![Reserva de Instrução Direta](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

A linha de Base de Reserva de VM tem o número total de horas cobertas pela reserva. Essa linha é $0,00, porque a Instância Reservada faz essa cobertura. A linha de Reserva-Windows Svr (1 núcleo) cobre o custo do software do Windows.

### <a name="daily-usage-section-of-csv"></a>Seção de uso diário do csv
Filtre por informações adicionais e digite sua ID de Reserva. A captura de tela a seguir mostra os campos relacionados à reserva. 

![Encargos de uso diário](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. ReservationId no campo Informações Adicionais representa a reserva usada para aplicar o benefício à VM.
2. ConsumptionMeter é a ID de Medição da VM.
3. A linha Reservation-Base VM, da coluna Meter Sub-category, representa a linha de custo $0 na seção de instrução. O custo da execução desta VM já foi pago pela reserva.
4. É a Id do Medidor de Reserva. O custo deste medidor é $0. Qualquer VM que se qualifique para Instância Reservada tem este MeterId no csv para responder pelo custo. 
5. Standard_DS1_v2 é uma VM vCPU, e a VM é implantada sem o Benefício Híbrido do Azure. Portanto, esse medidor abrange o custo extra de software do Windows. Consulte [Custos de software do Windows para Instâncias de VM de Reserva do Azure.](billing-reserved-instance-windows-software-costs.md) para localizar o medidor correspondente a 1 núcleo de VM, série D. Se for usado o Benefício Híbrido do Azure, esse custo extra não será aplicado. 

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as Instâncias de Máquina Virtual Reservadas, consulte os artigos a seguir.

- [Pagar antecipadamente pelas Máquinas Virtuais com Instâncias de VM Reservadas](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Gerenciar Instâncias de Máquina Virtual Reservadas](billing-manage-reserved-vm-instance.md)
- [Economizar dinheiro de máquinas virtuais com Instâncias de Máquinas Virtuais Reservadas](billing-save-compute-costs-reservations.md)
- [Entender como é aplicado o desconto em Instâncias de Máquina Virtual Reservada](billing-understand-vm-reservation-charges.md)
- [Entender o uso da Instância Reservada no seu registro de Empresa](billing-understand-reserved-instance-usage-ea.md)
- [Os custos de software do Windows não estão incluídos nas Instâncias Reservadas](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.