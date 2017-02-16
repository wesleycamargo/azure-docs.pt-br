---
title: "Solucionar problemas de inscrição do Azure | Microsoft Docs"
description: "Descreve como solucionar alguns problemas de inscrição no Azure."
services: 
documentationcenter: 
author: JiangChen79
manager: adpick
editor: 
tags: billing,top-support-issue
ms.assetid: a0907da1-cb2d-41d1-a97f-43841fabe355
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 01/24/2016
ms.author: cjiang
translationtype: Human Translation
ms.sourcegitcommit: 73c4ff6fdbde09dd08d1bcacf0ec87c7fa43f3b2
ms.openlocfilehash: 7ded94f432f8e61153ee8dfbf2508b288a4ad5fd


---
# <a name="i-cant-sign-up-for-azure"></a>Não consigo me inscrever no Azure
Se você não consegue se inscrever no Azure, há várias coisas que podem ser verificadas para solucionar o problema.

## <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>A barra de progresso trava na seção "Verificação de identidade por cartão"

Durante a inscrição do Azure, há uma seção chamada "Verificação de identidade por cartão". Se a barra de progresso travar:

![Capture a tela da seção Verificação de identidade por cartão travada durante a inscrição](./media/billing-troubleshoot-azure-sign-up-issues/identity-verification-hangs.PNG)

Esse problema ocorre quando os cookies de terceiros são bloqueados para seu navegador.

### <a name="suggestion"></a>Sugestão

1. Permita cookies de terceiros nas configurações do navegador.
  * No Edge, vá para Configurações-> Exibir configurações avançadas -> Cookies, selecione "Não bloquear cookies".
  * No Chrome, vá para Configurações-> Mostrar configurações avançadas -> Privacidade -> Configurações de conteúdo, desmarque "Bloquear cookies de terceiros e dados do site".
2. Atualize a página de inscrição do Azure e verifique se o problema foi resolvido.
3. Se a atualização não resolver o problema, feche e reinicie o navegador e tente novamente.

## <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>Nenhuma mensagem de texto ou chamada durante a verificação de conta de inscrição
* Verifique se o seu número de telefone pode receber SMS.
* Verifique novamente o número de telefone inserido, incluindo a seleção de código do país no menu suspenso.
* Certifique-se de que seu telefone possa receber mensagens de texto (SMS) se você usar "Enviar mensagem de texto", ou chamadas telefônicas, se você escolher a alternativa "Telefonar para mim".
* Se você usa um celular, verifique se tem uma conexão de telefone em boas condições.
* Aguarde até quatro minutos para que o sistema de mensagens entregue seu código de texto se você escolher a opção "Enviar mensagem de texto".
* Quando receber a mensagem de texto, insira o código na caixa de texto e clique no botão de verificação para continuar.

### <a name="suggestions"></a>Sugestões
* Se você não receber SMS (mensagens de texto) em seu telefone, use o método de verificação alternativo "Ligar para mim".
* Use outro número de telefone se a etapa de verificação de telefone falhar usando os métodos SMS e "Ligar para mim".
* Um número de telefone VOIP não pode ser usado para a verificação do telefone.

> [!NOTE]
> Você pode alterar o número de telefone preferencial posteriormente [atualizando as informações de seu perfil](billing-how-to-change-azure-account-profile.md).
>
>

## <a name="credit-card-declined-or-not-accepted"></a>Cartão de crédito recusado ou não aceito
Verifique se o método de pagamento que você está usando na inscrição tem suporte para pagamentos ou ativações do Azure.

* Não são aceitos cartões de débito/crédito virtuais e pré-pagos.
* Os provedores de cartão de crédito/débito aceitos variam de acordo com o país da conta.

### <a name="suggestion"></a>Sugestão
Para ver as causas comuns dos problemas de inscrição usando um cartão de crédito ou débito, confira [Seu cartão de crédito ou débito é recusado durante a inscrição do Azure](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>Não consigo ativar um plano de benefícios do Azure, por exemplo, MSDN, BizSpark, BizSparkPlus ou MPN
Verifique no canal do programa de benefícios se você está qualificado para o plano escolhido:

* MSDN
  * Verifique o status de qualificação na [página da sua conta MSDN](https://msdn.microsoft.com/subscriptions/manage/default.aspx).
  * Se não for possível verificar o status, entre em contato com o [Atendimento ao Cliente de Assinaturas MSDN](https://msdn.microsoft.com/subscriptions/contactus.aspx)
* MPN
  * Entre no [portal do MPN](https://mspartner.microsoft.com/en/us/Pages/Locale.aspx) e verifique o status da sua qualificação. Se tiver as [Competências de Plataforma de Nuvem](https://mspartner.microsoft.com/en/us/pages/membership/cloud-platform-competency.aspx)adequadas, você poderá estar qualificado para receber outros benefícios.
  * Se não for possível verificar seu status, contate o [suporte do MPN](https://mspartner.microsoft.com/en/us/Pages/Support/Premium/contact-support.aspx).
* Bizpark
  * Entre no [portal do BizSpark](https://www.microsoft.com/bizspark/default.aspx#start-two) e verifique o status de qualificação para BizSpark e BizSpark Plus.
  * Se não for possível verificar o status, entre em contato com o Suporte do Bizspark enviando um email para [Entrar em contato com a equipe do BizSpark](mailto:bizspark@microsoft.com?subject=BizSpark%20Support&body=Thank%20you%20for%20contacting%20BizSpark.%20Please%20provide%20as%20much%20of%20the%20following%20information%20as%20possible,%20as%20it%20will%20help%20expedite%20our%20response%20to%20you.%0aContact%20name:%0aStartup%20name:%0aMicrosoft%20Account/Live%20ID:%0aSpecific%20description%20of%20issue%20experienced%20or%20question:%0a%0aThank%20you,%0a%0aThe%20BizSpark%20Team)

### <a name="suggestion"></a>Sugestão
Se você tentar ativar uma nova assinatura de benefícios e incorrer em algum erro durante a inscrição, verifique se a configuração da assinatura foi concluída na [página de assinatura do Azure](http://account.windowsazure.com/Subscriptions). Pode demorar alguns minutos para que sua assinatura apareça como ativa. Quando sua assinatura estiver ativada, você receberá um email. Se o status de sua assinatura permanecer pendente por mais de quatro minutos, [entre em contato com o suporte do Azure](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409) para obter ajuda.

## <a name="cant-activate-new-azure-in-open-subscription"></a>Não consigo ativar uma nova assinatura do Azure via Open
Você deve ter uma chave OSA válida com pelo menos um token do Azure via Open associado a ela para ativar uma nova assinatura do Azure via Open.

### <a name="suggestion"></a>Sugestão
Se você não tiver uma chave OSA, entre em contato com um dos Parceiros Microsoft listados na [Microsoft Pinpoint](http://pinpoint.microsoft.com/).

## <a name="cant-activate-azure-free-trial"></a>Não consigo ativar uma Avaliação Gratuita do Azure
Você já usou uma assinatura do Azure anteriormente? O Contrato de Termos de Uso do Azure limita a ativação gratuita da avaliação para usuários que sejam novos no Azure. Se você já teve qualquer outro tipo de assinatura do Azure, não poderá ativar uma avaliação gratuita.

### <a name="suggestion"></a>Sugestão
* Se você já ativou uma assinatura do Azure anteriormente, e a ativação de avaliação gratuita falhar, considere usar uma assinatura pré-paga.
* Verifique se você está qualificado para uma oferta de benefício. Saiba mais na [página de detalhes da oferta do Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/). Os planos de benefícios exigem pré-requisitos específicos.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.
Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.



<!--HONumber=Jan17_HO1-->


