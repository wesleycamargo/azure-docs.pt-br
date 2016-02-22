
<properties
   pageTitle="Diretrizes sobre o Elasticsearch no Azure | Microsoft Azure"
   description="Diretrizes sobre o Elasticsearch no Azure."
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/05/2016"
   ms.author="mabsimms"/>

# Diretrizes sobre o Elasticsearch no Azure

O Elasticsearch é um banco de dados e mecanismo de pesquisa de código-fonte aberto altamente escalonável. Ele é adequado para situações que exigem análise rápida e descoberta de informações mantidas em grandes conjuntos de dados. Este diretrizes aborda alguns aspectos importantes a considerar ao criar um cluster Elasticsearch:

- As **[Diretrizes gerais][]** fornecem uma breve introdução à estrutura geral do Elasticsearch e descrevem como implementar um cluster Elasticsearch usando o Azure.
- As **[Diretrizes sobre ingestão de dados][]** descrevem as opções de implantação e configuração que devem ser consideradas para um cluster Elasticsearch que espera uma alta taxa de ingestão de dados.
- As **[Diretrizes de testes de desempenho][]** descrevem como configurar um ambiente de teste de desempenho de um cluster Elasticsearch.
- As **[Diretrizes sobre o JMeter][]** descrevem como criar e usar uma amostra do JUnit que pode gerar e carregar dados em um cluster Elasticsearch.
- As **[Considerações sobre o JMeter][]** resumem as experiências chave obtidas com a construção e execução de planos de teste de consulta e ingestão de dados. 

  > [AZURE.NOTE] Estas diretrizes presumem uma familiaridade básica com o Elasticsearch. Para obter informações mais detalhadas, visite o [site do Elasticsearch](https://www.elastic.co/products/elasticsearch).

[Diretrizes gerais]: guidance-elasticsearch.md
[Diretrizes sobre ingestão de dados]: guidance-elasticsearch-data-ingestion.md
[Diretrizes de testes de desempenho]: guidance-elasticsearch-performance-testing-environment.md
[Diretrizes sobre o JMeter]: guidance-elasticsearch-implementing-jmeter.md
[Considerações sobre o JMeter]: guidance-elasticsearch-deploy-jmeter-junit-sampler.md

<!---HONumber=AcomDC_0211_2016-->