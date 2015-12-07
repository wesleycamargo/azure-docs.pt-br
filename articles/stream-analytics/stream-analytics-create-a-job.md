<properties 
	pageTitle="Criar um trabalho | Microsoft Azure" 
	description="Criar um trabalho para o Stream Analytics | segmento do roteiro de aprendizagem."
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
	ms.date="11/23/2015" 
	ms.author="jeffstok"/>

# Criar um trabalho de análise de fluxo

O recurso de nível superior no Stream Analytics do Azure é um Trabalho do Stream Analytics. Ele consiste em uma ou mais fontes de dados de entrada, uma consulta que expressa a transformação de dados, e um ou mais destinos de saída nos quais os resultados são gravados.

Para começar a usar o Stream Analytics, crie um novo trabalho do Stream Analytics. Observe que essa ação não terá implicações de cobrança até que o trabalho seja iniciado.

1.  Entre no [portal do Microsoft Azure](http://manage.windowsazure.com) online ou no portal de visualização do Azure.
2.  No portal do Azure: **clique em Novo**, em **Serviços de Dados**, em **Stream Analytics** e em **Criação Rápida**.

    ![Assistente de Criação Rápida.](./media/stream-analytics-create-a-job/1-stream-analytics-create-a-job.png)

    No portal de visualização do Azure: clique em Novo, em Dados + Análise e em Stream Analytics do Azure.

    ![Criar Trabalho no Portal do Azure](./media/stream-analytics-create-a-job/4-stream-analytics-create-a-job.png)

3.  Especifique as configurações desejadas para o trabalho do Stream Analytics.
	- Na caixa **Nome do Trabalho**, digite um nome para identificar o trabalho do Stream Analytics. Quando o **Nome do Trabalho** for validado, uma marca de seleção verde será exibida na caixa Nome do Trabalho. O **Nome do Trabalho** pode conter apenas caracteres alfanuméricos e o caractere “-”, e deve ter entre 3 e 63 caracteres.
	- Use **Região** no portal do Azure ou **Local** no portal de visualização do Azure para especificar a localização geográfica onde você deseja executar o trabalho.
	- Se estiver usando o portal do Azure, selecione ou crie uma conta de armazenamento para usar como a **Conta de Armazenamento de Monitoramento Regional**. Essa conta de armazenamento é usada para armazenar dados de monitoramento de todos os trabalhos do Stream Analytics nessa região.
	- Se estiver usando o portal de visualização do Azure, especifique um **Grupo de Recursos** novo ou existente para armazenar recursos relacionados para seu aplicativo.

4.  Depois de configurar as novas opções de trabalho do Stream Analytics, clique em **Criar Trabalho do Stream Analytics**. Pode levar alguns minutos para que o trabalho do Stream Analytics seja criado. Para verificar o status, você pode monitorar o progresso no Hub de notificações.

    ![Hub de notificações](./media/stream-analytics-create-a-job/2-stream-analytics-create-a-job.png)

    ![Criar Trabalho no Portal de Visualização do Azure](./media/stream-analytics-create-a-job/5-stream-analytics-create-a-job.png)

5.  O novo trabalho será mostrado com um status de **Criado**. Observe que o botão **Iniciar** está desabilitado. Você deve configurar a entrada, a consulta e a saída do trabalho antes de iniciá-lo.

    ![Status do Trabalho](./media/stream-analytics-create-a-job/3-stream-analytics-create-a-job.png)

    ![Status do Trabalho no Portal de Visualização do Azure](./media/stream-analytics-create-a-job/6-stream-analytics-create-a-job.png)

## Obter ajuda
Para obter mais assistência, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/pt-BR/home?forum=AzureStreamAnalytics)

## Próximas etapas

- [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
- [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
- [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
- [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_1125_2015-->