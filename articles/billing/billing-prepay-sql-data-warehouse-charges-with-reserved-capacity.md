---
title: Pagar antecipadamente por encargos de SQL Data Warehouse com capacidade reservada do Azure | Microsoft Docs
description: Saiba como você pode pagar antecipadamente pelas tarifas do SQL Data Warehouse com capacidade reservada para economizar dinheiro.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 3d56ee99f8056139a234477b6eed93d9fcbe2bbb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60371182"
---
# <a name="prepay-for-sql-data-warehouse-charges-with-reserved-capacity"></a>Pagar antecipadamente por encargos de SQL Data Warehouse com capacidade reservada

Você pode economizar dinheiro com o Azure SQL Data Warehouse por planos pré-pagos para o uso de cDWU por uma duração de um ou três anos. Para adquirir capacidade reservada do SQL Data Warehouse, você precisa escolher o Azure região e termo. Em seguida, adicione o SKU do depósito de dados SQL ao seu carrinho e escolha a quantidade de unidades de cDWU que você deseja comprar.

Quando você compra uma reserva, o SQL Data Warehouse de uso que corresponde aos atributos de reserva não será cobrado com o pagamento medida que vá taxas.

Uma reserva não cobre encargos de rede associados com o uso do SQL Data Warehouse ou de armazenamento.

Quando a capacidade reservada expira, o SQL Data Warehouse instâncias continuam em execução, mas são cobradas à taxa pagamento medida que vá. As reservas não renovam automaticamente.

Para obter informações sobre preços, consulte o [SQL Data Warehouse reservado oferta de capacidade](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/).

Você pode comprar capacidade reservada do Azure SQL Data Warehouse na [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade). Para comprar uma capacidade reservada:

- Você deve ter a função de proprietário para pelo menos uma enterprise ou assinatura pré-paga.
- Para assinaturas do Enterprise, o **adicionar instâncias reservadas** opção deve ser habilitada no [portal EA](https://ea.azure.com/). Se a configuração estiver desabilitada, você deve ser um administrador de EA.
- Para o programa de provedor de solução de nuvem (CSP), somente os agentes administradores ou agentes de vendas podem comprar capacidade reservada do SQL Data Warehouse.

Para obter mais informações sobre como os clientes empresariais e clientes com assinaturas pré-pagas são cobrados para compras de reserva, consulte [entender o uso de reserva do Azure para seu registro Enterprise](billing-understand-reserved-instance-usage-ea.md) e [entender o do Azure uso de reserva para a sua assinatura paga conforme o uso](billing-understand-reserved-instance-usage.md).

## <a name="choose-the-right-size-before-purchase"></a>Escolha o tamanho correto antes da compra

O tamanho de reserva deve ser baseado no total do SQL Data Warehouse de computação unidades de data warehouse (cDWU) que você consumir. As compras são feitas em incrementos de 100 cDWU.

Por exemplo, suponha que seu consumo total do SQL Data Warehouse é DW3000c. Você deseja adquirir capacidade reservada para todos eles. Portanto, você deve comprar 30 unidades de capacidade de cDWU reservado.

## <a name="buy-sql-data-warehouse-reserved-capacity"></a>Comprar capacidade reservada do SQL Data Warehouse

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Selecione **Todos os serviços** > **Reservas**.
3. Selecione uma assinatura. Use a lista de assinaturas para escolher a assinatura que é usada para pagar pela capacidade reservada. O método de pagamento da assinatura é cobrado os custos iniciais para a capacidade reservada. O tipo de assinatura deve ser um contrato empresarial (números da oferta: MS-AZR-0017P ou MS-AZR-0148P) ou Pagamento Conforme o Uso (números da oferta: MS-AZR-0003P ou MS-AZR-0023P).
  - Para uma assinatura de empresa, os encargos são deduzidos do saldo do compromisso monetário do registro ou cobrados como média.
  - Para a assinatura de Pagamento Conforme o Uso, as cobranças são feitas ao cartão de crédito ou à forma de pagamento de faturas na assinatura.
4. Selecione um escopo. Use a lista de escopo para escolher um escopo de assinatura.
  - Com o **único** opção, o desconto de reserva é aplicada ao SQL Data Warehouse implantadas na assinatura selecionada.
  - Com o **compartilhado** opção, o desconto de reserva é aplicada às instâncias em execução em todas as assinaturas dentro de seu contexto de cobrança.
    - Para clientes empresariais, o contexto de cobrança é o registro de EA.
    - Para clientes de Pagamento Conforme o Uso, o escopo compartilhado consiste em todas as assinaturas de Pagamento Conforme o Uso criadas pelo administrador da conta.
5. Selecione uma região para escolher uma região do Azure que é coberta pela capacidade reservada.
6. Escolha uma quantidade. Insira a quantidade de 100 unidades de Data Warehouse (cDWU) que você deseja comprar.    
  Por exemplo, uma quantidade de 30 resultará em 3.000 cDWU de capacidade reservada a cada hora.
7. Examine o SQL Data Warehouse reservado custo de reserva de capacidade na **custos** seção.
8. Selecione **Comprar**.
9. Selecione **exibir esta reserva** para ver seu status de compra.

## <a name="cancellations-and-exchanges"></a>Cancelamentos e trocas

Se você precisar cancelar o SQL Data Warehouse capacidade reservada, pode haver uma taxa de rescisão antecipada de 12%. Os reembolsos baseiam-se no preço de compra ou no preço atual da reserva, o que for mais baixo. Reembolsos são limitados a US $50,000.00 por ano. O reembolso, que você recebe é o balanço proporcional restante, menos a taxa de rescisão antecipada de 12%. Para solicitar um cancelamento, vá até a reserva no portal do Azure e selecione **Reembolso** para criar uma solicitação de suporte.

Se você precisar alterar a capacidade do seu SQL Data Warehouse está reservado para outra região ou termo, você pode trocá-lo para outro reserva de valor igual ou maior. A data de início do prazo da nova reserva não é a mesma da reserva trocada. Aquele ou termo de três anos é iniciado quando você cria a nova reserva. Para solicitar uma troca, abra a reserva no portal do Azure e selecione **Exchange** para criar uma solicitação de suporte.

Para obter mais informações sobre como as reservas do exchange ou o reembolso, consulte [trocas de reserva e os reembolsos](billing-azure-reservations-self-service-exchange-and-refund.md).

O desconto de reserva é aplicado automaticamente ao número de instâncias do SQL Data Warehouse que correspondem ao escopo de capacidade reservada do SQL Data Warehouse e região. Você pode atualizar o escopo da capacidade reservada do SQL Data Warehouse com o [portal do Azure](https://portal.azure.com/), PowerShell, CLI ou por meio da API.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/).

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como os descontos de reserva se aplicam ao Azure SQL Data Warehouse, consulte [como descontos de reserva se aplicam ao Azure SQL Data Warehouse](billing-prepay-sql-data-warehouse-charges-with-reserved-capacity.md).

- Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:
  - [O que são Reservas do Azure?](billing-save-compute-costs-reservations.md)
  - [Gerenciar Reservas do Azure](billing-manage-reserved-vm-instance.md)
  - [Compreender o desconto de Reservas do Azure](billing-understand-reservation-charges.md)
  - [Entender o uso de reserva para a sua assinatura paga conforme o uso](billing-understand-reserved-instance-usage.md)
  - [Entender o uso de reserva para seu registro de empresa](billing-understand-reserved-instance-usage-ea.md)
