---
title: Pré-pagamento para banco de dados SQL do Azure vCores para economizar dinheiro | Microsoft Docs
description: Aprenda a comprar a capacidade reservada do Banco de Dados SQL do Azure para economizar nos custos de seu computador.
services: sql-database
documentationcenter: ''
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.devlang: na
ms.topic: conceptual
ms.date: 08/08/2018
ms.author: carlrab
ms.openlocfilehash: 1d1cad4b614eb35be9235a721368db8048be050a
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39631675"
---
# <a name="prepay-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>Pré-pagamento para recursos de computação do Banco de Dados SQL com capacidade reservada do Banco de Dados SQL do Azure

Economize dinheiro com o Banco de Dados SQL do Azure pré-pagando pelos recursos de computação do Banco de Dados SQL do Azure em comparação com os preços de pagamento conforme o uso. Com a capacidade reservada do Banco de Dados SQL do Azure, você assume um compromisso inicial com o Banco de Dados SQL por um período de um ou três anos para obter um desconto significativo nos custos de computação. Para adquirir a capacidade reservada do Banco de Dados SQL, você precisa especificar a região do Azure, o tipo de implantação, o serviço e o termo. 

Você não precisa atribuir a reserva às instâncias do Banco de Dados SQL. As instâncias de banco de dados SQL correspondentes, que já estão em execução ou recém-implantadas, receberão automaticamente o benefício. Ao comprar uma reserva, você está pagando antecipadamente os custos de computação das instâncias do Banco de Dados SQL por um período de um ou três anos. Assim que você compra uma reserva, as cobranças de computação do Banco de Dados SQL que correspondem aos atributos de reserva não são mais cobradas nas taxas de pagamento conforme o uso. Uma reserva não cobre custos de software, rede ou armazenamento associados à instância do banco de dados SQL. No final do período de reserva, o benefício de cobrança expira e os Bancos de Dados SQL são cobrados pelo preço de pré-pagamento. As reservas não são renovadas automaticamente. Para obter informações sobre preços, consulte a [oferta de capacidade reservada do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).

Você pode comprar capacidade reservada do Azure SQL Database na [portal do Azure](https://portal.azure.com). Capacidade reservada comprar o banco de dados SQL:
- Você deve ser na função de proprietário para pelo menos uma Enterprise ou assinatura paga conforme o uso.
- Para assinaturas Enterprise, as compras de reserva do Azure devem ser ativadas no [portal EA](https://ea.azure.com).
-  Para o programa Provedor de Solução de Nuvem (CSP), apenas os agentes admin ou agentes de vendas podem adquirir a capacidade reservada do Banco de Dados SQL.

Os detalhes sobre como os clientes corporativos e os clientes do Pay-As-You-Go são cobrados pelas compras de reserva são abordados em [Perguntas frequentes](#frequently-asked-questions).

## <a name="determine-the-right-sql-size-before-purchase"></a>Determinar o tamanho correto do SQL antes da compra

O tamanho da reserva deve ser baseado na quantidade total de computação usada pelos bancos de dados SQL existentes ou prestes a serem implantados e/ou pools elásticos dentro de uma região específica e usando a mesma camada de desempenho e geração de hardware. 

Por exemplo, vamos supor que você esteja executando uma finalidade geral, o pool elástico Gen5 - 16 vCore e dois bancos de dados únicos Gen5 - 4 vCore essenciais para os negócios. Além disso, suponhamos que você planeje implantar, no próximo mês, uma finalidade geral adicional, o pool elástico Gen5 - 16 vCore e um pool elástico de v5 core Gen5 - 32 para negócios críticos. Além disso, vamos supor que você saiba que precisará desses recursos por pelo menos 1 ano. Neste caso você deve comprar um 32 (2x16) vCores, 1 ano de reserva para banco de dados SQL Single / Elastic Pool General Purpose - Compute Gen5 e 40 (2x4 + 32) vCore 1 ano de reserva para SQL Database Single / Elastic Pool Business Critical - Compute Gen5.

## <a name="buy-sql-database-reserved-capacity"></a>Comprar capacidade reservada do banco de dados do SQL

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** > **Reservas**.
3. Selecione **Add** e, em seguida, no painel Selecionar tipo de produto, selecione **banco de dados SQL** para comprar uma nova reserva do banco de dados SQL.
4. Preencha os campos obrigatórios. Bancos de dados individuais ou novos existentes ou novos que correspondem aos atributos que você selecionou se qualificam para obter o desconto de capacidade reservado. O número real de suas instâncias do Banco de Dados SQL que recebem o desconto depende do escopo e da quantidade selecionada.

   ![Captura de tela antes de enviar a compra de capacidade reservada do Banco de Dados SQL](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

    | Campo      | DESCRIÇÃO|
    |:------------|:--------------|
    |NOME        |O nome dessa reserva.| 
    |Assinatura|A assinatura usada para pagar pela reserva de capacidade reservada do Banco de Dados SQL. O método de pagamento na assinatura é cobrado pelos custos iniciais da reserva de capacidade reservada do Banco de Dados SQL. O tipo de assinatura deve ser um contrato enterprise (número de oferta: MS-AZR-0017P) ou de Pagamento Conforme o Uso (número da oferta: MS-AZR-0003P). Para uma assinatura de empresa, os encargos são deduzidos do saldo do compromisso monetário do registro ou cobrados como média. Para a assinatura de Pagamento Conforme o Uso, as cobranças são feitas ao cartão de crédito ou à forma de pagamento de faturas na assinatura.|    
    |Escopo       |O escopo da reserva vCore pode cobrir uma assinatura ou várias assinaturas (escopo compartilhado). Se você selecionar: <ul><li>Assinatura única - O desconto de reserva vCore é aplicado às instâncias do Banco de Dados SQL nessa assinatura. </li><li>Compartilhado - o desconto de reserva de vCore é aplicado às instâncias de banco de dados SQL em execução em todas as assinaturas dentro de seu contexto de cobrança. Para clientes empresariais, o escopo compartilhado é o registro e inclui todas as assinaturas (exceto as assinaturas de desenvolvimento/teste) no registro. Para clientes de Pagamento Conforme o Uso, o escopo compartilhado consiste em todas as assinaturas de Pagamento Conforme o Uso criadas pelo administrador da conta.</li></ul>|
    |Região      |A região do Azure abrangida pela reserva de capacidade reservada do Banco de Dados SQL.|    
    |Tipo de implantação|O tipo de implantação de SQL para o qual você deseja comprar a reserva.|
    |Camada de serviço|A camada de serviço para as instâncias de banco de dados SQL.
    |Termo        |Um ano ou três anos.|
    |Quantidade    |O número de instâncias que estão sendo compradas na reserva de capacidade reservada do Banco de Dados SQL. A quantidade é o número de instâncias do Banco de Dados SQL em execução que podem receber o desconto de faturamento. Por exemplo, se você estiver executando 10 instâncias do Banco de Dados SQL no Leste dos EUA, especifique a quantidade como 10 para maximizar o benefício de todos os computadores em execução. |

5. Examine o custo do banco de dados SQL reservadas reserva de capacidade na **custos** seção.
6. Selecione **Comprar**.
7. Selecione **Exibir essa Reserva** para ver o status de sua compra.

## <a name="next-steps"></a>Próximas etapas 
O desconto de reserva vCore é aplicado automaticamente ao número de instâncias do Banco de Dados SQL que correspondem ao escopo e aos atributos de reserva de capacidade reservados do Banco de Dados SQL. Você pode atualizar o escopo da reserva de capacidade reservada do Banco de Dados SQL por meio do [Portal do Azure](https://portal.azure.com), PowerShell, CLI ou por meio da API. 

Para saber como gerenciar o banco de dados do SQL reservadas de reserva de capacidade, consulte [capacidade de gerenciar o banco de dados SQL reservada](../billing/billing-manage-reserved-vm-instance.md).

Para saber mais sobre a reserva do Azure, consulte os seguintes artigos:

- [Quais são as reservas do Azure?](../billing/billing-save-compute-costs-reservations.md)
- [Gerenciar reservas do Azure](../billing/billing-manage-reserved-vm-instance.md)
- [Entender o uso de reserva para a sua assinatura paga conforme o uso](../billing/billing-understand-reserved-instance-usage.md)
- [Entenda o uso de reservas para o seu registro Enterprise](../billing/billing-understand-reserved-instance-usage-ea.md)
- [Reservas do Azure no programa de parceiro provedor de solução de nuvem Center (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.

