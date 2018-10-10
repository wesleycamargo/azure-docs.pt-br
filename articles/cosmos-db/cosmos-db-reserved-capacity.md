---
title: Pagar antecipadamente por recursos do Azure Cosmos DB para economizar dinheiro | Microsoft Docs
description: Aprenda a comprar a capacidade reservada do Azure Cosmos DB para economizar nos custos de computação.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 1be2d67d8a1ee51c4883ae1f50b80ad3a9691c2d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46981960"
---
# <a name="prepay-for-azure-cosmos-db-resources-with-reserved-capacity"></a>Pagar antecipadamente por recursos do Azure Cosmos DB com capacidade reservada

A capacidade reservada do Azure Cosmos DB ajuda você a economizar dinheiro ao pagar antecipadamente pelos recursos do Azure Cosmos DB por um período de um a três anos. A capacidade reservada do Azure Cosmos DB permite que você obtenha um desconto na taxa de transferência provisionada para recursos do Cosmos DB, por exemplo, bancos de dados e contêineres (tabelas/coleções/grafos). A capacidade reservada do Azure Cosmos DB pode reduzir significativamente os custos do Cosmos DB, até 65% nos preços regulares, com um compromisso antecipado de um a três anos. A capacidade reservada proporciona um desconto de cobrança e não afeta o estado do tempo de execução dos seus recursos do Cosmos DB.

A capacidade reservada do Azure Cosmos DB abrange a taxa de transferência provisionada para seus recursos, ela não abrange as cobranças de rede e armazenamento. Assim que você compra uma reserva, as cobranças de taxa de transferência que correspondem aos atributos de reserva não são mais cobradas nas taxas de pagamento conforme o uso. Para obter mais informações sobre reservas, confira o artigo [Reservas do Azure](../billing/billing-save-compute-costs-reservations.md). 

Você pode comprar a capacidade reservada do Azure Cosmos DB no [portal do Azure](https://portal.azure.com). Para comprar uma capacidade reservada:

* Você deve ser na função de proprietário para pelo menos uma Enterprise ou assinatura paga conforme o uso.  
* Para assinaturas Enterprise, as compras de reserva do Azure devem ser habilitadas no [portal EA](https://ea.azure.com/).  
* Para o programa CSP (Provedor de Solução de Nuvem), apenas os agentes admin ou agentes de vendas podem comprar a capacidade reservada do Azure Cosmos DB.

## <a name="determine-the-required-throughput-before-purchase"></a>Determinar a taxa de transferência necessária antes da compra

O tamanho da reserva deve ser baseado na quantidade total da taxa de transferência usada pelos recursos do Azure Cosmos DB existentes ou a serem implantados em breve (por exemplo, bancos de dados ou contêineres: coleções, tabelas ou grafos). Você pode determinar a taxa de transferência necessária das seguintes maneiras:

* Navegue até o [portal do Azure](https://portal.azure.com), localize sua conta do Azure Cosmos DB, abra a folha Métricas e obtenha os detalhes da taxa de transferência/s média na guia **Taxa de Transferência** em um período de 3 a 6 meses. Forneça esse tamanho como as unidades de capacidade reservada ao comprar.

Como alternativa, se você for um cliente do EA (Contrato Enterprise), poderá baixar o arquivo de uso e consultar o valor de **Tipo de Serviço** na seção **Informações adicionais** do arquivo de uso para obter os detalhes de taxa de transferência do Azure Cosmos DB.

Você também pode somar a taxa de transferência média para todas as cargas de trabalho em suas contas do Azure Cosmos DB que antecipou executar para o próximo ano, ou três próximos, e usar essa quantidade para a reserva.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Comprar capacidade reservada do Azure Cosmos DB

1. Entre no [Portal do Azure](https://portal.azure.com).  

2. Selecione **Todos os serviços** > **Reservas** > **Adicionar**.  

3. No painel **Selecionar Tipo de Produto**, escolha **Azure Cosmos DB** e **Selecionar** para comprar uma nova reserva.  

4. Preencha os campos obrigatórios, conforme descrito na tabela a seguir:

   ![Preencher o formulário de capacidade reservada](./media/cosmos-db-reserved-capacity/fill_reserved_capacity_form.png) 

   |Campo  |DESCRIÇÃO  |
   |---------|---------|
   |NOME   |    Nome da reserva. Este campo é populado automaticamente com o `CosmosDB_Reservation_<timeStamp>`. Você pode fornecer um nome diferente durante a criação da reserva ou renomeá-la depois que a reserva é criada.      |
   |Assinatura  |   A assinatura usada para pagar pela capacidade reservada do Azure Cosmos DB. O método de pagamento na assinatura selecionada é usado na cobrança dos custos iniciais. O tipo de assinatura deve ser um dos a seguir: <br/><br/>  [Contrato Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/) (número da oferta: MS-AZR-0017P): para uma assinatura de empresa, os encargos são deduzidos do saldo do compromisso monetário do registro ou cobrados como excedente. <br/><br/> [Pago conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/) (número da oferta: MS-AZR-0003P): para a assinatura de Pagamento Conforme o Uso, as cobranças são feitas ao cartão de crédito ou à forma de pagamento de faturas na assinatura.    |
   |Escopo   |   O escopo da reserva controla quantas assinaturas podem utilizar o benefício de cobrança associado com a reserva, além de controlar como a reserva é aplicada a assinaturas específicas. Uma reserva tem um escopo de assinatura única ou compartilhada. Se você selecionar:   <br/><br/>  **Assinatura única**: o desconto de reserva é aplicado às instâncias do Azure Cosmos DB na assinatura selecionada. <br/><br/>  **Compartilhada**: o desconto de reserva é aplicado às instâncias do Azure Cosmos DB em execução em qualquer assinatura dentro do seu contexto de cobrança. O contexto de cobrança é determinado com base em como você se inscreveu no Azure. Para clientes empresariais, o escopo compartilhado é o registro e inclui todas as assinaturas (exceto as assinaturas de desenvolvimento/teste) no registro. Para clientes de Pagamento Conforme o Uso, o escopo compartilhado consiste em todas as assinaturas de Pagamento Conforme o Uso criadas pelo administrador da conta.  <br/><br/> Você pode alterar o escopo de reserva após adquirir a capacidade reservada.  |
   |Tipo de capacidade reservada   |  O tipo de capacidade reservada é a taxa de transferência provisionada em termos de unidades de solicitação.|
   |Unidades de capacidade reservada  |      A quantidade de taxa de transferência que você deseja reservar. Você pode calcular esse valor determinando a taxa de transferência necessária para todos os seus recursos do Cosmos DB (por exemplo, bancos de dados ou contêineres) por região e, em seguida, multiplicando pelo número de regiões que associará ao seu banco de dados do Cosmos DB.  <br/> Por exemplo: se você tiver cinco regiões com 1 milhão de RU/s em todas as regiões, selecione 5 milhões de RU/s para compra de capacidade de reserva.    |
   |Termo  |   Um ano ou três anos.   |

5. Examine o desconto e o preço da reserva na seção **Custos**. Esse preço de reserva é aplicável aos recursos do Azure Cosmos DB com a taxa de transferência provisionada entre todas as regiões.  

6. Selecione **Comprar**. Você pode ver a seguinte captura de tela quando a compra é realizada com êxito. 

   ![Preencher o formulário de capacidade reservada](./media/cosmos-db-reserved-capacity/reserved_capacity_successful.png) 

Depois de comprar uma reserva, ela será aplicada imediatamente a qualquer recurso existente do Azure Cosmos DB para corresponder aos termos da reserva. Se você não tiver nenhum recurso existente do Azure Cosmos DB, a reserva será aplicada quando implantar uma nova instância do Cosmos DB que corresponde aos termos da reserva. Em ambos os casos, o período da reserva começa imediatamente após a compra com êxito. 

Quando sua reserva expirar, as instâncias do Azure Cosmos DB continuarão a ser executado e são cobradas com as taxas pagas conforme o uso regulares.

## <a name="next-steps"></a>Próximas etapas

O desconto da reserva é aplicado automaticamente aos recursos do Azure Cosmos DB que correspondem ao escopo e aos atributos da reserva. Você pode atualizar o escopo da reserva por meio do portal do Azure, PowerShell, CLI ou por meio da API.

*  Para saber como os descontos da capacidade reservada são aplicados ao Azure Cosmos DB, confira [Compreender o desconto de Reservas do Azure](../billing/billing-understand-cosmosdb-reservation-charges.md)

* Para saber mais sobre as reservas do Azure, consulte os seguintes artigos:

   * [Quais são as reservas do Azure?](../billing/billing-save-compute-costs-reservations.md)  
   * [Gerenciar Reservas do Azure](../billing/billing-manage-reserved-vm-instance.md).  
   * [Entender o uso de reserva para seu registro de empresa](../billing/billing-understand-reserved-instance-usage-ea.md)  
   * [Entender o uso de reserva para a sua assinatura paga conforme o uso](../billing/billing-understand-reserved-instance-usage.md)
   * [Reservas do Azure no programa de parceiro provedor de solução de nuvem Center (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.

