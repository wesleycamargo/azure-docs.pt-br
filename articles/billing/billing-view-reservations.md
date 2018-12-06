---
title: Exibir as Reservas do Azure | Microsoft Docs
description: Saiba como exibir as Reservas do Azure no portal do Azure.
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
ms.date: 10/03/2018
ms.author: cwatson
ms.openlocfilehash: 4506d65d173ea483558a772d89fe7cbc8cd84944
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52584580"
---
# <a name="view-azure-reservations-in-the-azure-portal"></a>Exibir Reservas do Azure no portal do Azure

Dependendo do tipo de assinatura e permissões, há algumas maneiras de exibir Reservas do Azure.

## <a name="view-reservations-as-owner-or-reader"></a>Exibir Reservas como Proprietário ou Leitor

Por padrão, quando você compra uma reserva, você e o administrador da conta podem exibir a reserva. Você e o administrador da conta obtém automaticamente a função de proprietário na reserva. Para permitir que outras pessoas exibam a reserva, você deve adicioná-las como **Proprietário** ou **Leitor** na reserva. Para obter mais informações, consulte [Adicionar ou alterar os usuários que podem gerenciar uma reserva](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
 
Para exibir uma reserva como Proprietário ou Leitor,

1. Entre no [Portal do Azure]( http://portal.azure.com).
1. Pesquise em **Reservas**.

    ![Captura de tela que mostra a pesquisa do portal do Azure](./media/billing-view-reservation/portal-reservation-search.png)

1. Você verá uma lista das reservas de onde você tem a função Proprietário ou Leitor.

Se você precisar alterar o escopo de uma reserva, dividir uma reserva ou alterar quem pode gerenciar uma reserva, consulte [Gerenciar as Reservas do Azure](billing-manage-reserved-vm-instance.md).

## <a name="view-reservation-transactions-for-enterprise-enrollments"></a>Exibir transações de reserva para registros do Enterprise

 Se você tiver um registro de Enterprise liderado pelo parceiro, exiba reservas acessando **Relatórios** no portal do EA. Para outros registros do Enterprise, você pode exibir as reservas no portal do EA e no portal do Azure. Você deve ser um administrador EA para exibir transações de reserva.

Exibir transações de reserva no portal do Azure,

1. Entre no [Portal do Azure]( http://portal.azure.com).
1. Pesquise **Gerenciamento de Custos do Azure + Cobrança**.

    ![Captura de tela que mostra a pesquisa do portal do Azure](./media/billing-view-reservation/portal-cm-billing-search.png)

1. Selecione **Transações de reserva**.
1. Para filtrar os resultados, selecione **Timespan**, **Tipo**, ou **Descrição**.
1. Escolha **Aplicar**.

    ![Captura de tela que mostra os resultados de transações de Reserva](./media/billing-view-reservation/portal-billing-reservation-transaction-results.png)

Para obter os dados usando uma API, consulte [Obter Cobranças de Transação de Instância Reservada para clientes corporativos](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:

- [O que são Reservas do Azure?](billing-save-compute-costs-reservations.md)
- [Pagar antecipadamente por capacidade reservada do Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md)
- [Pagar antecipadamente por recursos de computação de banco de dados SQL com capacidade reservada do Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Pré-pagamento para máquinas virtuais com instâncias de VMs reservadas do Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Gerenciar Reservas do Azure](billing-manage-reserved-vm-instance.md)
- [Entender o uso de reserva para a sua assinatura paga conforme o uso](billing-understand-reserved-instance-usage.md)
- [Entenda o uso de reservas para o seu registro Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Entender o uso de reserva para assinaturas de CSP](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de Ajuda, [criar uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
