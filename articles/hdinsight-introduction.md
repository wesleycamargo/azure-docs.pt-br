<properties linkid="manage-services-hdinsight-introduction-hdinsight" urlDisplayName="HDInsight Introduction" pageTitle="Introduction to Hadoop in HDInsight | Azure" metaKeywords="" description="Learn how Azure HDInsight uses Apache Hadoop clusters in the cloud, to provide a software framework to manage, analyze, and report on big data." metaCanonical="" services="hdinsight" documentationCenter="" title="Introduction to Hadoop in HDInsight" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# Introdução ao Hadoop no HDInsight

## Visão geral

O Azure HDInsight é um serviço que implanta e provisiona clusters do Apache™ Hadoop® na nuvem, fornecendo uma estrutura de software criada para gerenciar, analisar e relatar Big Data.

### Big data

Os dados são descritos como "Big Data" para indicar que estão sendo coletados em volumes sempre crescentes, em velocidades cada vez mais altas e por uma variedade cada vez maior de formatos não estruturados e contextos semânticos variáveis. A coleta de Big Data não oferece sozinha valor para uma empresa. Para que a solução de Big Data ofereça valor na forma de inteligência ou informações úteis, não só as perguntas certas devem ser feitas e os dados relevantes aos problemas devem ser coletados, como também os dados devem estar acessíveis, estar limpos, ser analisados e, em seguida, ser apresentados de uma maneira útil, geralmente combinados com dados de várias outras fontes que estabelecem perspectiva e contexto no que atualmente é conhecido como mashup.

### Apache Hadoop

O Apache Hadoop é uma estrutura de software que facilita a análise e o gerenciamento de Big Data. O núcleo do Apache Hadoop fornece armazenamento de dados confiável com o HDFS (Sistema de Arquivos Distribuído Hadoop) e um modelo de programação simples MapReduce para processar e analisar, paralelamente, os dados armazenados nesse sistema distribuído. O HDFS usa replicação de dados para solucionar problemas de falha de hardware que podem surgir durante a implantação desses sistemas altamente distribuídos.

### MapReduce e YARN

Para simplificar as complexidades da análise de dados não estruturados de várias fontes, o modelo de programação MapReduce fornece uma abstração de núcleo que garante o encerramento das operações mapeamento e redução. O modelo de programação MapReduce exibe todos os seus trabalhos como cálculos em conjuntos de dados que consistem em pares de chave-valor. Portanto, tanto os arquivos de entrada como os arquivos de saída devem conter conjuntos de dados que consistam apenas em pares de chave-valor. O principal aspecto dessa restrição é que os trabalhos do MapReduce são, consequentemente, combináveis.

Outros projetos relacionados ao Hadoop, como Pig e Hive, são compilados sobre o HDFS e a estrutura MapReduce. Projetos como esses são usados para simplificar o gerenciamento de um cluster, em vez de se trabalhar diretamente com os programas MapReduce. O Pig, por exemplo, permite que você escreva programas usando uma linguagem de procedimento denominada Pig Latin que são compilados para os programas MapReduce no cluster. Ele também oferece controles fluentes para gerenciar o fluxo de dados. O Hive é uma infraestrutura de data warehouse que fornece uma abstração de tabela para os dados contidos nos arquivos armazenados em um cluster que pode ser consultado usando instruções SQL semelhantes em uma linguagem declarativa denominada HiveQL.

### HDInsight

O Azure HDInsight torna o Apache Hadoop disponível como um serviço na nuvem. Ele disponibiliza o HDFS/estrutura de software MapReduce e projetos relacionados, como Pig, Hive e Oozie, em um ambiente mais simples, mais escalonável e econômico.

Um segundo headnode foi adicionado aos clusters Hadoop implantados pelo HDInsight para aumentar a disponibilidade do serviço. Implementações padrão de clusters Hadoop normalmente têm um único headnode. O HDInsight remove esse ponto único de falha com a adição de um headnode secundário. A mudança para uma nova configuração de cluster HA não altera o preço do cluster, a menos que os clientes provisionem clusters com um headnode extragrande.

Uma das principais eficiências introduzidas pelo HDInsight é o seu modo de gerenciamento e armazenamento de dados. O HDInsight usa o armazenamento de Blob do Azure como o sistema de arquivos padrão. O armazenamento de Blob e o HDFS são sistemas de arquivos distintos que são otimizados, respectivamente, para o armazenamento de dados e cálculos nesses dados.

-   O armazenamento de Blob do Azure fornece uma opção de armazenamento altamente escalonável e disponível, de baixo custo, de longo prazo e compartilhável para os dados a serem processados usando o HDInsight.
-   Os clusters Hadoop implantados pelo HDInsight no HDFS são otimizados para a execução de tarefas computacionais do MapReduce nos dados.

Os clusters HDInsight são implantados no Azure em nós de computação para executar tarefas do MapReduce e poderão ser descartados pelos usuários após a conclusão dessas tarefas. A manutenção dos dados nos clusters HDFS após a conclusão dos cálculos seria uma maneira cara de armazenar esses dados. O armazenamento de Blob é uma solução de armazenamento do Azure robusta, para fins gerais. Portanto, o armazenamento de dados no armazenamento de Blob permite que os clusters usados para cálculo sejam excluídos com segurança sem que ocorra perda de dados do usuário. Mas o armazenamento de Blob não é apenas uma solução de baixo custo: ele fornece uma interface de sistema de arquivos HDFS completa que fornece uma experiência perfeita para os clientes, permitindo que o conjunto completo de componentes no ecossistema do Hadoop possa operar (por padrão) diretamente nos dados gerenciados por ele.

O HDInsight usa o PowerShell do Azure para configurar, executar e pós-processar os trabalhos do Hadoop. O HDInsight também fornece um conector Sqoop que pode ser usado para importar dados de um banco de dados SQL do Azure para o HDFS ou para exportar dados para um banco de dados SQL do Azure SQL a partir do HDFS.

O HDInsight também disponibilizou o YARN. Trata-se de uma nova estrutura distribuída de gerenciamento de aplicativos para uso geral, que substitui a estrutura clássica do Apache Hadoop MapReduce para processamento de dados nos clusters Hadoop. Ela efetivamente serve como o sistema operacional do Hadoop e leva o Hadoop de uma plataforma de dados de acesso único para processamento em lotes para uma plataforma multiuso que permite processamento em lotes, interativo, online e de fluxo. Essa nova estrutura de gerenciamento melhora a escalabilidade e a utilização do cluster de acordo com critérios, como garantias de capacidade, integridade e contratos de nível de serviço.

O Microsoft Power Query para Excel está disponível para importar dados do Azure HDInsight ou qualquer HDFS para o Excel. Esse complemento aprimora a experiência de BI de autoatendimento no Excel, simplificando a descoberta de dados e o acesso a uma ampla variedade de fontes de dados. Além do Power Query, o Driver ODBC do Microsoft Hive está disponível para integrar as ferramentas de BI (business intelligence), como Excel, SQL Server Analysis Services e Reporting Services, facilitando e simplificando a análise de dados de ponta a ponta.

### Estrutura de tópicos

Este tópico descreve o ecossistema do Hadoop com suporte do HDInsight, os principais cenários de uso do HDInsight e um guia dos recursos adicionais. Ele contém as seguintes seções:

-   [O ecossistema do Hadoop no HDInsight][O ecossistema do Hadoop no HDInsight]: o HDInsight fornece implementações de Pig, Hive e Sqoop e oferece suporte a outras ferramentas de BI, como Excel, SQL Server Analysis Services e Reporting Services, que estão integradas ao armazenamento de Blob/HDFS e à estrutura MapReduce usando o Power Query ou o Driver ODBC do Microsoft Hive. Esta seção descreve com quais trabalhos esses programas no ecossistema do Hadoop foram projetados para lidar.

-   [Cenários de Big Data para o HDInsight][Cenários de Big Data para o HDInsight]: Esta seção responde à pergunta: para quais tipos de trabalhos o HDInsight é uma tecnologia apropriada?

-   [Recursos para o HDInsight][Recursos para o HDInsight]: esta seção indica onde encontrar recursos relevantes para obter informações adicionais.

## <a name="Ecosystem">O ecossistema do Hadoop no Azure </a>

### Introdução

O HDInsight oferece uma estrutura que implementa a solução baseada em nuvem da Microsoft para lidar com Big Data. Esse ecossistema federado gerencia e analisa grandes quantidade de dados, explorando os recursos de processamento paralelos do modelo de programação MapReduce. As tecnologias Hadoop compatíveis com o Apache que podem ser usadas com o HDInsight são discriminadas e descritas brevemente nesta seção.

O HDInsight fornece implementações do Hive e do Pig para integrar o processamento de dados e os recursos de armazenamento. A solução de Big Data da Microsoft é integrada às ferramentas de BI da Microsoft, como SQL Server Analysis Services, Reporting Services, PowerPivot e Excel. Isso lhe permitirá executar uma BI simples nos dados armazenados e gerenciados pelo HDInsight no armazenamento de Blob.

Outras tecnologias compatíveis com o Apache e tecnologias irmãs que fazem parte do ecossistema do Hadoop e foram desenvolvidas para serem executadas sobre clusters Hadoop também podem ser baixadas e usadas com o HDInsight. Elas incluem tecnologias de código aberto, como Sqoop, que são integradas ao HDFS com armazenamentos de dados relacionais.

### Pig

O Pig é uma plataforma de alto nível para o processamento de Big Data em clusters Hadoop. O Pig consiste em uma linguagem de fluxo de dados, denominada Pig Latin, que oferece suporte para a escrita de consultas em grandes conjuntos de dados e um ambiente de execução que processa os programas de um console. Os programas Pig Latin consistem em uma série de transformações de conjuntos de dados convertida de forma oculta, em uma série de programas MapReduce. As abstrações de Pig Latin fornecem estruturas de dados mais sofisticadas do que o MapReduce e executam para o Hadoop o mesmo executado pelo SQL para os sistemas RDBMS. O Pig Latin é totalmente extensível. As UDFs (Funções Definidas pelo Usuário), escritas em Java, Python, Ruby, C# ou JavaScript, podem ser chamadas para personalizar cada estágio do caminho de processamento durante a redação da análise. Para obter informações adicionais, consulte [Bem-vindo ao Apache Pig!][Bem-vindo ao Apache Pig!]

### Hive

O Hive é um data warehouse distribuído que gerencia os dados armazenados em um HDFS. Ele é o mecanismo de consulta Hadoop. O Hive é destinado a analistas com fortes habilidades SQL, fornecendo uma interface semelhante ao SQL e um modelo de dados relacional. O Hive usa uma linguagem denominada HiveQL, que é um dialeto do SQL. O Hive, assim como o Pig, é uma abstração sobre o MapReduce e, quando executado, converte as consultas em uma série de trabalhos de MapReduce. Os cenários do Hive conceitualmente se aproximam mais dos cenários do RDBMS e, portanto, são adequados para serem usados com dados mais estruturados. Para dados não estruturados, o Pig é a melhor opção. Para obter informações adicionais, consulte [Bem-vindo ao Apache Hive!][Bem-vindo ao Apache Hive!]

### Sqoop

O Sqoop é uma ferramenta que transfere dados em massa entre o Hadoop e bancos de dados relacionais, como o SQL, ou outros armazenamentos de dados estruturados, do modo mais eficiente possível. Use o Sqoop para importar dados de armazenamentos de dados estruturados externos para o HDFS ou para sistemas relacionados, como o Hive. O Sqoop também pode extrair dados do Hadoop e exportar os dados extraídos para bancos de dados relacionais externos, data warehouses corporativos ou qualquer outro tipo de armazenamento de dados estruturado. Para obter informações adicionais, consulte o site do [Apache Sqoop][Apache Sqoop].

### Oozie

O Apache Oozie é um sistema de fluxo de trabalho/coordenação que gerencia trabalhos do Hadoop. É integrado com a pilha do Hadoop e oferece suporte a trabalhos do Hadoop para o Apache MapReduce, Apache Pig, Apache Hive e Apache Sqoop. Também pode ser usado para agendar trabalhos específicos para um sistema, como programas Java ou scripts de shell.

### Ambari

O Apache Ambari serve para provisionar, gerenciar e monitorar clusters do Apache Hadoop. Inclui uma coleção de ferramentas intuitivas para operador e um conjunto abrangente de APIs que ocultam a complexidade do Hadoop, simplificando a operação de clusters. Para obter mais informações sobre as APIs, consulte [referência de API do Ambari][referência de API do Ambari]. Atualmente, o HDInsight oferece suporte apenas ao recurso de monitoramento da Ambari. A API da Ambari versão 1.0 tem suporte do cluster HDInsight versões 2.1 e 3.0. Para obter mais informações sobre o Ambari, consulte o site do [Apache Ambari][Apache Ambari].

### Biblioteca do Microsoft Avro

A Biblioteca do Microsoft Avro implementa o sistema de serialização de dados Apache Avro para o ambiente Microsoft.NET. O Apache Avro fornece um formato de intercâmbio de dados binários compactos para serialização. Ele utiliza o [JSON][JSON] para definir o esquema com neutralidade de linguagem que subscreve a interoperabilidade da linguagem. Os dados serializados em uma linguagem podem ser lidos em outra diferente. Atualmente, há suporte para C, C++, C#, Java, PHP, Python e Ruby. Informações detalhadas sobre o formato podem ser encontradas na [Especificação do Apache Avro][Especificação do Apache Avro]. Observe que a versão atual da biblioteca do Microsoft Avro não dá suporte à parte de RPCs (chamadas de procedimento remoto) destas especificações.

O formato de serialização Apache Avro é amplamente usado no Azure HDInsight e em outros ambientes do Apache Hadoop. O Avro fornece um meio conveniente de representar estruturas de dados complexas em um trabalho MapReduce do Hadoop. O formato dos arquivos do Avro foi projetado para suportar o modelo de programação distribuído do MapReduce. O recurso principal que habilita a distribuição é o fato de os arquivos serem divisíveis, no sentido de que uma pessoa pode buscar qualquer ponto em um arquivo e iniciar a leitura por meio de um bloco em específico. Para obter informações adicionais, consulte [Serializar dados com a biblioteca do Microsoft .NET para Avro][Serializar dados com a biblioteca do Microsoft .NET para Avro].

### Conectores e ferramentas de business intelligence

As ferramentas conhecidas de BI (business intelligence), como o Excel, o PowerPivot, o SQL Server Analysis Services e o Reporting Services, recuperam, analisam e relatam os dados integrados ao HDInsight usando o suplemento Power Query ou o Driver ODBC do Microsoft Hive.

-   O Microsoft Power Query para Excel pode ser baixado do [Centro de Download da Microsoft][Centro de Download da Microsoft].

-   O Driver ODBC do Microsoft Hive pode ser baixado deste [site de download][site de download].

-   Para obter informações sobre o Analysis Services, consulte [SQL Server 2012 Analysis Services][SQL Server 2012 Analysis Services].

-   Para obter informações sobre o Reporting Services, consulte [SQL Server 2012 Reporting][SQL Server 2012 Reporting].

## <a name="Scenarios"></a>Cenários de Big Data para o HDInsight

Um cenário exemplar que fornece um caso de uso do HDInsight é uma análise ad hoc, em modo de lotes, em um conjunto inteiro de dados não estruturados armazenados em nós do Azure, que não exige atualizações frequentes.

Essas condições aplicam-se a uma grande variedade de atividades de negócios, ciências e controle. Elas podem incluir, por exemplo, monitoramento de cadeias de fornecedores no setor de varejo, padrões de negociação suspeitos nas finanças, padrões de demanda por serviços e utilidades públicas, qualidade do ar e da água dos conjuntos de sensores ambientais ou padrões de criminalidade nas áreas metropolitanas.

O HDInsight (e as tecnologias Hadoop em geral) são mais adequadas para lidar com uma grande quantidade de dados registrados ou arquivados que não precisam de atualização contínua após serem escritos e que são lidos com frequência, normalmente para fazer uma análise completa. Esse cenário é complementar aos dados mais adequados tratados por um RDBMS que requer quantidades menores de dados (gigabytes em vez de petabytes) e que devem ser continuamente atualizados ou consultados para pontos de dados específicos dentro do conjunto de dados inteiro. O RDBMS funciona melhor com dados estruturados organizados e armazenados de acordo com um esquema fixo. O MapReduce funciona bem com dados não estruturados sem esquema predefinido, pois ele é capaz de interpretar esses dados quando eles estão sendo processados.

## <a name="Resources"></a>Próximas etapas: recursos para o HDInsight

**Microsoft: HDInsight**

-   [Documentação do HDInsight][Documentação do HDInsight]: a página de documentação do Azure HDInsight com links para artigos, vídeos e mais recursos.

-   [Notas de versão do HDInsight][Notas de versão do HDInsight]: notas sobre as versões mais recentes.

-   [Introdução ao Azure HDInsight][Introdução ao Azure HDInsight]: um tutorial que fornece um início rápido para usar o HDInsight.

-   [Executar os exemplos do HDInsight][Executar os exemplos do HDInsight]: um tutorial sobre como executar os exemplos que acompanham o HDInsight.

-   [Big data e Azure][Big data e Azure]: cenários de Big Data que exploram o que você pode compilar com o Azure.

-   [SDK do Azure HDInsight][SDK do Azure HDInsight]: documentação de referência do SDK do HDinsight.

**Microsoft: Windows e Banco de dados SQL**

-   [Página inicial do Azure][Página inicial do Azure]: cenários, inscrição para uma avaliação gratuita, ferramentas de desenvolvimento e documentação necessários para você começar a compilar aplicativos.

-   [Banco de dados SQL do Azure][Banco de dados SQL do Azure]: Documentação de MSDN para o Banco de Dados SQL.

-   [Portal de Gerenciamento para o Banco de Dados SQL][Portal de Gerenciamento para o Banco de Dados SQL]: uma ferramenta de gerenciamento de banco de dados leve e fácil de usar para gerenciamento do Banco de Dados SQL na nuvem.

-   [Adventure Works para o Banco de Dados SQL][Adventure Works para o Banco de Dados SQL]: página de download do banco de dados de amostra do Banco de Dados SQL.

**Microsoft: Business intelligence**

-   [Conectar o Excel ao HDInsight com o Power Query][Conectar o Excel ao HDInsight com o Power Query]: saiba como conectar o Excel à conta de armazenamento do Azure que armazena os dados associados ao seu cluster HDInsight usando o Microsoft Power Query para Excel.

-   [Conectar o Excel ao HDInsight com o driver ODBC do Microsoft Hive][Conectar o Excel ao HDInsight com o driver ODBC do Microsoft Hive]: saiba como importar dados do Azure HDInsight com o Driver ODBC do Microsoft Hive.

-   [Microsoft BI PowerPivot][Microsoft BI PowerPivot]: baixe e obtenha informações sobre uma ferramenta avançada de exploração e mashup de dados.

-   [SQL Server 2012 Analysis Services][SQL Server 2012 Analysis Services]: baixe uma cópia de avaliação do SQL Server 2012 e saiba como criar soluções analíticas abrangentes e de escala corporativa que ofereçam informações úteis.

-   [SQL Server 2012 Reporting][1]: baixe uma cópia de avaliação do SQL Server 2012 e saiba como criar soluções abrangentes e altamente escalonáveis que permitam tomar decisões em tempo real em toda a empresa.

**Apache Hadoop**:

-   [Apache Hadoop][Apache Hadoop]: saiba mais sobre a biblioteca de software Apache Hadoop, uma estrutura que permite o processamento distribuído de grandes conjuntos de dados em clusters de computadores.

-   [HDFS][HDFS]: saiba mais sobre a arquitetura e o design do HDFS (Sistema de Arquivos Distribuído Hadoop), o sistema de armazenamento primário usado pelos aplicativos Hadoop.

-   [MapReduce][MapReduce]: saiba mais sobre a estrutura de programação para escrever aplicativos Hadoop que processam rapidamente grandes quantidades de dados paralelamente em grandes clusters de nós de computação.

  [O ecossistema do Hadoop no HDInsight]: #Ecosystem
  [Cenários de Big Data para o HDInsight]: #Scenarios
  [Recursos para o HDInsight]: #Resources
  [Bem-vindo ao Apache Pig!]: http://pig.apache.org/
  [Bem-vindo ao Apache Hive!]: http://hive.apache.org/
  [Apache Sqoop]: http://sqoop.apache.org/
  [referência de API do Ambari]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md
  [Apache Ambari]: http://ambari.apache.org/
  [JSON]: http://www.json.org
  [Especificação do Apache Avro]: http://avro.apache.org/docs/current/spec.html
  [Serializar dados com a biblioteca do Microsoft .NET para Avro]: ../hdinsight-dotnet-avro-serialization/
  [Centro de Download da Microsoft]: http://go.microsoft.com/fwlink/?LinkID=286689
  [site de download]: http://go.microsoft.com/fwlink/?LinkID=286698
  [SQL Server 2012 Analysis Services]: http://www.microsoft.com/sqlserver/en/us/solutions-technologies/business-intelligence/SQL-Server-2012-analysis-services.aspx
  [SQL Server 2012 Reporting]: http://www.microsoft.com/pt-br/sqlserver/solutions-technologies/business-intelligence/reporting.aspx
  [Documentação do HDInsight]: http://go.microsoft.com/fwlink/?LinkID=285601
  [Notas de versão do HDInsight]: http://azure.microsoft.com/pt-br/documentation/articles/hdinsight-release-notes/
  [Introdução ao Azure HDInsight]: ../hdinsight-get-started/
  [Executar os exemplos do HDInsight]: ../hdinsight-run-samples/
  [Big data e Azure]: http://azure.microsoft.com/pt-br/solutions/big-data/
  [SDK do Azure HDInsight]: http://msdnstage.redmond.corp.microsoft.com/pt-br/library/dn479185.aspx
  [Página inicial do Azure]: http://azure.microsoft.com/pt-br/
  [Banco de dados SQL do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee336279.aspx
  [Portal de Gerenciamento para o Banco de Dados SQL]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg442309.aspx
  [Adventure Works para o Banco de Dados SQL]: http://msftdbprodsamples.codeplex.com/releases/view/37304
  [Conectar o Excel ao HDInsight com o Power Query]: ../hdinsight-connect-excel-power-query/
  [Conectar o Excel ao HDInsight com o driver ODBC do Microsoft Hive]: ../hdinsight-connect-excel-hive-ODBC-driver/
  [Microsoft BI PowerPivot]: http://www.microsoft.com/pt-br/bi/PowerPivot.aspx
  [Apache Hadoop]: http://hadoop.apache.org/
  [HDFS]: http://hadoop.apache.org/docs/r0.18.1/hdfs_design.html
  [MapReduce]: http://mapreduce.org/
