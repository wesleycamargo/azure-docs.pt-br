<properties
   pageTitle="Implementação de um plano de teste JMeter para o Elasticsearch | Microsoft Azure"
   description="Como executar testes de desempenho para o Elasticsearch com o JMeter."
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
   
# Implementar um plano de teste do JMeter para o Elasticsearch

Este artigo faz [parte de uma série](guidance-elasticsearch.md).

Os testes de desempenho conduzidos no Elasticsearch foram implementados usando planos de teste JMeter junto com o código Java incorporado como um teste JUnit para execução de tarefas como o carregamento de dados no cluster. Os planos de teste e o código JUnit são descritos em [Ajustando o Desempenho de Ingestão de Dados com o Elasticsearch no Azure][] e [Ajustando a Agregação de Dados e Desempenho de Consulta com o Elasticsearch no Azure][].

A finalidade deste documento é resumir a principal experiência obtida com a criação e execução desses planos de teste. A página [Práticas Recomendadas do JMeter](http://jmeter.apache.org/usermanual/best-practices.html) no site do Apache JMeter contém orientações mais gerais sobre como usar o JMeter efetivamente.

## Implementar um plano de teste JMeter

A lista a seguir resume os itens que você deve considerar ao criar um plano de teste JMeter:

- Crie um grupo de threads separado para cada teste que você deseja executar. Um teste pode incluir várias etapas, formadas por controladores de lógica, temporizadores, pré-processadores e pós-processadores, amostras e ouvintes.

- Evite a criação de muitos threads em um grupo de threads. Um número excessivo de threads causará a falha do JMeter com exceções "Memória insuficiente". É melhor adicionar mais servidores subordinados do JMeter, cada um deles executando uma pequena quantidade de threads, do que tentar executar uma grande quantidade de threads em um único servidor JMeter.

![](./media/guidance-elasticsearch/jmeter-testing1.png)

- Para avaliar o desempenho do cluster, incorpore o plug-in [Coletor de Métricas Perfmon](http://jmeter-plugins.org/wiki/PerfMon/) ao plano de teste; esse plug-in é um ouvinte do JMeter disponível como um dos plug-ins padrão do JMeter. Salve os dados de desempenho brutos em um conjunto de arquivos no formato CSV e processe-os após a conclusão do teste. Isso é mais eficiente e aplica uma sobrecarga menor no JMeter do que tentar processar os dados conforme eles são capturados. 

Você pode usar uma ferramenta como o Excel para importar os dados e gerar vários gráficos para fins de análise.

![](./media/guidance-elasticsearch/jmeter-testing2.png)

Considere a possibilidade de capturar as informações a seguir:

- Utilização da CPU para cada nó no cluster Elasticsearch.

- O número de bytes lidos por segundo do disco para cada nó.

- Se possível, a porcentagem do tempo de CPU gasto aguardando pela execução de E/S a ser executada em cada nó. Isso nem sempre é possível para VMs do Windows mas, para o Linux, você pode criar uma métrica personalizada (uma métrica EXEC) que executa o seguinte comando do shell para invocar *vmstat* em um nó:

```Shell
sh:-c:vmstat 1 5 | awk 'BEGIN { line=0;total=0;}{line=line+1;if(line&gt;1){total=total+\$16;}}END{print total/4}'
```

O campo 16 na saída de *vmstat* contém o tempo de CPU gasto aguardando a E/S. Para saber mais sobre o funcionamento dessa instrução, consulte o [comando vmstat](http://linuxcommand.org/man_pages/vmstat8.html).

- O número de bytes enviados e recebidos pela rede para cada nó.

- Use os ouvintes separados do Relatório de Agregados para registrar o desempenho e a frequência de operações bem-sucedidas e com falhas. Capture dados de êxito e de falha em arquivos diferentes.

![](./media/guidance-elasticsearch/jmeter-testing3.png)

- Mantenha cada caso de teste JMeter o mais simples possível a fim de permitir que você correlacione diretamente o desempenho com ações de teste específicas. Para casos de teste que exigem uma lógica complexa, considere o encapsulamento dessa lógica em um teste JUnit e use a amostra da Solicitação JUnit no JMeter para executar o teste.

- Use a amostra da Solicitação HTTP para executar operações HTTP, como GET, POST, PUT ou DELETE. Por exemplo, você pode executar pesquisas do Elasticsearch usando uma consulta POST e fornecendo os detalhes da consulta na caixa *Corpo de Dados*:

![](./media/guidance-elasticsearch/jmeter-testing4.png)

- Para facilitar a reutilização e a capacidade de repetição, parametrize os planos de teste do JMeter. Assim, você pode usar scripts para automatizar a execução dos planos de teste.

## Implementando um teste JUnit

Você pode incorporar um código complexo em um plano de teste JMeter criando um ou mais testes JUnit. Você pode escrever um teste JUnit usando um Java IDE, como o Eclipse. [Implantando uma Amostra de JUnit JMeter para Testar o Desempenho do Elasticsearch][] fornece informações sobre como configurar um ambiente de desenvolvimento apropriado.

A lista a seguir resume algumas práticas recomendadas que você deve seguir ao escrever o código para um teste JUnit:

- Use o construtor da classe de teste para passar parâmetros de inicialização ao teste. O JMeter pode usar um construtor que aceita um argumento de cadeia única. No construtor, analise esse argumento em seus elementos individuais, conforme mostra o exemplo de código a seguir:

```Java
private String hostName = "";
private String indexName = "";
private String typeName = "";
private int port = 0;
private String clusterName = "";
private int itemsPerBatch = 0;

/* JUnit test class constructor */
public ElasticsearchLoadTest2(String params) {
	/* params is a string containing a set of comma separated values for:
		hostName
		indexName
		typeName
		port
		clustername
		itemsPerBatch
	*/

    /* Parse the parameter string into an array of string items */
	String delims = "[ ]*,[ ]*"; // comma surrounded by zero or more spaces
	String[] items = params.split(delims);

    /* Note: Parameter validation code omitted */

	/* Use the parameters to populate variables used by the test */
	hostName = items[0];
	indexName = items[1];
	typeName = items[2];
	port = Integer.parseInt(items[3]);
	clusterName = items[4];
	itemsPerBatch = Integer.parseInt(items[5]);

	if(itemsPerBatch == 0)
		itemsPerBatch = 1000;
}
```

- Evite operações dispendiosas ou de E/S no construtor ou na classe do teste de configuração, pois elas são executadas sempre que o teste JUnit é executado (o mesmo teste JUnit pode executar várias milhares de vezes para cada teste de desempenho executado no JMeter).

- Considere o uso de uma configuração única para a inicialização do caso de teste dispendioso.

- Se o teste exigir uma grande quantidade de parâmetros de entrada, armazene as informações de configuração do teste em um arquivo de configuração separado e passe o local desse arquivo para o construtor.

- Evite a codificação de caminhos de arquivo no código de teste de carga. Eles podem causar falhas devido a diferenças entre sistemas operacionais, como o Windows e o Linux.

- Use asserções para indicar falhas nos métodos de teste JUnit, para que você possa controlá-los com o JMeter e usá-los como métricas de negócios. Se possível, passe informações sobre a causa da falha, conforme exibido em negrito no exemplo de código a seguir:

```Java
@Test
public void bulkInsertTest() throws IOException {
	...
	BulkResponse bulkResponse = bulkRequest.execute().actionGet();
	assertFalse(
		bulkResponse.buildFailureMessage(), bulkResponse.hasFailures());
		...
}
```

[Running Elasticsearch on Azure]: guidance-elasticsearch-running-on-azure.md
[Ajustando o Desempenho de Ingestão de Dados com o Elasticsearch no Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Implantando uma Amostra de JUnit JMeter para Testar o Desempenho do Elasticsearch]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
[Ajustando a Agregação de Dados e Desempenho de Consulta com o Elasticsearch no Azure]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md

<!---HONumber=AcomDC_0224_2016-->