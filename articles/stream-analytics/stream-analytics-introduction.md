<properties 
	pageTitle="Introdução ao Stream Analytics | Microsoft Azure" 
	description="Saiba mais sobre o Stream Analytics do Azure, um serviço de nuvem totalmente gerenciado que ajuda a analisar dados de streaming de Internet das coisas (IoT) em tempo real." 
	keywords="análise de big data, serviço de nuvem, internet das coisas, serviço gerenciado, processamento de fluxo, streaming analytics, dados de streaming"
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
	ms.date="10/05/2015" 
	ms.author="jeffstok"/>


# O que é o Stream Analytics do Azure?

O Stream Analytics do Azure (ASA) é um mecanismo de processamento de eventos em tempo real econômico, totalmente gerenciado que ajuda a revelar uma profunda compreensão sobre os dados. O Stream Analytics facilita a configuração de computações analíticas em tempo real em dados de streaming de dispositivos, sensores, sites da web, mídia social, aplicativos, sistemas de infra-estrutura e muito mais.

Com alguns cliques no portal do Azure, você pode criar um trabalho do Stream Analytics especificando a fonte de entrada dos dados de streaming, o coletor de saída para os resultados de seu trabalho e uma transformação de dados expressa em uma linguagem semelhante ao SQL. Você pode monitorar e ajustar a escala/velocidade do seu trabalho no portal do Azure para escalonar alguns quilobytes para um gigabyte ou mais eventos processados por segundo.

O Stream Analytics aproveita anos de trabalho do Microsoft Research no desenvolvimento de mecanismos de streaming altamente ajustados para processamento de detecção de hora e integrações de linguagem para tais especificações intuitivas.

## Para o que psso usar o Stream Analytics?
Grandes quantidades de dados estão fluindo em alta velocidade na rede hoje. Organizações que podem processar e agir sobre esses dados em tempo real podem melhorar consideravelmente a eficiência e se diferenciar no mercado. Cenários de análise de streaming em tempo real podem ser encontrados em todos os setores: análise de comércio de ações personalizadas em tempo real e alertas oferecidos por empresas de serviços financeiros; detecção de fraudes em tempo real; serviços de proteção de identidade e de dados; ingestão confiável e análise de dados gerados por sensores e atuadores inseridos em objetos físicos (Internet das Coisas ou IoT); análise de sequência de cliques na Web; e aplicativos de CRM (Gerenciamento de Relacionamento com o Cliente) emitem alertas quando a experiência do cliente em um período de tempo está degradada. As empresas estão procurando a maneira mais flexível, confiável e econômica de fazer tal análise de dados de fluxo de eventos em tempo real para serem bem-sucedidas no mundo dos negócios modernos, que é altamente competitivo.

## Principais recursos e benefícios
-   **Facilidade de uso:** o Stream Analytics dá suporte a um modelo de consulta simples e declarativo para descrever as transformações. Para otimizar a facilidade de uso, o Stream Analytics usa uma variante SQL e elimina a necessidade dos clientes de lidar com as complexidades técnicas de sistemas de processamento de fluxo. Usando a [linguagem de consulta do Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx), no editor de consulta do navegador, você tem o preenchimento automático do Intellisense para ajudá-lo a implementar funções temporais rápida e facilmente, incluindo junções com base temporal, agregações em janela, filtros temporais, bem como outras operações comuns, como junções, agregações, projeções e filtros. Além disso, o teste da consulta no navegador para um arquivo de dados de exemplo para permitir um desenvolvimento rápido e iterativo.  

-   **Escalabilidade:** O Stream Analytics é capaz de lidar com altas taxas de transferência de evento, de até 1GB por segundo. A Integração com [Hubs de Eventos do Azure](http://azure.microsoft.com/services/event-hubs/) permite que a solução assimile milhões de eventos por segundo, provenientes de dispositivos conectados, clique em fluxos e arquivos de log, para citar alguns. Para conseguir isso, o Stream Analytics aproveita a funcionalidade de particionamento dos Hubs de eventos que podem produzir 1 MB/s por partição. Os usuários podem particionar o cálculo em uma série de etapas lógicas na definição de consulta, cada um com a capacidade de ser mais particionados para aumentar a escalabilidade.

-   **Confiabilidade, capacidade de repetição e recuperação rápida:** sendo um serviço gerenciado na nuvem, o Stream Analytics ajuda a evitar perda de dados e fornece continuidade de negócios na presença de falhas de nó por meio de seus recursos de ponto de verificação e de recuperação integrados. Com a capacidade de manter o estado internamente, o serviço fornece resultados reproduzíveis garantindo que é possível arquivar eventos e aplicar novamente o processamento no futuro, sempre obtendo os mesmos resultados. Isso permite que os clientes voltem no tempo e investiguem cálculos ao fazer a análise de causas raiz, análises hipotéticas, etc.

-   **Custo baixo:** como um serviço de nuvem, o Stream Analytics é otimizado para fornecer baixíssimos custos aos usuários, para que continuem mantendo soluções de análise em tempo real. O serviço foi desenvolvido para ser pago com base no uso de uma unidade de Streaming e a quantidade de dados processados pelo sistema. O uso é obtido com base no volume de eventos processados e na quantidade da capacidade de computação provisionada no cluster para lidar com os respectivos trabalhos do Stream Analytics.

-   **Dados de referência:** o Stream Analytics fornece aos usuários a capacidade de especificar e usar dados de referência. Eles podem ser dados históricos ou simplesmente dados que não são de streaming que são alterados com menos frequência ao longo do tempo. O sistema simplifica o uso dos dados de referência para que sejam tratados como qualquer outro fluxo de eventos recebidos para se unir a outros fluxos de eventos incluídos em tempo real para executar transformações.

-   **Conectividade:** o Stream Analytics se conecta diretamente ao Hub de Eventos do Azure para a inclusão de fluxo e o serviço Blob do Azure para incluir dados históricos. Os resultados podem ser gravados do Stream Analytics para os blobs ou tabelas de armazenamento do Azure, DB do SQL Azure, Hubs de eventos, tópicos do Barramento de Serviço do Azure ou filas e Power BI, onde, em seguida, pode ser visualizado, processados posteriormente por fluxos de trabalho, usados na análise do lote via [Azure HDInsight](http://azure.microsoft.com/services/hdinsight/) ou processado novamente como uma série de eventos. Ao usar os Hubs de eventos do Azure, é fácil criar várias Stream Analytics junto com outras fontes de dados e mecanismos de processamento sem perder a natureza de streaming dos cálculos.

## Obter ajuda
Para obter mais assistência, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/pt-BR/home?forum=AzureStreamAnalytics)

## Próximas etapas
Você foi apresentado ao Stream Analytics, um serviço gerenciado para análise de streaming em dados da Internet das coisas. Para saber mais sobre esse serviço, consulte:

- [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
- [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
- [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=Oct15_HO4-->