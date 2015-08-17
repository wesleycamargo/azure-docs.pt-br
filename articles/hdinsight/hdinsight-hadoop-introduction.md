<properties
	pageTitle="O que é o Hadoop no HDInsight: análise de Big Data na nuvem | Microsoft Azure"
	description="Uma introdução aos componentes do Hadoop na nuvem no HDInsight. Saiba como o HDInsight usa clusters Hadoop para gerenciar, analisar e gerar relatórios de Big Data."
	services="hdinsight"
	documentationCenter=""
	authors="cjgronlund"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/11/2015"
   ms.author="cgronlun"/>


# Introdução ao Hadoop no HDInsight: análise de Big Data e processamento na nuvem

Veja uma introdução ao Hadoop, seu ecossistema e Big Data no Azure HDInsight: o que é o Hadoop no HDInsight e quais são os componentes, a terminologia comum e os cenários de análise de Big Data? Além disso, saiba mais sobre tutoriais e a documentação do Hadoop e os recursos para usar o Hadoop no HDInsight.

## O que é o Hadoop no HDInsight?

O Azure HDInsight implanta e provisiona clusters do Apache Hadoop na nuvem, fornecendo uma estrutura de software criada para gerenciar, analisar e informar sobre o Big Data com alta confiabilidade e disponibilidade. O HDInsight usa a distribuição de Hadoop **HDP (Hortonworks Data Platform)**. O Hadoop geralmente se refere a todo o ecossistema de componentes no em Hadoop, o que inclui clusters Storm e HBase, bem como outras tecnologias sob o espectro do Hadoop. Consulte [Visão geral do ecossistema do Hadoop no HDInsight](#overview) abaixo para obter detalhes.


## O que é big data?
Big data se refere aos dados que estão sendo coletados em volumes sempre crescentes, em velocidades cada vez mais altas e para uma variedade cada vez maior de formatos não estruturados e contextos semânticos variáveis.

Os Big Data descrevem qualquer grande quantidade de informações digitais, do texto em um feed do Twitter a informações de sensores em equipamentos industriais e a informações sobre navegação e compras de consumidores em um catálogo online. Os big data podem ser históricos (referentes a dados armazenados) ou em tempo real (o que significa que são transmitidos diretamente da fonte).

Para que o Big Data forneça inteligência ou insight acionáveis, não só devemos fazer as perguntas certas e dados serem relevantes para que os problemas sejam coletados; os dados também devem estar acessíveis, limpos, analisados e serem apresentados de forma útil. É exatamente nisso que a análise de Big Data no Hadoop no HDInsight pode ajudar.


## <a name="overview"></a>Visão geral do ecossistema Hadoop no HDInsight

O HDInsight é uma implementação de nuvem no Microsoft Azure da pilha de tecnologia Apache Hadoop, que está se expandindo rapidamente e é a solução exata para a análise de Big Data. Ele inclui implementações de Storm, HBase, Pig, Hive, Sqoop, Oozie, Ambari e assim por diante. O HDInsight também se integra a ferramentas de business intelligence (BI), como Excel, SQL Server Analysis Services e SQL Server Reporting Services.

### Cluster do Windows e Linux

O Azure HDInsight implanta e provisiona clusters Hadoop na nuvem, usando **Linux** ou **Windows** como sistema operacional subjacente.

* **HDInsight no Linux (visualização)**: um cluster Hadoop no Ubuntu. Use esta opção se estiver familiarizado com o Linux ou Unix, se estiver migrando de uma solução Hadoop baseada em Linux existente ou se quiser fácil integração com componentes do ecossistema Hadoop criados para Linux.

* **HDInsight no Windows**: um cluster Hadoop no Windows Server. Use esta opção se estiver familiarizado com o Windows, se estiver migrando de uma solução Hadoop baseada em Windows existente ou se quiser integrar ao .NET ou a outros recursos do Windows.

A tabela a seguir compara os dois:

Categoria | Hadoop no Linux | Hadoop no Windows
---------| -------------------| --------------------
**Sistema operacional do cluster** | Ubuntu 12.04 Long Term Support (LTS) | Windows Server 2012 R2
**Tipo de cluster** | O Hadoop | Hadoop, HBase, Storm
**Implantação** | Portal de gerenciamento do Azure, CLI do Azure, PowerShell do Azure | Portal de gerenciamento do Azure, CLI do Azure, PowerShell do Azure, SDK .NET do HDInsight
**IU do cluster** | Ambari | Painel do cluster
**Acesso remoto** | SSH | Protocolo RDP



### Hadoop, HBase, Storm e clusters personalizados

O HDInsight fornece configurações de cluster para cargas de trabalho específicas: Hadoop, HBase ou Storm. Ou, você também pode <a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-customize-cluster/" target="_blank">Personalizar os clusters com ações de script</a>.

* **Hadoop** (a carga de trabalho "Consulta"): fornece armazenamento de dados confiável com [HDFS](#HDFS) e um modelo de programação [MapReduce](#mapreduce) simples para processar e analisar dados em paralelo.

* **<a target="_blank" href="http://hbase.apache.org/">HBase</a>** (a carga de trabalho "NoSQL"): um banco de dados NoSQL baseado em Hadoop que fornece acesso aleatório e coerência forte para grandes volumes de dados não estruturados e semiestruturados - potencialmente, bilhões de linhas vezes milhões de colunas. Consulte [Visão geral do HBase no HDInsight](hdinsight-hbase-overview.md).

* **<a  target="_blank" href="https://storm.incubator.apache.org/">Apache Storm</a>** (a carga de trabalho "Stream"): é um sistema de computação distribuído e em tempo real para o processamento rápido de grandes fluxos de dados. O Storm é oferecido como um cluster gerenciado no HDInsight. Consulte [Analisar dados do sensor em tempo real usando o Storm e o Hadoop](hdinsight-storm-sensor-data-analysis.md).


## Quais são os componentes do Hadoop?

Além das configurações gerais anteriores, os seguintes componentes individuais também estão incluídos nos clusters HDInsight.

* **[Ambari](#ambari)**: provisionamento, gerenciamento e monitoramento de clusters.

* **[Avro](#avro)** (Biblioteca de Microsoft .NET para Avro): serialização de dados para o ambiente Microsoft .NET.

* **[Hive e HCatalog](#hive)**: consulta similar a SQL (Structured Query Language) e uma camada de gerenciamento de armazenamento e tabela.

* **[Mahout](#mahout)**: aprendizado de máquina.

* **[MapReduce e YARN](#mapreduce)**: processamento e gerenciamento de recursos distribuídos.

* **[Oozie](#oozie)**: gerenciamento de fluxo de trabalho.

* **[Phoenix](#phoenix)**: uma camada de banco de dados relacional em HBase.

* **[Pig](#pig)**: script mais simples para transformações de MapReduce.

* **[Sqoop](#sqoop)**: importação e exportação de dados.

* **[Tez](#tez)**: permite que os processos de uso intensivo de dados sejam executados com eficiência em grande escala.

* **[ZooKeeper](#zookeeper)**: coordenação de processos em sistemas distribuídos.

> [AZURE.NOTE]Para obter informações sobre os componentes específicos e informações de versão, consulte [O que há de novo nas versões de cluster Hadoop fornecidas pelo HDInsight?][component-versioning]

###<a name="ambari"></a>Ambari

O Apache Ambari serve para provisionar, gerenciar e monitorar clusters do Apache Hadoop. Inclui uma coleção de ferramentas intuitivas para operador e um conjunto abrangente de APIs que ocultam a complexidade do Hadoop, simplificando a operação de clusters. Consulte [Gerenciar clusters HDInsight usando o Ambari](hdinsight-hadoop-manage-ambari.md) (somente no Linux), [Monitorar clusters Hadoop no HDInsight usando a API do Ambari](hdinsight-monitor-use-ambari-api.md) e <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">Referência de API do Apache Ambari</a>.

### <a name="avro"></a>Avro (Biblioteca do Microsoft .NET para Avro)

A Biblioteca do Microsoft .NET para Avro implementa o formato de intercâmbio de dados binário compacto do Apache Avro para serialização para o ambiente Microsoft.NET. Ela usa <a target="_blank" href="http://www.json.org/">JSON (JavaScript Object Notation)</a> para definir um esquema independente de linguagem que garante a interoperabilidade de linguagem, isto é, dados serializados em uma linguagem podem ser lidos em outra linguagem. Você encontra informações detalhadas sobre o formato na <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">Especificação do Apache Avro</a>. O formato dos arquivos do Avro suporta o modelo de programação distribuído do MapReduce. Os arquivos são “divisíveis”, o que significa que você pode buscar qualquer ponto em um arquivo e iniciar a leitura por meio de um bloco em específico. Para saber como fazer isso, consulte [Serializar dados com a Biblioteca do Microsoft .NET para Avro](hdinsight-dotnet-avro-serialization.md).


### <a name="hdfs"></a>HDFS

O Sistema de Arquivos Distribuído Hadoop (HDFS) é um sistema de arquivos distribuído que, com o MapReduce e o YARN, é o núcleo do ecossistema do Hadoop. HDFS é o sistema de arquivo padrão para clusters Hadoop no HDInsight.

### <a name="hive"></a>Hive & HCatalog

<a target="_blank" href="http://hive.apache.org/">Apache Hive</a> é um software de data warehouse baseado no Hadoop que permite consultar e gerenciar grandes conjuntos de dados no armazenamento distribuído usando uma linguagem semelhante ao SQL, chamada HiveQL. O Hive, como o Pig, é uma abstração sobre o MapReduce. Quando executado, o Hive converte as consultas em uma série de trabalhos de MapReduce. O Hive é conceitualmente mais próximo a um sistema de gerenciamento de banco de dados relacional que o Pig e, portanto, é mais adequado para uso com dados mais estruturados. Para dados não estruturados, o Pig é a melhor opção. Consulte [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md).

<a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a> é uma camada de gerenciamento de armazenamento e de tabela para Hadoop que apresenta aos usuários uma exibição de dados relacional. No HCatalog, você pode ler e gravar arquivos em qualquer formato para o qual um SerDe (serializador desserializador) Hive possa ser gravado.

### <a name="mahout"></a>Mahout

<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> é uma biblioteca dimensionável de algoritmos de aprendizado de máquina que são executados no Hadoop. Usando princípios de estatísticas, os aplicativos de aprendizado de máquina ensinam aos sistemas a aprender com os dados e a usar os resultados passados para determinar um comportamento futuro. Consulte [Gerar recomendações de vídeo usando o Mahout no Hadoop](hdinsight-mahout.md).

### <a name="mapreduce"></a>MapReduce e YARN
O Hadoop MapReduce é uma estrutura de software para escrever aplicativos que processam conjuntos de Big Data em paralelo. Um trabalho do MapReduce divide grandes conjuntos de dados e organiza os dados em pares de valores chave para processamento.

O Apache YARN é a próxima geração do MapReduce (MapReduce 2.0, ou MRv2), que divide as duas principais tarefas do JobTracker, gerenciamento de recursos e planejamento/monitoramento de trabalho, em entidades separadas.

Para obter mais informações sobre o MapReduce, consulte <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> no Wiki do Hadoop. Para saber mais sobre YARN, consulte <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (YARN)</a>.

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> é um sistema de coordenação do fluxo de trabalho que gerencia trabalhos do Hadoop. É integrado com a pilha do Hadoop e oferece suporte a trabalhos do Hadoop para MapReduce, Pig, Hive e Sqoop. Também pode ser usado para agendar trabalhos específicos para um sistema, como programas Java ou scripts de shell. Consulte [Usar um Coordenador Oozie baseado em tempo com o Hadoop](hdinsight-use-oozie-coordinator-time.md).

### <a name="phoenix"></a>Phoenix
O <a  target="_blank" href="http://phoenix.apache.org/">Apache Phoenix</a> é uma camada de banco de dados relacional em HBase. O Phoenix inclui um driver JDBC que permite aos usuários consultar e gerenciar tabelas SQL diretamente. O Phoenix converte consultas e outras instruções em chamadas de API NoSQL nativas, em vez de usar o MapReduce, permitindo, assim, aplicativos mais rápidos sobre repositórios NoSQL. Consulte [Usar Apache Phoenix e SQuirreL com clusters do HBase](hdinsight-hbase-phoenix-squirrel.md).


### <a name="pig"></a>Pig
<a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> é uma plataforma de alto nível que permite executar transformações complexas de MapReduce em enormes conjuntos de dados usando uma linguagem de script simples, chamada Pig Latin. O Pig traduz os scripts em Pig Latin para que sejam executados dentro do Hadoop. Você pode criar Funções Definidas pelo Usuário (UDFs) para estender a Pig Latin. Consulte [Usar o Pig com o Hadoop para analisar um arquivo de log do Apache](hdinsight-use-pig.md).

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> é a ferramenta que transferências dados em massa entre o Hadoop e bancos de dados relacionais, como um SQL, ou outros repositórios de dados estruturados, da forma mais eficiente possível. Consulte [Usar o Sqoop com o Hadoop](hdinsight-use-sqoop.md).

### <a name="tez"></a>Tez
O <a  target="_blank" href="http://tez.apache.org/">Apache Tez</a> é uma estrutura de aplicativo baseada no Hadoop YARN que executa gráficos complexos, gráficos acíclicos de processamento geral de dados. Ele é um sucessor mais flexível e poderoso para a estrutura do MapReduce que permite que processos de uso intensivo de dados, como o Hive, sejam executados com mais eficiência em grande escala. Consulte ["Usar o Apache Tez para melhorar o desempenho" em Usar o Hive e HiveQL](hdinsight-use-hive.md#usetez).


### <a name="zookeeper"></a>ZooKeeper
O <a  target="_blank" href="http://zookeeper.apache.org/">Apache ZooKeeper</a> coordena processos em grandes sistemas distribuídos por meio de um namespace hierárquico compartilhado de registros de dados (znodes). Os Znodes contêm pequenas quantidades de metainformações necessárias para coordenar processos: status, local, configuração e assim por diante.

## <a name="advantage"></a>Vantagens do Hadoop na nuvem

Como parte do ecossistema de nuvem do Azure, o Hadoop no HDInsight oferece uma série de benefícios, entre eles:

* O provisionamento automático de clusters Hadoop. É muito mais fácil criar clusters HDInsight do que configurar clusters Hadoop manualmente. Para obter detalhes, consulte [Provisionar clusters Hadoop no HDInsight](hdinsight-provision-clusters.md).

* Componentes do Hadoop de última geração. Para obter detalhes, consulte [O que há de novo nas versões de cluster Hadoop fornecidas pelo HDInsight?][component-versioning].

* Alta disponibilidade e confiabilidade dos clusters. Consulte [Disponibilidade e confiabilidade dos clusters Hadoop no HDInsight](hdinsight-high-availability.md) para obter detalhes.

* Armazenamento de dados eficiente e econômico com o armazenamento de Blob do Azure, uma opção compatível com o Hadoop. Consulte [Usar o armazenamento de Blob do Azure com o Hadoop no HDInsight](hdinsight-hadoop-use-blob-storage.md) para obter detalhes.

* Integração com outros serviços do Azure, incluindo [aplicativos Web](../documentation/services/app-service/web/) e [Banco de Dados SQL](../documentation/services/sql-database/).

* Baixo custo de entrada. Inicie uma [avaliação gratuita](/pricing/free-trial/) ou consulte os [Detalhes de preço do HDInsight](/pricing/details/hdinsight/).


Para ler mais sobre as vantagens do Hadoop no HDInsight, consulte a [Página de recursos do Azure para HDInsight][marketing-page].



## <a id="resources"></a>Recursos para aprender mais sobre análise de Big Data, Hadoop e HDInsight

Amplie esta introdução ao Hadoop no HDInsight e à análise de Big Data com os recursos a seguir.


### HDInsight no Linux (visualização)

* [Introdução ao HDInsight no Linux](hdinsight-hadoop-linux-tutorial-get-started.md): um tutorial de início rápido para provisionamento de clusters Hadoop do HDInsight no Linux e execução de consultas Hive de exemplo.

* [Provisionar o HDInsight no Linux usando opções personalizadas](hdinsight-hadoop-provision-linux-clusters.md): aprenda a provisionar um cluster Hadoop do HDInsight no Linux usando opções personalizadas por meio do Portal de Gerenciamento do Azure, da CLI do Azure ou do PowerShell do Azure.

* [Trabalhando com o HDInsight no Linux](hdinsight-hadoop-linux-information.md): veja algumas dicas rápidas sobre como trabalhar com clusters Hadoop em Linux provisionados no Azure.

* [Gerenciar clusters HDInsight usando o Ambari](hdinsight-hadoop-manage-ambari.md): aprenda a monitorar e gerenciar seu Hadoop baseado em Linux no cluster HDInsight usando Web do Ambari ou a API REST do Ambari.


### HDInsight no Windows

* [Documentação do HDInsight](http://azure.microsoft.com/documentation/services/hdinsight/): a página de documentação do Azure HDInsight com links para artigos, vídeos e mais recursos.

* [Mapa de aprendizado do HDInsight](hdinsight-learn-map.md): um tour guiado da documentação do Hadoop para HDInsight.

* [Introdução ao Azure HDInsight](hdinsight-hadoop-tutorial-get-started-windows.md): um tutorial de início rápido para usar o Hadoop no HDInsight.

* [Executar os exemplos do HDInsight](hdinsight-run-samples.md): um tutorial sobre como executar os exemplos fornecidos com o HDInsight.

* [SDK do Azure HDInsight](http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx): documentação de referência do SDK do HDInsight.


### Apache Hadoop

* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a>: saiba mais sobre a biblioteca de software Apache Hadoop, uma estrutura que permite o processamento distribuído de grandes conjuntos de dados entre clusters de computadores.

* <a target="_blank" href="http://hadoop.apache.org/docs/r0.18.1/hdfs_design.html">HDFS</a>: saiba mais sobre a arquitetura e o design do Sistema de Arquivos Distribuído do Hadoop, o principal sistema de armazenamento usado pelos aplicativos Hadoop.

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html">Tutorial do MapReduce</a>: saiba mais sobre a estrutura de programação para escrever aplicativos Hadoop que processam rapidamente grandes volumes de dados em paralelo em grandes clusters de nós de computação.

### Bancos de Dados SQL no Azure

* [Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/windowsazure/ee336279.aspx): documentação do MSDN sobre o Banco de Dados SQL.

* [Portal de Gerenciamento do Banco de Dados SQL](https://msdn.microsoft.com/library/azure/dn771027.aspx): uma ferramenta de gerenciamento de banco de dados leve e fácil de usar para gerenciamento do Banco de Dados SQL na nuvem.

* [Adventure Works para Banco de Dados SQL](http://msftdbprodsamples.codeplex.com/releases/view/37304): página para baixar um exemplo de Banco de Dados SQL.

### Microsoft Business Intelligence (para HDInsight no Windows)

Ferramentas de BI (business intelligence) conhecidas – como Excel, PowerPivot, SQL Server Analysis Services e SQL Server Reporting Services - recuperam, analisam e geram relatórios de dados, integrados com o HDInsight por meio do suplemento Power Query ou do Driver de ODBC do Microsoft Hive.

Essas ferramentas de BI podem ajudar sua análise de Big Data de diversas formas:

* [Conectar o Excel ao Hadoop com o Power Query](hdinsight-connect-excel-power-query.md): aprenda a conectar o Excel à conta de Armazenamento do Azure que armazena os dados associados ao seu cluster HDInsight usando o Microsoft Power Query para Excel.

* [Conectar o Excel ao Hadoop com o Driver de ODBC do Microsoft Hive](hdinsight-connect-excel-hive-ODBC-driver.md): aprenda a importar dados do HDInsight com o Driver de ODBC do Microsoft Hive.

* [Plataforma de nuvem da Microsoft](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx): saiba mais sobre o Power BI para Office 365, baixe a avaliação do SQL Server e configure o SharePoint Server 2013 e o SQL Server BI.

* <a target="_blank" href="http://msdn.microsoft.com/library/hh231701.aspx">Saiba mais sobre SQL Server Analysis Services</a>.

* <a target="_blank" href="http://msdn.microsoft.com/library/ms159106.aspx">Saiba mais sobre o SQL Server Reporting Services</a>.


### Experimente soluções Hadoop para a análise de Big Data (para HDInsight no Windows)

Use a análise de Big Data nos dados da sua organização para obter insights de seus negócios. Estes são alguns exemplos:

* [Dados de sensor de sistemas HVAC](hdinsight-hive-analyze-sensor-data.md): aprenda a analisar dados do sensor usando o Hive com HDInsight (Hadoop) e, depois, visualize os dados no Microsoft Excel. Neste exemplo, você usará o Hive para processar dados históricos produzidos por sistemas HVAC para identificar quais sistemas não conseguem manter uma temperatura determinada de maneira confiável.

* [Usar o Hive com HDInsight para analisar logs de sites](hdinsight-hive-analyze-website-log.md): aprenda a usar o HiveQL no HDInsight para analisar logs de sites para obter informações sobre a frequência de visitas em um dia provenientes de sites externos e um resumo dos erros de site que os usuários enfrentam.

* [Analisar dados de sensor em tempo real com Storm e HBase no HDInsight (Hadoop)](hdinsight-storm-sensor-data-analysis.md): aprenda a criar uma solução que usa um cluster Storm no HDInsight para processar dados de sensor de Hubs de Eventos do Azure e, depois, exiba os dados de sensor processados como informações quase em tempo real em um painel baseado na Web.


[marketing-page]: ../services/hdinsight/
[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/
 

<!---HONumber=August15_HO6-->