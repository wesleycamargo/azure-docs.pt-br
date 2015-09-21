<properties 
	pageTitle="Logs de Operação | Microsoft Azure" 
	description="Logs de Operação do Stream Analytics" 
	keywords="big data analytics,cloud service,internet of things,managed service,stream processing,streaming analytics,streaming data"
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="09/09/2015" 
	ms.author="jeffstok"/>

# Logs de operação

Todos os serviços do Azure fornecem mensagens em log operacionais aos usuários para registrar detalhes relacionados às operações de gerenciamento. No Stream Analytics, essas informações podem ser usadas para fins de depuração, como exibir o status do trabalho, o progresso do trabalho e as mensagens de falha para rastrear o andamento de um trabalho ao longo do tempo, do início do processamento até a saída.

## Localizando logs de operação

Os Logs de Operação podem ser acessados de duas maneiras:

- Painel do trabalho do Stream Analytics  
- Serviços de Gerenciamento no Portal do Azure  

## Painel do trabalho do Stream Analytics

Um link para os logs correspondentes de um trabalho do Stream Analytics é exibido na guia Painel do trabalho. Se você clicar nesse link, ele definirá os filtros para mostrar os últimos logs desse trabalho específico.

  ![Selecionar Serviços de Gerenciamento](./media/stream-analytics-operation-logs/01-stream-analytics-operation-logs.png)

## Serviços de Gerenciamento no Portal do Azure

Para navegar manualmente até os Logs de Operação do Stream Analytics e outros serviços no Portal do Azure:

1.	Clique em **Serviços de Gerenciamento** no [Portal do Azure](https://manage.windowsazure.com).
2.	Selecione **Stream Analytics** para **Tipo** e o nome do trabalho para **Nome do Serviço**.  

    ![Selecionar Stream Analytics](./media/stream-analytics-operation-logs/02-stream-analytics-operation-logs.png)

## Usar logs de operação

Você pode filtrar por Intervalo de Tempo e Status para exibir os logs do seu trabalho.

Clique no botão **Detalhes**, na parte inferior da janela, para exibir mais detalhes sobre um evento selecionado.

  ![Selecionar Detalhes](./media/stream-analytics-operation-logs/03-stream-analytics-operation-logs.png)

## Depurando um trabalho com falha

Clique no ícone Pesquisa e digite 'com falha'. Isso fornece um resultado de todos os logs com falhas.

  ![Depurando um trabalho com falha](./media/stream-analytics-operation-logs/04-stream-analytics-operation-logs.png)

Você pode selecionar qualquer uma das falhas e clicar nos **Detalhes** para saber mais sobre o erro. Algumas mensagens de erro também fornecem informações sobre como a minimizar o problema.

  ![Detalhes da Operação](./media/stream-analytics-operation-logs/05-stream-analytics-operation-logs.png)

Caso precise contatar o [Suporte](http://azure.microsoft.com/support/options/) ou fornecer informações à equipe pelo [fórum do MSDN](https://social.msdn.microsoft.com/Forums/pt-BR/home?forum=AzureStreamAnalytics), observe os Detalhes da Operação, especificamente a **ID de Correlação**.

## Obter ajuda
Para obter mais assistência, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/pt-BR/home?forum=AzureStreamAnalytics)

## Próximas etapas

- [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
- [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
- [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
- [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=Sept15_HO2-->