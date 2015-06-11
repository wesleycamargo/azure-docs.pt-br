<properties 
	pageTitle="Data Factory - Notas de Versão | Azure" 
	description="Notas de versão de fábrica de dados" 
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
	ms.date="04/10/2015" 
	ms.author="spelluru"/>

# Notas de versão do Data Factory do Azure

## Notas de versão 10/04/2015 da fábrica de dados
Você verá o **atualizado recentemente fatias** e **recentemente falha fatias** listas no **tabela** lâmina agora. Essas listas são classificadas por hora de atualização da fatia. O tempo de atualização de uma fatia é alterado nas seguintes situações.

-  Você atualiza o status da fatia manualmente, por exemplo, usando o **conjunto AzureDataFactorySliceStatus** (ou) clicando em **executar** no **FATIA** blade da fatia.
-  A fatia é alterado devido a uma execução (por exemplo, uma execução de Introdução, uma execução finalizada e falha, uma execução terminou e foi bem-sucedida, etc).

Clique no título da lista ou **... (reticências)** Para ver a lista de intervalos de maior. Clique em **filtro** na barra de ferramentas para filtrar as fatias.
 
Você ainda poderá exibir fatias classificadas por vezes a fatia clicando **fatias de dados (por hora fatia)** lado a lado. Fatias nessas coleções são ordenadas pelo tempo da fatia. Por exemplo, se for um agendamento por hora, as fatias seria: - 4/4/2015 às 17h em andamento - 4 de 4/4/2015 pm Succeeded - 3 de 4/4/2015 pm falhou

Mas, se uma fatia mais antiga for executada novamente, ele não aparecerão na parte superior desta lista, mesmo que é provavelmente o que o usuário esteja mais interessado no.

## Notas de versão de 31/3/2015 da fábrica de dados
- Atualizado **Data Management Gateway** pacote de instalação foi lançado em [Microsoft Download Center][adf-gateway-download].
- Copiando de **local no sistema de arquivos para BLOBs do Azure** agora há suporte. Consulte os seguintes tópicos para obter mais informações.
	-  [Sistema de arquivos vinculados serviço local](https://msdn.microsoft.com/library/dn930836.aspx)
	-  [Propriedades de OnPremisesFileSystemLocation em uma tabela de JSON](https://msdn.microsoft.com/library/dn894089.aspx#OnPremFileSystem)
	-  [Suporte para origens e coletores](https://msdn.microsoft.com/library/dn894007.aspx). Consulte a matriz de cópia atualizada e **FileSystemSource** propriedades. 
-  Copiando de **de dados Oracle para BLOBs do Azure local** agora há suporte. Consulte os seguintes tópicos para obter mais informações. 
	-  [Oracle local vinculado de serviço](https://msdn.microsoft.com/library/dn948537.aspx)
	-  [Propriedades de OnPremisesOracleTableLocation em uma tabela de JSON](https://msdn.microsoft.com/library/dn894089.aspx#Oracle) 
	-  [Suporte para origens e coletores](https://msdn.microsoft.com/library/dn894007.aspx). Consulte a matriz de cópia atualizada e **OracleSource** propriedades.
-  Você pode especificar a codificação para arquivos de texto em um Blob do Azure. Consulte o novo [propriedade encodingName](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob). 
- Você pode chamar um procedimento armazenado com parâmetros adicionais ao copiar em um coletor de SQL. Consulte [chamar o procedimento armazenado para coletor SQL][adf-copy-advanced] para obter detalhes.   

Consulte o blob de postagem: [atualização de fábrica de dados do Azure - novos armazenamentos de dados](http://azure.microsoft.com/blog/2015/03/30/azure-data-factory-update-new-data-stores/) para obter mais informações, incluindo exemplos.

## Notas de versão de 27/2/2015 da fábrica de dados

### Novos aprimoramentos
- **Editor de fábrica de dados do azure**. Editor de fábrica de dados, que faz parte do Portal de visualização do Azure, permite criar, editar e implantar arquivos JSON que definem os pipelines, conjuntos de dados e serviços vinculados. O principal objetivo do editor é fornecer uma rápido e leve-interface do usuário (UI) para criar os artefatos de fábrica de dados do Azure sem exigir que você instale o PowerShell do Azure e informe o logon usando cmdlets do PowerShell. Consulte o [Azure dados fábrica de Editor de-A luz peso Web][adf-editor-blog] postagem do blog para uma visão geral e um vídeo no Editor de fábrica de dados. Para visão geral detalhada do editor, consulte o [dados fábrica de Editor][adf-editor] artigo.          

### Alterações

## Notas de versão de 26/1/2015 da fábrica de dados ##

### Alterações
- Atualizado **Data Management Gateway** pacote de instalação foi lançado em [Microsoft Download Center][adf-gateway-download]. A partir desta versão, você pode encontrar o Gateway de gerenciamento de dados mais recentes para usar com o Azure Data Factory nesse local de download. Esse pacote de instalação serve a fábrica de dados do Azure e Power BI para serviços do Office 365. Se você estiver usando o serviços, observe que gateways para dados de fábrica e Power BI devem ser instalados em computadores diferentes e configurados de forma diferente de acordo com a orientação da documentação do alocador de dados ou Power BI.
- O **cópia atividade** agora oferece suporte à cópia de dados entre o banco de dados do SQL Server local e um banco de dados do SQL Azure. Consulte [cópia atividade][adf-copy-activity] para obter detalhes e [GitHub][adf-github-samples] para obter exemplos JSON.
- **SqlSink** oferece suporte a uma nova propriedade: **WriteBatchTimeout**. Essa propriedade oferece a flexibilidade para configurar quanto tempo de espera para a operação de inserção em lotes seja concluída antes da operação expira. Para obter uma cópia híbrido (operação de cópia que envolve uma fonte de dados local e uma fonte de dados na nuvem), você deve ter o gateway da versão 1.4 ou superior para usar esta propriedade. 
- **SQL Server vinculado serviço** agora oferece suporte a **autenticação do Windows**. 
	- Quando criar um SQL Server vinculado usando o portal de serviço, você agora pode optar por usar a autenticação do Windows e defina as credenciais apropriadas. Isso requer que você tenha o gateway de versão 1.4 ou superior. 
	- Quando criar um SQL Server vinculado serviço usando o PowerShell do Azure, você pode especificar informações de conexão em texto sem formatação ou criptografar as informações de conexão usando atualizada [cmdlet New-AzureDataFactoryEncryptValue][adf-encrypt-value-cmdlet] e, em seguida, use a cadeia de caracteres criptografada para a propriedade de cadeia de conexão na carga JSON service vinculado. Consulte [Serviços vinculados][adf-msdn-linked-services] para obter detalhes sobre como definir um serviço vinculado em JSON. O recurso de criptografia não é suportado pelo cmdlet New-AzureDataFactoryEncryptValue ainda. 

## Notas de versão 11/12/2014 da fábrica de dados ##

### Novos aprimoramentos

- Integração de Aprendizado de Máquina do Azure
	- Esta versão do service Factory de dados do Azure permite que você integre o alocador de dados do Azure com o aprendizado de máquina do Azure (ML) usando **AzureMLLinkedService** e **AzureMLBatchScoringActivity**. Consulte [criar pipelines de previsão usando dados de fábrica e aprendizado de máquina do Azure][adf-azure-ml] para obter detalhes. 
- É fornecido o status da versão do gateway
	- O status de "NewVersionAvailable" será mostrado no Portal de Visualização do Azure e na saída do cmdlet Get-AzureDataFactoryGateway, se houver uma versão mais recente do gateway disponível do que a que está instalada no momento. Você pode seguir o trajeto do portal para baixar o novo arquivo de instalação (. msi) e executá-lo para instalar o gateway mais recente. Não é necessária nenhuma configuração adicional.

### Alterações

- O JobsContainer em HdInsightOnDemandLinkedService é removido.
	- Na definição de JSON para um HDInsightOnDemandLinkedService, você não precisa especificar **jobsContainer** propriedade mais. Se você tiver a propriedade especificada para um serviço vinculado sob demanda, a propriedade será ignorada. Você pode remover a propriedade da definição do JSON para o serviço vinculado e atualizar a definição de serviço vinculada usando o cmdlet New-AzureDataFactoryLinkedService.
- Parâmetros de configuração opcional para HDInsightOnDemandLinkedService
	- Esta versão apresenta suporte para alguns parâmetros de configuração opcional para HDInsightOnDemandLinked (cluster de HDInsight sob demanda). Consulte [ClusterCreateParameters propriedades][on-demand-hdi-parameters] para obter detalhes.
- O gateway local foi removido
	- Ao criar um gateway do Data Factory do Azure por meio do portal ou do PowerShell (New-AzureDataFactoryGateway), você não precisa mais especificar o local para o gateway. A região do Data Factory será herdada. Da mesma forma, para configurar um serviço vinculado do SQL Server usando JSON, a propriedade "gatewayLocation" não é mais necessária. O SDK do .NET do Data Factory também é atualizado para refletir essas alterações.
	- Se você usar uma versão anterior do SDK e do PowerShell do Azure, ainda será necessário que você forneça a configuração local.
 
     

#### Alterações de última hora
	
- CustomActivity para DotNetActivity
	- **ICustomActivity** interface é renomeado para **IDotNetActivity**. Você precisará atualizar os pacotes do NuGet do Data Factory e alterar o ICustomActivity para IDotNetActivity no código-fonte para a atividade personalizada.  
	- O tipo de atividade personalizada na definição de atividade personalizada JSON deve ser alterado de **CustomActivity** para **DotNetActivity**. 
	- O **CustomActivity** e **CustomActivityProperties** classes foram renomeadas para **DotNetActivity** e **DotNetActivityProperties** com o mesmo conjunto de propriedades.

		Se você usar a versão mais antiga do SDK e do PowerShell do Azure, você pode continuar usando o CustomActivity em vez de DotNetActivity.
    
  		Consulte [usar atividades personalizadas em um pipeline do Azure Data Factory][adf-custom-activities] para obter uma explicação sobre como criar uma atividade personalizada e usá-lo em um pipeline de fábrica de dados do Azure.

[adf-azure-ml]: data-factory-create-predictive-pipelines.md
[adf-custom-activities]: data-factory-use-custom-activities.md
[adf-copy-activity]: data-factory-copy-activity.md
[adf-editor]: data-factory-editor.md
[adf-copy-advanced]: data-factory-copy-activity-advanced.md

[adf-editor-video]: http://channel9.msdn.com/Blogs/Windows-Azure/New-Azure-Data-Factory-Editor-UI
[adf-editor-blog]: http://azure.microsoft.com/blog/2015/03/02/azure-data-factory-editor-a-light-weight-web-editor/
[on-demand-hdi-parameters]: http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clustercreateparameters_properties.aspx
[adf-gateway-download]: http://www.microsoft.com/download/details.aspx?id=39717
[adf-github-samples]: https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON
[adf-msdn-linked-services]: https://msdn.microsoft.com/library/dn834986.aspx
[adf-encrypt-value-cmdlet]: https://msdn.microsoft.com/library/dn834940.aspx

<!---HONumber=GIT-SubDir--> 