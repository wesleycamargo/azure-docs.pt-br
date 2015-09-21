<properties 
	pageTitle="Executar um trabalho | Microsoft Azure" 
	description="Execute um trabalho de segmento de roteiro de aprendizagem."
	documentationCenter=""
	services="stream-analytics"
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

# Executar um trabalho

Quando uma entrada de trabalho, consulta e saída foram especificadas, você pode iniciar o trabalho do Stream Analytics.

Para iniciar o trabalho:

1.	No painel de trabalho, clique em Iniciar na parte inferior da página.

    ![Botão Iniciar](./media/stream-analytics-run-a-job/1-stream-analytics-run-a-job.png)

2.	Na caixa de diálogo que aparecerá, especifique um valor de **Iniciar Saída** para determinar quando esse trabalho começará a produzir uma saída. A configuração padrão para trabalhos que não foram iniciados anteriormente é **Hora de Início do Trabalho**, que o significa que o trabalho começará imediatamente o processamento de dados. Você também pode especificar um tempo **Personalizado** no passado (para o consumo de dados históricos) ou no futuro (para atrasar o processamento até um momento futuro). Para os casos em que um trabalho foi anteriormente iniciado e interrompido, a opção **Hora da Última Interrupção** está disponível para retomar o trabalho da última hora de saída e evitar a perda de dados.

    ![Hora de Início](./media/stream-analytics-run-a-job/2-stream-analytics-run-a-job.png)

3.	Clique no botão de seleção na parte inferior da caixa de diálogo. O status do trabalho será alterado para Iniciando e logo mudará para Em execução quando o trabalho for iniciado. Você pode monitorar o progresso da operação de Início no Hub de Notificação:

    ![Progresso](./media/stream-analytics-run-a-job/3-stream-analytics-run-a-job.png)

## Obter ajuda
Para obter mais assistência, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/pt-BR/home?forum=AzureStreamAnalytics)

## Próximas etapas

- [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
- [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
- [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
- [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=Sept15_HO2-->