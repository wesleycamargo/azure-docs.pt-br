---
title: Pré-pagamento para banco de dados SQL do Azure vCores para economizar dinheiro | Microsoft Docs
description: Aprenda a comprar a capacidade reservada do Banco de Dados SQL do Azure para economizar nos custos de seu computador.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 04/26/2019
ms.openlocfilehash: 74a52df4eab8a3e471e541889cde74846af00fb8
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64574200"
---
# <a name="prepay-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>Pré-pagamento para recursos de computação do Banco de Dados SQL com capacidade reservada do Banco de Dados SQL do Azure

Economize dinheiro com o Banco de Dados SQL do Azure pré-pagando pelos recursos de computação em comparação com os preços de pagamento conforme o uso. Com a capacidade reservada do Banco de Dados SQL do Azure, você assume um compromisso inicial com o Banco de Dados SQL por um período de um ou três anos para obter um desconto significativo nos custos de computação. Para comprar a capacidade reservada do Banco de Dados SQL, é necessário especificar a região do Azure, o tipo de implantação, o nível de desempenho e o prazo.


Você não precisa atribuir a reserva a instâncias específicas do Banco de Dados SQL (bancos de dados individuais, pools elásticos ou instâncias gerenciadas). As instâncias de banco de dados SQL correspondentes, que já estão em execução ou recém-implantadas, receberão automaticamente o benefício. Ao comprar uma reserva, você está pagando antecipadamente os custos de computação por um período de um ou três anos. Assim que você compra uma reserva, as cobranças de computação do Banco de Dados SQL que correspondem aos atributos de reserva não são mais cobradas nas taxas de pagamento conforme o uso. Uma reserva não cobre custos de software, rede ou armazenamento associados à instância do banco de dados SQL. No final do período de reserva, o benefício de cobrança expira e os Bancos de Dados SQL são cobrados pelo preço de pré-pagamento. As reservas não são renovadas automaticamente. Para obter informações sobre preços, consulte a [oferta de capacidade reservada do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).

Você pode comprar capacidade reservada do Banco de Dados SQL do Azure na [portal do Azure](https://portal.azure.com). Capacidade reservada comprar o banco de dados SQL:

- Você deve ser na função de proprietário para pelo menos uma assinatura Enterprise ou paga conforme o uso.
- Para as assinaturas Enterprise, a opção **Adicionar Instâncias Reservadas** deve estar habilitada no [Portal EA](https://ea.azure.com). Ou, se essa configuração estiver desabilitada, você deve ser um administrador de EA na assinatura.
- Para o programa Provedor de Solução de Nuvem (CSP), apenas os agentes admin ou agentes de vendas podem adquirir a capacidade reservada do Banco de Dados SQL.

Para saber detalhes sobre como clientes empresariais e clientes do método de pagamento conforme o uso são cobrados para compras de reserva, confira [Entenda o uso de reserva do Azure para seu registro Enterprise](../billing/billing-understand-reserved-instance-usage-ea.md) e [Entenda o uso de reserva do Azure para sua assinatura paga conforme o uso](../billing/billing-understand-reserved-instance-usage.md).

## <a name="determine-the-right-sql-size-before-purchase"></a>Determinar o tamanho correto do SQL antes da compra

O tamanho da reserva deve ser baseado na quantidade total de computação usada pelos bancos de dados existentes ou prestes a serem implantados e/ou pools elásticos ou instâncias gerenciadas dentro de uma região específica e usando o mesmo nível de desempenho e geração de hardware.

Por exemplo, vamos supor que você esteja executando uma finalidade geral, o pool elástico Gen5 - 16 vCore e dois bancos de dados únicos Gen5 - 4 vCore essenciais para os negócios. Além disso, suponhamos que você planeje implantar, no próximo mês, uma finalidade geral adicional, o pool elástico Gen5 - 16 vCore e um pool elástico de v5 core Gen5 - 32 para negócios críticos. Além disso, vamos supor que você saiba que precisará desses recursos por pelo menos 1 ano. Neste caso você deve comprar uma reserva de um ano de 32 (2x16) vCores para banco de dados individual/pool elástico de uso geral – Gen5 e uma reserva de um ano de 40 (2x4 + 32) vCores para banco de dados individual/pool elástico comercialmente crítico – Gen5.

## <a name="buy-sql-database-reserved-capacity"></a>Comprar capacidade reservada do banco de dados do SQL

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** > **Reservas**.
3. Selecione **Add** e, em seguida, no painel Selecionar tipo de produto, selecione **banco de dados SQL** para comprar uma nova reserva do banco de dados SQL.
4. Preencha os campos obrigatórios. Bancos de dados individuais, pools elásticos ou instâncias gerenciadas novos ou existentes que correspondem aos atributos que você selecionou se qualificam para obter o desconto de capacidade reserva. O número real de suas instâncias do Banco de Dados SQL que recebem o desconto depende do escopo e da quantidade selecionada.

   ![Captura de tela antes de enviar a compra de capacidade reservada do Banco de Dados SQL](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

    | Campo      | DESCRIÇÃO|
    |:------------|:--------------|
    |NOME        |O nome dessa reserva.|
    |Assinatura|A assinatura usada para pagar pela reserva de capacidade reservada do Banco de Dados SQL. O método de pagamento na assinatura é cobrado pelos custos iniciais da reserva de capacidade reservada do Banco de Dados SQL. O tipo de assinatura deve ser um contrato empresarial (números da oferta: MS-AZR-0017P ou MS-AZR-0148P) ou Pagamento Conforme o Uso (números da oferta: MS-AZR-0003P ou MS-AZR-0023P). Para uma assinatura de empresa, os encargos são deduzidos do saldo do compromisso monetário do registro ou cobrados como média. Para a assinatura de Pagamento Conforme o Uso, as cobranças são feitas ao cartão de crédito ou à forma de pagamento de faturas na assinatura.|
    |Escopo       |O escopo da reserva vCore pode cobrir uma assinatura ou várias assinaturas (escopo compartilhado). Se você selecionar: <br/><br/>**Assinatura única** – o desconto de reserva vCore é aplicado às instâncias do Banco de Dados SQL nessa assinatura. <br/><br/>**Assinatura compartilhada** – o desconto de reserva de vCore é aplicado às instâncias de Banco de Dados SQL em execução em todas as assinaturas dentro de seu contexto de cobrança. Para clientes empresariais, o escopo compartilhado é o registro e inclui todas as assinaturas no registro. Para clientes de Pagamento Conforme o Uso, o escopo compartilhado consiste em todas as assinaturas de Pagamento Conforme o Uso criadas pelo administrador da conta.|
    |Região      |A região do Azure abrangida pela reserva de capacidade reservada do Banco de Dados SQL.|
    |Tipo de implantação|O tipo de recurso de SQL para o qual você deseja comprar a reserva.|
    |Nível de desempenho|A camada de serviço para as instâncias do Banco de Dados SQL.
    |Termo        |Um ano ou três anos.|
    |Quantidade    |O número de instâncias que estão sendo compradas na reserva de capacidade reservada do Banco de Dados SQL. A quantidade é o número de instâncias do Banco de Dados SQL em execução que podem receber o desconto de faturamento. Por exemplo, se você estiver executando 10 instâncias do Banco de Dados SQL no Leste dos EUA, especifique a quantidade como 10 para maximizar o benefício de todos os computadores em execução. |
    |||

5. Examine o custo do banco de dados SQL reservadas reserva de capacidade na **custos** seção.
6. Selecione **Comprar**.
7. Selecione **Exibir essa Reserva** para ver o status de sua compra.

## <a name="cancellations-and-exchanges"></a>Cancelamentos e trocas

Caso precise cancelar sua reserva de capacidade reservada do Banco de Dados SQL, pode haver um valor de rescisão antecipada de 12%. Os reembolsos baseiam-se no preço de compra ou no preço atual da reserva, o que for mais baixo. Os reembolsos são limitados a US$ 50.000 por ano. O reembolso que você recebe é o saldo proporcional restante menos o valor de rescisão antecipada de 12%. Para solicitar um cancelamento, vá até a reserva no portal do Azure e selecione **Reembolso** para criar uma solicitação de suporte.

Se precisar alterar sua reserva da capacidade reservada do Banco de Dados SQL para outra região, tipo de implantação, nível de desempenho ou prazo, será possível trocá-la por outra reserva de valor igual ou maior. A data de início do prazo da nova reserva não é a mesma da reserva trocada. O prazo de 1 ou 3 anos começa quando você cria a nova reserva. Para solicitar uma troca, vá até a reserva no portal do Azure e selecione **Trocar** para criar uma solicitação de suporte.

Para obter mais informações sobre como as reservas do exchange ou o reembolso, consulte [trocas de reserva e os reembolsos](../billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="vcore-size-flexibility"></a>Flexibilidade de tamanho do vCore

A flexibilidade de tamanho do vCore ajuda você a aumentar ou diminuir dentro de uma região e nível de desempenho sem perder o benefício de capacidade reservada. A capacidade reservada do Banco de Dados SQL também lhe oferece a flexibilidade para transferir temporariamente seus bancos de dados frequentes entre pools e bancos de dados individuais como parte de suas operações normais (dentro da mesma região e nível de desempenho) sem perder o benefício de capacidade reservada. Ao manter um buffer não aplicado em sua reserva, é possível gerenciar com eficiência os picos de desempenho sem exceder o orçamento.

## <a name="next-steps"></a>Próximos passos

O desconto de reserva vCore é aplicado automaticamente ao número de instâncias do Banco de Dados SQL que correspondem ao escopo e aos atributos de reserva de capacidade reservados do Banco de Dados SQL. Você pode atualizar o escopo da reserva de capacidade reservada do Banco de Dados SQL por meio do [Portal do Azure](https://portal.azure.com), PowerShell, CLI ou por meio da API.

Para saber como gerenciar a reserva de capacidade reserva do Banco de Dados SQL, confira [gerenciar a capacidade reserva de Banco de Dados SQL](../billing/billing-manage-reserved-vm-instance.md).

Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:

- [O que são Reservas do Azure?](../billing/billing-save-compute-costs-reservations.md)
- [Gerenciar Reservas do Azure](../billing/billing-manage-reserved-vm-instance.md)
- [Compreender o desconto de Reservas do Azure](../billing/billing-understand-reservation-charges.md)
- [Entender o uso de reserva para a sua assinatura paga conforme o uso](../billing/billing-understand-reserved-instance-usage.md)
- [Entender o uso de reserva para seu registro de empresa](../billing/billing-understand-reserved-instance-usage-ea.md)
- [Reservas do Azure no programa de CSP (Provedor de Soluções na Nuvem) do Partner Center](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
