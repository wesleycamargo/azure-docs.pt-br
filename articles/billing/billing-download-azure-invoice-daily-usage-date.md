---
title: Baixar a fatura de cobrança e os dados de uso diário do Azure | Microsoft Docs
description: Descreve como baixar ou exibir a fatura de cobrança e os dados de uso diário do Azure.
keywords: fatura de cobrança, download de fatura, fatura do Azure, uso do Azure
services: billing
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/16/2018
ms.author: banders
ms.openlocfilehash: 669d4be52cb74296bb034c773b820e14d7eede96
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2019
ms.locfileid: "54902186"
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Baixar ou exibir a fatura de cobrança e os dados de uso diário do Azure

Para a maioria das assinaturas, você pode fazer o download da sua fatura no [portal do Microsoft Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ou enviá-la por e-mail. Se você for um cliente do Azure com um Contrato Enterprise (cliente da EA), não poderá fazer o download das faturas da sua organização. As faturas são enviadas para quem está configurado para receber as faturas da inscrição.

Se você quiser fazer o download do uso como um cliente da EA, ele estará disponível no [portal do  Microsoft Azure](https://portal.azure.com/) > **Gerenciamento de Custos + Faturamento** > **Uso + cobrança**. Para outras assinaturas, vá para o [Centro de Contas do Azure](https://account.azure.com/Subscriptions).

Apenas determinadas funções têm permissão para receber informações de fatura e uso de faturamento, como o administrador da conta ou o administrador corporativo. Para saber mais sobre como obter acesso a informações de cobrança, consulte [Gerenciar o acesso à cobrança do Azure usando funções](billing-manage-access.md).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="download-or-view-your-invoice"></a>Faça o download ou visualize sua fatura

 Se você é um cliente da EA, não pode fazer o download das faturas da sua organização. As faturas são enviadas para quem está configurado para receber as faturas da inscrição. Para outras assinaturas, você pode receber sua fatura por e-mail ou fazer o download no portal do Azure.

### <a name="get-your-invoice-in-email-pdf"></a>Obter sua fatura por email (.pdf)
Você pode aceitar e configurar destinatários adicionais para receber sua fatura do Azure em um email. Esse recurso pode não estar disponível para determinadas assinaturas, como ofertas de suporte, Enterprise Agreements ou Azure via Open.

1. Selecione sua assinatura na [página Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Aceitar para cada assinatura que você possui. Clique em **Faturas** e em **Enviar minha fatura por email**. 

    ![Captura de tela que mostra o fluxo de aceitação](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)
    
2. Clique em **Aceitar** e aceite os termos.

    ![Captura de tela que mostra a etapa 2 do fluxo de aceitação](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)
 
3. Depois de aceitar o contrato, você poderá configurar destinatários adicionais. Quando um destinatário é removido, o endereço de email não é armazenado. Se você mudar de ideia, você precisa adicioná-los novamente.

    ![Captura de tela que mostra a etapa 3 do fluxo de aceitação](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)
    
Se você não receber um email depois de seguir as etapas, verifique se seu endereço de email está correto nas [opções de comunicação de seu perfil](https://account.windowsazure.com/profile).

### <a name="opt-out-from-getting-your-invoice-in-email"></a>Recusar obtenção da sua fatura no email
Se você não quiser receber sua fatura por e-mail, clique em **Desativar faturas enviadas por e-mail**. Essa opção remove todos os endereços de e-mail configurados para receber faturas por e-mail. Se você optar por voltar, terá que reconfigurar os destinatários.

 ![Captura de tela que mostra o fluxo para recusar](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

### <a name="download-invoice-from-azure-portal-pdf"></a>Baixar fatura no Portal do Azure (.pdf)

1. Selecione sua assinatura da [página Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no Portal do Azure como [um usuário com acesso a faturas](billing-manage-access.md).

2. Selecione **Faturas**. 

    ![Captura de tela que mostra a opção de Cobrança e Uso](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png) 

3. Clique em **Baixar Fatura** para exibir uma cópia da sua fatura em PDF. Se o resultado for **Não disponível**, veja [Por que não encontro uma fatura do último período de cobrança?](#noinvoice)

    ![Captura de tela que mostra os períodos de cobrança, a opção de download e as cobranças totais para cada período de cobrança](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. Você também pode exibir seu uso diário clicando no período de cobrança. 

Para saber mais sobre a fatura, confira [Entenda sua fatura do Microsoft Azure](billing-understand-your-bill.md). Para obter ajuda sobre como gerenciar os custos, confira [Evitar custos inesperados com o gerenciamento de custos e a cobrança do Azure](billing-getting-started.md).

### <a name="noinvoice"></a> Por que não encontro uma fatura do último período de cobrança?

Pode haver vários motivos pelos quais você não vê uma fatura:

- Você tem um valor de crédito mensal com sua assinatura que não foi ultrapassado ou você tem uma Avaliação Gratuita. Uma fatura é gerada somente quando você é um devedor.

- Faz menos de 30 dias que você se inscreveu no Azure.

- A fatura não foi gerada ainda. Aguarde até o final do período de cobrança.

- Se você não é o Administrador da Conta, faturas anteriores talvez não estejam disponíveis para você.

## <a name="download-usage"></a>Fazer o download de uso

 Para a maioria das assinaturas, encontre seu arquivo de uso diário no [ Centro de Contas do Azure ](https://account.azure.com/Subscriptions). Se você quiser fazer o download do uso como um cliente da EA, ele estará disponível no [portal do  Microsoft Azure](https://portal.azure.com/) > **Gerenciamento de Custos + Faturamento** > **Uso + cobrança**. 

### <a name="download-usage-from-the-account-center-csv"></a>Baixar uso do Centro de Contas (.csv)

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

Somente o Administrador da Conta pode acessar o Centro de Contas do Azure. Outros administradores de cobrança, por exemplo um proprietário, podem obter informações de uso usando as [APIs de cobrança](billing-usage-rate-card-overview.md).

Para saber mais sobre o uso diário, confira [Entenda sua fatura do Microsoft Azure](billing-understand-your-bill.md). Para obter ajuda sobre como gerenciar os custos, confira [Evitar custos inesperados com o gerenciamento de custos e a cobrança do Azure](billing-getting-started.md).

### <a name="download-usage-for-ea-customers"></a>Faça o download uso para clientes do EA

Para visualizar e fazer o download dos dados de uso como um cliente da EA, você deve ser um Administrador Corporativo ou um Proprietário da Conta ou Administrador do Departamento com a política de cobranças de visualização ativada.

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Pesquise **Gerenciamento de Custos do Azure + Cobrança**.

    ![Captura de tela que mostra a pesquisa do portal do Azure](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. Selecione **uso + encargos**.
1. No mês que você deseja fazer o download, selecione **fazer o download**.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
