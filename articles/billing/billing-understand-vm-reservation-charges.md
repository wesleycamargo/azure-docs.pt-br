---
title: Entenda o desconto de instâncias reservadas do Azure | Microsoft Docs
description: Saiba como o desconto de Instância de VM Reservada do Azure é aplicado às máquinas virtuais em execução.
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
ms.date: 05/09/2018
ms.author: yashar
ms.openlocfilehash: d1229a49a5bb3bf3198c91a748ed37b7a626c506
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064187"
---
# <a name="understand-how-the-reserved-instance-discount-is-applied"></a>Entender como é aplicado o desconto na Instância Reservada
Depois de comprar uma Instância de VM Reservada do Azure, o desconto de instância reservada é aplicado automaticamente às máquinas virtuais que correspondem aos atributos e à quantidade da instância reservada. Uma Instância Reservada cobre os custos de infraestrutura das máquinas virtuais. A tabela a seguir ilustra os custos da máquina virtual após a compra de uma Instância Reservada. Em todos os casos, você é cobrado pelo uso de armazenamento e rede com as taxas normais.

| Tipo de Máquina Virtual  | Encargos com a Instância Reservada |    
|-----------------------|--------------------------------------------|
|VMs Linux sem software adicional | A Instância Reservada cobre os custos de infraestrutura da VM.|
|VMs Linux com encargos de software (por exemplo, Red Hat) | A Instância Reservada cobre os custos de infraestrutura. Você é cobrado por software adicional.|
|VMs Windows sem software adicional |A Instância Reservada cobre os custos de infraestrutura. Você é cobrado por software Windows.|
|VMs Windows com software adicional (por exemplo, SQL Server) | A Instância Reservada cobre os custos de infraestrutura. Você é cobrado para software Windows e por software adicional.|
|VMs Windows com [Benefício Híbrido do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | A Instância Reservada cobre os custos de infraestrutura. Os custos de software Windows são cobertos pelo Benefício Híbrido do Azure. Qualquer software adicional é cobrado separadamente.| 

## <a name="application-of-reserved-instance-discount-to-non-windows-vms"></a>Aplicativo de desconto de instância reservada para VMs não Windows
 O desconto da Instância Reservada do Azure é aplicado às instâncias de VM em execução por hora. As Instâncias Reservadas adquiridas são comparadas com o uso emitido pelas VMs em execução, para aplicação do desconto de Instância Reservada. Para VMs que podem não ser executadas por uma hora completa, a Instância Reservada será preenchida com outras VMs que não estejam usando uma Instância Reservada, incluindo VMs em execução simultânea. Ao final da hora, o aplicativo de Instância Reservada para VMs na hora é bloqueado. No caso de uma VM não ser executada por uma hora ou VMs simultâneas dentro da hora, não preencha a hora da instância reservada, a instância reservada será subutilizada para essa hora. O gráfico a seguir ilustra a aplicação de uma Instância Reservada ao uso faturável de VM. A ilustração baseia-se na compra de uma Instância Reservada e duas instâncias de VM correspondentes.

![Captura de tela de uma Instância Reservada aplicada e duas instâncias de VM correspondentes](media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1.  Qualquer uso acima da linha da Instância Reservada é cobrado com base nas taxas regulares pagas conforme o uso. Você não é cobrado por qualquer uso abaixo da linha de instâncias reservadas, uma vez que já foi pago como parte da compra de instância reservada.
2.  Na hora 1, a instância 1 é executada por 0,75 hora e a instância 2, por 0,5 hora. O uso total relativo à hora 1 é de 1,25 hora. São cobradas as taxas de pagamento pelo uso da 0,25 hora restante.
3.  Nas horas 2 e 3, ambas as instâncias foram executadas por 1 hora. Uma instância é coberta pela instância reservada e a outra é cobrada em taxas de pagamento conforme o uso.
4.  Na hora 4, a instância 1 é executada por 0,5 hora e a instância 2, por 1 hora. A instância 1 é totalmente coberta pela instância reservada e 0,5 hora da instância 2 é coberta. São cobradas as taxas de pagamento pelo uso da 0,5 hora restante.

Para entender e visualizar o aplicativo de instâncias reservadas do Azure nos relatórios de uso de faturamento, consulte [ Entendendo o uso de Instância Reservada ](https://go.microsoft.com/fwlink/?linkid=862757).

## <a name="application-of-reserved-instance-discount-to-windows-vms"></a>Aplicação do desconto de Instância Reservada a VMs do Windows
Quando você executa instâncias de VM do Windows, a Instância Reservada é aplicada para cobrir os custos de infraestrutura. A aplicação da Instância Reservada aos custos de infraestrutura de VM é igual para VMs do Windows e VMs que não são do Windows. Você é cobrado separadamente pelo software Windows por vCPU. Veja [ custos de software do Windows com instâncias reservadas ](https://go.microsoft.com/fwlink/?linkid=862756). Você pode cobrir os custos de licenciamento do Windows com [Benefício Híbrido do Azure para Windows Server] (https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing).

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as Instâncias Reservadas, confira os seguintes artigos:

- [O que são instâncias de VM reservadas do Azure?](billing-save-compute-costs-reservations.md)
- [Pré-pagamento para máquinas virtuais com instâncias de VMs reservadas do Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Gerenciar instâncias reservadas no Azure](billing-manage-reserved-vm-instance.md)
- [Entenda como o desconto de instância reservada é aplicado](billing-understand-vm-reservation-charges.md)
- [Entenda o uso de instâncias reservadas para sua assinatura do Pay-As-You-Go](billing-understand-reserved-instance-usage.md)
- [Entenda o uso reservado de instâncias para o seu registro Enterprise ](billing-understand-reserved-instance-usage-ea.md)
- [ Entenda o uso da Instância Reservada para assinaturas do CSP ](https://docs.microsoft.com/partner-center/azure-reservations)
- [Custos de software do Windows não incluídos com instâncias reservadas](billing-reserved-instance-windows-software-costs.md)


## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
