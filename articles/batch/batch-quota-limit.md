<properties
	pageTitle="Cotas de serviço e limites do Lote | Microsoft Azure"
	description="Saiba mais sobre cotas, limites e restrições para usar o serviço do Lote do Azure"
	services="batch"
	documentationCenter=""
	authors="mmacy"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/10/2016"
	ms.author="marsma"/>

# Cotas e limites para o serviço do Lote do Azure

Como com outros serviços do Azure, há limites em determinados recursos associados ao serviço de Lote. Muitos desses limites são cotas padrão aplicadas pelo Azure no nível da assinatura ou da conta. Este artigo discute esses padrões e como você pode solicitar aumentos de cotas.

Se você planeja executar cargas de trabalho de produção em Lote, talvez seja necessário aumentar uma ou mais cotas para acima do padrão. Se desejar aumentar a cota, você poderá abrir uma [solicitação de atendimento ao cliente](#increase-a-quota) online gratuitamente.

>[AZURE.NOTE] Uma cota é um limite de crédito, não uma garantia de capacidade. Se você precisar de capacidade em larga escala, entre em contato com o suporte do Azure.

## Cotas de assinatura
**Recurso**|**Limite padrão**|**Limite máximo**
---|---|---
Contas do Lote por região e assinatura | 1 | 50

## Cotas para conta do Lote
[AZURE.INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## Outros limites
**Recurso**|**Limite máximo**
---|---
[Tarefas simultâneas](batch-parallel-node-tasks.md) por nó de computação | 4 vezes o número de núcleos de nó
[Aplicativos](batch-application-packages.md) por conta do Lote | 20
Pacotes de aplicativos por aplicativo | 40
Tamanho do pacote de aplicativos (cada) | Aproximadamente 195 GB<sup>1</sup>

<sup>1</sup> O limite do Armazenamento do Azure para o tamanho máximo do blob de blocos

## Exibir cotas do Lote

Exibir suas cotas de conta do Lote no [portal do Azure][portal].

1. No portal, clique em **contas do Lote** e, em seguida, no nome da sua conta do Lote.

2. Na folha da conta, clique em **Todas as configurações** > **Propriedades**.

	![Cotas para conta do Lote][account_quotas]

3. A folha **Propriedades** exibe as cotas atualmente aplicadas à conta do lote.

## Aumentar uma cota

Siga as etapas abaixo para solicitar um aumento de cota usando o [Portal do Azure][portal].

1. Selecione o bloco **Ajuda + suporte** em seu painel do portal ou o ponto de interrogação (**?**) no canto superior direito do portal.

2. Selecione **Nova solicitação de suporte** > **Básico**.

3. Na folha **Fundamentos**:

	a. **Tipo de problema** > **Cota**

	b. Selecione sua assinatura.

	c. **Tipo de cota** > **Lote**

	d. **Plano de suporte** > **Suporte de cota - Incluído**

	Clique em **Próximo**.

4. Na folha **Problema**:

	a. Selecione uma **Gravidade** de acordo com o [impacto nos negócios][support_sev].

	b. Em **Detalhes**, especifique cada cota que você deseja alterar, o nome da conta do Lote e o novo limite.

	Clique em **Próximo**.

5. Na folha **Informações de contato**:

	a. Selecione um **método de contato preferencial**.

	b. Verifique e insira os detalhes de contato necessários.

	Clique em **Criar** para enviar a solicitação de suporte.

Depois que a solicitação de suporte foi enviada, o suporte do Azure entrará em contato com você. Observe que a conclusão do pedido pode levar até dois dias úteis.

## Tópicos relacionados

* [Criar e gerenciar uma conta do Lote do Azure](batch-account-create-portal.md)

* [Visão geral dos recursos do Lote do Azure](batch-api-basics.md)

* [Assinatura do Azure e limite de serviços, cotas e restrições](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: http://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.PNG

<!---HONumber=AcomDC_0615_2016-->