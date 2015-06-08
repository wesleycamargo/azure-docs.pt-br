<properties 
	pageTitle="Azure Data Factory - Perguntas frequentes" 
	description="Perguntas frequentes sobre o Azure Data Factory." 
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
	ms.date="04/23/2015" 
	ms.author="spelluru"/>

# Azure Data Factory - Perguntas frequentes

## Perguntas gerais

### P: o que é a fábrica de dados do Azure?

O Data Factory é um serviço totalmente gerenciado para os desenvolvedores comporem os serviços de processamento, movimento e armazenamento de dados em alta disponibilidade, pipelines de dados tolerantes a falhas. O Data Factory opera em armazenamento de dados em nuvem e local. Uma pipeline é um conjunto de entradas de dados, atividades de processamento e saídas de dados e é definida com o script JSON simples e ativada por meio de comandos do PowerShell. Uma vez ativada, o Data Factory orquestra e agenda as pipelines para executar no HDInsight (Hadoop) com as opções de gerenciamento de cluster automático em nome do usuário. O Data Factory também fornece um gerenciamento visual e experiência de monitoramento por meio do Portal de Visualização do Azure para monitorar todas as pipelines com detalhes sobre operações e informações de integridade do serviço em um painel de controle.
 
### P: quais desafio do cliente faz Data Factory resolver?

O Azure Data Factory equilibra a agilidade de armazenamento de dados aprimorados diversificados, serviços de processamento e movimentação através do armazenamento relacional tradicional junto com dados não estruturados, com o controle e o monitoramento de recursos de um serviço totalmente gerenciado.

### P: quem é o público-alvo de fábrica de dados?


- Desenvolvedores de Dados: são responsáveis por criar serviços de integração entre o Hadoop e outros sistemas:
	- Deve acompanhar e integrar com um cenário de dados crescente e alteração continuamente
	- Deve escrever o código personalizado para a produção de informações, e ele é caro, difícil de manter e pouco disponível ou tolerante a falhas

- Especialistas em TI: pretendem incorporar mais dados diversificados dentro de sua infraestrutura de TI:
	- Necessário para pesquisar em todos os dados de uma organização para obter resultados de negócios sofisticados
	- Deve gerenciar recursos de computação e armazenamento para o contabilização de custo e escala local e na nuvem
	- Deve adicionar rapidamente o processamento e origens diversas para direcionar as necessidades dos novos negócios, mantendo a visibilidade em todos os ativos de computação e armazenamento

###  P: onde encontrar a preços de detalhes de fábrica de dados do Azure?

Consulte [página de detalhes de preços de fábrica de dados][adf-pricing-details] para os detalhes de preços para a fábrica de dados do Azure.

### P. Como começar com a fábrica de dados do Azure?

- Para obter uma visão geral da fábrica de dados do Azure, consulte [Introdução ao Azure dados Factory][adf-introduction].
- Para obter um tutorial rápido, consulte [Introdução ao Azure Data Factory][adfgetstarted].
- Para documentação abrangente, consulte [documentação do Azure Data Factory][adf-documentation-landingpage].

  
### P: como os clientes podem acessar dados fábrica?

Os clientes podem obter acesso a fábrica de dados por meio de [Azure Preview Portal][azure-preview-portal].

### P: qual é a disponibilidade da região de dados de fábrica?

Em visualização pública, o Data Factory só estará disponível no Oeste dos EUA. Os serviços de computação e armazenamento utilizados por data factories podem estar em outras regiões.
 
### P: quais são os limites no número de fábricas/pipelines/atividades/conjuntos de dados? 


- Número de fábricas de dados dentro de uma assinatura: 50
- Número de pipelines dentro de uma fábrica de dados: 100
- Número de atividades em um pipeline: 10
- Número de conjuntos de dados com uma fábrica de dados: 100

### P: o que é a experiência de criação/desenvolvedor com o serviço de fábrica de dados do Azure?

Você pode criar fábricas de dados usando um dos seguintes itens:

- **Portal de visualização do azure**. As folhas da Fábrica de Dados no Portal de Visualização do Azure fornecem uma avançada interface do usuário para que você crie fábricas de dados e serviços vinculados. O **dados fábrica de Editor**, que também faz parte do portal, permite que você crie facilmente serviços vinculados, tabelas, conjuntos de dados e pipelines especificando definições de JSON para esses artefatos. Consulte [dados fábrica de Editor][data-factory-editor] para uma visão geral do editor e [Introdução aos dados fábrica][datafactory-getstarted] para obter um exemplo usando o portal/editor para criar e implantar uma fábrica de dados.   
- **PowerShell do Azure**. Se for um usuário do PowerShell e preferir usar o PowerShell em vez da interface do usuário do Portal, você poderá usar os cmdlets de Fábrica de Dados do Azure que são fornecidos como parte do PowerShell do Azure para criar e implantar fábricas de dados. Consulte [monitor fábrica de dados do Azure usando o PowerShell do Azure e criar][create-data-factory-using-powershell] para obter um exemplo simple e [Tutorial: mover e processar os arquivos de log usando o alocador de dados][adf-tutorial] para obter um exemplo avançado usando o PowerShell cmdles para criar ad implantar uma fábrica de dados. Consulte [dados de referência de Cmdlet de fábrica][adf-powershell-reference] conteúdo na biblioteca MSDN para uma documentação abrangente de cmdlets de fábrica de dados.  
- **Biblioteca de classes do .NET**. Você pode criar fábricas de dados programaticamente usando o SDK do .NET de Fábrica de Dados. Consulte [criar, monitorar e gerenciar as fábricas de dados usando o SDK do .NET][create-factory-using-dotnet-sdk] para obter uma explicação da criação de uma fábrica de dados usando o SDK do .NET. Consulte [referência de biblioteca de classe de fábrica de dados][msdn-class-library-reference] para uma documentação abrangente do SDK do .NET de fábrica de dados.  
- **API REST**. Você também pode usar a API REST exposta pelo serviço de Fábrica de Dados do Azure para criar e implantar fábricas de dados. Consulte [referência da API REST fábrica dados][msdn-rest-api-reference] para uma documentação abrangente da API de REST de fábrica de dados. 

## Atividades - perguntas Frequentes
### P: quais são as fontes de dados com suporte e atividades?

- **Suporte para fontes de dados:** (Blob e tabelas) de armazenamento do Azure, SQL Server, banco de dados do SQL Azure, sistema de arquivos, banco de dados Oracle.
- **Atividades de suporte:**: cópia atividade (no local para a nuvem e nuvem para locais), atividade HDInsight (Pig, Hive, MapReduce, transformações de Streaming do Hadoop), as atividades de pontuação do Azure máquina aprendizado em lotes, atividade de procedimento armazenado e atividades personalizadas do c#.

### Quando uma atividade é executado?
O **disponibilidade** configuração na tabela de saída de dados determina quando a atividade for executada. A atividade verifica se todas as dependências de dados de entrada sejam atendidas (ou seja, **pronto** estado) antes de iniciar a execução.

## Copiar atividade - perguntas Frequentes
### P: quais regiões são compatíveis com a atividade de cópia?

O oferece suporte à atividade de cópia copiando dados para as seguintes áreas: Leste dos EUA, Leste dos EUA 2, oeste dos EUA, centro dos EUA, Norte dos Estados Unidos, Centro Sul dos EUA, Norte da Europa, Europa Ocidental e Sudeste da Ásia.

Copiando dados em outras regiões também é suportado, usando uma das regiões acima para rotear os dados. A operação de cópia é medida com base na região de onde os dados são roteados.

Região de destino da cópia | Região usado para roteamento
-------------------------- | -----------------------
Ásia Oriental | Sudeste da Ásia
Leste do Japão | Oeste dos EUA
Oeste do Japão | Oeste dos EUA
Sul do Brasil | Leste dos EUA 2

### Como copiar a várias tabelas de saída?
Você pode ter várias tabelas de saída em um pipeline, como mostrado no exemplo a seguir:

	"outputs":  [ 
		{ "name": “outputtable1” }, 
		{ "name": “outputtable2” }  
	],
 
### É melhor ter um pipeline com várias atividades ou um pipeline separado para cada atividade? 
Pipelines devem para agrupar atividades relacionadas. Logicamente, você pode manter as atividades em um pipeline se as tabelas que conexão-los não consumidas por qualquer outra atividade fora do pipeline. Dessa forma, você não precisaria períodos ativo do pipeline de cadeia para que eles se alinham uns com os outros. Além disso, a integridade dos dados nas tabelas internas para o pipeline será melhor preservada ao atualizar o pipeline. Atualização de pipeline essencialmente interrompe todas as atividades no pipeline, removê-los e criá-los novamente. Da perspectiva de criação, ele também pode ser mais fácil de ver o fluxo de dados em que as atividades relacionadas em um arquivo JSON para o pipeline.

## Atividade de HDInsight - perguntas Frequentes

### P: quais regiões são suportados pelo HDInsight?

Consulte a seção de disponibilidade geográfica no seguinte artigo: ou [detalhes de preços do HDInsight][hdinsight-supported-regions].

### P: quais região é usado por um cluster de HDInsight sob demanda?

O cluster HDInsight sob demanda é criado na mesma região onde existe o armazenamento especificado para ser utilizado com o cluster existente.

### P: como associar mais contas de armazenamento ao cluster HDInsight?

Se você estiver usando seu próprio HDInsight Cluster (BYOC - traga seu próprio Cluster), consulte os seguintes tópicos:

- [Usando um Cluster HDInsight com Metastores e contas de armazenamento alternativo][hdinsight-alternate-storage]
- [Usar contas de armazenamento adicionais com o Hive do HDInsight][hdinsight-alternate-storage-2]

Se você estiver usando um cluster sob demanda que é criado pelo serviço de dados de fábrica, você precisa especificar contas de armazenamento adicionais para o HDInsight vinculado de serviço para que o serviço de dados fábrica pode registrá-los em seu nome. Na definição de JSON para o serviço vinculado sob demanda, use **additionalLinkedServiceNames** propriedade para especificar contas de armazenamento alternativo, como mostrado no seguinte trecho de JSON:
 
	{
	    "name": "MyHDInsightOnDemandLinkedService",
	    "properties":
	    {
	        "type": "HDInsightOnDemandLinkedService",
	        "clusterSize": 1,
	        "timeToLive": "00:01:00",
	        "linkedServiceName": "LinkedService-SampleData",
	        "additionalLinkedServiceNames": [ "otherLinkedServiceName1", "otherLinkedServiceName2" ] 
	    }
	} 

No exemplo acima, otherLinkedServiceName1 e otherLinkedServiceName2 representam serviços vinculados cujas definições contêm as credenciais que o cluster HDInsight precisa acessar contas de armazenamento alternativo.

## Atividade de procedimento armazenado - perguntas Frequentes
### Quais fontes de dados oferece suporte a atividade de procedimento armazenado?
A atividade de procedimento armazenado suporta apenas o Azure SQL Database no momento.

## Fatias - perguntas Frequentes

### Como executo uma fatia?
Você pode executar novamente uma fatia em uma das seguintes maneiras:

- Clique em **executar** na barra de comando o **FATIA de dados** blade da fatia no portal. 
- Executar **conjunto AzureDataFactorySliceStatus** cmdlet com Status definido como **PendingExecution** da fatia.   
	
		Set-AzureDataFactorySliceStatus -Status PendingExecution -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00" 

Consulte [conjunto AzureDataFactorySliceStatus][set-azure-datafactory-slice-status] para obter detalhes sobre o cmdlet.

### Quanto tempo levou para processar uma fatia?
1. Clique em **conjuntos de dados** bloco de **DATA FACTORY** blade da sua fábrica de dados.
2. Clique em conjunto de dados específico no **conjuntos de dados** lâmina.
3. Selecione a fatia que lhe interessam do **fatias recentes** lista o **tabela** blade.
4. Clique na atividade executada a partir de **atividade executa** lista o **FATIA de dados** lâmina. 
5. Clique em **propriedades** bloco de **detalhes de execução da atividade** blade. 
6. Você deve ver o **duração** campo com um valor. Isso é o tempo necessário para processar a fatia.   

### Como parar uma fatia em execução?
Se você precisar interromper o pipeline de execução, você poderá usar [Suspend-AzureDataFactoryPipeline](https://msdn.microsoft.com/library/dn834939.aspx) cmdlet. Atualmente, suspender o pipeline não interrompe as execuções de fatia que estão em andamento. Depois de concluir as execuções em andamento, nenhuma fatia extra é obtida.

Se você realmente desejar parar todas as execuções imediatamente, a única maneira seria excluir o pipeline e criá-lo novamente. Se você optar por excluir o pipeline, não é necessário excluir tabelas e serviços vinculados usados pelo pipeline.



[image-rerun-slice]: ./media/data-factory-faq/rerun-slice.png

[adfgetstarted]: data-factory-get-started.md
[adf-introduction]: data-factory-introduction.md
[adf-troubleshoot]: data-factory-troubleshoot.md
[data-factory-editor]: data-factory-editor.md
[datafactory-getstarted]: data-factory-get-started.md
[create-data-factory-using-powershell]: data-factory-monitor-manage-using-powershell.md
[adf-tutorial]: data-factory-tutorial.md
[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: https://msdn.microsoft.com/library/dn883654.aspx
[msdn-rest-api-reference]: https://msdn.microsoft.com/library/dn906738.aspx

[adf-powershell-reference]: https://msdn.microsoft.com/library/dn820234.aspx
[adf-documentation-landingpage]: http://go.microsoft.com/fwlink/?LinkId=516909
[azure-preview-portal]: http://portal.azure.com
[set-azure-datafactory-slice-status]: https://msdn.microsoft.com/library/azure/dn835095.aspx

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: http://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx

<!---HONumber=GIT-SubDir-->