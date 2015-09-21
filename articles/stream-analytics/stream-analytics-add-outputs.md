<properties 
	pageTitle="Adicionar saídas | Microsoft Azure" 
	description="Segmento do roteiro de aprendizagem Adicionar saídas."
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

# Adicionar saídas

Os trabalhos do Stream Analytics do Azure podem ser conectados a uma ou mais saídas, o que define uma conexão com um coletor de dados existente. Como o trabalho do Stream Analytics processa e transforma dados de entrada, um fluxo de eventos de saída são gravados na saída do trabalho.

As saídas do Stream Analytics podem ser usadas para dar origem a painéis ou alertas em tempo real, disparar fluxos de trabalho de downstream ou simplesmente arquivar dados para processamento em lote posteriormente. O Stream Analytics integra-se perfeitamente a vários serviços do Azure, que são documentados em detalhes aqui.

Para adicionar uma saída ao trabalho do Stream Analytics:

1. No trabalho do Stream Analytics, clique em **Saídas** e em **Adicionar Saída**.

    ![Adicionar saídas](./media/stream-analytics-add-outputs/1-stream-analytics-add-outputs.png)

2. Especifique o tipo de saída:

    ![Escolher Tipo de Dados](./media/stream-analytics-add-outputs/2-stream-analytics-add-outputs.png)

3. Forneça um nome amigável para essa saída na caixa **Alias de Saída**. Esse nome pode ser usado na consulta do seu trabalho posteriormente para fazer referência à saída.
    
    Preencha o restante das propriedades de conexão necessárias para se conectar à saída. Esses campos variam de acordo com o tipo de saída e são definidos em detalhes aqui.

    ![Adicionar propriedades](./media/stream-analytics-add-outputs/3-stream-analytics-add-outputs.png)

4. Dependendo do tipo de saída, talvez seja necessário especificar como os dados são serializados ou formatados. As configurações específicas de serialização para cada tipo de saída estão documentadas aqui.

    Preencha o restante das propriedades de conexão necessárias para se conectar à fonte de dados. Esses campos variam de acordo com o tipo de entrada e fonte e são definidos em detalhes [aqui](stream-analytics-create-a-job.md.)

    ![Adicionar hub de eventos](./media/stream-analytics-add-outputs/4-stream-analytics-add-outputs.png)

## Obter ajuda
Para obter mais assistência, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/pt-BR/home?forum=AzureStreamAnalytics)

## Próximas etapas

- [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
- [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
- [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
- [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=Sept15_HO2-->