<properties urlDisplayName="HDInsight Introduction" pageTitle="Introdu&ccedil;&atilde;o ao Hadoop no HDInsight: An&aacute;lise de big data na nuvem | Azure" metaKeywords="" description="Saiba como o Azure HDInsight usa clusters do Apache Hadoop na nuvem para fornecer uma estrutura de software para gerenciar, analisar e relatar sobre Big Data." metaCanonical="" services="hdinsight" documentationCenter="" title="Introdu&ccedil;&atilde;o ao Hadoop no HDInsight: Processamento e an&aacute;lise de big data na nuvem" authors="cgronlun" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="cgronlun" />

# Introdução ao Hadoop no HDInsight: Processamento e análise de big data na nuvem

Obtenha uma visão geral dos componentes do HDInsight, terminologia comum e cenários, e visualize os recursos para usar o Hadoop no HDInsight.

O Azure HDInsight implanta e provisiona clusters do Apache Hadoop na nuvem, fornecendo uma estrutura de software criada para gerenciar, analisar e relatar Big Data. O núcleo do Hadoop fornece armazenamento de dados confiável com o HDFS (Sistema de Arquivos Distribuído Hadoop) e um modelo de programação simples MapReduce para processar e analisar, paralelamente, os dados armazenados nesse sistema distribuído.

### O que é big data?

Big data se refere aos dados que estão sendo coletados em volumes sempre crescentes, em velocidades cada vez mais altas e para uma variedade cada vez maior de formatos não estruturados e contextos semânticos variáveis.

Big data descreve qualquer grande quantidade, desde informações digitais do texto em um feed do Twitter até as informações de sensores de equipamentos industriais, para obter informações relativas à navegação do cliente e a compras em um catálogo online. Os big data podem ser históricos (referentes a dados armazenados) ou em tempo real (o que significa que são transmitidos diretamente da fonte).

Para que os big data ofereçam inteligência ou informações úteis, não só as perguntas certas devem ser feitas e os dados relevantes aos problemas devem ser coletados, como também os dados devem estar acessíveis, estar limpos, ser analisados e, em seguida, ser apresentados de uma maneira útil. É aí que o Hadoop no HDInsight pode ajudar.

## Neste artigo

Este artigo oferece uma visão geral do Hadoop no HDInsight, incluindo:

-   **[Visão geral do ecossistema do Hadoop no HDInsight:][Visão geral do ecossistema do Hadoop no HDInsight:]** O HDInsight é a solução Hadoop no Azure e oferece implementações de Storm, HBase, Pig, Hive, Sqoop, Oozie, Ambari, e assim por diante. O HDInsight também se integra a ferramentas de business intelligence (BI), como Excel, SQL Server Analysis Services e SQL Server Reporting Services.
-   **[Vantagens do Hadoop na nuvem:][Vantagens do Hadoop na nuvem:]** Motivos pelos quais você deve considerar a implementação da nuvem do Hadoop do HDInsight.
-   **[Soluções do HDInsight para a análise de big data:][Soluções do HDInsight para a análise de big data:]** Algumas maneiras práticas em que você pode usar o HDInsight para responder perguntas para sua organização, desde analisar sentimentos do Twitter até analisar a eficácia do sistema de HVAC.
-   **[Recursos para aprender mais sobre a análise de big data, Hadoop e HDInsight:][Recursos para aprender mais sobre a análise de big data, Hadoop e HDInsight:]** Links para informações adicionais.

## <a name="overview"></a>Visão geral do ecossistema do Hadoop no HDInsight

Hadoop é a pilha de tecnologia em rápida expansão que é a solução preferida para análise de big data. O HDInsight é a estrutura para a implementação e nuvem do Microsoft Azure do Hadoop.

Os clusters Hadoop no HDInsight usam versões da distribuição de HortonWorks Data Platform (HDP) e um conjunto de componentes do Hadoop contidos nessa distribuição. Para obter informações sobre os componentes e suas versões nos clusters do HDInsight, consulte [O que há de novo nas versões de cluster Hadoop fornecidas pelo HDInsight?][O que há de novo nas versões de cluster Hadoop fornecidas pelo HDInsight?]

O Apache Hadoop é uma estrutura de software para a análise e o gerenciamento de big data. O núcleo do Apache Hadoop fornece armazenamento de dados confiável com o HDFS (Sistema de Arquivos Distribuído Hadoop) e um modelo de programação simples MapReduce para processar e analisar, paralelamente, os dados armazenados nesse sistema distribuído. O HDFS usa replicação de dados para solucionar problemas de falha de hardware que podem surgir durante a implantação desses sistemas altamente distribuídos.

A seguir são apresentadas as tecnologias de Hadoop no HDInsight:

-   **[Ambari][Ambari]:** Provisionamento, gerenciamento e monitoramento de cluster
-   **[Avro][Avro]** (Biblioteca do Microsoft .NET para Avro): Serialização de dados para o ambiente do Microsoft .NET
-   **[HBase][HBase]:** Banco de dados não relacional para tabelas muito grandes
-   **[HDFS][HDFS]:** Sistema de Arquivos Distribuído Hadoop
-   **[Hive][Hive]:** Consulta semelhante a SQL
-   **[Mahout][Mahout]:** Aprendizado de máquina
-   **[MapReduce e YARN][MapReduce e YARN]:** Processamento distribuído e gerenciamento de recursos
-   **[Oozie][Oozie]:** Gerenciamento de fluxo de trabalho
-   **[Pig][Pig]:** Script mais simples para transformações do MapReduce
-   **[Sqoop][Sqoop]:** Importação e exportação de dados
-   **[Storm][Storm]:** Processamento em tempo real de fluxos de dados rápidos e grandes
-   **[Zookeeper][Zookeeper]:** Processos de coordenadas em sistemas distribuídos

Além disso, saiba mais sobre as **[ferramentas de business intelligence][ferramentas de business intelligence]** que você pode usar com o HDInsight.

### <a name="ambari"></a>Ambari

O Apache Ambari serve para provisionar, gerenciar e monitorar clusters do Apache Hadoop. Inclui uma coleção de ferramentas intuitivas para operador e um conjunto abrangente de APIs que ocultam a complexidade do Hadoop, simplificando a operação de clusters. Consulte [Monitorar clusters Hadoop no HDInsight usando a API do Ambari][Monitorar clusters Hadoop no HDInsight usando a API do Ambari] e [Referência da API do Apache Ambari][Referência da API do Apache Ambari].

### <a name="avro"></a>Avro (Biblioteca do Microsoft .NET para Avro)

A Biblioteca do Microsoft .NET para Avro implementa o formato de intercâmbio de dados binário compacto do Apache Avro para serialização para o ambiente Microsoft.NET. Ela utiliza [JSON][JSON] para definir um esquema com neutralidade de linguagem que subscreve a interoperabilidade de linguagem, o que significa que os dados serializados em uma linguagem podem ser lidos em outra diferente. Informações detalhadas sobre o formato podem ser encontradas na [Especificação do Apache Avro][Especificação do Apache Avro].
O formato dos arquivos do Avro oferece suporte ao modelo de programação distribuído do MapReduce. Os arquivos são “divisíveis”, o que significa que você pode buscar qualquer ponto em um arquivo e iniciar a leitura por meio de um bloco em específico. Para saber como fazer isso, consulte [Serializar dados com a Biblioteca do Microsoft .NET para Avro][Serializar dados com a Biblioteca do Microsoft .NET para Avro].

### <a name="hbase"></a>HBase

O [Apache HBase][Apache HBase] é um banco de dados não relacional baseado no Hadoop e projetado para grandes quantidades de dados não estruturados e semiestruturados, possivelmente bilhões de linhas vezes milhões de colunas. Os clusters HBase são configurados para armazenar dados diretamente no armazenamento de Blob do Azure, com baixa latência e elasticidade elevada. Consulte [Visão geral do HBase no HDInsight][Visão geral do HBase no HDInsight].

### <a name="hdfs"></a>HDFS

O Sistema de Arquivos Distribuído Hadoop (HDFS) é um sistema de arquivos distribuído que, com o MapReduce e o YARN, é o núcleo do ecossistema do Hadoop. HDFS é o sistema de arquivo padrão para clusters Hadoop no HDInsight.

### <a name="hive"></a>Hive

[Apache Hive][Apache Hive] é um software de armazenamento de dados baseado no Hadoop que permite consultar e gerenciar grandes conjuntos de dados em armazenamento distribuído usando uma linguagem semelhante à SQL chamada HiveQL. O Hive, assim como o Pig, é uma abstração sobre o MapReduce e, quando executado, converte as consultas em uma série de trabalhos de MapReduce. O Hive é conceitualmente mais próximo a um sistema de gerenciamento de banco de dados relacional que o Pig e, portanto, é mais adequado para uso com dados mais estruturados. Para dados não estruturados, o Pig é a melhor opção. Consulte [Usar o Hive com Hadoop no HDInsight][Usar o Hive com Hadoop no HDInsight]

### <a name="mahout"></a>Mahout

O [Apache Mahout][Apache Mahout] é uma biblioteca escalável de algoritmos de aprendizado de máquina executada no Hadoop. Usando princípios de estatísticas, os aplicativos de aprendizado de máquina ensinam aos sistemas a aprender com os dados e a usar os resultados passados para determinar um comportamento futuro. Consulte [Gerar recomendações de vídeo usando o Mahout no Hadoop][Gerar recomendações de vídeo usando o Mahout no Hadoop].

### <a name="mapreduce"></a>MapReduce e YARN

O MapReduce do Hadoop é uma estrutura de software para escrever aplicativos que processam conjuntos de big data paralelamente. Um trabalho de MapReduce divide grandes conjuntos de dados e organiza os dados em pares de valores-chave para processamento.

O Apache YARN é a próxima geração do MapReduce (MapReduce 2.0, ou MRv2), que divide as duas principais tarefas do JobTracker, gerenciamento de recursos e planejamento/monitoramento de trabalho, em entidades separadas.

Para obter mais informações sobre o MapReduce, consulte [MapReduce][MapReduce] no Wiki do Hadoop. Para saber mais sobre o YARN, consulte [MapReduce de próxima geração do Apache Hadoop (YARN)][MapReduce de próxima geração do Apache Hadoop (YARN)].

### <a name="oozie"></a>Oozie

O [Apache Oozie][Apache Oozie] é um sistema de coordenação de fluxo de trabalho que gerencia trabalhos do Hadoop. É integrado com a pilha do Hadoop e oferece suporte a trabalhos do Hadoop para MapReduce, Pig, Hive e Sqoop. Também pode ser usado para agendar trabalhos específicos para um sistema, como programas Java ou scripts de shell. Consulte [Usar o Coordenador baseado em tempo do Oozie com o Hadoop no HDInsight][Usar o Coordenador baseado em tempo do Oozie com o Hadoop no HDInsight].

### <a name="pig"></a>Pig

O [Apache Pig][Apache Pig] é uma plataforma de alto nível que permite que você realize transformações complexas do MapReduce em conjuntos de dados bastante grandes usando uma linguagem de script simples chamada Pig Latin. O Pig traduz os scripts em Pig Latin para que sejam executados dentro do Hadoop. Você pode criar Funções Definidas pelo Usuário (UDFs) para estender a Pig Latin. Consulte [Usar o Pig com o Hadoop para analisar um arquivo de log do Apache][Usar o Pig com o Hadoop para analisar um arquivo de log do Apache].

### <a name="sqoop"></a>Sqoop

O [Apache Sqoop][Apache Sqoop] é uma ferramenta que transfere dados em massa entre o Hadoop e bancos de dados relacionais, como o SQL, ou outros armazenamentos de dados estruturados, do modo mais eficiente possível. Consulte [Usar o Sqoop com o Hadoop][Usar o Sqoop com o Hadoop].

### <a name="storm"></a>Storm

O [Apache Storm][Apache Storm] é um sistema de computação distribuído e em tempo real para o processamento rápido de grandes fluxos de dados. O Storm é oferecido como um cluster gerenciado no HDInsight. Consulte [Analisar dados do sensor em tempo real usando o Storm e o Hadoop][Analisar dados do sensor em tempo real usando o Storm e o Hadoop].

### <a name="zookeeper"></a>Zookeeper

O [Apache Zookeeper][Apache Zookeeper] coordena processos em grandes sistemas distribuídos por meio de um namespace hierárquico compartilhado de registros de dados (znodes). O Znodes contém pequenas quantidades de metainformações necessárias para coordenar os processos: status, local, configuração e assim por diante.

### <a name="bi"></a>Ferramentas de business intelligence

As ferramentas conhecidas de BI (business intelligence), como o Excel, o PowerPivot, o SQL Server Analysis Services e o Reporting Services, recuperam, analisam e relatam os dados integrados ao HDInsight usando o suplemento Power Query ou o Driver ODBC do Microsoft Hive.

Essas ferramentas de BI podem ajudar em sua análise de big data:

-   [Baixe o Microsoft Power Query para Excel][Baixe o Microsoft Power Query para Excel]
-   [Baixe o Driver ODBC do Microsoft Hive][Baixe o Driver ODBC do Microsoft Hive]
-   [Saiba mais sobre SQL Server Analysis Services][Saiba mais sobre SQL Server Analysis Services]
-   [Saiba mais sobre SQL Server Reporting Services][Saiba mais sobre SQL Server Reporting Services]

## <a name="advantage"></a>Vantagens do Hadoop na nuvem

Como parte do ecossistema de nuvem do Azure, o Hadoop no HDInsight oferece uma série de benefícios, entre eles:

-   Componentes do Hadoop de última geração. Para obter detalhes, consulte [O que há de novo nas versões de cluster Hadoop fornecidas pelo HDInsight?][O que há de novo nas versões de cluster Hadoop fornecidas pelo HDInsight?]
-   Alta disponibilidade e confiabilidade dos clusters. Consulte [Disponibilidade e confiabilidade dos clusters Hadoop no HDInsight][Disponibilidade e confiabilidade dos clusters Hadoop no HDInsight] para obter detalhes.
-   Armazenamento de dados eficiente e econômico com o armazenamento de Blob do Azure, uma opção compatível com o Hadoop. Consulte [Usar o armazenamento de Blob do Azure com o Hadoop no HDInsight][Usar o armazenamento de Blob do Azure com o Hadoop no HDInsight] para obter detalhes.
-   Integração com outros serviços do Azure, incluindo [Websites][Websites] e [Banco de Dados SQL][Banco de Dados SQL].
-   Baixo custo de entrada. Inicie uma [avaliação gratuita][avaliação gratuita] ou consulte os [Detalhes de preço do HDInsight][Detalhes de preço do HDInsight].

Para ler mais sobre as vantagens do Hadoop no HDInsight, consulte a [Página de recursos do Azure para HDInsight][Página de recursos do Azure para HDInsight].

## <a name="solutions"></a>Experimentar as soluções do HDInsight para a análise de big data

Analise dados de sua organização para obter informações sobre seus negócios. Estes são alguns exemplos:

-   [Analisar dados HVAC do sensor][Analisar dados HVAC do sensor]: Saiba como analisar dados do sensor usando o Hive com HDInsight (Hadoop) e depois visualize os dados no Microsoft Excel. Neste exemplo, você usará o Hive para processar dados históricos produzidos por sistemas HVAC para identificar quais sistemas não conseguem manter uma temperatura determinada de maneira confiável.
-   [Usar o Hive com o HDInsight para analisar os logs do Website][Usar o Hive com o HDInsight para analisar os logs do Website]: Saiba como usar HiveQL no HDInsight para analisar os logs do Website para obter informações sobre a frequência de visitas em um dia por meio de Websites externos e um resumo dos erros do Website enfrentados pelos usuários.
-   [Analisar dados do sensor em tempo real com o Storm e o HBase no HDInsight (Hadoop)][Analisar dados do sensor em tempo real usando o Storm e o Hadoop]: Saiba como criar uma solução que usa um cluster Storm no HDInsight para processar dados do sensor por meio de Hubs de Evento do Azure e depois exibe os dados do sensor processados como informações quase em tempo real em um painel baseado na Web.

Para experimentar o Hadoop no HDInsight, consulte os artigos de "Introdução" na seção Explorar na [Página de documentação do HDInsight][Página de documentação do HDInsight]. Para experimentar exemplos mais avançados, role a tela até a seção Analisar.

## <a name="resources"></a>Recursos para aprender mais sobre a análise de big data, Hadoop e HDInsight

### Hadoop no HDInsight

-   [Documentação do HDInsight][Página de documentação do HDInsight]: a página de documentação do Azure HDInsight com links para artigos, vídeos e mais recursos.

-   [Mapa de aprendizagem para Hadoop no HDInsight][Mapa de aprendizagem para Hadoop no HDInsight]: Um tour guiado da documentação do Hadoop para o HDInsight.

-   [Introdução ao Azure HDInsight][Introdução ao Azure HDInsight]: Um tutorial de início rápido para usar o Hadoop no HDInsight.

-   [Executar os exemplos do HDInsight][Executar os exemplos do HDInsight]: um tutorial sobre como executar os exemplos que acompanham o HDInsight.

-   [SDK do Azure HDInsight][SDK do Azure HDInsight]: documentação de referência do SDK do HDinsight.

### Bancos de Dados SQL no Azure

-   [Banco de dados SQL do Azure][Banco de dados SQL do Azure]: Documentação de MSDN para o Banco de Dados SQL.

-   [Portal de Gerenciamento para o Banco de Dados SQL][Portal de Gerenciamento para o Banco de Dados SQL]: uma ferramenta de gerenciamento de banco de dados leve e fácil de usar para gerenciamento do Banco de Dados SQL na nuvem.

-   [Adventure Works para o Banco de Dados SQL][Adventure Works para o Banco de Dados SQL]: página de download do banco de dados de amostra do Banco de Dados SQL.

### Microsoft Business Intelligence

-   [Conectar o Excel ao Hadoop com o Power Query][Conectar o Excel ao Hadoop com o Power Query]: saiba como conectar o Excel à conta de armazenamento do Azure que armazena os dados associados ao seu cluster HDInsight usando o Microsoft Power Query para Excel.

-   [Conectar o Excel ao Hadoop com o Driver ODBC do Microsoft Hive][Conectar o Excel ao Hadoop com o Driver ODBC do Microsoft Hive]: Saiba como importar dados do HDInsight com o Driver ODBC do Microsoft Hive.

-   [Microsoft Business Intelligence (BI)][Microsoft Business Intelligence (BI)]: Saiba mais sobre o Power BI para Office 365, baixe a versão de teste do SQL Server e instale o SharePoint Server 2013 e SQL Server BI.

### Apache Hadoop

-   [Apache Hadoop][Apache Hadoop]: saiba mais sobre a biblioteca de software Apache Hadoop, uma estrutura que permite o processamento distribuído de grandes conjuntos de dados em clusters de computadores.

-   [HDFS][1]: saiba mais sobre a arquitetura e o design do HDFS (Sistema de Arquivos Distribuído Hadoop), o sistema de armazenamento primário usado pelos aplicativos Hadoop.

-   [MapReduce][2]: saiba mais sobre a estrutura de programação para escrever aplicativos Hadoop que processam rapidamente grandes quantidades de dados paralelamente em grandes clusters de nós de computação.

  [Visão geral do ecossistema do Hadoop no HDInsight:]: #overview
  [Vantagens do Hadoop na nuvem:]: #advantage
  [Soluções do HDInsight para a análise de big data:]: #solutions
  [Recursos para aprender mais sobre a análise de big data, Hadoop e HDInsight:]: #resources
  [O que há de novo nas versões de cluster Hadoop fornecidas pelo HDInsight?]: ../hdinsight-component-versioning/
  [Ambari]: #ambari
  [Avro]: #avro
  [HBase]: #hbase
  [HDFS]: #hdfs
  [Hive]: #hive
  [Mahout]: #mahout
  [MapReduce e YARN]: #mapreduce
  [Oozie]: #oozie
  [Pig]: #pig
  [Sqoop]: #sqoop
  [Storm]: #storm
  [Zookeeper]: #zookeeper
  [ferramentas de business intelligence]: #bi
  [Monitorar clusters Hadoop no HDInsight usando a API do Ambari]: ../hdinsight-monitor-use-ambari-api/
  [Referência da API do Apache Ambari]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md
  [JSON]: http://www.json.org/
  [Especificação do Apache Avro]: http://avro.apache.org/docs/current/spec.html
  [Serializar dados com a Biblioteca do Microsoft .NET para Avro]: ../hdinsight-dotnet-avro-serialization/
  [Apache HBase]: http://hbase.apache.org/
  [Visão geral do HBase no HDInsight]: ../hdinsight-hbase-overview/
  [Apache Hive]: http://hive.apache.org/
  [Usar o Hive com Hadoop no HDInsight]: ../hdinsight-use-hive/
  [Apache Mahout]: https://mahout.apache.org/
  [Gerar recomendações de vídeo usando o Mahout no Hadoop]: ../hdinsight-mahout/
  [MapReduce]: http://wiki.apache.org/hadoop/MapReduce
  [MapReduce de próxima geração do Apache Hadoop (YARN)]: http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html
  [Apache Oozie]: http://oozie.apache.org/
  [Usar o Coordenador baseado em tempo do Oozie com o Hadoop no HDInsight]: ../hdinsight-use-oozie-coordinator-time/
  [Apache Pig]: http://pig.apache.org/
  [Usar o Pig com o Hadoop para analisar um arquivo de log do Apache]: ../hdinsight-use-pig/
  [Apache Sqoop]: http://sqoop.apache.org/
  [Usar o Sqoop com o Hadoop]: ../hdinsight-use-sqoop/
  [Apache Storm]: https://storm.incubator.apache.org/
  [Analisar dados do sensor em tempo real usando o Storm e o Hadoop]: ../hdinsight-storm-sensor-data-analysis/
  [Apache Zookeeper]: http://zookeeper.apache.org/
  [Baixe o Microsoft Power Query para Excel]: http://www.microsoft.com/pt-br/download/details.aspx?id=39379
  [Baixe o Driver ODBC do Microsoft Hive]: http://www.microsoft.com/pt-br/download/details.aspx?id=40886
  [Saiba mais sobre SQL Server Analysis Services]: http://www.microsoft.com/pt-br/server-cloud/solutions/business-intelligence/analysis.aspx
  [Saiba mais sobre SQL Server Reporting Services]: http://msdn.microsoft.com/pt-br/library/ms159106.aspx
  [Disponibilidade e confiabilidade dos clusters Hadoop no HDInsight]: ../hdinsight-high-availability/
  [Usar o armazenamento de Blob do Azure com o Hadoop no HDInsight]: ../hdinsight-use-blob-storage/
  [Websites]: ../documentation/services/websites/
  [Banco de Dados SQL]: ../documentation/services/sql-database/
  [avaliação gratuita]: /pricing/free-trial/
  [Detalhes de preço do HDInsight]: ../pricing/details/hdinsight/
  [Página de recursos do Azure para HDInsight]: ../services/hdinsight/
  [Analisar dados HVAC do sensor]: ../hdinsight-hive-analyze-sensor-data/
  [Usar o Hive com o HDInsight para analisar os logs do Website]: ../hdinsight-hive-analyze-website-log/
  [Página de documentação do HDInsight]: ../documentation/services/hdinsight/
  [Mapa de aprendizagem para Hadoop no HDInsight]: ../hdinsight-learn-map
  [Introdução ao Azure HDInsight]: ../hdinsight-get-started/
  [Executar os exemplos do HDInsight]: ../hdinsight-run-samples/
  [SDK do Azure HDInsight]: http://msdnstage.redmond.corp.microsoft.com/pt-br/library/dn479185.aspx
  [Banco de dados SQL do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee336279.aspx
  [Portal de Gerenciamento para o Banco de Dados SQL]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg442309.aspx
  [Adventure Works para o Banco de Dados SQL]: http://msftdbprodsamples.codeplex.com/releases/view/37304
  [Conectar o Excel ao Hadoop com o Power Query]: ../hdinsight-connect-excel-power-query/
  [Conectar o Excel ao Hadoop com o Driver ODBC do Microsoft Hive]: ../hdinsight-connect-excel-hive-ODBC-driver/
  [Microsoft Business Intelligence (BI)]: http://www.microsoft.com/pt-br/server-cloud/solutions/business-intelligence/default.aspx
  [Apache Hadoop]: http://hadoop.apache.org/
