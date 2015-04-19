<properties 
	pageTitle="Introdução ao Hadoop no HDInsight: Análise de big data na nuvem | Azure" 
	description="Saiba como o Azure HDInsight usa clusters do Apache Hadoop na nuvem para fornecer uma estrutura de software para gerenciar, analisar e relatar sobre Big Data." 
	services="hdinsight" 
	documentationCenter="" 
	authors="cjgronlund" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/23/2014" 
	ms.author="cgronlun"/>



# Introdução ao Hadoop no HDInsight: Processamento e análise de big data na nuvem

Obtenha uma visão geral dos componentes do HDInsight, terminologia comum e cenários, e visualize os recursos para usar o Hadoop no HDInsight.

O Azure HDInsight implanta e provisiona clusters do Apache Hadoop na nuvem, fornecendo uma estrutura de software criada para gerenciar, analisar e relatar Big Data. O núcleo do Hadoop fornece armazenamento de dados confiável com o HDFS (Sistema de Arquivos Distribuído Hadoop) e um modelo de programação simples MapReduce para processar e analisar, paralelamente, os dados armazenados nesse sistema distribuído. 

### O que é big data?
Big data se refere aos dados que estão sendo coletados em volumes sempre crescentes, em velocidades cada vez mais altas e para uma variedade cada vez maior de formatos não estruturados e contextos semânticos variáveis. 

Big data descreve qualquer grande quantidade, desde informações digitais do texto em um feed do Twitter até as informações de sensores de equipamentos industriais, para obter informações relativas à navegação do cliente e a compras em um catálogo online. Os big data podem ser históricos (referentes a dados armazenados) ou em tempo real (o que significa que são transmitidos diretamente da fonte). 

Para que os big data ofereçam inteligência ou informações úteis, não só as perguntas certas devem ser feitas e os dados relevantes aos problemas devem ser coletados, como também os dados devem estar acessíveis, estar limpos, ser analisados e, em seguida, ser apresentados de uma maneira útil. É aí que o Hadoop no HDInsight pode ajudar.


## Neste artigo

Este artigo oferece uma visão geral do Hadoop no HDInsight, incluindo:

* **[Visão geral do ecossistema do Hadoop no HDInsight:](#overview)**  O HDInsight é a solução Hadoop no Azure e oferece implementações de Storm, HBase, Pig, Hive, Sqoop, Oozie, Ambari, e assim por diante. O HDInsight também se integra a ferramentas de business intelligence (BI), como Excel, SQL Server Analysis Services e SQL Server Reporting Services.
* **[Vantagens do Hadoop na nuvem:](#advantage)** Motivos pelos quais você deve considerar a implementação da nuvem do Hadoop do HDInsight.
* **[Soluções do HDInsight para a análise de big data:](#solutions)** Algumas maneiras práticas pelas quais você pode usar o HDInsight para responder perguntas para sua organização, desde analisar sentimentos do Twitter até analisar a eficácia do sistema de HVAC.
* **[Recursos para aprender mais sobre a análise de big data, Hadoop e HDInsight:](#resources)** Links para informações adicionais.


## <a name="overview"></a>Visão geral do ecossistema do Hadoop no HDInsight

Hadoop é a pilha de tecnologia em rápida expansão que é a solução preferida para análise de big data. O HDInsight é a estrutura para a implementação e nuvem do Microsoft Azure do Hadoop.
 
Os clusters Hadoop no HDInsight usam versões da distribuição de HortonWorks Data Platform (HDP) e um conjunto de componentes do Hadoop contidos nessa distribuição. Para obter informações sobre os componentes e suas versões nos clusters do HDInsight, consulte [O que há de novo nas versões de cluster Hadoop fornecidas pelo HDInsight?][component-versioning]

O Apache Hadoop é uma estrutura de software para a análise e o gerenciamento de big data. O núcleo do Apache Hadoop fornece armazenamento de dados confiável com o HDFS (Sistema de Arquivos Distribuído Hadoop) e um modelo de programação simples MapReduce para processar e analisar, paralelamente, os dados armazenados nesse sistema distribuído. O HDFS usa replicação de dados para solucionar problemas de falha de hardware que podem surgir durante a implantação desses sistemas altamente distribuídos.

A seguir são apresentadas as tecnologias de Hadoop no HDInsight:

* **[Ambari](#ambari):** Provisionamento, gerenciamento e monitoramento de cluster 
* **[Avro](#avro)** (Biblioteca do Microsoft .NET para Avro): Serialização de dados para o ambiente do Microsoft .NET 
* **[HBase](#hbase):** Banco de dados não relacional para tabelas muito grandes
* **[HDFS](#hdfs):** Sistema de Arquivos Distribuído Hadoop
* **[Hive](#hive):** Consulta semelhante a SQL
* **[Mahout](#mahout):** Aprendizado de máquina
* **[MapReduce e YARN](#mapreduce):** Processamento distribuído e gerenciamento de recursos
* **[Oozie](#oozie):** Gerenciamento de fluxo de trabalho
* **[Pig](#pig):** Script mais simples para transformações do MapReduce
* **[Sqoop](#sqoop):** Importação e exportação de dados 
* **[Storm](#storm):** Processamento em tempo real de fluxos de dados rápidos e grandes
* **[Zookeeper](#zookeeper):** Processos de coordenadas em sistemas distribuídos
 
Além disso, descubra **[ferramentas de business intelligence](#bi)** que você pode usar com o HDInsight.

###<a name="ambari"></a>Ambari

O Apache Ambari serve para provisionar, gerenciar e monitorar clusters do Apache Hadoop. Inclui uma coleção de ferramentas intuitivas para operador e um conjunto abrangente de APIs que ocultam a complexidade do Hadoop, simplificando a operação de clusters. Consulte  [Monitorar clusters do Hadoop no HDInsight usando a API do Ambari](hdinsight-monitor-use-ambari-api.md) e <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">referência de API do Apache Ambari</a>.

### <a name="avro"></a>Avro (Biblioteca do Microsoft .NET para Avro)

A Biblioteca do Microsoft .NET para Avro implementa o formato de intercâmbio de dados binário compacto do Apache Avro para serialização para o ambiente Microsoft.NET. Ela utiliza o <a target="_blank" href="http://www.json.org/">JSON</a> para definir um esquema com neutralidade de linguagem que subscreve a interoperabilidade de linguagem, o que significa que os dados serializados em uma linguagem podem ser lidos em outra diferente. Informações detalhadas sobre o formato podem ser encontradas na <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">Especificação do Apache Avro</a>. 
O formato dos arquivos do Avro suporta o modelo de programação distribuído do MapReduce. Os arquivos são "divisíveis", o que significa que você pode buscar qualquer ponto em um arquivo e iniciar a leitura por meio de um bloco em específico. Para saber como fazer isso, consulte [Serializar dados com a Biblioteca do Microsoft .NET para Avro](hdinsight-dotnet-avro-serialization.md).

### <a name="hbase"></a>HBase

<a target="_blank" href="http://hbase.apache.org/">O Apache HBase</a> é um banco de dados não relacional baseado no Hadoop e projetado para grandes quantidades de dados não estruturados e semiestruturados, possivelmente bilhões de linhas vezes milhões de colunas. Os clusters HBase são configurados para armazenar dados diretamente no armazenamento de Blob do Azure, com baixa latência e elasticidade elevada. Consulte [Visão geral do HBase no HDInsight](hdinsight-hbase-overview.md).

### <a name="hdfs"></a>HDFS

O Sistema de Arquivos Distribuído Hadoop (HDFS) é um sistema de arquivos distribuído que, com o MapReduce e o YARN, é o núcleo do ecossistema do Hadoop. HDFS é o sistema de arquivo padrão para clusters Hadoop no HDInsight.

### <a name="hive"></a>Hive

<a target="_blank" href="http://hive.apache.org/">O Apache Hive</a> é um software de armazenamento de dados baseado no Hadoop que permite consultar e gerenciar grandes conjuntos de dados em armazenamento distribuído usando uma linguagem semelhante à SQL chamada HiveQL. O Hive, assim como o Pig, é uma abstração sobre o MapReduce e, quando executado, converte as consultas em uma série de trabalhos de MapReduce. O Hive é conceitualmente mais próximo a um sistema de gerenciamento de banco de dados relacional que o Pig e, portanto, é mais adequado para uso com dados mais estruturados. Para dados não estruturados, o Pig é a melhor opção. Consulte [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md).

### <a name="mahout"></a>Mahout

<a target="_blank" href="https://mahout.apache.org/">O Apache Mahout</a> é uma biblioteca escalável de algoritmos de aprendizado de máquina executada no Hadoop. Usando princípios de estatísticas, os aplicativos de aprendizado de máquina ensinam aos sistemas a aprender com os dados e a usar os resultados passados para determinar um comportamento futuro. Consulte [Gerar recomendações de vídeo usando o Mahout no Hadoop](hdinsight-mahout.md).

### <a name="mapreduce"></a>MapReduce e YARN
O MapReduce do Hadoop é uma estrutura de software para escrever aplicativos que processam conjuntos de big data paralelamente. Um trabalho de MapReduce divide grandes conjuntos de dados e organiza os dados em pares de valores-chave para processamento. 

O Apache YARN é a próxima geração do MapReduce (MapReduce 2.0, ou MRv2), que divide as duas principais tarefas do JobTracker, gerenciamento de recursos e planejamento/monitoramento de trabalho, em entidades separadas.

Para obter mais informações sobre o MapReduce, consulte <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> no Wiki do Hadoop. Para saber mais sobre YARN, consulte <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (YARN)</a>.

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">O Apache Oozie</a> é um sistema de coordenação de fluxo de trabalho que gerencia trabalhos do Hadoop. É integrado com a pilha do Hadoop e oferece suporte a trabalhos do Hadoop para MapReduce, Pig, Hive e Sqoop. Também pode ser usado para agendar trabalhos específicos para um sistema, como programas Java ou scripts de shell. Consulte [Usar o Coordenador baseado em tempo do Oozie com o Hadoop no HDInsight](hdinsight-use-oozie-coordinator-time.md).

### <a name="pig"></a>Pig

<a  target="_blank" href="http://pig.apache.org/">O Apache Pig</a> é uma plataforma de alto nível que permite que você realize transformações complexas do MapReduce em conjuntos de dados bastante grandes usando uma linguagem de script simples chamada Pig Latin. O Pig traduz os scripts em Pig Latin para que sejam executados dentro do Hadoop. Você pode criar Funções Definidas pelo Usuário (UDFs) para estender a Pig Latin. Consulte [Usar o Pig com o Hadoop para analisar um arquivo de log do Apache](hdinsight-use-pig.md).

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">O Apache Sqoop</a> é uma ferramenta que transfere dados em massa entre o Hadoop e bancos de dados relacionais, como o SQL, ou outros armazenamentos de dados estruturados, do modo mais eficiente possível. Consulte [Usar o Sqoop com o Hadoop](hdinsight-use-sqoop.md).

### <a name="storm"></a>Storm
<a  target="_blank" href="https://storm.incubator.apache.org/">O Apache Storm</a> é um sistema de computação distribuído e em tempo real para o processamento rápido de grandes fluxos de dados. O Storm é oferecido como um cluster gerenciado no HDInsight. Consulte [Analisar dados do sensor em tempo real usando o Storm e o Hadoop](hdinsight-storm-sensor-data-analysis.md).

### <a name="zookeeper"></a>Zookeeper
<a  target="_blank" href="http://zookeeper.apache.org/">O Apache Zookeeper</a> coordena processos em grandes sistemas distribuídos por meio de um namespace hierárquico compartilhado de registros de dados (znodes). Os Znodes contêm pequenas quantidades de metainformações necessárias para coordenar processos: status, local, configuração e assim por diante. 

### <a name="bi"></a>Ferramentas de business intelligence 
As ferramentas conhecidas de BI (business intelligence), como o Excel, o PowerPivot, o SQL Server Analysis Services e o Reporting Services, recuperam, analisam e relatam os dados integrados ao HDInsight usando o suplemento Power Query ou o Driver ODBC do Microsoft Hive. 

Essas ferramentas de BI podem ajudar em sua análise de big data:

* <a target="_blank" href="http://www.microsoft.com/pt-br/download/details.aspx?id=39379">Baixe o Microsoft Power Query para Excel</a> 
* <a target="_blank" href="http://www.microsoft.com/pt-br/download/details.aspx?id=40886">Baixe o Driver ODBC do Microsoft Hive</a>
* <a target="_blank" href="http://www.microsoft.com/pt-br/server-cloud/solutions/business-intelligence/analysis.aspx">Saiba mais sobre SQL Server Analysis Services</a>
* <a target="_blank" href="http://msdn.microsoft.com/library/ms159106.aspx">Saiba mais sobre SQL Server Reporting Services</a>

## <a name="advantage"></a>Vantagens do Hadoop na nuvem

Como parte do ecossistema de nuvem do Azure, o Hadoop no HDInsight oferece uma série de benefícios, entre eles:

* Componentes do Hadoop de última geração. Para obter detalhes, consulte [O que há de novo nas versões de cluster Hadoop fornecidas pelo HDInsight?][component-versioning]
* Alta disponibilidade e confiabilidade dos clusters. Consulte [Disponibilidade e confiabilidade dos clusters Hadoop no HDInsight](hdinsight-high-availability.md) para obter detalhes.
* Armazenamento de dados eficiente e econômico com o armazenamento de Blob do Azure, uma opção compatível com o Hadoop. Consulte [Usar o armazenamento de Blob do Azure com o Hadoop no HDInsight](hdinsight-use-blob-storage.md) para obter detalhes.
* Integração com outros serviços do Azure, incluindo [Websites](../documentation/services/websites/) and [Banco de Dados SQL](../documentation/services/sql-database/).
* Baixo custo de entrada. Inicie uma [avaliação gratuita](/pricing/free-trial/) ou consulte os [Detalhes de preço do HDInsight](../pricing/details/hdinsight/).

Para ler mais sobre as vantagens do Hadoop no HDInsight, consulte a [Página de recursos do Azure para HDInsight][marketing-page].

## <a name="solutions"></a>Experimentar as soluções do HDInsight para a análise de big data

Analise dados de sua organização para obter informações sobre seus negócios. Estes são alguns exemplos: 

* [Analisar dados HVAC do sensor](hdinsight-hive-analyze-sensor-data.md): Saiba como analisar dados do sensor usando o Hive com HDInsight (Hadoop) e depois visualize os dados no Microsoft Excel. Neste exemplo, você usará o Hive para processar dados históricos produzidos por sistemas HVAC para identificar quais sistemas não conseguem manter uma temperatura determinada de maneira confiável.
* [Usar o Hive com o HDInsight para analisar os logs do Website](hdinsight-hive-analyze-website-log.md): Saiba como usar HiveQL no HDInsight para analisar os logs do Website para obter informações sobre a frequência de visitas em um dia por meio de Websites externos e um resumo dos erros do Website enfrentados pelos usuários.
* [Analisar dados do sensor em tempo real com o Storm e o HBase no HDInsight (Hadoop)](hdinsight-storm-sensor-data-analysis.md): Saiba como criar uma solução que usa um cluster Storm no HDInsight para processar dados do sensor por meio de Hubs de Evento do Azure e depois exibe os dados do sensor processados como informações quase em tempo real em um painel baseado na Web.

Para experimentar o Hadoop no HDInsight, consulte os artigos de "Introdução" na seção Explorar na [Página de documentação do HDInsight](../documentation/services/hdinsight/). Para experimentar exemplos mais avançados, role a tela até a seção Analisar.



## <a name="resources"></a>Recursos para aprender mais sobre a análise de big data, Hadoop e HDInsight 

### Hadoop no HDInsight	

* [Documentação do HDInsight](../documentation/services/hdinsight/): a página de documentação do Azure HDInsight com links para artigos, vídeos e mais recursos.

* [Mapa de aprendizagem para Hadoop no HDInsight](hdinsight-learn-map.md): Um tour guiado da documentação do Hadoop para o HDInsight.

* [Introdução ao Azure HDInsight](hdinsight-get-started.md): Um tutorial de início rápido para usar o Hadoop no HDInsight.

* [Executar os exemplos do HDInsight](hdinsight-run-samples.md): um tutorial sobre como executar os exemplos que acompanham o HDInsight.
	
* [SDK do Azure HDInsight](http://msdnstage.redmond.corp.microsoft.com/pt-br/library/dn479185.aspx): documentação de referência do SDK do HDinsight.


### Bancos de Dados SQL no Azure	
		
* [Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/windowsazure/ee336279.aspx): Documentação de MSDN para o Banco de Dados SQL.
	
* [Portal de Gerenciamento para o Banco de Dados SQL](http://msdn.microsoft.com/library/windowsazure/gg442309.aspx): uma ferramenta de gerenciamento de banco de dados leve e fácil de usar para gerenciamento do Banco de Dados SQL na nuvem.

* [Adventure Works para o Banco de Dados SQL](http://msftdbprodsamples.codeplex.com/releases/view/37304): página de download do banco de dados de amostra do Banco de Dados SQL.	

### Microsoft Business Intelligence		

* [Conectar o Excel ao Hadoop com o Power Query](hdinsight-connect-excel-power-query.md): saiba como conectar o Excel à conta de armazenamento do Azure que armazena os dados associados ao seu cluster HDInsight usando o Microsoft Power Query para Excel. 

* [Conectar o Excel ao Hadoop com o driver ODBC do Microsoft Hive](hdinsight-connect-excel-hive-ODBC-driver.md): Saiba como importar dados do HDInsight com o Driver ODBC do Microsoft Hive.

* [Microsoft Business Intelligence (BI)](http://www.microsoft.com/pt-br/server-cloud/solutions/business-intelligence/default.aspx): Saiba mais sobre o Power BI para Office 365, baixe a versão de teste do SQL Server e instale o SharePoint Server 2013 e SQL Server BI.
			

### Apache Hadoop			

* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a>: saiba mais sobre a biblioteca de software Apache Hadoop, uma estrutura que permite o processamento distribuído de grandes conjuntos de dados em clusters de computadores.	

*  <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html">HDFS</a>: saiba mais sobre a arquitetura e o design do HDFS (Sistema de Arquivos Distribuído Hadoop), o sistema de armazenamento primário usado pelos aplicativos Hadoop.		

* <a target="_blank" href="http://mapreduce.org/">MapReduce</a>: saiba mais sobre a estrutura de programação para escrever aplicativos Hadoop que processam rapidamente grandes quantidades de dados paralelamente em grandes clusters de nós de computação.	


[marketing-page]: ../services/hdinsight/
[component-versioning]: ../hdinsight-component-versioning/

[zookeeper]: http://zookeeper.apache.org/ 
<!--HONumber=42-->
