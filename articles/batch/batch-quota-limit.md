<properties
	pageTitle="Cotas de serviço e limites do Lote | Microsoft Azure"
	description="Saiba mais sobre cotas, limites e restrições para usar o serviço do Lote do Azure"
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/26/2015"
	ms.author="danlep"/>



# Cotas e limites para o serviço do Lote do Azure

Este artigo lista o padrão e os limites máximos de determinados recursos que você pode usar com o serviço do Lote do Azure. A maioria destes limites são cotas que o Azure aplica à sua assinatura ou contas do Lote.

Se você planeja executar cargas de trabalho em Lote de produção, talvez seja necessário aumentar uma ou mais cotas para acima do valor padrão. Se desejar aumentar a cota, abra uma solicitação de suporte online ao cliente de forma gratuita.

>[AZURE.NOTE]Uma cota é um limite de crédito, não uma garantia de capacidade. Se você precisar de capacidade em larga escala, entre em contato com o suporte do Azure.

## Cotas de assinatura
Recurso|Limite padrão|Limite máximo
---|---|---
Contas do Lote por região e assinatura|1|50

## Cotas para conta do Lote
[AZURE.INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## Outros limites
Recurso|Limite máximo
---|---
Tarefas por nó de computação|4 vezes o número de núcleos de nó

## Exibir cotas do Lote

Exibir suas cotas de conta do Lote no [portal do Azure](https://portal.azure.com).

1. No portal de visualização, clique em **contas do Lote** e, em seguida, no nome da sua conta do Lote.

2. Na folha da conta, clique em **Configurações** > **Propriedades**.

	![Cotas para conta do Lote][account_quotas]

3. Na folha de **Propriedades**, revise as cotas que atualmente se aplicam à conta do Lote.

## Aumentar uma cota

Siga as etapas a seguir para solicitar um aumento de cota no portal do Azure (você também pode solicitar um aumento no [portal clássico do Azure](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)).

1. No painel do portal de visualização, clique em **Ajuda + suporte**.

2. Clique em **Criar solicitação de suporte > Básico**.

3. Na folha **Básico**, faça o seguinte:

	a. Em **Tipo de problema**, selecione **Cota**.

	b. Selecione sua assinatura.

	c. Em **Serviço**, selecione **Serviço do Lote**.

	d. Em **Plano de suporte**, selecione **Plano de suporte do Azure - Desenvolvedor**.

	Clique em **Próximo**.

4. Na folha **Problema**, faça o seguinte:

	a. Em **Tipo de problema**, selecione **Lote**.

	b. Em **Detalhes**, liste a cota ou cotas que você deseja alterar em uma conta específica e os novos limites que quiser.

	Clique em **Próximo**.

5. Na folha **Informações de contato**, coloque seus detalhes de contato e clique em **Próximo**.

6. Clique em **Criar** para enviar a nova solicitação de suporte.

O suporte do Azure entrará em contato com você. A conclusão do pedido pode levar até 2 dias úteis.

## Tópicos relacionados

* [Criar e gerenciar uma conta do Lote do Azure](batch-account-create-portal.md)

* [Visão geral dos recursos do Lote do Azure](batch-api-basics.md)

* [Assinatura do Azure e limite de serviços, cotas e restrições](../azure-subscription-service-limits.md)

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.PNG

<!---HONumber=AcomDC_1203_2015-->