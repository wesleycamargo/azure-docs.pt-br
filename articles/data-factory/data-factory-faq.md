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
	ms.date="06/16/2015" 
	ms.author="spelluru"/>

# Azure Data Factory - Perguntas frequentes

## Perguntas gerais

### P: O que é o Azure Data Factory?

O Data Factory é um serviço totalmente gerenciado para os desenvolvedores comporem os serviços de processamento, movimento e armazenamento de dados em alta disponibilidade, pipelines de dados tolerantes a falhas. O Data Factory opera em armazenamento de dados em nuvem e local. Uma pipeline é um conjunto de entradas de dados, atividades de processamento e saídas de dados e é definida com o script JSON simples e ativada por meio de comandos do PowerShell. Uma vez ativada, o Data Factory orquestra e agenda as pipelines para executar no HDInsight (Hadoop) com as opções de gerenciamento de cluster automático em nome do usuário. O Data Factory também fornece um gerenciamento visual e experiência de monitoramento por meio do Portal de Visualização do Azure para monitorar todas as pipelines com detalhes sobre operações e informações de integridade do serviço em um painel de controle.
 
### P: Qual desafio do cliente é resolvido pelo Data Factory?

O Azure Data Factory equilibra a agilidade de armazenamento de dados aprimorados diversificados, serviços de processamento e movimentação através do armazenamento relacional tradicional junto com dados não estruturados, com o controle e o monitoramento de recursos de um serviço totalmente gerenciado.

### P: Quais são os públicos-alvo do Data Factory?


- Desenvolvedores de Dados: são responsáveis por criar serviços de integração entre o Hadoop e outros sistemas:
	- Deve acompanhar e integrar com um cenário de dados crescente e alteração continuamente
	- Deve escrever o código personalizado para a produção de informações, e ele é caro, difícil de manter e pouco disponível ou tolerante a falhas

- Especialistas em TI: pretendem incorporar mais dados diversificados dentro de sua infraestrutura de TI:
	- Necessário para pesquisar em todos os dados de uma organização para obter resultados de negócios sofisticados
	- Deve gerenciar recursos de computação e armazenamento para o contabilização de custo e escala local e na nuvem
	- Deve adicionar rapidamente o processamento e origens diversas para direcionar as necessidades dos novos negócios, mantendo a visibilidade em todos os ativos de computação e armazenamento

###  P: Onde posso encontrar detalhes de preços do Azure Data Factory?

Confira a [página de Detalhes de Preços do Data Factory][adf-pricing-details] para os detalhes de preços para o Azure Data Factory.

### P. Como faço para começar a utilizar o Azure Data Factory?

- Para obter uma visão geral do Azure Data Factory, confira [Introdução ao Azure Data Factory][adf-introduction].
- Para obter um tutorial rápido, confira [Introdução ao Azure Data Factory][adfgetstarted].
- Para obter a documentação abrangente, confira a [Documentação do Azure Data Factory][adf-documentation-landingpage].

  
### P: Como os clientes podem acessar o Data Factory?

Os clientes podem obter acesso ao Data Factory por meio do [Portal de Visualização do Azure][azure-preview-portal].

### P: Qual é a disponibilidade regional do Data Factory?

Em visualização pública, o Data Factory só estará disponível no Oeste dos EUA. Os serviços de computação e armazenamento utilizados por data factories podem estar em outras regiões.
 
### P: Quais são os limites de número de fábricas de dados/pipelines/atividades/conjuntos de dados? 


- Número de fábricas de dados em uma assinatura: 50
- Número de pipelines em uma fábrica de dados: 100
- Número de atividades em um pipeline: 10
- Número de conjuntos de dados em uma fábrica de dados: 100

### P: O que é a experiência de criação/desenvolvedor com o serviço Azure Data Factory?

Você pode criar fábricas de dados usando um dos seguintes itens:

- **Portal de Visualização do Azure**. As folhas da Fábrica de Dados no Portal de Visualização do Azure fornecem uma avançada interface do usuário para que você crie fábricas de dados e serviços vinculados. O **Editor Data Factory**, que também faz parte do portal, permite que você crie facilmente serviços vinculados, tabelas, conjuntos de dados e pipelines especificando definições de JSON para esses artefatos. Confira [Editor Data Factory][data-factory-editor] para obter uma visão geral do editor e [Introdução ao Data Factory][datafactory-getstarted] para obter um exemplo de uso do portal/editor para criar e implantar uma fábrica de dados.   
- **PowerShell do Azure**. Se for um usuário do PowerShell e preferir usar o PowerShell em vez da interface do usuário do Portal, você poderá usar os cmdlets de Fábrica de Dados do Azure que são fornecidos como parte do PowerShell do Azure para criar e implantar fábricas de dados. Confira [Criar e monitorar o Azure Data Factory usando o PowerShell do Azure][create-data-factory-using-powershell] para obter um exemplo simples e [Tutorial: mover e processar os arquivos de log usando o Data Factory][adf-tutorial] para obter um exemplo avançado de uso de cmdlets do PowerShell para criar e implantar uma fábrica de dados. Confira o conteúdo de [Referência de cmdlets de Data Factory][adf-powershell-reference] na Biblioteca do MSDN para obter uma documentação abrangente de cmdlets de Data Factory.  
- **Biblioteca de classes do .NET**. Você pode criar fábricas de dados programaticamente usando o SDK do .NET de Fábrica de Dados. Confira [Criar, monitorar e gerenciar fábricas de dados usando o SDK do .NET][create-factory-using-dotnet-sdk] para obter uma explicação sobre a criação de uma fábrica de dados usando o SDK do .NET. Confira a [Referência da Biblioteca de Classes de Data Factory][msdn-class-library-reference] para obter uma documentação abrangente sobre o SDK do .NET de Data Factory.  
- **API REST**. Você também pode usar a API REST exposta pelo serviço de Fábrica de Dados do Azure para criar e implantar fábricas de dados. Confira a [Referência da API REST de Data Factory][msdn-rest-api-reference] para obter uma documentação abrangente da API REST de Data Factory. 

### P: Posso renomear um Data Factory?
Não. Como outros recursos do Azure, o nome de uma Data Factory do Azure não pode ser alterado.

## Atividades - Perguntas frequentes
### P: Quais são as fontes de dados e atividades com suporte?

- **Fontes de dados com suporte para:** 
	- Armazenamento do Azure (Blob e Tabelas)
	- SQL Azure
	- Banco de Dados de Documentos do Azure
	- SQL Server local
	- Oracle local 
	- Sistema de arquivos local
	- MySQL local
	- DB2 local
	- Teradata local
	- Sybase local
	- PostgreSQL local.  
- **Atividades com suporte para**: 
	- Copiar atividade (do local para a nuvem e da nuvem para o local)
	- Atividade de HDInsight (Pig, Hive, MapReduce, transformações de Streaming do Hadoop)
	- Atividade de pontuação do Lote de Aprendizado de Máquina do Azure
	- Atividade de procedimento armazenado do SQL Azure
	- Atividades personalizadas do .NET.

### Quando uma atividade é executada?
A configuração **disponibilidade** na tabela de dados de saída determina quando a atividade é executada. A atividade verifica se todas as dependências de dados de entrada foram atendidas (ou seja, estado **Pronto**) antes de começar a ser executada.

## Atividade de Cópia - Perguntas frequentes
### P: Quais as regiões têm suporte pela Atividade de Cópia?

A Atividade de Cópia dá suporte a cópia de dados nas seguintes regiões: Leste dos EUA, Leste dos EUA 2, Oeste dos EUA, Região Central dos EUA, Centro-Norte dos EUA, Centro-Sul dos EUA, Europa do Norte, Europa Ocidental, Sudeste Asiático, Leste do Japão e Sul do Brasil.

Também há suporte para a cópia de dados para outras regiões, utilizando uma das regiões acima para rotear os dados. A operação de cópia é medida com base na região de onde os dados são roteados.

Região de destino da cópia | Região usada para roteamento
-------------------------- | -----------------------
Ásia Oriental | Sudeste da Ásia
Oeste do Japão | Leste do Japão

### Como posso fazer cópia para múltiplas tabelas de saída?
Você pode ter várias tabelas de saída em um pipeline, como mostrado no exemplo a seguir:

	"outputs":  [ 
		{ "name": “outputtable1” }, 
		{ "name": “outputtable2” }  
	],
 
### É melhor ter um pipeline com várias atividades ou um pipeline separado para cada atividade? 
Pipelines devem agrupar atividades relacionadas. Logicamente, você pode manter as atividades em um pipeline se as tabelas que as conectam não são consumidas por nenhuma outra atividade fora do pipeline. Desse modo, você não precisaria encadear períodos ativos do pipeline para que eles se alinhem uns com os outros. Além disso, a integridade dos dados nas tabelas internas ao pipeline será melhor preservada ao atualizar o pipeline. A atualização de pipeline interrompe essencialmente todas as atividades no pipeline, remove-as e cria essas atividades novamente. Da perspectiva de criação, também pode ser mais fácil ver o fluxo de dados nas atividades relacionadas em um arquivo JSON para o pipeline.

## Atividade de HDInsight - Perguntas frequentes

### P: Quais regiões têm suporte do HDInsight?

Confira a seção de Disponibilidade Geográfica no artigo a seguir, ou em [Detalhes de Preços do HDInsight][hdinsight-supported-regions].

### P: Qual região é utilizada por um cluster HDInsight sob demanda?

O cluster HDInsight sob demanda é criado na mesma região onde existe o armazenamento especificado para ser utilizado com o cluster existente.

### P: Como associar contas de armazenamento adicionais ao cluster HDInsight?

Se você estiver usando seu próprio cluster HDInsight (BYOC - traga seu próprio Cluster), confira os tópicos a seguir:

- [Usando um Cluster HDInsight com metastores e contas de armazenamento alternativas][hdinsight-alternate-storage]
- [Usar contas de armazenamento adicionais com o Hive do HDInsight][hdinsight-alternate-storage-2]

Se você estiver usando um cluster sob demanda que é criado pelo serviço Data Factory, você precisa especificar contas de armazenamento adicionais para o serviço vinculado HDInsight para que o serviço Data Factory possa registrá-los em seu nome. Na definição de JSON para o serviço vinculado sob demanda, use a propriedade **additionalLinkedServiceNames** para especificar contas de armazenamento alternativas, como mostrado no trecho de JSON a seguir:
 
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

No exemplo acima, otherLinkedServiceName1 e otherLinkedServiceName2 representam serviços vinculados cujas definições contêm as credenciais de que o cluster HDInsight precisa para acessar contas de armazenamento alternativas.

## Atividade de procedimento armazenado - Perguntas frequentes
### Quais fontes de dados têm suporte pela atividade de procedimento armazenado?
A atividade de procedimento armazenado dá suporte apenas ao Banco de Dados SQL do Azure no momento.

## Fatias - Perguntas frequentes

### Como executo novamente uma fatia?
Você pode executar novamente uma fatia de uma das seguintes maneiras:

- Clique em **Executar** na barra de comando na folha **FATIA DE DADOS** para a fatia no portal. 
- Execute o cmdlet **Set-AzureDataFactorySliceStatus** com Status definido como **PendingExecution** para a fatia.   
	
		Set-AzureDataFactorySliceStatus -Status PendingExecution -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00" 

Confira [Set-AzureDataFactorySliceStatus][set-azure-datafactory-slice-status] para obter detalhes sobre o cmdlet.

### Quanto tempo levou para processar uma fatia?
1. Clique no bloco **Conjuntos de dados** da folha **DATA FACTORY** da sua fábrica de dados.
2. Clique no conjunto de dados específico na folha **Conjuntos de dados**.
3. Selecione a fatia em que você está interessado na lista **Fatias recentes** na folha **TABELA**.
4. Clique na execução da lista **Execuções de Atividade** na folha **FATIA DE DADOS**. 
5. Clique no bloco **Propriedades** da folha **DETALHES DE EXECUÇÃO DA ATIVIDADE**. 
6. Você deve ver o campo **DURAÇÃO** com um valor. Isso é o tempo necessário para processar a fatia.   

### Como parar uma fatia em execução?
Se você precisar interromper a execução do pipeline, poderá usar o cmdlet [Suspend-AzureDataFactoryPipeline](https://msdn.microsoft.com/library/dn834939.aspx). Atualmente, suspender o pipeline não interrompe as execuções de fatia que estão em andamento. Depois de concluir as execuções em andamento, nenhuma fatia extra é coletada.

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
 

<!---HONumber=July15_HO5-->