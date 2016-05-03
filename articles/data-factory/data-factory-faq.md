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
	ms.date="04/18/2016" 
	ms.author="spelluru"/>

# Azure Data Factory - Perguntas frequentes

## Perguntas gerais

### O que é o Data Factory do Azure?

O Data Factory é um serviço de integração de dados baseado em nuvem que **automatiza a movimentação e a transformação dos dados**. Assim como uma fábrica que usa equipamentos para pegar matérias-primas e transformá-las em produtos, o Data Factory orquestra serviços existentes que coletam dados brutos e os transformam em informações para uso imediato.
 
O serviço Data Factory permite que você crie fluxos de trabalho orientados a dados para mover dados entre armazenamentos de dados local e na nuvem, além de processar/transformar dados usando serviços de computação, como Azure HDInsight e Análise Azure Data Lake. Depois de criar um pipeline que executa a ação que você precisa, você pode programá-lo para ser executado periodicamente (por hora, diariamente, semanalmente etc.).

Consulte [Visão geral e principais conceitos](data-factory-introduction.md) para obter mais detalhes.

### Onde posso encontrar detalhes de preços do Data Factory do Azure?

Confira a [página de Detalhes de Preços do Data Factory][adf-pricing-details] para os detalhes de preços para o Azure Data Factory.

### Como faço para começar a utilizar o Azure Data Factory?

- Para obter uma visão geral do Azure Data Factory, confira [Introdução ao Azure Data Factory](data-factory-introduction.md).
- Para obter um tutorial rápido, confira [Introdução ao Azure Data Factory](data-factory-get-started.md).
- Para obter a documentação abrangente, confira a [Documentação do Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/).
  
### Qual é a disponibilidade regional do Data Factory?
O Data Factory está disponível no **Oeste dos EUA** e no **Europa Setentrional**. Os serviços de computação e armazenamento utilizados por data factories podem estar em outras regiões. Consulte [Regiões com suporte](data-factory-introduction.md#supported-regions).
 
### Quais são os limites de número de fábricas/pipelines/atividades/conjuntos de dados?
 
Consulte a seção **Limites do Azure Data Factory** do artigo [Limites, cotas e restrições da assinatura e do serviço do Azure](../azure-subscription-service-limits.md#data-factory-limits).


### O que é a experiência de criação/desenvolvedor com o serviço Azure Data Factory?

Você pode criar fábricas de dados usando um dos seguintes itens:

- **Azure Portal** As folhas do Data Factory no Portal do Azure fornecem uma interface do usuário avançada para que você crie serviços vinculados a anúncios de fábricas de dados. O **Editor Data Factory**, que também faz parte do portal, permite que você crie facilmente serviços vinculados, tabelas, conjuntos de dados e pipelines especificando definições de JSON para esses artefatos. Confira [Introdução ao Data Factory](data-factory-get-started.md) para obter um exemplo de como usar o portal/editor para criar e implantar um Data Factory.   

- **Azure PowerShell** Se você for um usuário do PowerShell e preferir usar o PowerShell em vez da interface do usuário do Portal, poderá usar os cmdlets do Azure Data Factory que são fornecidos como parte do Azure PowerShell para criar e implantar fábricas de dados. Confira [Criar e monitorar o Azure Data Factory usando o PowerShell do Azure](data-factory-monitor-manage-using-powershell.md) para obter um exemplo simples e [Tutorial: mover e processar os arquivos de log usando o Data Factory][adf-tutorial] para obter um exemplo avançado de uso de cmdlets do PowerShell para criar e implantar uma fábrica de dados. Confira o conteúdo de [Referência de cmdlets de Data Factory][adf-powershell-reference] na Biblioteca do MSDN para obter uma documentação abrangente de cmdlets de Data Factory.
  
- **Visual Studio** Você também pode usar o Visual Studio para criar, monitorar e gerenciar as fábricas de dados programaticamente. Consulte [Criar, monitorar e gerenciar data factories do Azure usando o SDK do .NET do Data Factory](data-factory-create-data-factories-programmatically.md) para obter detalhes.
  
- **Biblioteca de classes .NET** Você pode criar fábricas de dados programaticamente usando o SDK do .NET de Data Factory. Confira [Criar, monitorar e gerenciar fábricas de dados usando o SDK do .NET][create-factory-using-dotnet-sdk] para obter uma explicação sobre a criação de uma fábrica de dados usando o SDK do .NET. Confira a [Referência da Biblioteca de Classes de Data Factory][msdn-class-library-reference] para obter uma documentação abrangente sobre o SDK do .NET de Data Factory.

- **REST API** Você também pode usar a API REST exposta pelo serviço do Azure Data Factory para criar e implantar fábricas de dados. Confira a [Referência da API REST de Data Factory][msdn-rest-api-reference] para obter uma documentação abrangente da API REST de Data Factory.
 
- **Modelo do Azure Resource Manager** Consulte [Tutorial: Criar a sua primeira Azure Data Factory usando o modelo do Azure Resource Manager](data-factory-build-your-first-pipeline-using-arm.md) para obter detalhes.

### Posso renomear um Data Factory?
Não. Como outros recursos do Azure, o nome de uma Data Factory do Azure não pode ser alterado.

## Atividades - Perguntas frequentes
### Quais são os tipos diferentes de atividades que você pode usar em um pipeline do Azure Data Factory? 

- [Atividades de Movimentação de Dados](data-factory-data-movement-activities.md) para mover dados.
- [Atividades de Transformação de Dados](data-factory-data-transformation-activities.md) para processar/transformar dados. 

### Quando uma atividade é executada?
A configuração **disponibilidade** na tabela de dados de saída determina quando a atividade é executada. A atividade verifica se todas as dependências de dados de entrada foram atendidas (ou seja, estado **Pronto**) antes de começar a ser executada, se os conjuntos de dados de entrada forem especificados.

## Atividade de Cópia - Perguntas frequentes
### É melhor ter um pipeline com várias atividades ou um pipeline separado para cada atividade? 
Pipelines devem agrupar atividades relacionadas. Logicamente, você pode manter as atividades em um pipeline se as tabelas que as conectam não são consumidas por nenhuma outra atividade fora do pipeline. Desse modo, você não precisaria encadear períodos ativos do pipeline para que eles se alinhem uns com os outros. Além disso, a integridade dos dados nas tabelas internas ao pipeline será melhor preservada ao atualizar o pipeline. A atualização de pipeline interrompe essencialmente todas as atividades no pipeline, remove-as e cria essas atividades novamente. Da perspectiva de criação, também pode ser mais fácil ver o fluxo de dados nas atividades relacionadas em um arquivo JSON para o pipeline.

## Atividade de HDInsight - Perguntas frequentes

### O HDInsight dá suporte a quais regiões?

Confira a seção de Disponibilidade Geográfica no artigo a seguir, ou em [Detalhes de Preços do HDInsight][hdinsight-supported-regions].

### Qual região é utilizada por um cluster HDInsight sob demanda?

O cluster HDInsight sob demanda é criado na mesma região onde existe o armazenamento especificado para ser utilizado com o cluster existente.

### Como associar contas de armazenamento adicionais ao cluster HDInsight?

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

## Fatias - Perguntas frequentes

### Como executo novamente uma fatia?
Você pode executar novamente uma fatia de uma das seguintes maneiras:

- Clique em **Executar** na barra de comando na folha **FATIA DE DADOS** para a fatia no portal. 
- Execute o cmdlet **Set-AzureRmDataFactorySliceStatus** com Status definido como **Aguardando** para a fatia.   
	
		Set-AzureRmDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00" 

Confira [Set-AzureRmDataFactorySliceStatus][set-azure-datafactory-slice-status] para obter detalhes sobre o cmdlet.

### Quanto tempo levou para processar uma fatia?
1. Clique no bloco **Conjuntos de dados** da folha **DATA FACTORY** da sua fábrica de dados.
2. Clique no conjunto de dados específico na folha **Conjuntos de dados**.
3. Selecione a fatia em que você está interessado na lista **Fatias recentes** na folha **TABELA**.
4. Clique na execução da lista **Execuções de Atividade** na folha **FATIA DE DADOS**. 
5. Clique no bloco **Propriedades** da folha **DETALHES DE EXECUÇÃO DA ATIVIDADE**. 
6. Você deve ver o campo **DURAÇÃO** com um valor. Isso é o tempo necessário para processar a fatia.   

### Como parar uma fatia em execução?
Se você precisar interromper a execução do pipeline, poderá usar o cmdlet [Suspend-AzureRmDataFactoryPipeline](https://msdn.microsoft.com/library/mt603721.aspx). Atualmente, suspender o pipeline não interrompe as execuções de fatia que estão em andamento. Depois de concluir as execuções em andamento, nenhuma fatia extra é coletada.

Se você realmente desejar parar todas as execuções imediatamente, a única maneira seria excluir o pipeline e criá-lo novamente. Se você optar por excluir o pipeline, não é necessário excluir tabelas e serviços vinculados usados pelo pipeline.


[adf-tutorial]: data-factory-tutorial.md
[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: https://msdn.microsoft.com/library/dn883654.aspx
[msdn-rest-api-reference]: https://msdn.microsoft.com/library/dn906738.aspx

[adf-powershell-reference]: https://msdn.microsoft.com/library/dn820234.aspx
[azure-portal]: http://portal.azure.com
[set-azure-datafactory-slice-status]: https://msdn.microsoft.com/library/mt603522.aspx

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: http://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx
 

<!---HONumber=AcomDC_0420_2016-->