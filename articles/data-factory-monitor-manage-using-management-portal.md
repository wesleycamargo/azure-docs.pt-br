<properties title="Monitor and manage Azure Data Factory using Azure Preview Portal" pageTitle="Monitorar e gerenciar o Azure Data Factory utilizando o Portal de visualização do Azure" description="Learn how to use Azure Management Portal to monitor and manage Azure data factories you have created." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Monitorar o Data Factory do Azure utilizando o Portal de visualização do Azure

- [Exibir todas os data factories em uma assinatura do Azure](#AllDataFactories)
- [Exibir detalhes sobre uma data factory](#DataFactoryDetails)
- [Modo de exibição de diagrama de uma data factory](#DataFactoryDiagram)
- [Exibir serviços vinculados a um data factory](#DataFactoryLinkedServices)
- [Exibir detalhes sobre um serviço vinculado](#DataFactoryLinkedService) 
- [Exibir conjuntos de dados em uma data factory](#DataFactoryDatasets)
- [Exibir detalhes sobre um conjunto de dados](#DataFactoryDataset)
- [Exibir detalhes sobre uma fatia](#DataFactorySlice) 
- [Exibir todas as execuções de atividade de uma fatia](#DataFactoryActivtyRuns) 
- [Exibir detalhes sobre uma execução de atividade](#DataFactoryActivtyRunDetails)
- [Lentes de operação - eventos da semana anterior](#EventsInThePastweek)  
   


## <a name="AllDataFactories"></a> Exibir todas os data factories em uma assinatura do Azure

- Entre no [Portal de visualização do Azure][azure-preview-portal].
- Clique em **PROCURAR** hub à esquerda e clique em **Data factories**.  

	![BROWSE hub -> Data Factories][image-data-factory-browse-datafactories]

	Caso não visualize os **Data factories**, clique em **Tudo** e, em seguida, clique em **Data factories** na folha **Procurar**.

	![BROWSE hub -> Everything] [image-data-factory-browse-everything]

- Você deve ver todas os data factories na folha **Data factories**.

	![Data factories blade][image-data-factory-datafactories-blade]

    
## <a name="DataFactoryDetails"></a> Exibir detalhes sobre uma data factory

Para exibir detalhes sobre uma data factory, siga um destes procedimentos: 


- Clique em um data factory na folha **Data factories** mostrada acima.
- Clique no link da data factory no **Quadro inicial**. **O Quadro inicial** é a folha exibida ao realizar o logon no Portal de visualização do Azure. Se você tiver selecionado **Adicionar ao quadro inicial** durante a criação de uma data factory (opção padrão), é necessário ver a data factory vinculada no quadro inicial, conforme mostrado na imagem a seguir. Neste exemplo, os links de data factory **ADFTutorialDataFactory**, **ADFTutorialDataFactoryDF** e **LogProcessingFactory** estão disponíveis no **Quadro inicial**.


![Data factory from the Startboard][image-data-factory-datafactory-from-startboard]

De qualquer forma, você verá a folha **DATA FACTORY** da data factory selecionada, conforme mostrado na imagem a seguir. 

 ![Data Factory Home Page][image-data-factory-datafactory-home-page]
 
## <a name="DataFactoryDiagram"></a> Modo de exibição de diagrama da data factory
Na folha **DATA FACTORY** da data factory, clique no bloco de **Diagrama** para visualizar a exibição do diagrama da data factory. 

![Data Factory Diagram View][image-data-factory-diagram-view]
 

## <a name="DataFactoryLinkedServices"></a> Exibir serviços vinculados a um data factory
Na folha **DATA FACTORY** da data factory, clique no bloco de **Serviços vinculados** para visualizar todos os serviços vinculados em uma lista. 

![Linked Services Blade][image-data-factory-linked-services]

## <a name="DataFactoryLinkedService"></a> Exibir detalhes sobre um serviço vinculado
Na folha **SERVIÇOS VINCULADOS**, clique no serviço vinculado na lista para visualizar detalhes sobre ele. 

![Linked Service Blade][image-data-factory-linked-service]

## <a name="DataFactoryDatasets"></a> Exibir conjuntos de dados em uma data factory 
Na folha **DATA FACTORY** da data factory, clique no bloco de **Conjunto de dados** para visualizar todas as tabelas na data factory.

![Data Sets Blade][image-data-factory-datasets] 

## <a name="DataFactoryDataset"></a>  Exibir detalhes sobre um conjunto de dados
Clique na lista de conjuntos de dados na folha CONJUNTOS DE DADOS para visualizar os detalhes sobre o conjunto de dados. Observe que uma tabela é um conjunto de dados retangular que tem um esquema. É o único tipo de conjunto de dados com suporte neste momento. 

![Table Blade][image-data-factory-table]

Na folha **TABELA** acima, é possível visualizar as **Fatias recentes**, bem como as **Fatias do problema**. Clique em **... (reticências)** para visualizar todas as fatias. 

![All Slices of a Table][image-data-factory-all-slices]

Na folha **Fatias de dados**, é possível utilizar um filtro para visualizar as fatias específicas que você deseja analisar.


## <a name="DataFactorySlice"></a> Exibir detalhes sobre uma fatia
Clique em uma fatia na lista de fatias da folha **TABELA** ou da folha **Fatias de dados** para visualizar detalhes sobre essa fatia. 

![Data Slice][image-data-factory-dataslice]


### <a name="DataFactoryActivtyRuns"></a> Exibir todas as execuções de atividade de uma fatia
Para uma fatia, pode haver mais de uma execução. Por exemplo, quando uma fatia falha, o serviço pode tentar novamente por algumas vezes. Também é possível executar novamente uma fatia que falhou em todas as tentativas. É possível visualizar toda as execuções de atividade na folha** Fatia de dados** na lista na parte inferior. 

## <a name="DataFactoryActivtyRunDetails"></a>  Exibir detalhes sobre uma execução de atividade
Clique em uma atividade em execução na lista de execuções na folha **Fatia de dados** para visualizar os detalhes sobre a execução da atividade. 

![Activity Run Details][image-data-factory-activity-run-details]

## <a name="EventsInThePastweek"></a> Lentes de operação - eventos da semana anterior
Na folha **DATA FACTORY** (ou página inicial) da data factory, clique em **Eventos na semana anterior** na lente de **Operações** para visualizar os eventos da semana anterior. Isso ajuda a ter uma visão geral das operações realizadas pelo data factory na última semana. Também ajuda a solucionar quaisquer erros com o processamento/movimentação de dados. 

![ Data Factory Events][image-data-factory-events]


## Consulte também

Artigo | Descrição
------ | ---------------
[Monitorar e gerenciar o Azure Data Factory utilizando o PowerShell][monitor-manage-using-powershell] | Este artigo descreve como monitorar uma data factory do Azure utilizando os cmdlets do PowerShell do Azure. 
[Habilitar pipelines para trabalhar com dados locais][use-onpremises-datasources] | Este artigo possui um passo a passo que mostra como copiar dados de um banco de dados SQL Server local para um blob do Azure.
[Usar o Pig e o Hive com o Data Factory][use-pig-and-hive-with-data-factory] | Este artigo tem um passo a passo que mostra como usar a Atividade de HDInsight para executar um script do hive/pig para processar dados de entrada a fim de gerar dados de saída. 
[Tutorial: Mover e processar arquivos de log usando o Data Factory][adf-tutorial] | Este artigo fornece um passo a passo que mostra como implementar um cenário próximo do real usando o Data Factory do Azure para transformar dados de arquivos de log em informações.
[Usar atividades personalizadas em um Data Factory][use-custom-activities] | Este artigo fornece um passo a passo com instruções para criar uma atividade personalizada e usá-la em um pipeline. 
[Solucionar problemas de Data Factory][troubleshoot] | Este artigo descreve como solucionar problemas do Data Factory do Azure.
[Referência do Desenvolvedor do Data Factory do Azure][developer-reference] | A Referência do Desenvolvedor tem o conteúdo de referência abrangente de cmdlets, script JSON, funções, etc... 
[Referência de cmdlet do Data Factory do Azure][cmdlet-reference] | Este conteúdo de referência apresenta detalhes sobre todos os **cmdlets do Data Factory**.


[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[troubleshoot]: ../data-factory-troubleshoot
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[azure-preview-portal]: http://portal.azure.com/

[image-data-factory-browse-everything]: ./media/data-factory-monitor-manage-using-management-portal/BrowseEverything.png

[image-data-factory-browse-datafactories]: ./media/data-factory-monitor-manage-using-management-portal/BrowseDataFactories.png

[image-data-factory-datafactories-blade]: ./media/data-factory-monitor-manage-using-management-portal/DataFactories.png

[image-data-factory-datafactory-from-startboard]: ./media/data-factory-monitor-manage-using-management-portal/DataFactoryFromStartboard.png

[image-data-factory-datafactory-home-page]: ./media/data-factory-monitor-manage-using-management-portal/DataFactoryHomePage.png

[image-data-factory-diagram-view]: ./media/data-factory-monitor-manage-using-management-portal/DiagramView.png

[image-data-factory-linked-services]: ./media/data-factory-monitor-manage-using-management-portal/LinkedServicesBlade.png

[image-data-factory-linked-service]: ./media/data-factory-monitor-manage-using-management-portal/LinkedServiceBlade.png

[image-data-factory-datasets]: ./media/data-factory-monitor-manage-using-management-portal/Datasets.png

[image-data-factory-table]: ./media/data-factory-monitor-manage-using-management-portal/Table.png

[image-data-factory-all-slices]: ./media/data-factory-monitor-manage-using-management-portal/AllSlices.png

[image-data-factory-filter]: ./media/data-factory-monitor-manage-using-management-portal/Filter.png

[image-data-factory-dataslice]: ./media/data-factory-monitor-manage-using-management-portal/DataSlice.png

[image-data-factory-activity-run-details]: ./media/data-factory-monitor-manage-using-management-portal/ActivityRunDetails.png

[image-data-factory-events]: ./media/data-factory-monitor-manage-using-management-portal/Events.png
