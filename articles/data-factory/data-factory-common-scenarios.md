<properties 
	pageTitle="Cenários comuns para uso da Fábrica de Dados do Azure" 
	description="Aprenda sobre alguns cenários comuns para usar o serviço de Fábrica de Dados do Azure" 
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
	ms.date="07/07/2015" 
	ms.author="spelluru"/>

# Cenários comuns para uso da Fábrica de Dados do Azure
Esta seção descreve alguns cenários de exemplo nos quais a Data Factory do Azure pode oferecer suporte hoje e continuará crescendo como cenários de Hub.

> [AZURE.NOTE]Leia o artigo [Introdução à Fábrica de Dados do Azure][datafactory-introduction] antes de ler este.

##Cenário nº 1: fontes de dados para o Hub de dados
![Hub de dados de origem][image-data-factory-introduction-secenario1-source-datahub]

As empresas têm dados de diferentes tipos, localizados em diferentes fontes. A primeira etapa na criação de um sistema de produção de informações é se conectar a todas as fontes necessárias de dados e processamento, como serviços SaaS, os serviços web compartilhamentos, FTP, arquivos e mover os dados conforme necessário para processamento posterior.

Sem o Data Factory, as empresas devem criar componentes de movimentação de dados personalizados ou gravar serviços personalizados para integrar essas fontes de dados e processamento. Isso é caro e é difícil de integrar e manter esses sistemas, além de, geralmente, não ter o monitoramento e os alertas de nível empresarial e os controles que podem oferecer um serviço completamente gerenciado.
  
Com os serviços de armazenamento e processamento de dados do Azure Data Factory são coletados em um contêiner de Hub que facilita e otimiza as atividades de computação e armazenamento, permite o gerenciamento de consumo de recurso unificado e fornece serviços para movimentação de dados conforme necessário.

##Cenário nº 2: colocar em operação a produção de informações
Cenários de operacionalização são a próxima etapa lógica após cenários de fornecimento de dados. Depois que os dados estão presentes em um Hub, você deseja criar e colocar em operação as pipelines de dados para produzir confiavelmente os dados transformados em uma agenda controlada e passível de manutenção para alimentar os ambientes de produção com dados confiáveis. A transformação de dados no Data Factory do Azure é por meio de Hive, Pig e execução de processamento C# personalizado no Hadoop (Azure HDInsight). Essas transformações podem ser usadas para limpar dados, campos de dados críticos de máscara e realizar outras operações nos dados em uma ampla variedade de formas complexas. Normalmente, a operacionalização é obtida com uma infraestrutura complexa e difícil de manter e serviços personalizados e apresenta vários desafios de implantação, gerenciamento, dimensionamento, solução de problemas e a versão dessa solução.
  
Com o Data Factory como um serviço totalmente gerenciado, os usuários podem colocar em operação essas pipelines definindo-as com agendamentos recorrentes únicos ou complexos e orquestração tratada diretamente pelo serviço de Data Factory. Com os Hubs, gerenciamento de cluster para todos os dados e o processamento em um Hub é tratado em nome do usuário para que os usuários possam se concentrar na análise transformadora em vez do gerenciamento de infraestrutura. A Data Factory do Azure remove os desafios de trabalhar com serviços personalizados frágeis e permite que as empresas produzam informações confiáveis e reproduzíveis.


##Cenário nº 3: integrar a produção de informações com a descoberta de dados
As tradicionais abordagens e tecnologias BI, enquanto fornecem uma "fonte autoritativa da verdade”, quase sempre têm um efeito colateral sério: uma lista de pendências constante de solicitações devido a um processo de solicitação de alteração cuidadosamente controlado. Para adaptar-se a alterar rapidamente as questões de negócios, é necessário obter maior flexibilidade para as empresas para conectar seus sistemas de produção de informações com seus sistemas de consumo de informações. A Data Factory do Azure ajuda você a resolver o desafio de se conectar a esses sistemas com pipelines de dados simplificados de produção de informações e o desafio de consumo de informações ao disponibilizar dados atualizados confiáveis em formulários facilmente consumíveis.
  
O Azure Data Factory oferece suporte para os seguintes recursos para habilitar o consumo simples dos dados produzidos:

- Mover facilmente (uma vez ou agendado) os ativos de dados produzidos para armazéns de dados relacionais para consumo utilizando as ferramentas de BI existentes (Excel, Tableau, etc...).
- Consumir os ativos de dados produzidos por uma data factory utilizando o Power Query diretamente no Excel.

Consulte os tópicos a seguir para consumir dados utilizando o Power Query:

- [Power Query: conectar-se à tabela de armazenamento do Microsoft Azure][Power-Query-Azure-Table]
- [Power Query: conectar-se ao armazenamento de blob do Microsoft Azure][Power-Query-Azure-Blob]
- [Power Query: conectar-se ao Banco de Dados SQL do Microsoft Azure][Power-Query-Azure-SQL]
- [Power Query: conectar-se ao SQL Server local da Microsoft][Power-Query-OnPrem-SQL] 


[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx

[copy-data-with-adf]: http://azure.microsoft.com/documentation/articles/data-factory-copy-activity/
[use-pig-hive]: http://azure.microsoft.com/documentation/articles/data-factory-pig-hive-activities/
[run-map-reduce]: http://azure.microsoft.com/documentation/articles/data-factory-map-reduce/
[azure-ml-adf]: http://azure.microsoft.com/documentation/articles/data-factory-create-predictive-pipelines/

[msdn-stored-procedure-activity]: https://msdn.microsoft.com/library/dn912649.aspx

[adf-tutorial]: data-factory-tutorial.md
[datafactory-getstarted]: data-factory-get-started.md
[datafactory-introduction]: data-factory-introduction.md

[image-data-factory-introduction-secenario1-source-datahub]: ./media/data-factory-common-scenarios/Scenario1SourceDataHub.png

[image-data-factory-introduction-secenario2-operationalize-infoproduction]: ./media/data-factory-common-scenarios/Scenario2-OperationalizeInformationProduction.png



 

<!---HONumber=July15_HO3-->