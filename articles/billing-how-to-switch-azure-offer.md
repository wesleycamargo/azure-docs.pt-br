<properties
	pageTitle="Trocar a assinatura do Azure por outra oferta | Microsoft Azure"
	description="Saiba mais sobre como alterar sua assinatura do Azure e mude por uma oferta diferente usando o portal de gerenciamento de assinaturas"
	services=""
	documentationCenter=""
	authors="genlin"
	manager="msmbaldwin"
	editor=""
	tags="billing,top-support-issue"/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/06/2016"
	ms.author="genli"/>

# Trocar a assinatura do Azure por outra oferta

## O que você precisa saber

Você [pode ser elegível](#where-is-the-button) para mudar sua assinatura para uma oferta mais favorável sem nenhum tempo de inatividade do serviço. Veja abaixo os caminhos atualmente com suporte no [Centro de Contas](https://account.windowsazure.com/Subscriptions). Clique nos links para saber mais sobre os requisitos.

| Da | Para |
|-------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
| [Pré-paga](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Desenvolvimento/Teste pré-pago](https://azure.microsoft.com/offers/ms-azr-0023p/) |
| [Pré-paga](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Pré-paga de 12 meses](https://azure.microsoft.com/offers/ms-azr-0026p/) |
| [Pré-paga](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |
| [Pré-paga](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |
| [Pré-paga](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Plataformas MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/) |
| [Pré-paga](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |
| [Pré-paga](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Visual Studio Enterprise (Bizspark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |
> [AZURE.NOTE] Se você estiver em avaliação gratuita, saiba como [atualizar para Pré-pago](billing-buy-sign-up-azure-subscription.md#upgrade-azure-free-trial-to-pay-as-you-go).

> Se você já se inscreveu para sua assinatura recentemente, você deve aguardar até que seu primeiro período de cobrança seja concluído antes de alterar ofertas.

> Para obter outras alterações da oferta, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
	
## Guia para trocar sua assinatura por outra oferta

> [AZURE.VIDEO switch-to-a-different-azure-offer]

1.	Entre no [Centro de Contas do Azure](https://account.windowsazure.com/Subscriptions).
2.	Selecione a assinatura para alterar.
3.	Clique em **Alterar para outra oferta**. Descubra o motivo, [caso você não veja o botão](#where-is-the-button).

	![switchbutton](./media/billing-how-to-switch-azure-offer/switchbutton.png)
	
4.	Serão apresentadas todas as ofertas para as quais sua assinatura poderá ser alterada. Essa lista variará com base nas associações de sua conta e também pela oferta atual. Você pode clicar no link **Saiba mais** em cada oferta para obter informações detalhadas sobre ela. Clique na oferta desejada para prosseguir para a próxima etapa.

	![selectoffer](./media/billing-how-to-switch-azure-offer/selectoffer.png)
5.	Dependendo da oferta para qual esteja trocando, é provável que você veja uma observação sobre o impacto da troca. Examine a lista cuidadosamente e siga as instruções antes de prosseguir.

	![thingstonote](./media/billing-how-to-switch-azure-offer/thingstonote.png)
6.	Você pode renomear sua assinatura. Por padrão, podemos defini-la com o nome da nova oferta. Clique em **Alterar Oferta** para concluir o processo.

	![confirmpage](./media/billing-how-to-switch-azure-offer/confirmpage.png)
7.	Sucesso! Sua assinatura foi trocada pela nova oferta.

## Perguntas frequentes (FAQ)

### Para quais trocas de oferta vocês oferecem suporte?

Consulte a lista de [ofertas disponíveis para as quais você pode trocar](#what-you-need-to-know).

### Onde está o botão?

Talvez você não veja **Alterar para outra oferta** se:

- Você ainda estiver em seu primeiro período de cobrança; você deve aguardar até que seu primeiro período de cobrança seja concluído antes de poder alterar ofertas.

- Você não é elegível para outras alterações de oferta. Verifique a lista de [ofertas disponíveis para as quais você pode alterar](#what-you-need-to-know) para verificar se há suporte para a alteração de oferta desejada.

### A troca de ofertas afeta meu serviço de alguma forma?

Não há tempo de inatividade do serviço. No entanto, a nova oferta poderá ter restrições. Por exemplo, algumas ofertas proíbem o uso em produção. Dessa forma, você teria que mover os recursos de produção para outra assinatura.

### A troca de ofertas afeta os administradores de serviço de alguma forma? 

Nenhum usuário associado à assinatura será afetado.

### Quais alterações relacionadas à cobrança posso esperar ao alterar as ofertas?

No dia da alteração, será gerada uma fatura para todos os encargos pendentes. Em seguida, sua assinatura será cobrada de acordo com os termos de preços da nova oferta. O vencimento da cobrança da assinatura será alterado para a data da alteração da oferta. Os dados de uso e de cobrança anteriores à alteração da oferta não serão mantidos, portanto, recomendamos que você os baixe antes da troca.

> [AZURE.NOTE] Devido às restrições relacionadas à cobrança, alterações de oferta não são possíveis no primeiro ciclo de cobrança após a criação de uma assinatura.

### Posso usar esse mecanismo para migrar da Web direto para o CSP ([Provedor de Soluções na Nuvem](https://partner.microsoft.com/Solutions/cloud-reseller-overview)) ou para uma oferta de EA ([Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/))?

Esse mecanismo pode ser usado para trocar somente de e para as ofertas Web Direct. Para mover sua assinatura existente para o EA, peça ao seu administrador de registro para adicionar sua conta ao EA. Depois disso, você receberá um email de convite. Ao seguir as instruções para aceitar o convite, as assinaturas serão automaticamente movidas para o Enterprise Agreement. Atualmente, não há uma opção para mover uma assinatura Web Direct existente para o CSP.

## Próximas etapas

- Saiba como [gerenciar funções de administrador](billing-add-change-azure-subscription-administrator.md) para sua assinatura

- Acompanhe o uso ao [baixar dados de uso e nota fiscal](billing-download-azure-invoice-daily-usage-date.md)

> [AZURE.NOTE] Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.

<!---HONumber=AcomDC_0914_2016-->