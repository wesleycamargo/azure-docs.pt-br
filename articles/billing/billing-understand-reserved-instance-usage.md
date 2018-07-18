---
title: Entender o uso de instância reservada do Azure para sua assinatura paga pelo uso | Microsoft Docs
description: Saiba como interpretar seu uso para entender como a Instância de VM Reservada do Azure para a sua assinatura Paga pelo uso é aplicada.
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
ms.openlocfilehash: 7e303f3e5ce0e618d941be4190f6fadb40f2e09d
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064191"
---
# <a name="understand-reserved-instance-usage-for-your-pay-as-you-go-subscription"></a>Entender o uso de instância reservado para sua assinatura paga pelo uso

Entender o uso de uma instância de VM do Azure reservados usando o ReservationId de [página reserva](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) e o arquivo de uso do [portal de contas do Azure](https://account.azure.com).


>[!NOTE]
>Este artigo não se aplica aos clientes do EA. Se você for cliente do EA, consulte [Entender o uso da Instância Reservada no seu registro de Empresa.](billing-understand-reserved-instance-usage-ea.md) Este artigo também pressupõe que a instância reservada é aplicada a uma única assinatura. Se a instância reservada é aplicada a mais de uma assinatura, o benefício de instância reservado pode abranger vários arquivos csv de uso. 

Para a seção a seguir, suponha que você está executando uma VM do Windows Standard_DS1_v2 na região Leste região dos EUA e suas informações de instância reservada parece a tabela a seguir:

| Campo | Valor |
|---| :---: |
|ReservationId |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|Quantidade |1|
|SKU | Standard_DS1_v2|
|Região | eastus |

## <a name="reserved-instance-application"></a>Aplicativo de instância reservada

A parte de hardware da VM é coberta porque a VM implementada corresponde aos atributos da instância reservada. Para ver qual software do Windows não é coberto pela Instância Reservada, vá até [Custos de software do Windows para Instâncias de VM de Reserva do Azure.](billing-reserved-instance-windows-software-costs.md)

### <a name="statement-section-of-csv"></a>Seção de instrução do csv
Esta seção do seu csv mostra o uso total para sua instância reservada. Aplique o filtro no campo subcategoria de medidor que contém "Reserva-" e seus dados se parecem com captura de tela a seguir: ![captura de tela de detalhes de uso de instância reservado filtrado e encargos](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

Linha de Base de reserva de VM tem o número total de horas que são abordados pela instância reservada. Essa linha é $0,00 porque cobre a instância reservada. A linha de Reserva-Windows Svr (1 núcleo) cobre o custo do software do Windows.

### <a name="daily-usage-section-of-csv"></a>Seção de uso diário do csv
Filtre por informações adicionais e digite sua **ID de Reserva**. Captura de tela a seguir mostra os campos relacionados à instância reservada. 

![Captura de tela de detalhes de uso diário e cobranças](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. **ReservationId** no campo Informações Adicionais é a instância reservada usada para aplicar benefícios à VM.
2. ConsumptionMeter é a ID de Medição da VM.
3. A linha Subcategoria do Medidor das Instâncias Reservadas de VM de base representa a linha de custo $0 na seção de instrução. Custo da execução desta VM já é pago por instância reservada.
4. Esta é a Id de medidor para instâncias reservadas. O custo deste medidor é $0. Qualquer VM que se qualificam para instâncias reservadas tem este MeterId em csv para considerar o custo. 
5. Standard_DS1_v2 é uma VM vCPU, e a VM é implantada sem o Benefício Híbrido do Azure. Portanto, esse medidor abrange o custo extra de software do Windows. Consulte [Custos de software do Windows para Instâncias de VM de Reserva do Azure.](billing-reserved-instance-windows-software-costs.md) para localizar o medidor correspondente a 1 núcleo de VM, série D. Se for usado o Benefício Híbrido do Azure, esse custo extra não será aplicado. 

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre instâncias reservadas, consulte os seguintes artigos:

- [O que são instâncias de VM reservadas do Azure?](billing-save-compute-costs-reservations.md)
- [Pré-pagamento para máquinas virtuais com instâncias de VMs reservadas do Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Gerenciar instâncias reservadas no Azure](billing-manage-reserved-vm-instance.md)
- [Entenda como o desconto de instância reservada é aplicado](billing-understand-vm-reservation-charges.md)
- [Entenda o uso reservado de instâncias para o seu registro Enterprise ](billing-understand-reserved-instance-usage-ea.md)
- [Custos de software do Windows não incluídos com instâncias reservadas](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
