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
	ms.date="05/06/2015" 
	ms.author="spelluru"/>

# Introdução ao serviço de Fábrica de Dados do Azure
<!--
The **Azure Data Factory** service is a fully managed service for composing data storage, processing, and movement services into streamlined, scalable, and reliable data production pipelines.  Developers can use Data Factory to transform semi-structured, unstructured and structured data from on-premises and cloud sources into trusted information. Developers build data-driven workflows (pipelines) that join, aggregate and transform data sourced from their on-premises, cloud-based and internet services, and set up complex data processing through simple JSON scripting. The Azure Data Factory service provides monitoring and management of these pipelines at a glance with a rich visual experience offered through the Azure Preview Portal. The information produced by pipelines can be easily consumed using BI and analytics tools, and other applications to reliably drive key business insights and decisions.
-->


A **Azure Data Factory** é um serviço completamente gerenciado para compor serviços de movimentação de dados, processamento de dados e armazenamento de dados em pipelines de produção de dados eficiente, escalonável e confiável. O serviço de Fábrica de Dados permite que você:

- Compile fluxos de trabalho orientados a dados (pipelines) que unem, agregam e transformam dados originados no local, baseados em nuvem e de repositórios de dados da Internet. 
- Transforme dados semiestruturados, não estruturados e estruturados de diversas fontes de dados em informações confiáveis.
- Produza dados que possam ser facilmente consumidos por meio de BI (business intelligence), ferramentas de análise e outros aplicativos. 
- Configure processamento de dados complexo por meio de scripts JSON simples.
- Monitore e gerencie pipelines em um relance com uma avançada experiência visual oferecida pelo Portal de Visualização do Azure.  

O vídeo a seguir fornece uma visão geral rápida do serviço Azure Data Factory.


- [Vídeo: Introdução à Azure Data Factory](http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory/)


<!--
> [AZURE.VIDEO azure-data-factory-overview]

This article provides an overview of the Azure Data Factory service, the value it provides, and the scenarios it supports.
-->

## Visão geral
Tradicionalmente, os projetos de integração de dados têm girado em torno da criação de processos de ETL (extração, transformação e carregamento) que extraem dados de várias fontes de dados dentro de uma organização, transformam os dados de acordo com o esquema de destino de um EDW (Enterprise Data Warehouse) e carregam os dados em um EDW. O EDW é acessado como a única fonte verdadeira para soluções de análise de BI.

![ETL tradicional][image-data-factory-introduction-traditional-ETL]

O panorama de dados atual para as empresas continua a crescer exponencialmente em volume, variedade e complexidade. Ele está mais diversificado do que nunca com dados em nuvem e locais de velocidades e formas diferentes. O processamento de dados deve ocorrer em locais geográficos e inclui uma combinação de software livre, soluções comerciais e serviços de processamento personalizados e que são caros e difíceis de integrar e manter. A agilidade necessária para se adaptar o grande cenário de dados atual é uma oportunidade para mesclar o EDW tradicional com recursos necessários para um sistema de produção de informações modernos.

![Diversos cenários de processamento de hoje][image-data-factory-introduction-todays-diverse-processing-landspace]

O serviço **Azure Data Factory** é a plataforma de composição para trabalhar em EDWs tradicionais e o cenário de dados mutável que permite às empresas aproveitar todos os dados disponíveis a elas para a tomada de decisões orientada a dados. Ele capacita as empresas a aproveitar essa diversidade, fornecendo uma plataforma para compor os serviços de processamento, armazenamento e movimentação de dados em pipelines de produção de informações e gerenciar ativos de dados confiáveis.

O serviço de Fábrica de Dados do Azure permite que você:

- **Trabalhe facilmente com os diversos sistemas de armazenamento e processamento de dados.** O serviço de Fábrica de Dados permite criar pipelines de produção de informações que movem e processam fontes de dados locais (como o SQL Server) e fontes de dados de nuvem como o Banco de Dados SQL do Azure, a tabela do Azure e blobs. 
- **Transforme dados em informações confiáveis.** O serviço de Fábrica de Dados dá suporte a Hive, Pig e processamento C#, além de recursos de processamento de chave, como gerenciamento automático de cluster Hadoop (HDInsight), novas tentativas para falhas transitórias, políticas de tempo limite configurável e alertas.  
- **Monitore os pipelines de dados em um único lugar.** O serviço de Fábrica de Dados fornece uma exibição confiável e completa de seus serviços de armazenamento, processamento e movimentação de dados. Ele ajuda a avaliar rapidamente a integridade de dados de ponta a ponta do pipeline, identificar problemas e tomar uma ação corretiva, se necessário. Você também pode controlar visualmente a linhagem de dados e as relações entre os dados em qualquer uma de suas fontes e ver um histórico completo de execução de trabalhos, integridade do sistema e dependências em um único painel de monitoramento.
- **Obtenha informações completas de dados transformados** O serviço Data Factory permite criar pipelines de dados que geram dados confiáveis, os quais podem ser consumidos pelas ferramentas de análise e business intelligence, bem como por outros aplicativos.

<!--
Today, to take advantage of the benefits of Data Factory, developers interact directly with individual data pipelines, storage services, and compute services.  As the Data Factory service evolves over time, we will introduce additional storage and processing services, and new mechanisms of grouping compute and storage services and data pipelines together into ‘Hubs’.  We describe Hubs here in our introduction, as this nascent concept appears throughout the service as a precursor for future releases.

An Azure Data Factory Hub is a container for storage and compute services (both referred to as Linked services), as well as for the data pipelines that use and run on those resources. The Hub container allows the Data Factory to be divided into logical or domain specific groupings.  For example, an enterprise may have a “West US Azure Hub” which manages all of the Linked services and pipelines focused in the West US data center, or a “Sales EDW Hub” which manages all the Linked services and pipelines associated with populating and processing data for the Sales EDW.  An important characteristic of Hubs is that a pipeline runs on a single hub. This means that when defining a pipeline, all of the Linked services referenced by tables or activities within that pipeline must have the same Hub name as the pipeline itself.

Hubs will help to encapsulate storage and compute in a way where pipelines can reference only a Hub rather than the specific services and tables it uses. The Hub can then use policies to decide where to run a pipeline. This will have several important impacts. One is that it will provide easier scale-up as more Linked services can be added to a Hub, and pipelines can be load-balanced across these new Linked services. Another is that it will reuse of pipeline definitions on different Hubs.

-->

## Modelo do aplicativo
O diagrama a seguir ilustra o modelo de aplicativo com suporte pelo serviço de Fábrica de Dados do Azure.

![Modelo do aplicativo][image-data-factory-application-model]

Há três estágios de produção de informações em uma fábrica de dados do Azure:

- **Conectar e coletar**. Nesse estágio, os dados de várias fontes de dados são importados para hubs de dados. Um pipeline em uma fábrica de dados pode ter uma ou mais atividades. Use uma ou mais atividades de **Cópia** em um pipeline de dados para coletar dados dos repositórios de dados de origem para um repositório de dados de destino em um hub de dados para processamento futuro. Um cluster HDInsight (computação) e seu armazenamento de blob do Azure associado (armazenamento) formam juntos um hub de dados, um hub de dados do HDInsight. Para usar um hub de dados do HDInsight, você pode copiar todos os dados de origem para um armazenamento de BLOBs do Azure associado ao HDInsight para que os dados possam ser processados pelo cluster HDInsight. Um pipeline é executado em um recurso de computação em um hub de dados como um cluster HDInsight.      
- **Transformar e enriquecer**. Nesse estágio, os dados coletados são processados. Por exemplo, uma **Atividade do HDInsight** em um pipeline pode processar dados armazenados no repositório de blob do Azure associado executando transformações usando scripts Hive/Pig para gerar informações confiáveis. Os pipelines podem ser encadeados (conforme mostrado no diagrama) de forma que conjuntos de dados de saída de um pipeline possam ser conjuntos de dados de entrada para outro pipeline no mesmo hub de dados ou em outro hub de dados.  
- **Publicar**. Nesse estágio, os dados são publicados para que eles possam ser consumidos por ferramentas de BI, ferramentas de análise e outros aplicativos. Por exemplo, uma Atividade de Cópia no pipeline pode copiar dados de saída do processamento realizado no estágio de Transformação e Enriquecimento para um repositório de dados (por exemplo: SQL Server local), com base no qual soluções de business intelligence podem ser criadas.   

<!--

Data Factories enable developers to create pipelines which are groups of data movement and/or processing activities that accept one or more input datasets and produce one or more output datasets. Pipelines can be executed once or on a flexible range of schedules (hourly, daily, weekly, etc…). A dataset is a named view of data. The data being described can vary from simple bytes, semi-structured data like CSV files all the way to tables or models.

Pipelines comprised of data movement activities (for example: Copy Activity) are often used to import/export data from all the data sources (databases, files, SaaS services, etc…) used by the organization into a data hub.
 
Once data is in a **hub**, **pipelines** hosted by the compute services of the hub, are used to transform data into a form suitable for consumption (by BI tools, applications, customers, etc.).  
  
Finally, **pipelines** can be chained (as shown in the diagram) such that the output **dataset(s)** of one are the input(s) of another.  This allows complex data flows to be factored into **pipelines** that run within a data hub or span multiple hubs.  Using **pipelines** in this way provides organizations the building blocks to compose the best of breed on-premises, cloud and Software-as-a-Service (SaaS) services all through the lens of a single, easily managed data factory.
-->


##Próximas etapas
1. [Introdução à Data Factory][datafactory-getstarted]. Este artigo fornece um tutorial de ponta a ponta que mostra como criar uma fábrica de dados do Azure que copia dados de um blob do Azure para um banco de dados SQL do Azure.
2. [Tutorial: Mover e processar arquivos de log usando a Data Factory][adf-tutorial]. Este artigo apresenta um **passo a passo completo** que mostra como implementar um **cenário do mundo real** usando a Azure Data Factory para transformar dados dos arquivos de log em informações.

## Consulte também
- [Data Factory — terminologia][adf-terminology]. Este artigo apresenta a terminologia usada na criação de data factories usando o serviço Azure Data Factory 
- [Data Factory — perguntas frequentes][adf-faq]. Este artigo fornece uma lista de perguntas frequentes e as respostas.
- [Cenários comuns para uso da Azure Data Factory][adf-common-scenarios]. Este artigo descreve alguns cenários comuns para usar o serviço de Fábrica de Dados do Azure. 


[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx

[adf-faq]: data-factory-faq.md
[adf-terminology]: data-factory-terminology.md
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

[image-data-factory-introduction-todays-diverse-processing-landspace]: ./media/data-factory-introduction/TodaysDiverseDataProcessingLandscape.PNG

[image-data-factory-application-model]: ./media/data-factory-introduction/DataFactoryApplicationModel.png

[image-data-factory-data-flow]: ./media/data-factory-introduction/DataFactoryDataFlow.png



 

<!---HONumber=62-->