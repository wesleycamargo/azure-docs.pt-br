<properties 
	pageTitle="Introdução à Fábrica de Dados do Azure" 
	description="Saiba como você pode usar o serviço de Fábrica de Dados do Azure para compor o processamento de dados, armazenamento de dados e serviços de movimentação de dados para criar pipelines que produzem informações confiáveis." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/09/2015" 
	ms.author="spelluru"/>

# Introdução ao serviço de Fábrica de Dados do Azure
<!--
The **Azure Data Factory** service is a fully managed service for composing data storage, processing, and movement services into streamlined, scalable, and reliable data production pipelines.  Developers can use Data Factory to transform semi-structured, unstructured and structured data from on-premises and cloud sources into trusted information. Developers build data-driven workflows (pipelines) that join, aggregate and transform data sourced from their on-premises, cloud-based and internet services, and set up complex data processing through simple JSON scripting. The Azure Data Factory service provides monitoring and management of these pipelines at a glance with a rich visual experience offered through the Azure Preview Portal. The information produced by pipelines can be easily consumed using BI and analytics tools, and other applications to reliably drive key business insights and decisions.
-->

A **Fábrica de Dados do Azure** é um serviço completamente gerenciado para compor serviços de armazenamento, processamento e movimentação de dados em pipelines de produção de dados simplificada, escalonável e confiável. O serviço de Fábrica de Dados permite que você: 

- Compile fluxos de trabalho orientados a dados (pipelines) que unem, agregam e transformam dados originados no local, baseados em nuvem e de repositórios de dados da Internet. 
- Transforme dados semiestruturados, não estruturados e estruturados de diversas fontes de dados em informações confiáveis.
- Produza dados que possam ser facilmente consumidos por meio de BI (business intelligence), ferramentas de análise e outros aplicativos. 
- Configure processamento de dados complexo por meio de scripts JSON simples.
- Monitore e gerencie pipelines em um relance com uma avançada experiência visual oferecida pelo Portal de Visualização do Azure.  


<!--
This article provides an overview of the Azure Data Factory service, the value it provides, and the scenarios it supports.
--> 

## Visão geral
Tradicionalmente, os projetos de integração de dados têm girado em torno da criação de processos de ETL (extração, transformação e carregamento) que extraem dados de várias fontes de dados dentro de uma organização, transformam os dados de acordo com o esquema de destino de um EDW (Enterprise Data Warehouse) e carregam os dados em um EDW. O EDW é acessado como a única fonte verdadeira para soluções de análise de BI. 

![Traditional ETL][image-data-factory-introduction-traditional-ETL]

O panorama de dados atual para as empresas continua a crescer exponencialmente em volume, variedade e complexidade. Ele está mais diversificado do que nunca com dados em nuvem e locais de velocidades e formas diferentes.  O processamento de dados deve ocorrer em locais geográficos e inclui uma combinação de software livre, soluções comerciais e serviços de processamento personalizados e que são caros e difíceis de integrar e manter.  A agilidade necessária para se adaptar o grande cenário de dados atual é uma oportunidade para mesclar o EDW tradicional com recursos necessários para um sistema de produção de informações modernos.  

![Todays Diverse Processing Landscape][image-data-factory-introduction-todays-diverse-processing-landspace]

O serviço de **Fábrica de Dados do Azure** é a plataforma de composição para o trabalho em EDWs tradicionais e o cenário de dados mutável para capacitar as empresas a aproveitar todos os dados que estarão disponíveis para a tomada de decisões orientadas a dados. Ele capacita as empresas a aproveitar essa diversidade, fornecendo uma plataforma para compor os serviços de processamento, armazenamento e movimentação de dados em pipelines de produção de informações e gerenciar ativos de dados confiáveis.

O serviço de Fábrica de Dados do Azure permite que você:

- **Trabalhe facilmente com os sistemas de armazenamento e processamento de dados diversificados.**  
O serviço de Fábrica de Dados permite criar pipelines de produção de informações que movem e processam fontes de dados locais (como o SQL Server) e fontes de dados de nuvem como o Banco de Dados SQL do Azure, a tabela do Azure e blobs. 
- **Transforme dados em informações confiáveis.** 
O serviço de Fábrica de Dados dá suporte a Hive, Pig e processamento C#, além de recursos de processamento de chave, como gerenciamento automático de cluster Hadoop (HDInsight), novas tentativas para falhas transitórias, políticas de tempo limite configurável e alertas.  
- **Monitore os pipelines de dados em um único lugar.** 
O serviço de Fábrica de Dados fornece uma exibição confiável e completa de seus serviços de armazenamento, processamento e movimentação de dados.  Ele ajuda a avaliar rapidamente a integridade de dados de ponta a ponta do pipeline, identificar problemas e tomar uma ação corretiva, se necessário. Você também pode controlar visualmente a linhagem de dados e as relações entre os dados em qualquer uma de suas fontes e ver um histórico completo de execução de trabalhos, integridade do sistema e dependências em um único painel de monitoramento.
- **Obtenha percepções avançadas dos dados transformados**
O serviço de Fábrica de Dados permite criar pipelines de dados que geram dados confiáveis, os quais podem ser consumidos por ferramentas de business intelligence e análise e outros aplicativos.

<!--
Today, to take advantage of the benefits of Data Factory, developers interact directly with individual data pipelines, storage services, and compute services.  As the Data Factory service evolves over time, we will introduce additional storage and processing services, and new mechanisms of grouping compute and storage services and data pipelines together into 'Hubs'.  We describe Hubs here in our introduction, as this nascent concept appears throughout the service as a precursor for future releases.

An Azure Data Factory Hub is a container for storage and compute services (both referred to as Linked services), as well as for the data pipelines that use and run on those resources. The Hub container allows the Data Factory to be divided into logical or domain specific groupings.  For example, an enterprise may have a "West US Azure Hub" which manages all of the Linked services and pipelines focused in the West US data center, or a "Sales EDW Hub" which manages all the Linked services and pipelines associated with populating and processing data for the Sales EDW.  An important characteristic of Hubs is that a pipeline runs on a single hub. This means that when defining a pipeline, all of the Linked services referenced by tables or activities within that pipeline must have the same Hub name as the pipeline itself.

Hubs will help to encapsulate storage and compute in a way where pipelines can reference only a Hub rather than the specific services and tables it uses. The Hub can then use policies to decide where to run a pipeline. This will have several important impacts. One is that it will provide easier scale-up as more Linked services can be added to a Hub, and pipelines can be load-balanced across these new Linked services. Another is that it will reuse of pipeline definitions on different Hubs.

-->

## Modelo do aplicativo
O diagrama a seguir ilustra o modelo de aplicativo com suporte pelo serviço de Fábrica de Dados do Azure.

![Application Model][image-data-factory-application-model]

Há três estágios de produção de informações em uma fábrica de dados do Azure:

- **Conectar e Coletar**. Nesse estágio, os dados de várias fontes de dados são importados para hubs de dados. Um pipeline em uma fábrica de dados pode ter uma ou mais atividades. Você usa uma ou mais atividades de **cópia** em um pipeline de dados para coletar dados de repositórios de dados de origem para um repositório de dados de destino em um hub de dados para processamento adicional. Um cluster HDInsight (computação) e seu armazenamento de blob do Azure associado (armazenamento) formam juntos um hub de dados, um hub de dados do HDInsight. Para usar um hub de dados do HDInsight, você pode copiar todos os dados de origem para um armazenamento de BLOBs do Azure associado ao HDInsight para que os dados possam ser processados pelo cluster HDInsight. Um pipeline é executado em um recurso de computação em um hub de dados como um cluster HDInsight.      
- **Transformar e Enriquecer**. Nesse estágio, os dados coletados são processados. Por exemplo, uma **Atividade do HDInsight** em um pipeline pode processar dados armazenados no armazenamento de blob do Azure associado executando transformações usando scripts Hive/Pig para produzir informações confiáveis. Os pipelines podem ser encadeados (conforme mostrado no diagrama) de forma que conjuntos de dados de saída de um pipeline possam ser conjuntos de dados de entrada para outro pipeline no mesmo hub de dados ou em outro hub de dados.  
- **Publicar**. Nesse estágio, os dados são publicados para que eles possam ser consumidos por ferramentas de BI, ferramentas de análise e outros aplicativos. Por exemplo, uma Atividade de Cópia no pipeline pode copiar dados de saída do processamento realizado no estágio de Transformação e Enriquecimento para um repositório de dados (por exemplo: SQL Server local), com base no qual soluções de business intelligence podem ser criadas.   

<!--

As Fábricas de Dados permitem que os desenvolvedores criem pipelines que são grupos de movimentação de dados e/ou atividades de processamento que aceitam um ou mais conjuntos de dados de entrada e geram um ou mais conjuntos de dados de saída. Os pipelines podem ser executados uma vez ou em um intervalo flexível de agendas (horárias, diárias, semanais, etc...). Um conjunto de dados é uma visualização de dados nomeada. Os dados que estão sendo descritos podem variar de simples bytes, dados semiestruturados como arquivos CSV para tabelas ou modelos.

Os Pipelines consistem em atividade de movimentação de dados (por exemplo: Atividade de Cópia) costumam ser utilizados para importar/exportar dados de todas as fontes de dados (bancos de dados, arquivos, serviços SaaS etc.) utilizados pela empresa para um hub de dados.
 
Depois que os dados estão em um **hub**, os **pipelines** hospedados pelos serviços de computação do hub são utilizados para transformar dados em um formato adequado para consumo (por ferramentas de BI, aplicativos, os clientes, etc.).  
  
Por fim, os **pipelines** podem ser encadeados (conforme mostrado no diagrama) de modo que o(s) **conjunto(s) de dados** de saída de um sejam a entrada de outro.  Isso permite que os fluxos de dados complexos sejam decompostos em **pipelines** que são executadas dentro de um hub de dados ou abrangem vários hubs.  O uso de **pipelines** dessa maneira fornece às organizações blocos de construção para compor o melhor dos serviços de SaaS (Software-as-a-Service), nuvem e local da geração por meio das lentes de uma fábrica de dados única e fácil de gerenciar.
--> 

## Experiência de criação

Você pode criar fábricas de dados usando um dos seguintes itens:

- **Portal de visualização do Azure**. As folhas da Fábrica de Dados no Portal de Visualização do Azure fornecem uma avançada interface do usuário para que você crie fábricas de dados e serviços vinculados. O **Editor de Fábrica de Dados**, que também faz parte do portal, permite que você crie facilmente serviços vinculados, tabelas, conjuntos de dados e pipelines especificando definições de JSON para esses artefatos. Consulte [Editor de Fábrica de Dados][data-factory-editor] para obter uma visão geral do editor e [Introdução à Fábrica de Dados][datafactory-getstarted] para obter um exemplo de uso do portal/editor para criar e implantar uma fábrica de dados.   
- **PowerShell do Azure**. Se for um usuário do PowerShell e preferir usar o PowerShell em vez da interface do usuário do Portal, você poderá usar os cmdlets de Fábrica de Dados do Azure que são fornecidos como parte do PowerShell do Azure para criar e implantar fábricas de dados. Consulte [Criar e monitorar uma Fábrica de Dados do Azure usando o PowerShell do Azure][create-data-factory-using-powershell] para obter um exemplo simples e o [Tutorial: mover e processar arquivos de log usando o a Fábrica de Dados][adf-tutorial] para obter um exemplo avançado do uso de cmdlets do PowerShell para criar e implantar uma fábrica de dados. Consulte o conteúdo de [Referência de cmdlets da Fábrica de Dados][adf-powershell-reference] na Biblioteca do MSDN para obter uma documentação abrangente de cmdlets da Fábrica de Dados.  
- **Biblioteca de classes do .NET**. Você pode criar fábricas de dados programaticamente usando o SDK do .NET de Fábrica de Dados. Consulte [Criar, monitorar e gerenciar fábricas de dados usando o SDK do .NET][create-factory-using-dotnet-sdk] para obter uma explicação sobre a criação de uma fábrica de dados usando o SDK do .NET. Consulte a [Referência da Biblioteca de Classes de Fábrica de Dados][msdn-class-library-reference] para obter uma documentação abrangente sobre o SDK do .NET de Fábrica de Dados.  
- **API REST**. Você também pode usar a API REST exposta pelo serviço de Fábrica de Dados do Azure para criar e implantar fábricas de dados. Consulte a [Referência da API REST de Fábrica de Dados][msdn-rest-api-reference] para obter uma documentação abrangente da API REST de Fábrica de Dados. 


##Terminologia
Esta seção apresenta a terminologia relacionada à Fábrica de Dados do Azure.

### Fábrica de dados
Uma **fábrica de dados do Azure** tem um ou mais pipelines que processam dados em repositórios de dados vinculados (Armazenamento do Azure, Banco de Dados SQL do Azure, SQL Server local etc.) usando serviços de computação vinculados, como Azure HDInsight. Uma fábrica de dados do Azure em si não contém dados. Em vez disso, os dados são armazenados nos repositórios de dados mencionados acima.  

### Serviço vinculado
Um **serviço vinculado** é um serviço que está vinculado a uma fábrica de dados do Azure. Um serviço vinculado pode ser uma das seguintes opções:

- Um serviço de **armazenamento de dados**, como o Armazenamento do Azure, o Banco de Dados SQL do Azure ou um banco de dados do SQL Server local. Um repositório de dados é um contêiner de conjuntos de dados de entrada/saída.    
- Um serviço de **computação** como o Azure HDInsight e o Aprendizado de Máquina do Azure. Um serviço de computação processa os dados de entrada e produz os dados de saída.  

### Conjunto de dados
Um **conjunto de dados** é um modo de exibição nomeado de dados. Os dados que estão sendo descritos podem variar de simples bytes, dados semiestruturados como arquivos CSV até mesmo modelos ou tabelas relacionais. Uma **tabela** de Fábrica de Dados é um conjunto de dados que tem um esquema e é retangular. Depois de criar um serviço vinculado em um repositório de dados que se refere a um repositório de dados, você define conjuntos de dados que representam dados de entrada/saída que são armazenados no repositório de dados. 


###Pipeline
Um **pipeline** em uma fábrica de dados do Azure processa dados nos serviços de armazenamento vinculados utilizando serviços de computação vinculados. Ele contém uma sequência de atividades em que cada atividade executa uma operação de processamento específica. Por exemplo, uma **Atividade de Cópia** copia dados de um armazenamento de origem para um armazenamento de destino e a **Atividade de HDInsight** utiliza um cluster do Azure HDInsight para processar os dados utilizando consultas Hive ou scripts Pig. Uma fábrica de dados pode ter um ou mais pipelines. 

As etapas comuns para criar uma instância de Fábrica de Dados do Azure são:

1. Criar uma **fábrica de dados**.
2. Criar um **serviço vinculado** para cada armazenamento de dados ou serviço de computação
3. Criar **conjuntos de dados de entrada e saída**
4. Criar um **pipeline**. 

###Atividade
Uma etapa um processamento de dados em um pipeline que usa um ou mais conjuntos de dados de entrada e produz um ou mais conjuntos de dados de saída.  As atividades são executadas em um ambiente de execução (por exemplo: cluster HDInsight do Azure) e dados de leitura/gravação para um armazenamento de dados associado/vinculado à instância da fábrica de dados do Azure. 

O serviço de Fábrica de Dados do Azure dá suporte às seguintes atividades em um pipeline: 

- **Atividade de Cópia** copia os dados de um repositório de dados para outro repositório de dados. Consulte [Copiar dados com a Fábrica de Dados do Azure][copy-data-with-adf] para obter detalhes sobre quais repositórios de dados dão suporte à Atividade de Cópia. 
- **Atividade de HDInsight** processa dados executando scripts Hive/Pig ou programas MapReduce em um cluster HDInsight. Consulte [Usar Pig e Hive com uma Fábrica de Dados][use-pig-hive] e [Invocar programas MapReduce da Fábrica de Dados][run-map-reduce] para obter detalhes. 
- **Atividade de pontuação em lotes do Aprendizado de Máquina do Azure** invoca a API de pontuação em lotes do Aprendizado de Máquina do Azure. Consulte [Criar pipelines de previsão usando a Fábrica de Dados do Azure e o Aprendizado de Máquina do Azure][azure-ml-adf] para obter detalhes. 
- **Atividade de Procedimento Armazenado** invoca um procedimento armazenado em um Banco de Dados SQL do Azure. Consulte a [Atividade de Procedimento Armazenado][msdn-stored-procedure-activity] na biblioteca MSDN para obter detalhes.   

###Fatia
Uma tabela em uma fábrica de dados do Azure é composta de fatias. A largura de uma fatia é determinada pelo agendamento - por hora/diariamente. Quando o agendamento é "por hora", uma fatia é produzida por hora com a hora de início e de término de um pipeline. Por exemplo, se a data a e hora de início do pipeline forem 03/03/2015 06:00:00 (6h) e a data e a hora de término forem 03-03/2015 09:00:00 (9h no mesmo dia), três fatias de dados serão produzidas, uma fatia para cada intervalo de uma hora: 6h-7h, 7h-8h e 8h-9h.    

As fatias fornecem a capacidade de trabalhar com um subconjunto dos dados gerais para uma janela de tempo específico (por exemplo: a fatia é gerada para a duração (horas): 13:00 às 14:00). 

### Execução de atividade para uma fatia
A **execução** ou uma execução de atividade é uma unidade de processamento de uma fatia. Pode haver uma ou mais execuções de uma fatia no caso de repetições ou se você executar novamente a fatia em caso de falha. Uma fatia é identificada pela sua hora de início.

###Gateway de gerenciamento de dados
O **Gateway de Gerenciamento de Dados** da Microsoft é um software que se conecta a fontes de dados locais para serviços de nuvem para consumo. É necessário ter pelo menos um gateway instalado no seu ambiente corporativo e registrá-lo com o portal da Fábrica de Dados do Azure antes de adicionar fontes de dados locais como serviços vinculados.
 
###Hub de dados
Um **Hub de Dados** é um contêiner lógico para serviços de computação e armazenamento de dados. Por exemplo, um cluster de Hadoop com HDFS como armazenamento e Hive/Pig como computação (processamento) é um hub de dados. Da mesma forma, um EDW(data warehouse corporativo) pode ser estabelecido como um hub de dados (banco de dados como armazenamento, procedimentos armazenados e/ou ferramenta ETL, como serviços de computação).  Os pipelines utilizam armazenamentos de dados e executam em recursos de computação em um hub de dados. Apenas o hub de HDInsight tem suporte neste momento.

O Hub de dados permite que uma fábrica de dados seja dividida em agrupamentos específicos de domínio ou lógica, como o "Hub de Azure do oeste dos EUA" que gerencia todos os serviços vinculados (armazenamentos de dados e computação) e pipelines voltados para o data center do oeste dos EUA ou para as "Vendas de hub de EDW" que gerencia todos os serviços vinculados e pipelines interessados com dados de preenchimento e processamento para o data warehouse da empresa de vendas.

Uma característica importante do Hub é que um pipeline é executado em um único hub. Isso significa que, ao definir um pipeline, todos os serviços vinculados referenciados por tabelas ou atividades dentro do pipeline devem ter o mesmo nome do hub que o pipeline em si. Se a propriedade HubName não for especificada para um serviço vinculado, o serviço vinculado é colocado no hub "Padrão".

##Próximas etapas

1. [Introdução à Fábrica de Dados][datafactory-getstarted]. Este artigo fornece um tutorial de ponta a ponta que mostra como criar uma fábrica de dados do Azure que copia dados de um blob do Azure para um banco de dados SQL do Azure.
2. [Tutorial: Mover e processar arquivos de log usando a Fábrica de Dados][adf-tutorial]. Este artigo fornece um **passo a passo de ponta a ponta** que mostra como implantar um **cenário do mundo real** usando o alocador de dados do Azure para transformar dados de arquivos de log em ideias.
3. [Fábrica de dados - perguntas frequentes][adf-faq]. Este artigo fornece uma lista de perguntas frequentes e as respostas. 
3. [Cenários comuns para uso da Fábrica de Dados do Azure][adf-common-scenarios]. Este artigo descreve alguns cenários comuns para usar o serviço de Fábrica de Dados do Azure.


[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx

[adf-faq]: data-factory-faq.md

[copy-data-with-adf]: data-factory-copy-activity.md
[use-pig-hive]: data-factory-pig-hive-activities.md
[run-map-reduce]: data-factory-map-reduce.md
[azure-ml-adf]: data-factory-create-predictive-pipelines.md
[adf-common-scenarios]: data-factory-common-scenarios.md
[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[data-factory-editor]: data-factory-editor.md
[create-data-factory-using-powershell]: data-factory-monitor-manage-using-powershell.md

[adf-powershell-reference]: https://msdn.microsoft.com/library/dn820234.aspx 


[msdn-stored-procedure-activity]: https://msdn.microsoft.com/library/dn912649.aspx
[msdn-class-library-reference]: https://msdn.microsoft.com/library/dn883654.aspx
[msdn-rest-api-reference]: https://msdn.microsoft.com/library/dn906738.aspx

[adf-tutorial]: data-factory-tutorial.md
[datafactory-getstarted]: data-factory-get-started.md

[image-data-factory-introduction-traditional-ETL]: ./media/data-factory-introduction/TraditionalETL.PNG

[image-data-factory-introduction-todays-diverse-processing-landspace]:./media/data-factory-introduction/TodaysDiverseDataProcessingLandscape.PNG

[image-data-factory-application-model]:./media/data-factory-introduction/DataFactoryApplicationModel.png

[image-data-factory-data-flow]:./media/data-factory-introduction/DataFactoryDataFlow.png




<!--HONumber=49-->