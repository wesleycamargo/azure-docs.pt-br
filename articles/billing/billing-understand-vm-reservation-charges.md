---
title: "Entender a aplicação de desconto em Instâncias Reservadas de Máquina Virtual do Azure | Microsoft Docs"
description: "Saiba como o desconto em Instâncias Reservadas de VM do Azure é aplicado às VMs em execução."
services: billing
documentationcenter: 
author: vikramdesai01
manager: vikdesai
editor: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/10/2017
ms.author: vikdesai
ms.openlocfilehash: 3fd12bd3c51eeef57c896da030a83e447dc3e8ff
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2017
---
# <a name="understand-how-the-reserved-virtual-machine-instance-discount-is-applied"></a>Entender como é aplicado o desconto em Instâncias Reservadas de Máquina Virtual
Após você adquirir uma Instância Reservada de VM, o desconto de reserva é aplicado automaticamente às máquinas virtuais correspondentes aos atributos e à quantidade da reserva. Uma reserva abrange os custos de infraestrutura das suas máquinas virtuais. A tabela a seguir ilustra os custos para sua máquina virtual após a compra de uma reserva. Em todos os casos, você é cobrado pelo uso de armazenamento e rede com as taxas normais.

| Tipo de Máquina Virtual  | Encargos de reserva |    
|-----------------------|--------------------------------------------| 
|VMs Linux sem software adicional | A reserva abrange os custos de infraestrutura da sua VM.|
|VMs Linux com encargos de software (por exemplo, Red Hat) | A reserva abrange os custos de infraestrutura. Você é cobrado por software adicional.|
|VMs Windows sem software adicional |A reserva abrange os custos de infraestrutura. Você é cobrado por software Windows.|
|VMs Windows com software adicional (por exemplo, SQL Server) | A reserva abrange os custos de infraestrutura. Você é cobrado para software Windows e por software adicional.|
|VMs Windows com [Benefício Híbrido do Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | A reserva abrange os custos de infraestrutura. Os custos de software Windows são cobertos pelo Benefício Híbrido do Azure. Qualquer software adicional é cobrado separadamente.| 

## <a name="application-of-reservation-discount-to-non-windows-vms"></a>Aplicação do desconto de reserva a VMs não - Windows
 O desconto de reserva é aplicado às instâncias de VM execução, por hora. Para a aplicação do desconto de reserva, as reservas compradas são comparadas ao uso emitido pelas VMs em execução. O gráfico a seguir ilustra a aplicação de uma reserva ao uso faturável de VM. A ilustração baseia-se na compra de uma reserva e duas instâncias de VM correspondentes.

![Aplicação de Instância Reservada de VM](media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1.  Qualquer uso acima da linha de Instância Reservada de VM é cobrado com as taxas regulares de pagamento pelo uso. Não há cobrança por qualquer uso abaixo da linha de Instâncias Reservadas de VM, pois ele já foi pago como parte da compra de reserva.
2.  Na hora 1, a instância 1 é executada por 0,75 hora e a instância 2, por 0,5 hora. O uso total relativo à hora 1 é de 1,25 hora. São cobradas as taxas de pagamento pelo uso da 0,25 hora restante.
3.  Nas horas 2 e 3, ambas as instâncias foram executadas por 1 hora. Uma instância está coberta pela reserva e a outro é cobrada com as taxas de pagamento pelo uso.
4.  Na hora 4, a instância 1 é executada por 0,5 hora e a instância 2, por 1 hora. A instância 1 está totalmente coberta pela reserva, e também 0,5 hora da instância 2. São cobradas as taxas de pagamento pelo uso da 0,5 hora restante.

Para entender e exibir a aplicação das suas reservas nos relatórios de uso de cobrança, consulte [Entender o uso da Instância Reservada de VM](https://go.microsoft.com/fwlink/?linkid=862757).

## <a name="application-of-reservation-discount-to-windows-vms"></a>Aplicação do desconto de reserva a VMs Windows
Quando você executa instâncias de VM Windows, a reserva é aplicada para cobrir os custos de infraestrutura. A aplicação da reserva aos custos de infraestrutura de VMs é igual para máquinas virtuais Windows e não - Windows. Você é cobrado separadamente pelo software Windows por vCPU. Consulte [Custos do software Windows com reservas](https://go.microsoft.com/fwlink/?linkid=862756). É possível cobrir seu custos de licenciamento com [Benefício Híbrido do Azure para Windows Server] (https://docs.microsoft.com/en-us/azure/virtual-machines/windows/hybrid-use-benefit-licensing).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se precisar de ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
