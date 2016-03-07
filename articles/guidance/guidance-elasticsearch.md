
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
   ms.date="02/18/2016"
   ms.author="masimms"/>

# Diretrizes sobre o Elasticsearch no Azure

O Elasticsearch é um banco de dados e mecanismo de pesquisa de código-fonte aberto altamente escalonável. Ele é adequado para situações que exigem análise rápida e descoberta de informações mantidas em grandes conjuntos de dados. Este diretrizes aborda alguns aspectos importantes a considerar ao criar um cluster Elasticsearch:

- **[Executar o Elasticsearch no Azure][]** fornece uma breve introdução à estrutura geral do Elasticsearch e descreve como implementar um cluster do Elasticsearch usando o Azure.
- **[Ajustando o Desempenho de Ingestão de Dados para Elasticsearch no Azure][]** descreve as opções de implantação e configuração que devem ser consideradas para um cluster Elasticsearch que espera uma alta taxa de ingestão de dados.
- **[Ajustando de Agregação de Dados e Desempenho de Consulta para Elasticsearch no Azure][]** resume as opções que você pode considerar ao determinar a melhor maneira de otimizar seu sistema em relação ao desempenho de consulta e pesquisa.
- **[Configurando a Resiliência e a Recuperação no Elasticsearch no Azure][]** resume as opções de resiliência e recuperação disponíveis com o Elasticsearch quando hospedado no Azure.
- **[Criando um Ambiente de Teste de Desempenho para o Elasticsearch no Azure][]** descreve como configurar um ambiente para testar o desempenho de um cluster do Elasticsearch.
- **[Implementando um Plano de Teste JMeter para o Elasticsearch][]** descreve como criar e usar uma amostra do JUnit que pode gerar e carregar dados para um cluster do Elasticsearch.
- **[Implantando uma Amostra de JUnit JMeter para Testar o Desempenho do Elasticsearch][]** resume as principais experiências obtidas com a construção e execução de planos de teste de consulta e ingestão de dados. 
- **[Executando Testes de Resiliência Automatizados do Elasticsearch][]** resume a execução dos testes de resiliência usados no artigo acima.
- **[Executando Testes de Desempenho Automatizados do Elasticsearch][]** resume a execução dos testes de desempenho usados no artigo acima.

> [AZURE.NOTE] Estas diretrizes presumem uma familiaridade básica com o Elasticsearch. Para obter informações mais detalhadas, visite o [site do Elasticsearch](https://www.elastic.co/products/elasticsearch).

[Executar o Elasticsearch no Azure]: guidance-elasticsearch-running-on-azure.md
[Ajustando o Desempenho de Ingestão de Dados para Elasticsearch no Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Criando um Ambiente de Teste de Desempenho para o Elasticsearch no Azure]: guidance-elasticsearch-creating-performance-testing-environment.md
[Implementando um Plano de Teste JMeter para o Elasticsearch]: guidance-elasticsearch-implementing-jmeter-test-plan.md
[Implantando uma Amostra de JUnit JMeter para Testar o Desempenho do Elasticsearch]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
[Ajustando de Agregação de Dados e Desempenho de Consulta para Elasticsearch no Azure]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md
[Configurando a Resiliência e a Recuperação no Elasticsearch no Azure]: guidance-elasticsearch-configuring-resilience-and-recovery.md
[Executando Testes de Resiliência Automatizados do Elasticsearch]: guidance-elasticsearch-running-automated-resilience-tests.md
[Executando Testes de Desempenho Automatizados do Elasticsearch]: guidance-elasticsearch-running-automated-performance-tests.md

<!---HONumber=AcomDC_0224_2016-->