---
title: "Como obter a fatura de cobrança e os dados de uso diário do Azure | Microsoft Docs"
description: "Descreve como obter sua fatura de cobrança e dados de uso diário do Azure"
services: 
documentationcenter: 
author: genlin
manager: ruchic
editor: 
tags: billing
ms.assetid: 6d568d1d-3bd6-4348-97d0-1098b5fe0661
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 4d974590ee7677a4f3fcebd9e2474c99a974f663
ms.openlocfilehash: 60ac57fd22f237b7fe62773486fe46913aca8dd0


---
# <a name="how-to-get-your-azure-billing-invoice-and-daily-usage-data"></a>Como obter sua fatura de cobrança e dados de uso diário do Azure
Você pode aceitar e configurar destinatários adicionais para receber sua fatura do Azure em um email. Você também pode baixar sua fatura no [Portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Além disso, é possível baixar um csv de uso no [Centro de Contas do Azure](https://account.windowsazure.com). Somente o Administrador da Conta tem permissão para obter as informações de uso e a fatura de cobrança. Para descobrir quem é o Administrador da Conta da assinatura, confira [Transferring ownership of an Azure subscription - FAQ](billing-subscription-transfer.md#faq) (Transferindo a propriedade de uma assinatura do Azure — perguntas frequentes).

## <a name="get-your-invoice-over-email-pdf"></a>Obter sua fatura por email (.pdf)
1. Selecione sua assinatura na [folha de assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Você precisa aceitar cada assinatura que possui. Clique em **Faturas** e em **Enviar minha fatura por email**. Se você não for o Administrador da Conta, essa opção não estará visível.

    ![Captura de tela que mostra o fluxo de aceitação](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)
    
2. Clique em **Aceitar** e aceite os termos:

    ![Captura de tela que mostra o fluxo de aceitação](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)
 
3. Depois de aceitar o contrato, você poderá configurar destinatários adicionais:

    ![Captura de tela que mostra o fluxo de aceitação](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)
    
Você também pode acessar esse link profundo da folha em seu email de notificação de extrato mensal

### <a name="i-cant-access-the-email-settings-blade"></a>Não é possível acessar a folha de configurações de email
* Você deve ser o Administrador da Conta para configurar essa definição. Não sabe o que isso significa? [Saiba mais aqui](billing-add-change-azure-subscription-administrator.md).
* Se você tiver uma fatura mensal, mas não estiver recebendo um email, verifique se você [definiu o email de comunicação corretamente](https://account.windowsazure.com/profile).
* Esse recurso pode não estar disponível para determinadas assinaturas, como ofertas de suporte, Enterprise Agreements ou Azure via Open.

## <a name="download-invoice-from-azure-portal-pdf"></a>Baixar fatura no Portal do Azure (.pdf)

1. Entre no [Portal do Azure](https://portal.azure.com) como Administrador da Conta. 
2. No menu Hub, selecione **Assinaturas**. 

    ![Captura de tela que mostra a opção de Assinatura](./media/billing-download-azure-invoice-daily-usage-date/submenu.png) 

3. Na folha **Assinaturas**, selecione a assinatura que você deseja exibir e selecione **Cobrança e Uso**. 

    ![Captura de tela que mostra a opção de Cobrança e Uso](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png) 

4. Na folha **Cobrança e Uso**, clique em **Baixar Fatura** para exibir uma cópia da sua fatura em pdf. 

    ![Captura de tela que mostra os períodos de cobrança, a opção de download e as cobranças totais para cada período de cobrança](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

5. Você pode exibir seu uso diário clicando no período de cobrança. 

Para saber mais sobre a fatura, confira [Entenda sua fatura do Microsoft Azure](billing-understand-your-bill.md).

## <a name="download-usage-from-the-account-center-csv"></a>Baixar uso do Centro de Contas (.csv)
1. Entre no [Centro de Contas do Azure](https://account.windowsazure.com/subscriptions) como o Administrador da Conta.
2. Selecione a assinatura da qual deseja obter informações de fatura e uso.
3. Clique em **HISTÓRICO DE COBRANÇA**. 

    ![Captura de tela que mostra a opção de histórico de cobrança](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. Você pode ver os demonstrativos dos últimos seis períodos de cobrança e o período atual não faturado. 

    ![Captura de tela que mostra os períodos de cobrança, as opções para baixar a fatura e o uso diário e as cobranças totais para cada período de cobrança](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. Selecione **Exibir Demonstrativo Atual** para ver uma estimativa dos seus encargos no momento em que a estimativa foi gerada. Essas informações são atualizadas diariamente e talvez não incluam todo o seu uso. Sua fatura mensal pode ser diferente dessa estimativa.

    ![Captura de tela que mostra a opção de Exibir Demonstrativo Atual](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![Captura de tela que mostra a estimativa das cobranças atuais](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. Selecione **Baixar Uso** para baixar os dados de uso diário como um arquivo CSV. Se vir duas versões disponíveis, baixe a versão 2.

    ![Captura de tela que mostra a opção de Baixar Uso](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

Para saber mais sobre o uso diário, confira [Entenda sua fatura do Microsoft Azure](billing-understand-your-bill.md).

## <a name="a-namenoinvoicea-why-dont-i-see-an-invoice-for-the-last-billing-period"></a><a name="noinvoice"></a> Por que não encontro uma fatura do último período de cobrança?
Pode haver vários motivos pelos quais você não vê uma fatura:
- Você tem um valor de crédito mensal com sua assinatura que não foi ultrapassado ou você tem uma Avaliação Gratuita. Uma fatura não será gerada a menos que você deva dinheiro.
- Faz menos de 30 dias que você se inscreveu no Azure.
- A fatura não foi gerada ainda. Aguarde até o final do período de cobrança.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.
Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.




<!--HONumber=Feb17_HO3-->


