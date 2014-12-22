<properties title="Introduction to Azure Data Factory" pageTitle="Introdução ao Data Factory do Azure" description="Learn how you can use the Azure Data Factory service to compose data processing, data storage and data movement services to create pipelines that produce trusted information." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Introdução ao serviço do Azure Data Factory
O serviço do **Azure Data Factory** é um serviço totalmente gerenciado para compor serviços de armazenamento, processamento e movimentação de dados em pipelines de produção de dados simplificados, escalonáveis e confiáveis.  Os desenvolvedores podem utilizar o Data Factory para transformar dados estruturados, semiestruturados e não estruturados de fontes locais e em nuvem em informações confiáveis. Os desenvolvedores compilam fluxos de trabalho de dados orientados (pipelines) que unem, agregam e transformam os dados originários de seu local, baseados em nuvem e serviços de Internet e configuram o processamento de dados complexos por meio de scripts JSON simples. O serviço do Azure Data Factory fornece monitoramento e gerenciamento dessas pipelines rapidamente com uma rica experiência visual oferecida pelo Portal de visualização do Azure. As informações geradas pelas pipelines podem ser consumidas facilmente utilizando o BI e outros aplicativos e ferramentas de análise para orientar confiavelmente as decisões e as ideias de negócios essenciais.

Este artigo fornece uma visão geral do serviço do Azure Data Factory, o valor que ele oferece e os cenários que com suporte. 

##Visão geral do Azure Data Factory
Tradicionalmente, os projetos de integração de dados têm girado em torno da criação de processos de ETL (extração, transformação e carregamento) que extraem dados de várias fontes de dados dentro de uma organização, transformam os dados de acordo com o esquema de destino de um EDW (Enterprise Data Warehouse) e carregam os dados em um EDW conforme mostrado na imagem a seguir. O EDW é acessado como a única fonte verdadeira para soluções de análise de BI. 

![Traditional ETL][image-data-factory-introduction-traditional-ETL]

O cenário de dados atual para as empresas continua a crescer exponencialmente em volume, variedade e complexidade, conforme mostrado na imagem a seguir.  Ele está mais diversificado do que nunca com dados em nuvem e locais de velocidades e formas diferentes.  O processamento de dados deve ocorrer em locais geográficos e inclui uma combinação de software livre, soluções comerciais e serviços de processamento personalizados e que são caros e difíceis de integrar e manter.  A agilidade necessária para se adaptar o grande cenário de dados atual é uma oportunidade para mesclar o EDW tradicional com recursos necessários para um sistema de produção de informações modernos.  O serviço do Data Factory do Azure é a plataforma de composição ao trabalho em EDWs tradicionais e o cenário de dados mutável para capacitar as empresas a aproveitar todos os dados que estarão disponíveis para a tomada de decisões de dados orientados.

![Todays Diverse Processing Landscape][image-data-factory-introduction-todays-diverse-processing-landspace]


O serviço do **Azure Data Factory** capacita as empresas a aproveitar essa diversidade, fornecendo uma plataforma para **compor os serviços de processamento, armazenamento e movimentação de dados em pipelines de produção de informações e gerenciar ativos de dados confiáveis**.

O serviço do Azure Data Factory permite que você:

- **Trabalhe facilmente com os sistemas de armazenamento e processamento de dados diversificados.** 
O Data Factory permite que você utilize dados onde são armazenados e compostos serviços para movimentação, processamento e armazenamento de dados.  Por exemplo, é possível compor pipelines de produção de informações para processar dados de locais como o SQL Server, junto com os dados em nuvem como banco de dados SQL, blobs e tabelas do Azure.  
- **Transforme dados em informações confiáveis.** 
A combinação e a modelagem de dados complexos podem levar mais de uma tentativa para dar certo e modelos de dados de alteração podem ser caros e demorados.  Utilizando o Data Factory é possível focalizar em análises transformadoras enquanto o serviço se encarrega dos detalhes técnicos.  O Data Factory oferece suporte para Hive, Pig e processamento C#, além de recursos de processamento de chave, como gerenciamento automático de cluster Hadoop (HDInsight), novas tentativas para falhas transitórias, políticas de tempo limite configurável e alertas.  
- **Monitorar os pipelines de dados em um único lugar.** 
Com um portfólio diversificado de dados, é importante ter uma visão completa e confiável de seus serviços de movimentação, processamento e armazenamento de dados.  O Data Factory ajuda a avaliar a integridade de dados de ponta a ponta da pipeline rapidamente, identificar problemas e tomar uma ação corretiva, se necessário. Acompanhe visualmente a linhagem de dados e as relações entre seus dados através de qualquer uma das suas fontes. Veja um histórico completo de estatísticas de execução do trabalho, a integridade do sistema e as dependências de um único painel de monitoramento
- **Obter boas ideias de dados transformados**
Adaptar-se às perguntas instável que sua organização precisa responder e permanecer no topo quando a produção de dados estiver pronta para continuar.  Melhorar a capacidade para direcionar melhores ideias de negócios ao produzir informações oportunas e confiáveis para consumo. Utilizar as pipelines de dados para fornecer dados transformados da nuvem para fontes locais como o SQL Server ou mantê-los nas suas fontes de armazenamento em nuvem para consumo por outros aplicativos e ferramentas de BI e análise.

Hoje, para tirar proveito dos benefícios do Data Factory, os desenvolvedores interagem diretamente com as pipelines de dados individuais, serviços de armazenamento e serviços de computação.  Como o serviço do Data Factory evolui ao longo do tempo, apresentaremos mais armazenamento e serviços de processamento e novos mecanismos de serviços de computação e armazenamento e pipelines de dados juntos em 'Hubs'.  Nós descrevemos Hubs aqui em nossa Introdução, como esse conceito emergente que aparece em todo o serviço como um precursor para versões futuras.

Um Hub do Azure Data Factory é um contêiner para serviços de computação e armazenamento (ambos denominados serviços vinculados), bem como para as pipelines de dados que utilizam e executam esses recursos. O contêiner de Hub permite que o Data Factory possa ser dividido em agrupamentos específicos de domínio e lógica.  Por exemplo, uma empresa pode ter um "Azure Hub do Oeste do EUA" que gerencia todos os serviços vinculados e pipelines voltadas para o centro de dados do Oeste dos EUA ou um "vendas EDW Hub" que gerencia todos os serviços vinculados e pipelines associados ao preencher e processar dados para as vendas EDW.  Uma característica importante dos Hubs é que uma pipeline é executada em um único hub. Isso significa que, ao definir uma pipeline, todos os serviços vinculados referenciados por tabelas ou atividades dentro dessa pipeline devem ter o mesmo nome do Hub que a pipeline em si.

Os Hubs irão ajudar a encapsular o armazenamento e a computação de uma maneira onde as pipelines possam fazer referência apenas a um Hub, em vez da serviços específicos e tabelas que elas utilizam. O Hub pode utilizar políticas para decidir onde executar uma pipeline. Isso terá vários impactos importantes. Um é que ele fornecerá mais fácil a ampliação, como mais serviços vinculados que podem ser adicionados a um Hub e as pipelines que podem ser balanceadas por carga entre esses novos serviços vinculados. Outra vantagem é que ele reutilizará as definições de pipeline em Hubs diferentes.

## Cenários
Há uma grande variedade de cenários de pipelines de dados que se estendem por sistemas locais, sistemas de nuvem, sistemas de SaaS bem como produção de dados de streaming e baseados em lote.  Esta seção descreve alguns cenários de exemplo nos quais o Azure Data Factory pode oferecer suporte hoje e continuará a crescer como cenários de Hub.

###Cenário #1: Fontes de dados para o Hub de dados
![Source the Data Hub][image-data-factory-introduction-secenario1-source-datahub]

As empresas têm dados de diferentes tipos, localizados em diferentes fontes.  A primeira etapa na criação de um sistema de produção de informações para se conectar a todas as fontes necessárias de dados e processamento, como serviços SaaS, os serviços Web, compartilhamentos de arquivos, FTP e mover os dados conforme necessário para processamento posterior.

Sem o Data Factory, as empresas devem criar componentes de movimentação de dados personalizados ou gravar serviços personalizados para integrar essas fontes de dados e processamento.  Isso é caro, e é difícil integrar e manter esses sistemas, além de, geralmente, não ter o monitoramento e os alertas de nível empresarial e os controles que podem oferecer um serviço completamente gerenciado.
  
Com os serviços de armazenamento e processamento de dados do Azure Data Factory são coletados em um contêiner de Hub que facilita e otimiza as atividades de computação e armazenamento, permite o gerenciamento de consumo de recurso unificado e fornece serviços para movimentação de dados conforme necessário.

###Cenário #2: Colocar em operação a produção de informações

Cenários de operacionalização são a próxima etapa lógica após cenários de fornecimento de dados. Depois que os dados estão presentes em um Hub, você deseja criar e colocar em operação as pipelines de dados para produzir confiavelmente os dados transformados em uma agenda controlada e passível de manutenção para alimentar os ambientes de produção com dados confiáveis.  A transformação de dados no Data Factory do Azure é por meio de Hive, Pig e execução de processamento C# personalizado no Hadoop (Azure HDInsight).  Essas transformações podem ser utilizadas para limpar dados, mascarar campos de dados críticos e realizar outras operações nos dados em uma ampla variedade de formas complexas.  Normalmente, a operacionalização é obtida com infraestrutura difícil de manter e complexa e serviços personalizados e apresenta vários desafios de implementação, gerenciamento, dimensionamento, solução de problemas e versão como uma solução.
  
Com o Data Factory como um serviço totalmente gerenciado, os usuários podem colocar em operação essas pipelines definindo-as com agendamentos recorrentes únicos ou complexos e orquestração tratada diretamente pelo serviço de Data Factory.  Com os Hubs, o gerenciamento de cluster para todos os dados e o processamento em um Hub é tratado em nome do usuário para que usuários possam se concentrar na análise transformadora ao invés do gerenciamento de infraestrutura.  O Azure Data Factory remove os desafios de se trabalhar com serviços personalizados frágeis e permite que as empresas produzam informações confiáveis reproduzível e confiável.


###Cenário #3:  Integrar a produção de informações com a detecção de dados
Abordagens e tecnologias BI tradicionais, enquanto fornecem uma "fonte autoritativa da verdade", quase sempre têm um efeito colateral sério: uma lista de pendências constante de solicitações devido a um processo de solicitação de alteração cuidadosamente controlado.  Para adaptar-se a alterar rapidamente as questões de negócios, é necessário obter maior flexibilidade para as empresas para conectar seus sistemas de produção de informações com seus sistemas de consumo de informações.  O Data Factory do Azure ajuda você a resolver o desafio de se conectar a esses sistemas com pipelines de dados simplificados de produção de informações e o desafio de consumo de informações ao disponibilizar dados atualizados confiáveis em formulários facilmente consumíveis.
  
O Azure Data Factory oferece suporte para os seguintes recursos para habilitar o consumo simples dos dados produzidos:

- Mover facilmente (uma vez ou agendado) os ativos de dados produzidos para armazéns de dados relacionais para consumo utilizando as ferramentas de BI existentes (Excel, Tableau, etc...).
- Consumir os ativos de dados produzidos por uma data factory utilizando o Power Query diretamente no Excel.

Consulte os tópicos a seguir para consumir dados utilizando o Power Query: 

- [Consulta de energia: Conecte-se ao armazenamento do Microsoft Azure] [Power-Query-Azure-Table]
- [Consulta de energia: Conecte-se ao armazenamento de blob do Microsoft Azure] [Power-Query-Azure-Blob]
- [Consulta de energia: Conecte-se ao Banco de Dados SQL do Microsoft Azure] [Power-Query-Azure-SQL]
- [Consulta de energia: Conecte-se ao SQL Server local da Microsoft][Power-Query-OnPrem-SQL] 

## Modelo do aplicativo
O diagrama a seguir ilustra o modelo de aplicativo com suporte pelo Data Factory do Azure.

![Application Model][image-data-factory-application-model]

Há três estágios de produção de informações em uma data factory do Azure:


- **Conectar e coletar**. Nesse estágio, os dados de várias fontes de dados são importados para hubs de dados.
- **Transformar e enriquecer**. Nesse estágio, os dados coletados são processados.
- **Publicar**. Nesse estágio, os dados são publicados para que eles possam ser consumido por ferramentas de BI, ferramentas de análise e outros aplicativos.

As Data Factories permitem que os desenvolvedores criem **pipelines** que são grupos de movimentação de dados e/ou **atividades** de processamento que aceitam um ou mais **conjuntos de dados** de entrada e geram um ou mais conjuntos de dados de saída.  As pipelines podem ser executadas uma vez ou em um intervalo flexível de agendas (horárias, diárias, semanais, etc...).  Um **conjunto de dados** é uma visualização de dados nomeada. Os dados que estão sendo descritos podem variar de simples bytes, dados semiestruturados como arquivos CSV para **tabelas** ou **modelos**.

As **Pipelines** consistem em **atividades** de movimentação de dados (por exemplo: Atividade de cópia) costumam ser utilizadas para importar/exportar dados de todas as **fontes de dados** (bancos de dados, arquivos, serviços SaaS, etc...) utilizados pela empresa para um **hub de dados**.
    
Depois que os dados estão em um **hub**, as *pipelines* hospedadas pelos serviços de computação do hub são utilizadas para transformar dados em um formato adequado para consumo (por ferramentas de BI, aplicativos, os clientes, etc.).  
  
Por fim, as **pipelines** podem ser encadeadas (conforme mostrado no diagrama) como aquele **conjunto(s) de dados** de saída que um é a entrada de outro.  Isso permite que os fluxos de dados complexos ser decomposto em **pipelines** que são executadas dentro de um hub de dados ou abrangem vários hubs.  Utilizar as **pipelines** dessa maneira fornece às organizações os blocos de construção para compor o melhor dos serviços de SaaS (Software-as-a-Service), nuvem e local da geração por meio das lentes de uma data factory única e fácil de gerenciar. 


##Terminologia
Esta seção apresenta a terminologia relacionada ao Azure Data Factory.

### Data Factory

O **Data Factory do Azure** é um serviço completamente gerenciado para compor serviços de armazenamento, processamento e movimentação de dados em pipelines de produção de dados simplificada, escalonável e confiável. 

O serviço do Data Factory consome, produz, gerencia e publica os **conjuntos de dados**. Uma assinatura do Azure pode ter uma ou mais instâncias do Azure Data Factory. Uma data factory do Azure pode ser vinculada a um ou mais armazenamento ou serviços de computação (chamados de serviços vinculados).
 
Uma data factory do Azure pode ter uma ou mais pipelines que processam dados em armazenamentos vinculados utilizando os serviços de computação vinculado como Azure HDInsight. Uma data factory do Azure não contém os dados nele contidos. Em vez disso, os dados são armazenados fora da data factory, no sistema de armazenamento existente do usuário.


### Serviço vinculado
Um serviço vinculado é um serviço que está vinculado a uma data factory do Azure. Um serviço vinculado pode ser uma das seguintes opções:


- Um armazenamento de dados, como o armazenamento do Azure, banco de dados SQL do Azure e banco de dados SQL Server local
- Um serviço de computação como Azure HDInsight.

Um armazenamento de dados é um contêiner de dados ou conjuntos de dados e o Azure HDInsight é o único serviço de computação com suporte no momento. Primeiro, crie um serviço vinculado para apontar para um armazenamento de dados e, em seguida, defina os conjuntos de dados para representar os dados desse armazenamento de dados. 

### Conjunto de dados
Uma visualização de dados nomeada. Os dados que estão sendo descritos podem variar de simples bytes, dados semiestruturados como arquivos CSV até mesmo modelos ou tabelas relacionais. Uma **tabela** é um conjunto de dados que tem um esquema e é retangular.

###Pipeline
Uma **pipeline** em uma data factory do Azure processa dados nos serviços de armazenamento vinculados utilizando serviços de computação vinculados. Ela contém uma sequência de atividades em que cada atividade executa uma operação de processamento específica. Por exemplo, uma atividade de cópia copia dados de um armazenamento de origem para um armazenamento de destino e atividades de Hive/Pig, utiliza um cluster do Azure HDInsight para processar os dados utilizando consultas ou scripts do Pig.

As etapas comuns para criar uma instância de data factory do Azure são:

1. Criar uma data factory
2. Criar um serviço vinculado para cada armazenamento de dados ou serviço de computação
3. Criar conjuntos de dados de entrada e saída
4. Criar uma pipeline 

###Atividade
Uma etapa um processamento de dados em uma pipeline que usa um ou mais conjuntos de dados de entrada e produz um ou mais conjuntos de dados de saída.  As atividades são executadas em um ambiente de execução (por exemplo: cluster do Azure HDInsight) e dados de leitura/gravação para um armazenamento de dados associado à instância da data factory do Azure.
 
###Hub de dados
Um Hub de dados do Azure é um contêiner para serviços de computação e armazenamento de dados. Por exemplo, um cluster de Hadoop com HDFS como armazenamento e Hive/Pig como computação (processamento) é um hub de dados. Da mesma forma, um EDW(data warehouse corporativo) pode ser estabelecido como um hub de dados (banco de dados como armazenamento, procedimentos armazenados e/ou ferramenta ETL, como serviços de computação).  As pipelines utilizam armazenamentos de dados e executam em recursos de computação em um hub de dados. Apenas o hub de HDInsight tem suporte neste momento.

O Hub de dados permite que um data factory seja dividido em agrupamentos específicos de domínio ou lógica, como o "Hub de Azure do oeste dos EUA" que gerencia todos os serviços vinculados (armazenamentos de dados e computação) e pipelines voltados para o data center do oeste dos EUA ou para as "Vendas de hub de EDW" que gerencia todos os serviços vinculados e pipelines interessados com dados de preenchimento e processamento para o data warehouse da empresa de vendas.

Uma característica importante do Hub é que uma pipeline é executada em um único hub. Isso significa que, ao definir uma pipeline, todos os serviços vinculados referenciados por tabelas ou atividades dentro dessa pipeline devem ter o mesmo nome do hub que a pipeline em si. Se a propriedade HubName não for especificada para um serviço vinculado, o serviço vinculado é colocado no hub "Padrão".

###Fatia
Uma tabela em um data factory do Azure é composta de fatias ao longo do eixo de tempo. A largura de uma fatia é determinada pelo agendamento - por hora/diariamente. Quando o agendamento é "por hora", uma fatia é produzida por hora com a hora de início e de término de uma pipeline e assim por diante.  

Fatias fornecem a capacidade para profissionais de TI trabalharem com um subconjunto de dados gerais para uma janela de tempo específico (por exemplo: a fatia que é gerada para a duração (horas): 13:00 às 14:00). Também pode exibir todas as fatias de dados downstream para um determinado momento interno e executar novamente uma fatia em caso de falha.

A execução é uma unidade de processamento de uma fatia. Pode haver uma ou mais execuções de uma fatia no caso de repetições ou se você executar novamente a fatia em caso de falhas. Uma fatia é identificada pela sua hora de início. 

###Gateway de gerenciamento de dados
O Gateway de gerenciamento de dados da Microsoft é um software que se conecta a fontes de dados locais para serviços de nuvem para consumo. É necessário ter pelo menos um gateway instalado no seu ambiente corporativo e registrá-lo com o portal do Azure Data Factory antes de adicionar fontes de dados locais como serviços vinculados.


##Próximas etapas
[Introdução ao Data Factory][datafactory-getstarted]. Este artigo fornece um tutorial de ponta a ponta que mostra como criar uma data factory do Azure que copia dados de um blob do Azure para um banco de dados SQL do Azure.

[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx


[datafactory-getstarted]: ../data-factory-get-started/

[image-data-factory-introduction-traditional-ETL]: ./media/data-factory-introduction/TraditionalETL.PNG

[image-data-factory-introduction-todays-diverse-processing-landspace]:./media/data-factory-introduction/TodaysDiverseDataProcessingLandscape.PNG

[image-data-factory-introduction-secenario1-source-datahub]:./media/data-factory-introduction/Scenario1SourceDataHub.png

[image-data-factory-introduction-secenario2-operationalize-infoproduction]:./media/data-factory-introduction/Scenario2-OperationalizeInformationProduction.png

[image-data-factory-application-model]:./media/data-factory-introduction/DataFactoryApplicationModel.png

[image-data-factory-data-flow]:./media/data-factory-introduction/DataFactoryDataFlow.png



