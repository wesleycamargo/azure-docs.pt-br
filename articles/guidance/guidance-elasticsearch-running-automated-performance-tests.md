
<properties
   pageTitle="Executando testes automatizados de desempenho do Elasticsearch | Microsoft Azure"
   description="Descrição de como você pode executar testes de desempenho em seu próprio ambiente."
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
   
# Executando os testes automatizados de desempenho do Elasticsearch

Este artigo faz [parte de uma série](guidance-elasticsearch.md).

Os documentos [ Ajuste do desempenho da ingestão de dados com o Elasticsearch no Azure] e [Ajuste de agregação de dados e desempenho de consulta para Elasticsearch no Azure] descrevem uma quantidade de testes de desempenho que foram executados em um cluster Elasticsearch de exemplo.

Esses testes foram colocados em script para que possam ser executados de maneira automática. Este documento descreve como você pode repetir os testes em seu próprio ambiente.

## Pré-requisitos

Os testes automatizados exigem os seguintes itens:

-  Um cluster Elasticsearch.

- Uma configuração de ambiente JMeter conforme descrita pelo documento [Criando um ambiente de teste de desempenho para o Elasticsearch no Azure].

- O software a seguir instalado somente na VM mestre do JMeter.

    [Python 3.5.1](https://www.python.org/downloads/release/python-351/)


## Como funcionam os testes
Os testes são executados usando o JMeter. Um servidor JMeter mestre carrega um plano de teste e o transmite a um conjunto de servidores JMeter subordinados que efetivamente executam os testes. O servidor JMeter mestre coordena os servidores JMeter subordinados e acumula os resultados.

Os seguintes planos de teste são fornecidos:

* [elasticsearchautotestplan3nodes.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan3nodes.jmx). Esse plano de teste executa o teste de ingestão em um cluster de três nós.

* [elasticsearchautotestplan6nodes.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan6nodes.jmx). Esse plano de teste executa o teste de ingestão em um cluster de seis nós.

* [elasticsearchautotestplan6qnodes.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan6qnodes.jmx). Esse plano de teste executa o teste de ingestão e consulta em um cluster de seis nós.

* [elasticsearchautotestplan6nodesqueryonly.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan6nodesqueryonly.jmx). Esse plano de teste executa o teste somente consulta em um cluster de seis nós.


Você pode usar esses planos de teste como base para seus próprios cenários se precisar de menos ou de mais nós.

Os planos de teste usam uma Amostra de Solicitação do JUnit para gerar e carregar os dados de teste. O plano de teste do JMeter cria e executa essa amostra e monitora os dados de desempenho de cada um dos nós do Elasticsearch. Para saber mais, confira os apêndices dos documentos [Ajuste do desempenho da ingestão de dados com o Elasticsearch no Azure] e [Ajuste de agregação de dados e desempenho de consulta para Elasticsearch no Azure].

## Criando e implantando o JAR JUnit e as dependências
Antes de executar os testes de resiliência, você deve baixar, compilar e implantar os testes JUnit localizados na pasta performance/junitcode. Esses testes são referenciados pelo plano de teste JMeter. Para saber mais, confira o procedimento Importando um projeto de teste JUnit existente no Eclipse no documento [Importando uma amostra de JUnit JMeter para testar o desempenho do Elasticsearch].

Há duas versões dos testes JUnit: – [Elasticsearch1.73](https://github.com/mspnp/azure-guidance/tree/master/ingestion-and-query-tests/junitcode/elasticsearch1.73). Use esse código para executar os testes de ingestão. Esses testes usam o Elasticsearch 1.73 – [Elasticsearch2](https://github.com/mspnp/azure-guidance/tree/master/ingestion-and-query-tests/junitcode/elasticsearch2). Use esse código para executar os testes de consulta. Esses testes usam o Elasticsearch 2.1 e versões posteriores.

Copie o arquivo JAR apropriado junto com o restante das dependências nos seus computadores com o JMeter. O processo é descrito em [Implantado uma amostra do JUnit JMeter para testar o desempenho do Elasticsearch][].

> **Importante** Depois de implantar um teste JUnit, use JMeter para carregar e configurar os planos de teste que referencie esse teste JUnit e verifique se o grupo de threads *BulkInsertLarge* referencia o arquivo JAR,o nome da classe JUnit e o método de teste corretos:
> 
> ![](./media/guidance-elasticsearch/performance-tests-image1.png)
> 
> Salve os planos de teste atualizados antes de executar os testes.

## Criando os índices de teste
Cada teste executa ingestão e/ou consultas em um único índice especificado quando é executado. Você deve criar o índice usando os esquemas descritos nos apêndices dos documentos [Ajuste do desempenho da ingestão de dados com o Elasticsearch no Azure] e [Ajuste de agregação de dados e desempenho de consulta para Elasticsearch no Azure] e configurá-los de acordo com o cenário de teste (valores de documento habilitados/desabilitados, várias réplicas etc.) Observe que os planos de teste supõem que o índice consiste em um único tipo denominado *ctip*.

## Configurando os parâmetros de script de teste
Copie os seguintes arquivos de parâmetro de script de teste no computador do servidor JMeter:

* [run.properties](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/run.properties). Esse arquivo especifica o número de threads de teste JMeter a serem usados, a duração do teste (em segundos), o endereço IP de um nó (ou um balanceador de carga) no cluster Elasticsearch e o nome do cluster:

  ```ini
  nthreads=3
  duration=300
  elasticip=<IP Address or DNS Name Here>
  clustername=<Cluster Name Here>
  ```
  
  Edite o arquivo e especifique os valores apropriados para seu teste e cluster.

* [query-config-win.ini](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/query-config-win.ini) e [query-config-nix.ini](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/query-config-nix.ini). Esses dois arquivos contêm as mesmas informações. O arquivo *win* é formatado para nomes de arquivos e caminhos do Windows e o arquivo *nix* está formatado para caminhos e nomes de arquivos do Linux:

  ```ini
  [DEFAULT]
  debug=true #se true, mostrar logs do console.

  [RUN]
  pathreports=C:\\Users\\administrator1\\jmeter\\test-results\\ #caminho em que os resultados de teste são salvos.
  jmx=C:\\Users\\administrator1\\testplan.jmx #path para o plano de teste JMeter.
  machines=10.0.0.1,10.0.02,10.0.0.3 #IPs dos nós de dados Elasticsearch separados por vírgulas.
  reports=aggr,err,tps,waitio,cpu,network,disk,response,view #Nome dos relatórios separados por vírgulas.
  tests=idx1 #Nome do índice do Elasticsearch a ser testado.
  properties=run.properties #Nome do arquivo de propriedades.
  ```

  Edite esse arquivo para especificar os locais dos resultados do teste, o nome do plano de teste JMeter a ser executado, os endereços IP dos nós de dados Elasticsearch, os relatórios que contêm os dados brutos de desempenho que serão gerados e os nomes dos índices a serem testados. Se o arquivo *run.properties* estiver localizado em uma pasta diferente ou diretório, especifique o caminho completo para esse arquivo.

## Executando os testes

* Copie o arquivo [query-test.py](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/query-test.py) para o computador do servidor JMeter, para mesma pasta que os arquivos *run.properties* e *query-config-Win* (*query-config-nix.ini*).

* Verifique se *jmeter.bat* (Windows) ou *jmeter.sh* (Linux) está no caminho do executável para o seu ambiente.

* Execute o script *query-test.py* na linha de comando para executar os testes:

  ```cmd
  py query-test.py
  ```

* Quando o teste for concluído, os resultados serão armazenados como o conjunto de arquivos CSV especificado nos arquivos *query-config-win.ini* (*query-config-nix.ini*). Você pode usar o Excel para analisar e para criar um gráfico com esses dados.


[ Ajuste do desempenho da ingestão de dados com o Elasticsearch no Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Ajuste do desempenho da ingestão de dados com o Elasticsearch no Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Ajuste de agregação de dados e desempenho de consulta para Elasticsearch no Azure]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md
[Criando um ambiente de teste de desempenho para o Elasticsearch no Azure]: guidance-elasticsearch-creating-performance-testing-environment.md
[Implantado uma amostra do JUnit JMeter para testar o desempenho do Elasticsearch]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
[Importando uma amostra de JUnit JMeter para testar o desempenho do Elasticsearch]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md

<!---HONumber=AcomDC_0224_2016-->
