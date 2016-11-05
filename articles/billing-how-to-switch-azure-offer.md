---
title: Trocar a assinatura do Azure por outra oferta | Microsoft Docs
description: Saiba mais sobre como alterar sua assinatura do Azure e mude por uma oferta diferente usando o portal de gerenciamento de assinaturas
services: ''
documentationcenter: ''
author: genlin
manager: mbaldwin
editor: ''
tags: billing,top-support-issue

ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: genli

---
# <a name="switch-your-azure-subscription-to-another-offer"></a>Trocar a assinatura do Azure por outra oferta
Como um cliente [Pré-pago](https://azure.microsoft.com/offers/ms-azr-0003p/), você poderá alterar sua assinatura do Azure para outra oferta no [Centro de Contas](https://account.windowsazure.com/Subscriptions). Por exemplo, você pode usar esse recurso para aproveitar os [créditos mensais para assinantes do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Se você estiver em [Avaliação Gratuita](https://azure.microsoft.com/free/), saiba como [fazer upgrade para Pré-pago](billing-buy-sign-up-azure-subscription.md#upgrade-azure-free-trial-to-pay-as-you-go).

#### <a name="what's-supported:"></a>O que tem suporte:
| Da | Para |
| --- | --- |
| [Pré-paga](https://azure.microsoft.com/offers/ms-azr-0003p/) |[Desenvolvimento/Teste pré-pago](https://azure.microsoft.com/offers/ms-azr-0023p/) |
| [Pré-paga](https://azure.microsoft.com/offers/ms-azr-0003p/) |[Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |
| [Pré-paga](https://azure.microsoft.com/offers/ms-azr-0003p/) |[Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |
| [Pré-paga](https://azure.microsoft.com/offers/ms-azr-0003p/) |[Plataformas MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/) |
| [Pré-paga](https://azure.microsoft.com/offers/ms-azr-0003p/) |[Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |
| [Pré-paga](https://azure.microsoft.com/offers/ms-azr-0003p/) |[Visual Studio Enterprise (Bizspark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |

> [!NOTE]
> Para obter outras alterações da oferta, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
> 
> 

## <a name="switch-subscription-offer"></a>Alternar oferta de assinatura
> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Switch-to-a-different-Azure-offer/player]
> 
> 

1. Entre no [Centro de Contas do Azure](https://account.windowsazure.com/Subscriptions).
2. Selecione sua assinatura Pré-paga.
3. Clique em **Alterar para outra oferta**. O botão só estará disponível se você estiver em Pré-pago e com o primeiro período de cobrança.
   
   ![switchbutton](./media/billing-how-to-switch-azure-offer/switchbutton.png)
4. **Selecione a oferta desejada** da lista de ofertas para as quais sua assinatura pode ser alternada. Essa lista varia com base nas associações em que sua conta está associada. Se nada estiver disponível, verifique a [lista de ofertas disponíveis para as quais você pode alternar](#whats-supported) e confirme se você tem as associações corretas. 
   
   ![selectoffer](./media/billing-how-to-switch-azure-offer/selectoffer.png)
5. Dependendo da oferta para qual esteja trocando, é provável que você veja uma observação sobre o impacto da troca. Examine a lista cuidadosamente e siga as instruções antes de prosseguir.
   
   ![thingstonote](./media/billing-how-to-switch-azure-offer/thingstonote.png)
6. Você pode renomear sua assinatura. Por padrão, podemos defini-la com o nome da nova oferta. Clique em **Alterar Oferta** para concluir o processo.
   
   ![confirmpage](./media/billing-how-to-switch-azure-offer/confirmpage.png)
7. Sucesso! Sua assinatura foi trocada pela nova oferta.

## <a name="why-can't-i-switch-offers?"></a>Por que não é possível alternar ofertas?
Talvez você não veja **Alterar para outra oferta** se:

* Você não está em [Pré-pago](https://azure.microsoft.com/offers/ms-azr-0003p/). No momento, apenas assinaturas em Pré-pago podem ser alternadas para outra oferta.
  
  * Se você estiver em [Avaliação Gratuita](https://azure.microsoft.com/free/), saiba como [fazer upgrade para Pré-pago](billing-buy-sign-up-azure-subscription.md#upgrade-azure-free-trial-to-pay-as-you-go).
  * Para alternar a oferta de uma assinatura diferente, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
* Você ainda está em seu primeiro período de cobrança. Você deve aguardar até que seu primeiro período de cobrança seja concluído antes de poder alternar as ofertas.

Talvez você veja **Não há ofertas disponíveis em seu país ou região no momento** se:

* Você não está qualificado para outras alterações de oferta. Consulte a [lista de ofertas disponíveis para as quais você pode alternar](#whats-supported).

## <a name="what-does-switching-azure-offers-do-to-my-service-and-billing?"></a>O que a troca das ofertas do Azure oferece ao meu serviço e à cobrança?
Aqui estão os detalhes do que acontece quando você alterna entre os planos do Azure no Centro de Contas.

### <a name="access-to-services"></a>Acesso aos serviços
Não há nenhum tempo de inatividade do serviço para os usuários associados à assinatura. No entanto, a nova oferta poderá ter restrições. Por exemplo, algumas ofertas proíbem o uso de produção, portanto, seria necessário mover os recursos de produção para outra assinatura.

### <a name="billing"></a>Cobrança
No dia da alteração, uma fatura é gerada para todos os encargos pendentes. Em seguida, sua assinatura é cobrada pelos termos de preços da nova oferta. O vencimento da cobrança da assinatura será alterado para a data da alteração da oferta. Os dados de uso e de cobrança anteriores à alteração da oferta não serão mantidos, portanto, recomendamos que você baixe uma cópia antes da troca.

> [!NOTE]
> Devido às restrições relacionadas à cobrança, alterações de oferta não são possíveis no primeiro ciclo de cobrança após a criação de uma assinatura.
> 
> 

## <a name="can-i-migrate-from-pay-as-you-go-to-[cloud-solution-provider](https://partner.microsoft.com/solutions/cloud-reseller-overview)-(csp)-or-[enterprise-agreement](https://azure.microsoft.com/pricing/enterprise-agreement/)-(ea)?"></a>Posso migrar de Pré-pago para [Provedor de Soluções na Nuvem](https://partner.microsoft.com/Solutions/cloud-reseller-overview) (CSP) ou [Contrato Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/) (EA)?
Atualmente, não oferecemos suporte à alteração de oferta para CSP ou EA no Centro de Contas. Para mover sua assinatura existente para o EA, peça ao seu administrador de registro para adicionar sua conta ao EA. Depois disso, você receberá um email de convite. Ao seguir as instruções para aceitar o convite, as assinaturas serão automaticamente movidas para o Contrato Enterprise. Não há opções para mover uma assinatura existente para o CSP hoje.

## <a name="next-steps"></a>Próximas etapas
* Saiba como [gerenciar funções de administrador](billing-add-change-azure-subscription-administrator.md) para sua assinatura
* Acompanhe o uso ao [baixar dados de uso e nota fiscal](billing-download-azure-invoice-daily-usage-date.md)

## <a name="need-help?-contact-support."></a>Precisa de ajuda? Entre em contato com o suporte.
Se ainda tiver mais dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.

<!--HONumber=Oct16_HO2-->


