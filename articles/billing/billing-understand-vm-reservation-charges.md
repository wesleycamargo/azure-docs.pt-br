---
title: Entenda o desconto de Instâncias de VM Reservadas do Azure | Microsoft Docs
description: Saiba como o desconto de Instância de VM Reservada do Azure é aplicado às máquinas virtuais em execução.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: b112dd881d4b2e87e617111d00bc82c6151d7750
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60011190"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>Como o desconto de reserva do Azure é aplicado às máquinas virtuais

Após uma Instância Reservada de Máquina Virtual do Azure, o desconto de reserva é aplicado automaticamente às máquinas virtuais correspondentes aos atributos e à quantidade da reserva. Uma reserva abrange os custos de infraestrutura das suas máquinas virtuais.

Um desconto de reserva se aplica às VMs base que você compra do Azure Marketplace.

Para a capacidade reservada do Banco de Dados SQL do Microsoft Azure, consulte [Entenda o desconto de Instâncias Reservadas do Azure](billing-understand-reservation-charges.md).

A tabela a seguir ilustra os custos da máquina virtual após a compra de uma instância reservada de máquina virtual. Em todos os casos, você é cobrado pelo uso de armazenamento e rede com as taxas normais.

| Tipo de Máquina Virtual  | Encargos com a Instância Reservada de VM |
|-----------------------|--------------------------------------------|
|VMs Linux sem software adicional | A reserva abrange os custos de infraestrutura da sua VM.|
|VMs Linux com encargos de software (por exemplo, Red Hat) | A reserva abrange os custos de infraestrutura. Você é cobrado por software adicional.|
|VMs Windows sem software adicional |A reserva abrange os custos de infraestrutura. Você é cobrado por software do Windows.|
|VMs Windows com software adicional (por exemplo, SQL Server) | A reserva abrange os custos de infraestrutura. Você é cobrado para software Windows e por software adicional.|
|VMs Windows com [Benefício Híbrido do Azure](../virtual-machines/windows/hybrid-use-benefit-licensing.md) | A reserva abrange os custos de infraestrutura. Os custos de software Windows são cobertos pelo Benefício Híbrido do Azure. Qualquer software adicional é cobrado separadamente.|

## <a name="how-reservation-discount-is-applied"></a>Como o desconto de reserva é aplicado

Um desconto de reserva é "*uso-it-ou-perder-it*". Dessa forma, se você não tiver recursos correspondentes para qualquer hora, em seguida, você perderá uma quantidade de reserva para essa hora. Você não pode transportar encaminhar horas reservadas não utilizadas.

Quando você desligar um recurso, o desconto de reserva se aplica automaticamente a outro recurso correspondente no escopo especificado. Se nenhum recurso de correspondência é encontrado no escopo especificado, são as horas reservadas *perdido*.

## <a name="reservation-discount-for-non-windows-vms"></a>Desconto de reserva para VMs não - Windows

 O desconto de reserva do Azure é aplicado às instâncias de VM execução, por hora. Para a aplicação do desconto de reserva, as reservas compradas são comparadas ao uso emitido pelas VMs em execução. Para VMs que não podem ser executadas por uma hora completa, a reserva será preenchida com outras VMs que não estejam usando uma reserva, incluindo VMs em execução simultânea. Ao final da hora, o aplicativo de reserva para VMs na hora é bloqueado. No caso de uma máquina virtual não ser executada por uma hora ou VMs simultâneas em uma hora não preencherem a hora de reserva, a reserva é subutilizada nessa hora. O gráfico a seguir ilustra a aplicação de uma reserva ao uso faturável de VM. A ilustração baseia-se na compra de uma reserva e duas instâncias de VM correspondentes.

![Captura de tela de uma reserva aplicada e duas instâncias de VM correspondentes](./media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1. Qualquer uso acima da linha de reserva é cobrado com as taxas pré-pagas regulares. Não há cobrança por qualquer uso abaixo das reservas, pois ele já foi pago como parte da compra de reserva.
2. Na hora 1, a instância 1 é executada por 0,75 hora e a instância 2, por 0,5 hora. O uso total relativo à hora 1 é de 1,25 hora. São cobradas as taxas de pagamento pelo uso da 0,25 hora restante.
3. Nas horas 2 e 3, ambas as instâncias foram executadas por 1 hora. Uma instância está coberta pela reserva e a outro é cobrada com as taxas de pagamento pelo uso.
4. Na hora 4, a instância 1 é executada por 0,5 hora e a instância 2, por 1 hora. A instância 1 está totalmente coberta pela reserva, e também 0,5 hora da instância 2. São cobradas as taxas de pagamento pelo uso da 0,5 hora restante.

Para entender e exibir o aplicativo das Reservas do Azure nos relatórios de uso de cobrança, consulte [Entender o uso de reserva](billing-understand-reserved-instance-usage-ea.md).

## <a name="reservation-discount-for-windows-vms"></a>Desconto de reserva para VMs do Windows

Quando você executa instâncias de VM Windows, a reserva é aplicada para cobrir os custos de infraestrutura. A aplicação da reserva aos custos de infraestrutura de VMs é igual para máquinas virtuais Windows e não - Windows. Você é cobrado separadamente pelo software Windows por vCPU. Consulte [Custos do software Windows com Reservas](billing-reserved-Instance-windows-software-costs.md). Você pode cobrir os custos de licenciamento do Windows com [Benefício Híbrido do Azure para Windows Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

## <a name="discount-can-apply-to-different-sizes"></a>Desconto pode aplicar a tamanhos diferentes

Ao comprar uma instância de VM Reservada, se você selecionar **Otimizado para**: **flexibilidade de tamanho da instância**, a cobertura de desconto dependerá do tamanho de VM selecionado. A reserva pode ser aplicada aos tamanhos de VMs (máquinas virtuais) no mesmo grupo de série de tamanho. Para obter mais informações, confira [Flexibilidade de tamanho de máquina virtual com Instâncias de VM Reservadas](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

## <a name="discount-applies-to-matching-servicetype-only"></a>Desconto aplica-se a correspondência apenas de ServiceType

Um desconto de reserva se aplica somente ao uso de VM em que o `ServiceType` valor em `AdditionalInfo` corresponde a reserva adquirida. Aplicativo de desconto de reserva ignora o medidor usado para as VMs e avalia apenas `ServiceType`. Sabe qual serviço de tipo que você adquiriu a VM para. Você pode trocar um reserva de VM de uma reserva de armazenamento premium ou da maneira oposta de armazenamento não premium.

## <a name="classic-vms-and-cloud-services"></a>Serviços de nuvem e VMs clássicas

Instâncias de VM reservada se aplicam a ambas as VMs clássicas e serviços de nuvem quando a flexibilidade de tamanho de instância é habilitada automaticamente. Para serviços de nuvem, o desconto de reserva se aplica apenas ao custo de computação. Quando o desconto de reserva é aplicado aos serviços de nuvem, os encargos de uso estão divididos em encargos de computação (medidor de Linux) e encargos (medidor de gerenciamento de serviços de nuvem) dos serviços de nuvem. Para obter mais informações, consulte [como o desconto de reserva se aplica aos serviços de nuvem](billing-reserved-instance-windows-software-costs.md#cloud-services-software-meters-not-included-in-reservation-cost).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Se você tiver dúvidas ou precisar de Ajuda, [criar uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:

- [Quais são as reservas para o Azure?](billing-save-compute-costs-reservations.md)
- [Pré-pagamento para máquinas virtuais com instâncias de VMs reservadas do Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Pagar antecipadamente por recursos de computação de banco de dados SQL com capacidade reservada do Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Gerenciar reservas para o Azure](billing-manage-reserved-vm-instance.md)
- [Entender o uso de reserva para a sua assinatura paga conforme o uso](billing-understand-reserved-instance-usage.md)
- [Entenda o uso de reservas para o seu registro Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [ Entenda o uso da reserva para assinaturas do CSP](/partner-center/azure-reservations)
- [Os custos de software do Windows não incluídos com instâncias reservadas](billing-reserved-instance-windows-software-costs.md)
