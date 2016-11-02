<properties
    pageTitle="Entenda os encargos dos serviços externos do Azure | Microsoft Azure"
    description="Saiba mais sobre a cobrança de serviços externos, anteriormente conhecidos como Marketplace, no Azure."
    services=""
    documentationCenter=""
    authors="adpick"
    manager="felixwu"
    editor=""
    tags="billing"
    />

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="adpick"/>


# <a name="understand-your-azure-external-service-charges"></a>Entenda os encargos dos serviços externos do Azure

Este artigo explica a cobrança de serviços externos no Azure. Os serviços externos costumavam ser chamados de pedidos do Marketplace. Os serviços externos são fornecidos por prestadores de serviços independentes, mas estão completamente integrados ao ecossistema do Azure. Saiba como:

- Identificar serviços externos
- Compreender como a cobrança difere de outros recursos do Azure
- Exibir e acompanhar os custos acumulados por uso de serviços externos
- Gerenciar pedidos de serviços externos e como você paga por eles

## <a name="what-are-azure-external-services?"></a>O que são serviços externos do Azure?

Os serviços externos costumavam ser chamados de Azure Marketplace. Em geral, são serviços publicados por terceiros disponíveis para o Azure. Por exemplo, ClearDB e SendGrid são serviços externos que você pode adquirir no Azure, mas não são publicados pela Microsoft.

### <a name="identify-external-services"></a>Identificar serviços externos

Quando você provisiona um novo recurso ou serviço externo, um aviso é exibido:

![Aviso de compra no Marketplace](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

>[AZURE.NOTE] Os serviços externos são publicados por empresas que não são a Microsoft, embora, às vezes, produtos da Microsoft também sejam categorizados como serviços externos.

### <a name="external-services-are-billed-separately"></a>Os serviços externos são cobrados separadamente

Os serviços externos são tratados como pedidos individuais dentro de sua assinatura do Azure. O período de cobrança para cada serviço é definido quando você adquire o serviço. Ele não deve ser confundido com o período de cobrança da assinatura sob a qual você fez a compra. Você também recebe faturas separadas e seu cartão de crédito é cobrado separadamente.

### <a name="each-external-service-has-a-different-billing-model"></a>Cada serviço externo tem um modelo de cobrança diferente

Alguns serviços são pré-pagos, enquanto outros usam um modelo baseado em pagamento mensal. Você precisa de um cartão de crédito para os serviços externos do Azure. Não é possível adquirir serviços externos com pagamento por fatura.

### <a name="you-can't-use-monthly-free-credits-for-external-services"></a>Não é possível usar créditos gratuitos mensais para serviços externos

Se você estiver usando uma assinatura do Azure que inclua [créditos gratuitos](https://azure.microsoft.com/pricing/spending-limits/), eles não poderão ser aplicados a faturas de serviços externos. Use um cartão de crédito para adquirir serviços externos.

## <a name="view-external-service-spending-and-history"></a>Exibir o histórico e os gastos com serviços externos

Você pode exibir uma lista dos serviços externos que estão em cada assinatura no [Portal do Azure](https://portal.azure.com/): 

1. Entre no [Portal do Azure](https://portal.azure.com/) e [navegue até a folha **Cobrança**](https://portal.azure.com/?flight=1#blade/Microsoft_Azure_Billing/BillingBlade).

    ![Selecione Cobrança no menu Hub](./media/billing-understand-your-azure-marketplace-charges/billing-button.png) 
  
2. Na seção **Custos da assinatura**, escolha a assinatura que você quer exibir. 
   
    ![Selecione uma assinatura na folha de cobrança](./media/billing-understand-your-azure-marketplace-charges/select-sub.png)

3. Clique em **Serviços externos**.

    ![Clique em Serviços externos na folha da assinatura](./media/billing-understand-your-azure-marketplace-charges/external-service-blade.png)

4. Você deve ver cada um de seus pedidos de serviços externos, o nome do publicador, a camada de serviço adquirida, o nome que você deu ao recurso e o status atual do pedido. Selecione um serviço externo para ver as faturas passadas.

    ![Selecione um serviço externo](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)

5. A partir daqui, você pode exibir os valores de faturas anteriores, incluindo o detalhamento dos impostos.

    ![Exibir histórico de cobrança de serviços externos](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png)

## <a name="manage-payment-methods-for-external-service-orders"></a>Gerenciar formas de pagamento para pedidos de serviços externos

Atualize suas formas de pagamento para pedidos de serviços externos no [Centro de Contas](https://account.windowsazure.com/).

> [AZURE.NOTE] Se tiver adquirido sua assinatura com uma conta corporativa ou de estudante, você deve [entrar em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para alterar sua forma de pagamento.

1. Entre no [Centro de Contas](https://account.windowsazure.com/) e [navegue até a guia **marketplace**](https://account.windowsazure.com/Store)

    ![Selecione marketplace no centro de contas](./media/billing-understand-your-azure-marketplace-charges/select-marketplace.png)

2. Selecione o serviço externo que você deseja gerenciar

    ![Selecione o serviço externo que você deseja gerenciar](./media/billing-understand-your-azure-marketplace-charges/select-ext-service.png)

3. Clique em **Alterar forma de pagamento** no lado direito da página. Este link leva você a um portal diferente para gerenciar sua forma de pagamento.
    
    ![Resumo do pedido](./media/billing-understand-your-azure-marketplace-charges/change-payment.PNG)

4. Clique em **Editar informações** e siga as instruções para atualizar suas informações de pagamento.

    ![Selecione editar informações](./media/billing-understand-your-azure-marketplace-charges/edit-info.png)
    
## <a name="cancel-an-external-service-order"></a>Cancelar pedido de serviço externo

Se você deseja cancelar seu pedido de serviço externo, será preciso excluir o recurso no [Portal do Azure](https://portal.azure.com).

![Excluir recurso](./media/billing-understand-your-azure-marketplace-charges/deleteMarketplaceOrder.PNG)

## <a name="need-help?-contact-support."></a>Precisa de ajuda? Entre em contato com o suporte.

Se ainda tiver mais dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.



<!--HONumber=Oct16_HO2-->


