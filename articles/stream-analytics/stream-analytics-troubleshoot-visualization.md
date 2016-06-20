<properties
	pageTitle="Visualizar e solucionar problemas com trabalhos do Stream Analytics | Microsoft Azure"
	description="Saiba como visualizar um pipeline de trabalho do Stream Analytics para solução de problemas de autoatendimento usando o recurso de diagrama de diagnóstico."
	keywords=""
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"
/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-services"
	ms.date="05/31/2016"
	ms.author="jeffstok"
/>


# Visualizar e solucionar problemas com trabalhos do Stream Analytics

No Stream Analytics, assim como em outras tecnologias baseadas em nuvem, a solução de problemas às vezes é necessária para examinar por que um trabalho não produz a saída esperada (ou até mesmo nenhuma saída). Considerando isso, o Stream Analytics oferece a capacidade de visualizar um trabalho de streaming. Isso também é útil como uma ferramenta de modelagem e traz um benefício adicional para os que exigem documentação de seu trabalho.

No painel de visualização, as entradas são visíveis, bem como a consulta que estão sendo executadas, e então todas as saídas são configuradas. Problemas de conectividade ou a configuração podem se tornar mais aparentes e também pode ser útil ver uma representação visual da sua configuração.

## Usando a ferramenta de diagrama de diagnóstico

Para acessar esse visualizador, basta clicar no botão "Diagrama de diagnóstico" na folha de "Configurações" do trabalho do Stream Analytics.

![stream-analytics-troubleshoot-visualization-diagnosis-diagram](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-diagnosis-diagram1.png)

Cada entrada e saída é codificadas por cores para indicar o estado atual desse componente, conforme mostrado abaixo.

![stream-analytics-troubleshoot-visualization-input-map](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-input-map.png)

Quando o usuário desejar examinar as etapas de consulta intermediárias para entender os padrões de fluxo de dados dentro de um trabalho, a ferramenta de visualização fornece uma exibição da separação da consulta em suas etapas componentes e a sequência do fluxo. Clique em cada etapa de consulta para mostrar a seção correspondente em um painel de edição de consulta conforme ilustrado.

![stream-analytics-troubleshoot-visualization-intermediate-steps](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-intermediate-steps.png)




## Próximas etapas

- [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
- [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
- [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
- [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0608_2016-->