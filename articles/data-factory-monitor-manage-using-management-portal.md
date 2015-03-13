<properties 
	pageTitle="Monitorar e gerenciar o Azure Data Factory utilizando o Portal de visualização do Azure" 
	description="Saiba como usar o portal de gerenciamento do Azure para monitorar e gerenciar as fábricas de dados do Azure que você criou." 
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
	ms.date="2/10/2015" 
	ms.author="spelluru"/>

# Monitorar o Data Factory do Azure utilizando o Portal de visualização do Azure

- [Exibir todas os data factories em uma assinatura do Azure](#AllDataFactories)
- [Exibir detalhes sobre um data factory](#DataFactoryDetails)
- [Modo de exibição de diagrama de um data factory](#DataFactoryDiagram)
- [Exibir serviços vinculados a um data factory](#DataFactoryLinkedServices)
- [Exibir detalhes sobre um serviço vinculado](#DataFactoryLinkedService) 
- [Exibir conjuntos de dados em um data factory](#DataFactoryDatasets)
- [Exibir detalhes sobre um conjunto de dados](#DataFactoryDataset)
- [Exibir detalhes sobre uma fatia](#DataFactorySlice) 
- [Exibir todas as execuções de atividade de uma fatia](#DataFactoryActivtyRuns) 
- [Exibir detalhes sobre uma execução de atividade](#DataFactoryActivtyRunDetails)
- [Lentes de operação - Eventos da semana anterior](#EventsInThePastweek)  
   


## <a name="AllDataFactories"></a> Exibir todas os data factories em uma assinatura do Azure

- Entre no [Portal de Visualização do Azure][azure-preview-portal].
- Clique no hub**PROCURAR** à esquerda e clique em **Data factories**.  

	![BROWSE hub -> Data Factories][image-data-factory-browse-datafactories]

	Caso não visualize os **Data factories**, clique em **Tudo** e, em seguida, clique em **Data factories** na folha **Procurar**.

	![BROWSE hub -> Everything] [image-data-factory-browse-everything]

- Você deve ver todas os data factories na folha **Data factories**.

	![Data factories blade][image-data-factory-datafactories-blade]

    
## <a name="DataFactoryDetails"></a> Exibir detalhes sobre uma data factory

Para exibir detalhes sobre uma data factory, siga um destes procedimentos: 


- Clique em um data factory na folha **Data factories** mostrada acima.
- Clique no link do data factory no **Quadro inicial**. **O Quadro inicial** é a folha exibida ao realizar o logon no Portal de Visualização do Azure. Se você tiver selecionado **Adicionar ao Quadro Inicial** durante a criação de um data factory (opção padrão), é necessário ver o data factory vinculado no quadro inicial, conforme mostrado na imagem a seguir. Neste exemplo, os links de data factory **ADFTutorialDataFactory**, **ADFTutorialDataFactoryDF** e **LogProcessingFactory** estão disponíveis no **Quadro inicial**.


![Data factory from the Startboard][image-data-factory-datafactory-from-startboard]

De qualquer forma, você verá a folha **DATA FACTORY** do data factory selecionado, conforme mostrado na imagem a seguir. 

 ![Data Factory Home Page][image-data-factory-datafactory-home-page]
 
## <a name="DataFactoryDiagram"></a> Modo de exibição de diagrama da data factory
Na folha **DATA FACTORY** do data factory, clique no bloco de **Diagrama** para visualizar a exibição do diagrama do data factory. 

![Data Factory Diagram View][image-data-factory-diagram-view]
 

## <a name="DataFactoryLinkedServices"></a> Exibir serviços vinculados a um data factory
Na folha **DATA FACTORY** do data factory, clique no bloco de **Serviços vinculados** para visualizar todos os serviços vinculados em uma lista. 

![Linked Services Blade][image-data-factory-linked-services]

## <a name="DataFactoryLinkedService"></a> Exibir detalhes sobre um serviço vinculado
Na folha **SERVIÇOS VINCULADOS**, clique no serviço vinculado na lista para visualizar detalhes sobre ele. 

![Linked Service Blade][image-data-factory-linked-service]

## <a name="DataFactoryDatasets"></a> Exibir conjuntos de dados em uma data factory 
Na folha **DATA FACTORY** da data factory, clique no bloco de **Conjunto de dados** para visualizar todas as tabelas no data factory.

![Data Sets Blade][image-data-factory-datasets] 

## <a name="DataFactoryDataset"></a>  Exibir detalhes sobre um conjunto de dados
Clique na lista de conjuntos de dados na folha CONJUNTOS DE DADOS para visualizar os detalhes sobre o conjunto de dados. Observe que uma tabela é um conjunto de dados retangular que tem um esquema. É o único tipo de conjunto de dados com suporte neste momento. 

![Table Blade][image-data-factory-table]

Na folha **TABELA** acima, é possível visualizar as **Fatias recentes**, bem como as **Fatias do problema**. Clique em **... (Reticências)** para visualizar todas as fatias. 

![All Slices of a Table][image-data-factory-all-slices]

Sobre a folha**Fatias de dados**, clique no botão de Filtro para ver a folha de Filtro que permite que você **filtre** as fatias para ver as fatias específicas que você deseja analisar.

![Filter Blade][image-data-factory-filter-blade]


Ao iniciara folha **Filtro**, o campo **Até** será definido automaticamente com a hora mais recente (arredondada) para limitar o número de registros retornados. O campo **A partir de** é também automaticamente configurado. Você pode alterar a data **A partir de** clicando no botão **calendário**. A data **Até** será alterada automaticamente quando você altera a data **A partir de**. 

Você pode clicar nos botões **Anterior**/**Próximo** para exibir fatias no período anterior/próximo período. O intervalo de tempo para os botões **Anterior** e **Próximo** é definido com base na frequência da fatia e do intervalo conforme mostrado na tabela a seguir.

Frequência | Valor do intervalo | Parte de hora resultante
----------| -------------------- | --------------------
Minutos | 1-4 | 6 horas
Minutos | 5-29 | 1 dia
Minutos | 30-180 | 7 dias
Minutos | 180 + | 28 dias (aproximado. mês)
Hora | 1-3 | 7 dias
Hora | 4-11 | 28 dias (aproximado. mês)
Hora | 12 a 72 | 182 dias (aproximados. 6 meses)
Hora | 73 + | 1 ano
Dia | 1 a 6 | 1 ano
Dia | 7 a 20 | 5 ano
Dia | 21 + | 10 anos
Semana | 1-3 | 5 anos
Semana | 4 + | 10 anos
Mês | qualquer | 10 anos
 
Por exemplo, se você definir **frequência** em **Horas** e **intervalo** de **2**, clicando nos botões **Próximo**/**Anterior** muda o intervalo de tempo em **7 dias** em qualquer direção. Essa lógica se aplica a folha de filtro se você estiver exibindo todas as fatias fatias/problema fatias/recentes.

A folha **Filtro** permite filtrar as fatias com base nos seus **status**. A tabela a seguir descreve todos os status de fatia e suas descrições.
 
Status de fatias | Descrição
------------ | ------------
PendingExecution | Processamento de dados ainda não foi iniciado.
InProgress | Processamento de dados está em andamento.
Ready | O processamento de dados foi concluído e a fatia de dados está pronta.
Failed | Falha na execução que produz a fatia.
Skip | Ignorar o processamento da fatia.
Retry | Repetir a execução que produz a fatia.
Timed Out | O processamento de dados da fatia expirou.
PendingValidation |A fatia de dados está aguardando a validação em relação às políticas de validação antes de serem processadas.
RetryValidation | Repetir a validação da fatia.
FailedValidation | Falha na validação da fatia.
LongRetry | Uma fatia terá esse status se o LongRetry for especificado na tabela JSON e as repetições regulares da fatia falharem.
ValidationInProgress | A validação da fatia (com base em diretrizes definidas na tabela JSON) está sendo executada.



## <a name="DataFactorySlice"></a> Exibir detalhes sobre uma fatia
Clique em uma fatia na lista de fatias da folha **TABELA** ou da folha **Fatias de Dados** para visualizar detalhes sobre essa fatia. 

![Data Slice][image-data-factory-dataslice]


### <a name="DataFactoryActivtyRuns"></a> Exibir todas as execuções de atividade de uma fatia
Para uma fatia, pode haver mais de uma execução. Por exemplo, quando uma fatia falha, o serviço pode tentar novamente por algumas vezes. Também é possível executar novamente uma fatia que falhou em todas as tentativas. É possível visualizar toda as execuções de atividade na folha** Fatia de Dados** na lista na parte inferior. 

## <a name="DataFactoryActivtyRunDetails"></a>  Exibir detalhes sobre uma execução de atividade
Clique em uma atividade em execução na lista de execuções na folha **Fatia de Dados** para visualizar os detalhes sobre a execução da atividade. 

![Activity Run Details][image-data-factory-activity-run-details]

## <a name="EventsInThePastweek"></a> Lentes de operação - eventos da semana anterior
Na folha **DATA FACTORY** (ou página inicial) do data factory, clique em **Eventos na semana anterior** na lente de **Operações** para visualizar os eventos da semana anterior. Isso ajuda a ter uma visão geral das operações realizadas pelo data factory na última semana. Também ajuda a solucionar quaisquer erros com o processamento/movimentação de dados. 

![ Data Factory Events][image-data-factory-events]


## Consulte também

Artigo | Descrição
------ | ---------------
[Monitorar e gerenciar o Data Factory do Azure usando o PowerShell][monitor-manage-using-powershell] | Este artigo descreve como monitorar um Data Factory do Azure usando cmdlets do PowerShell do Azure.
[Habilitar seus pipelines para trabalhar com dados locais][use-onpremises-datasources] | Este artigo tem um passo a passo que mostra como copiar dados de um banco de dados SQL Server local em um blob do Azure.
[Usar o Pig e o Hive com o Data Factory][use-pig-and-hive-with-data-factory] | Este artigo tem um passo a passo que mostra como usar a Atividade de HDInsight para executar um script do hive/pig para processar dados de entrada a fim de gerar dados de saída.
[Tutorial: Mover e processar arquivos de log usando a Data Factory][adf-tutorial] | Este artigo fornece um passo a passo que mostra como implementar um cenário próximo do real usando a Data Factory do Azure para transformar dados de arquivos de log em informações.
[Usar atividades personalizadas em uma Data Factory][use-custom-activities] | Este artigo fornece um passo a passo com instruções para criar uma atividade personalizada e usá-la em uma pipeline.
[Solucionar problemas de Data Factory][troubleshoot]| Este artigo descreve como solucionar problemas do Data Factory do Azure.
[Referência do Desenvolvedor da Data Factory do Azure][developer-reference]A Referência do Desenvolvedor tem um conteúdo de referência abrangente de cmdlets, script JSON, funções, etc... 
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

[image-data-factory-filter-blade]: ./media/data-factory-monitor-manage-using-management-portal/FilterBlade.png

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

<!--HONumber=35.2-->

<!--HONumber=46--> 
