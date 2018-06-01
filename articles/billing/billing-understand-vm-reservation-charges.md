---
title: Entender o desconto de Instâncias Reservadas do Azure – cobrança do Azure | Microsoft Docs
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
ms.openlocfilehash: a0800bafc3d6b858387e28a3b75bc7b3a6bfe6e8
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301406"
---
# <a name="understand-how-the-reserved-instance-discount-is-applied"></a>Entender como é aplicado o desconto de Instância Reservada
Depois que você compra uma Instância de VM Reservada do Azure, o desconto de Instância Reservada do Azure é aplicado automaticamente às máquinas virtuais correspondentes aos atributos e à quantidade da Instância Reservada. Uma Instância Reservada cobre os custos de infraestrutura das máquinas virtuais. A tabela a seguir ilustra os custos da máquina virtual após a compra de uma Instância Reservada. Em todos os casos, você é cobrado pelo uso de armazenamento e rede com as taxas normais.

| Tipo de Máquina Virtual  | Encargos com a Instância Reservada |    
|-----------------------|--------------------------------------------|
|VMs Linux sem software adicional | A Instância Reservada cobre os custos de infraestrutura da VM.|
|VMs Linux com encargos de software (por exemplo, Red Hat) | A Instância Reservada cobre os custos de infraestrutura. Você é cobrado por software adicional.|
|VMs Windows sem software adicional |A Instância Reservada cobre os custos de infraestrutura. Você é cobrado por software Windows.|
|VMs Windows com software adicional (por exemplo, SQL Server) | A Instância Reservada cobre os custos de infraestrutura. Você é cobrado para software Windows e por software adicional.|
|VMs Windows com [Benefício Híbrido do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | A Instância Reservada cobre os custos de infraestrutura. Os custos de software Windows são cobertos pelo Benefício Híbrido do Azure. Qualquer software adicional é cobrado separadamente.| 

## <a name="application-of-reserved-instance-discount-to-non-windows-vms"></a>Aplicação de desconto de Instância Reservada a VMs que não são do Windows
 O desconto de Instância Reservada é aplicado às instâncias de VM em execução, por hora. As Instâncias Reservadas adquiridas são comparadas com o uso emitido pelas VMs em execução, para aplicação do desconto de Instância Reservada. Para VMs que podem não ser executadas por uma hora completa, a Instância Reservada será preenchida com outras VMs que não estejam usando uma Instância Reservada, incluindo VMs em execução simultânea. Ao final da hora, o aplicativo de Instância Reservada para VMs na hora é bloqueado. No caso de uma VM não ser executada por uma hora ou as VMs simultâneas na hora não preencherem a hora da Instância Reservada, a Instância Reservada será subutilizada nessa hora. O gráfico a seguir ilustra a aplicação de uma Instância Reservada ao uso faturável de VM. A ilustração baseia-se na compra de uma Instância Reservada e duas instâncias de VM correspondentes.

![Captura de tela de uma Instância Reservada aplicada e duas instâncias de VM correspondentes](media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1.  Qualquer uso acima da linha da Instância Reservada é cobrado com base nas taxas regulares pagas conforme o uso. Não há cobrança por qualquer uso abaixo da linha das Instâncias Reservadas, pois ele já foi pago como parte da compra de Instância Reservada.
2.  Na hora 1, a instância 1 é executada por 0,75 hora e a instância 2, por 0,5 hora. O uso total relativo à hora 1 é de 1,25 hora. São cobradas as taxas de pagamento pelo uso da 0,25 hora restante.
3.  Nas horas 2 e 3, ambas as instâncias foram executadas por 1 hora. Uma instância é coberta pela Instância Reservada e a outra é cobrada com base nas taxas pagas conforme o uso.
4.  Na hora 4, a instância 1 é executada por 0,5 hora e a instância 2, por 1 hora. A instância 1 é totalmente coberta pela Instância Reservada, bem como 0,5 hora da instância 2. São cobradas as taxas de pagamento pelo uso da 0,5 hora restante.

Para entender e exibir a aplicação de suas Instâncias Reservadas nos relatórios de uso de cobrança, confira [Entender o Uso da Instância Reservada](https://go.microsoft.com/fwlink/?linkid=862757).

## <a name="application-of-reserved-instance-discount-to-windows-vms"></a>Aplicação do desconto de Instância Reservada a VMs do Windows
Quando você executa instâncias de VM do Windows, a Instância Reservada é aplicada para cobrir os custos de infraestrutura. A aplicação da Instância Reservada aos custos de infraestrutura de VM é igual para VMs do Windows e VMs que não são do Windows. Você é cobrado separadamente pelo software Windows por vCPU. Confira [Custos do software Windows com Instâncias Reservadas](https://go.microsoft.com/fwlink/?linkid=862756). Você pode cobrir os custos de licenciamento do Windows com [Benefício Híbrido do Azure para Windows Server] (https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing).

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as Instâncias Reservadas, confira os seguintes artigos:

- [Economizar dinheiro em máquinas virtuais com as Instâncias Reservadas do Azure](billing-save-compute-costs-reservations.md)
- [Pagar antecipadamente por Máquinas Virtuais com Instâncias Reservadas](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Gerenciar as Instâncias Reservadas](billing-manage-reserved-vm-instance.md)
- [Entender como é aplicado o desconto de Instância Reservada](billing-understand-vm-reservation-charges.md)
- [Entender o uso da Instância Reservada na sua assinatura Paga pelo Uso](billing-understand-reserved-instance-usage.md)
- [Entender o uso da Instância Reservada no seu registro de Empresa](billing-understand-reserved-instance-usage-ea.md)
- [ Entenda o uso da Instância Reservada para assinaturas do CSP ](https://docs.microsoft.com/partner-center/azure-reservations)
- [Os custos de software do Windows não estão incluídos nas Instâncias Reservadas](billing-reserved-instance-windows-software-costs.md)


## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
