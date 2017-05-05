---
title: "Evitar custos inesperados e gerenciar a cobrança – Azure | Microsoft Docs"
description: Saiba como evitar encargos inesperados em sua conta do Azure. Use recursos de gerenciamento e controle de custos para uma assinatura do Microsoft Azure.
services: 
documentationcenter: 
author: jlian
manager: mattstee
editor: 
tags: billing
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2017
ms.author: jlian
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 539a3e628dfec1088f683652bc9b743b87875d36
ms.lasthandoff: 04/26/2017


---
# <a name="prevent-unexpected-costs-with-azure-billing-and-cost-management"></a>Evitar custos inesperados com o gerenciamento de custo e cobrança do Azure

Ao se inscrever no Azure, há várias coisas que podem ser feitas para você ter uma ideia melhor de seus gastos. No [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), ao selecionar a assinatura, é possível ver o detalhamento de custo atual e a taxa de gravação. Você também pode [baixar faturas anteriores e arquivos de uso detalhado](billing-download-azure-invoice-daily-usage-date.md). Se desejar agrupar os custos dos recursos usados em diferentes projetos ou equipes, examine a [marcação de recursos](../azure-resource-manager/resource-group-using-tags.md). Se sua organização tiver um sistema de relatórios que você prefira usar, confira as [APIs de cobrança](billing-usage-rate-card-overview.md). 

Para saber mais sobre o uso diário, confira [Entenda sua fatura do Microsoft Azure](billing-understand-your-bill.md).

Se sua assinatura foi feita por meio de um EA (Contrato Enterprise), CSP (Provedor de Soluções na Nuvem) ou Azure Sponsorship, vários recursos neste artigo não se aplicam ao seu caso. Em vez disso, temos um conjunto diferente de ferramentas que pode ser usado para o gerenciamento de custo. Consulte [Recursos adicionais do EA, CSP e Sponsorship](#other-offers).

Se sua assinatura for uma Avaliação Gratuita, do [Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), AIO (Azure via Open) ou BizSpark, saiba mais sobre os [limites de gastos](#spending-limit) para evitar que ela seja desabilitada de forma inesperada. 

## <a name="day-0-before-you-add-azure-services"></a>Dia 0: Antes de adicionar os serviços do Azure

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Estimar custo online usando a calculadora de preços

Confira a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) e a [calculadora do custo total de propriedade](https://aka.ms/azure-tco-calculator) para obter uma estimativa do custo mensal do serviço em que você está interessado. Por exemplo, uma VM Windows A1 é calculada ao custo de US$ 66,96/mês em horas de computação se você deixá-la em execução o tempo todo:

![Captura de tela da calculadora de preços mostrando que uma VM Windows A1 é estimada ao custo de US$ 66,96 por mês](./media/billing-getting-started/pricing-calc.PNG)

Para saber mais, confira [Perguntas frequentes sobre preço](https://azure.microsoft.com/pricing/faq/). Se você quiser falar com alguém, ligue para 1-800-867-1389.

### <a name="check-your-subscription-and-access"></a>Verificar assinatura e acesso

A exibição dos custos exige [acesso no nível das assinaturas a informações de cobrança](billing-manage-access.md), mas somente o Administrador da conta pode acessar o [Centro de Contas](https://account.windowsazure.com/Home/Index), alterar informações de cobrança e gerenciar assinaturas. O Administrador da conta é a pessoa que passou pelo processo de inscrição. Para gerenciar essas funções, consulte [Adicionar ou alterar as funções de administrador do Azure que gerenciam a assinatura ou serviços](billing-add-change-azure-subscription-administrator.md).

Para ver se você é o Administrador da conta, vá para a [folha Assinaturas no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e examine a lista de assinaturas a que você tem acesso. Procure **Minha Função**. Se estiver indicado *Administrador de conta*, tudo bem. Se estiver indicado *Proprietário*, você não tem todos os privilégios.

![Captura de tela de sua função no modo de exibição Assinaturas no portal do Azure](./media/billing-getting-started/sub-blade-view.PNG)

Se você não for o Administrador da conta, alguém provavelmente lhe deu acesso parcial pelo [RBAC (Controle de Acesso Baseado em Função) do Azure Active Directory](../active-directory/role-based-access-control-configure.md). Para gerenciar assinaturas e alterar informações de cobrança, [localize o Administrador da conta](billing-subscription-transfer.md#whoisaa) e peça-lhe para executar as tarefas ou [transferir a assinatura para você](billing-subscription-transfer.md).

Se o Administrador da conta não está mais na sua organização e você precisa gerenciar a cobrança, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). 

### <a name="spending-limit"></a>Verifique se você tem um limite de gastos ativado

Se você tiver uma assinatura que usa crédito, o limite de gastos é ativado para você por padrão. Dessa forma, quando você gasta todo o seu crédito, não há cobrança a mais no seu cartão de crédito. Consulte a [lista completa de ofertas do Azure e a disponibilidade do limite de gastos](https://azure.microsoft.com/support/legal/offer-details/).

No entanto, se você atingir o limite de gastos, os serviços serão desabilitados. Isso significa que suas VMs serão desalocadas. Para evitar o tempo de inatividade do serviço, você deve desabilitar o limite de gastos. O excedente é cobrado em seu cartão de crédito cadastrado. 

Para ver se você já tem limite de gastos ativado, vá para o [modo de exibição Assinaturas no Centro de Contas](https://account.windowsazure.com/Subscriptions). Uma faixa será exibida se o limite de gastos estiver ativo:

![Captura de tela que mostra um aviso sobre a ativação do limite de gastos no Centro de Contas](./media/billing-getting-started/spending-limit-banner.PNG)

Clique na faixa e siga os prompts para remover o limite de gastos. Se você não inseriu informações de cartão de crédito na inscrição, deverá inseri-las para remover o limite de gastos. Para saber mais, confira [Limite de gastos do Azure – Como funciona e como habilitá-lo ou removê-lo](https://azure.microsoft.com/pricing/spending-limits/).

### <a name="set-up-billing-alerts"></a>Configurar alertas de cobrança

Configure alertas de cobrança para receber emails quando os custos de uso excederem um valor especificado. Se você tiver créditos mensais, configure alertas para o uso de um valor especificado. Para obter mais informações, veja [Configurar alertas de cobrança para suas assinaturas do Microsoft Azure](billing-set-up-alerts.md).

![Captura de tela de um email de alerta de cobrança](./media/billing-getting-started/billing-alert.png)

> [!NOTE]
> Este recurso ainda está em visualização e,portanto, verifique seu uso regularmente.

Talvez seja melhor usar a estimativa de custo da calculadora de preços como uma diretriz para seu primeiro alerta.

### <a name="understand-limits-and-quotas-for-your-subscription"></a>Entender os limites e as cotas da sua assinatura

Há limites padrão em cada assinatura para itens como o número de núcleos de CPU e endereços IP. Tenha cuidado com esses limites. Para saber mais, confira [Assinatura e limites de serviço, cotas e restrições do Azure](../azure-subscription-service-limits.md). Você pode solicitar um aumento do limite ou da cota [contatando o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="day-1-as-you-add-services"></a>Dia 1: Durante a adição de serviços

### <a name="review-the-estimated-cost-in-the-portal"></a>Examine o custo estimado no portal

Normalmente, quando você adiciona um serviço no portal do Azure, há um modo de exibição que mostra um custo estimado semelhante por mês. Por exemplo, quando você escolhe o tamanho de sua VM do Windows, vê o custo mensal estimado para as horas de computação:

![Exemplo: uma VM Windows A1 é estimada ao custo de US$ 66,96 por mês](./media/billing-getting-started/vm-size-cost.PNG)

### <a name="tags"></a>Adicionar marcas aos seus recursos para agrupar os dados de cobrança

Você pode usar marcas para agrupar os dados de cobrança no caso de serviços com suporte. Por exemplo, se você executa várias VMs para equipes diferentes, pode usar marcas para categorizar os custos por centro de custo (RH, marketing, finanças) ou ambiente (pré-produção, produção, teste). 

![Captura de tela que mostra a configuração de marcas no portal](./media/billing-getting-started/tags.PNG)

As marcas aparecem em vários modos de exibição de relatório diferentes. Por exemplo, elas são visíveis logo no [modo de exibição de análise de custo](#costs) e no [. csv de uso detalhado](#invoice-and-usage) após o primeiro período de cobrança.

Para obter mais informações, veja [Usando marcas para organizar os recursos do Azure](../azure-resource-manager/resource-group-using-tags.md).

### <a name="consider-enabling-cost-cutting-features-like-auto-shutdown-for-vms"></a>Considere a possibilidade de habilitar recursos de redução de custos, como desligamento automático de VMs

Dependendo do cenário, você pode configurar o desligamento automático para suas VMs no portal do Azure. Para saber mais, confira [Desligamento automático de máquinas virtuais usando o Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Captura de tela da opção de desligamento automático no portal](./media/billing-getting-started/auto-shutdown.PNG)

O desligamento automático não é igual a desligar a VM com as opções de energia padrão. O desligamento automático interrompe e desaloca suas VMs para parar as cobranças adicionais. Para saber mais, confira os estados de VM nas Perguntas frequentes sobre preços de [VMs Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [VMs Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).

Para obter mais recursos de redução de custos para os ambientes de desenvolvimento e teste, confira [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

## <a name="cost-reporting"></a> Dia 2: Depois do uso dos serviços, exiba o uso

### <a name="costs"></a>Verifique regularmente no portal o detalhamento de custos e a taxa de gravação

Depois de colocar seus serviços em funcionamento, verifique regularmente quanto eles estão custando. Você pode ver o gasto atual e a taxa de gravação no portal do Azure. 

1. Visite a [folha Assinaturas no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Selecione a assinatura que você deseja ver. Você pode só ter uma para selecionar.

3. Você deve ver o detalhamento de custo e a taxa de gravação na folha pop-up. Ele pode não ter suporte da sua oferta (um aviso seria exibido na parte superior). Aguarde 24 horas depois de adicionar um serviço de dados para preenchê-lo.
    
    ![Captura de tela da taxa de gravação e do detalhamento no portal do Azure](./media/billing-getting-started/burn-rate.PNG)

4. Talvez seja melhor fixar o modo de exibição no seu painel.

    ![Captura de tela da fixação de um modo de exibição no painel](./media/billing-getting-started/pin.PNG)

5. Clique em **Análise de custo** na lista à esquerda para ver a análise de custo por recurso.

    ![Captura de tela do modo de exibição de análise de custo no portal do Azure](./media/billing-getting-started/cost-analysis.PNG)

6. Você pode filtrar por propriedades diferentes, como [marcas](#tags), grupo de recursos e período de tempo. Clique em **Aplicar** para confirmar os filtros e **Baixar** para exportar o modo de exibição para um arquivo .csv (valores separado por vírgulas).

7. Clique em um recurso para ver o histórico de gastos e quanto ele custa a você por dia.

    ![Captura de tela da exibição do histórico de gastos no portal do Azure](./media/billing-getting-started/spend-history.PNG)

Recomendamos que você verifique os custos, consulte as estimativas vistas quando selecionou os serviços. Se os custos diferem bastante das estimativas, verifique novamente o plano de preços (VMs A1 x A0, por exemplo) que você selecionou para os seus recursos. 

#### <a name="view-costs-for-all-your-subscriptions-in-the-billing-blade"></a>Confira os custos de todas as suas assinaturas na folha Cobrança

Se você gerenciar várias assinaturas como o Administrador da conta, poderá ver o valor total e o detalhado da fatura de todas as assinaturas na [folha Cobrança](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade). 

<!-- Add screenshots of multiple subs each with billed usage -->

### <a name="turn-on-and-check-out-azure-advisor-recommendations"></a>Habilitar e conferir as recomendações do Azure Advisor

[O Azure Advisor](../advisor/advisor-overview.md) é um recurso de visualização que ajuda a reduzir os custos identificando recursos com baixa utilização. Habilite-o no portal do Azure:

![Captura de tela do botão Azure Advisor no portal do Azure](./media/billing-getting-started/advisor-button.PNG)

Em seguida, você pode obter recomendações práticas na guia **Custo** no painel do Advisor:

![Captura de tela de exemplo de recomendação de custo do Advisor](./media/billing-getting-started/advisor-action.PNG)

Para saber mais, veja [Advisor Cost recommendations](../advisor/advisor-cost-recommendations.md) (Recomendações de custo do Advisor).

### <a name="invoice-and-usage"></a> Baixe sua fatura e detalhes de uso após o primeiro período de cobrança

Após o primeiro período de cobrança, você pode baixar sua fatura em PDF (Portable Document Format) e os detalhes de uso em CSV (valores separados por vírgulas). Também é possível escolher que a fatura seja enviada para você. Esses arquivos ajudam a compreender o que é efetivamente cobrado depois de descontados impostos, deduções e créditos. Se você não tiver um método de pagamento associado à assinatura, esses arquivos podem não estar disponíveis para você. Para saber mais, confira [Como baixar sua fatura de cobrança e dados de uso diário do Azure](billing-download-azure-invoice-daily-usage-date.md) e [Entenda sua fatura do Microsoft Azure](billing-understand-your-bill.md).

![Captura de tela de uma fatura em .pdf](./media/billing-getting-started/invoice.png)

As marcas que você definiu anteriormente aparecem nos arquivos .csv de uso detalhado:

![Captura de tela que mostra as marcas no. csv de uso](./media/billing-getting-started/csv.png)

### <a name="billing-api"></a>API de Cobrança

Use nossa API de Cobrança para obter dados de uso programaticamente. Use a API RateCard junto com a API de Uso para obter seu uso cobrado. Para saber mais, confira [Obtenha informações sobre o consumo de recursos do Microsoft Azure](billing-usage-rate-card-overview.md).

## <a name="other-offers"></a>Recursos adicionais do EA, do CSP e do Sponsorship

Fale com seu gerente de conta ou parceiro do Azure para começar.

| Oferta | Recursos |
|-------------------------------|-----------------------------------------------------------------------------------|
| EA (Enterprise Agreement) | [Portal EA](https://ea.azure.com/), [documentos de ajuda](https://ea.azure.com/helpdocs) e [relatório do Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Programa do CSP (Provedor de Soluções na Nuvem) | Fale com seu provedor |
| Azure Sponsorship | [Portal do Sponsorship](https://www.microsoftazuresponsorships.com/) |

Se estiver gerenciando TI em uma grande organização, recomendamos a leitura de [Azure enterprise scaffold](../azure-resource-manager/resource-manager-subscription-governance.md) e [enterprise IT white paper](http://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (baixe o .pdf, somente em inglês).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se precisar de ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
