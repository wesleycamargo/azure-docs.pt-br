<properties 
	pageTitle="Análise de armazenamento e cobrança" 
	description="Saiba como você pode aproveitar a análise de armazenamento para compreender melhor a sua fatura de serviço de armazenamento" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>


# Análise de armazenamento e cobrança

Este tópico discute o custo de análise de armazenamento e como usar métricas e dados de log para compreender a sua fatura de serviços de armazenamento.


## Custo de análise de armazenamento

A análise de armazenamento é habilitada por um proprietário de conta de armazenamento; ela não é habilitada por padrão. Todos os dados de métricas são gravados pelos serviços de uma conta de armazenamento. Como resultado, cada operação de gravação executada pela análise de armazenamento é faturável. Além disso, a quantidade de armazenamento usado por dados de métrica também é faturável.

As seguintes ações executadas pela análise de armazenamento são faturáveis:

- Solicitações para criar blobs para registro em log

- Solicitações para criar entidades de tabela para métricas

Se você configurou uma política de retenção de dados, você não é cobrado para excluir transações quando a análise de armazenamento exclui dados antigos de log e métricas. No entanto, excluir as transações de um cliente são faturáveis. Para obter mais informações sobre as políticas de retenção, consulte [Definindo uma política de retenção de dados de análise de armazenamento](https://msdn.microsoft.com/library/azure/hh343263.aspx).

## Noções básicas sobre solicitações faturáveis

Todas as solicitações feitas ao serviço de armazenamento da conta é faturável ou não faturável. A análise de armazenamento registra cada solicitação individual feita a um serviço, incluindo uma mensagem de status que indica como a solicitação foi processada. Da mesma forma, a análise de armazenamento armazena métricas para um serviço e as operações de API desse serviço, incluindo os percentuais e a contagem de determinadas mensagens de status. Juntos, esses recursos podem ajudá-lo a analisar suas solicitações faturáveis, fazer melhorias em seu aplicativo e diagnosticar problemas com solicitações de serviços. Para obter mais informações sobre a cobrança, consulte [Noções básicas sobre cobrança no armazenamento do Azure - largura de banda, transações e capacidade](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

Ao analisar os dados de análise de armazenamento, você pode usar as tabelas no tópico [Operações registradas de análise de armazenamento e mensagens de Status](https://msdn.microsoft.com/library/azure/hh343260.aspx) para determinar quais solicitações são faturáveis. Em seguida, você pode comparar seus logs e dados de métricas para as mensagens de status para ver se houve cobrança por uma determinada solicitação. Você também pode usar as tabelas no tópico anterior para investigar a disponibilidade de um serviço de armazenamento ou operação de API individual.

## Próximas etapas
[Operações registradas de análise de armazenamento e mensagens de Status](https://msdn.microsoft.com/library/azure/hh343260.aspx)

[Sobre o registro em log da análise de armazenamento](https://msdn.microsoft.com/library/azure/hh343262.aspx)

[Sobre as métricas de análise de armazenamento](../storage-about-analytics-metrics.md) 

<!--HONumber=47-->
