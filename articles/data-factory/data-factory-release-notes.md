<properties 
	pageTitle="Data Factory - Notas de Versão | Azure" 
	description="Notas de versão da Data Factory" 
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

## Notas da versão de 10/04/2015 da Data Factory
Você verá as listas **Fatias atualizadas recentemente** e **Fatias com falha recente** na folha **TABELA** agora. Essas listas são classificadas segundo o horário de atualização da fatia. O horário de atualização de uma fatia é alterado nas situações a seguir.

-  Você atualiza o status da fatia manualmente, por exemplo, usando o **Set-AzureDataFactorySliceStatus** (ou) clicando em **EXECUTAR** na folha **FATIA** da fatia.
-  A fatia é alterada devido a uma execução (por exemplo, uma execução iniciada, uma execução finalizada e com falha, uma execução finalizada e bem-sucedida, etc).

Clique no título das listas ou em **... (reticências)** para ver a lista maior de fatias. Clique em **Filtrar** na barra de ferramentas para filtrar as fatias.
 
Você ainda poderá exibir fatias classificadas segundo os horários da fatia clicando no bloco **Fatias de dados (por horário da fatia)**. As fatias nessas coleções são ordenadas segundo o horário da fatia. Por exemplo, se for um agendamento por hora, as fatias serão: - 4/4/2015 às 17h em andamento - 4/4/2015 às 16h Bem-sucedida - 4/4/2015 15h Falhou

Porém, se uma fatia mais antiga for executada novamente, ela não aparecerá na parte superior desta lista mesmo que provavelmente seja nisso que o usuário esteja mais interessado.

## Notas da versão de 31/03/2015 da Data Factory
- Atualizado **Gateway de gerenciamento de dados**, pacote de instalação foi lançado no [Centro de Download da Microsoft][adf-gateway-download].
- Agora há suporte para cópia por meio do **sistema de arquivos local para blob do Azure**. Para obter mais informações, consulte os tópicos a seguir.
	-  [Serviço vinculado de sistema de arquivos local](https://msdn.microsoft.com/library/dn930836.aspx)
	-  [Propriedades de OnPremisesFileSystemLocation em uma tabela de JSON](https://msdn.microsoft.com/library/dn894089.aspx#OnPremFileSystem)
	-  [Origens e coletores com suporte](https://msdn.microsoft.com/library/dn894007.aspx). Consulte a matriz de cópia atualizada e as propriedades **FileSystemSource**. 
-  Agora há suporte para cópia por meio do **Banco de dados Oracle local para blob do Azure**. Para obter mais informações, consulte os tópicos a seguir. 
	-  [Serviço vinculado Oracle local](https://msdn.microsoft.com/library/dn948537.aspx)
	-  [Propriedades de OnPremisesOracleTableLocation em uma tabela de JSON](https://msdn.microsoft.com/library/dn894089.aspx#Oracle) 
	-  [Origens e coletores com suporte](https://msdn.microsoft.com/library/dn894007.aspx). Consulte a matriz de cópia atualizada e as propriedades **OracleSource**.
-  Você pode especificar a codificação para arquivos de texto em um Blob do Azure. Consulte a nova [propriedade encodingName](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob). 
- Você pode invocar um procedimento armazenado com parâmetros adicionais ao copiar em um coletor de SQL. Consulte [Invocar o procedimento armazenado para o coletor SQL][adf-copy-advanced] para mais detalhes.   

Consulte a postagem de blog: [Atualização da Azure Data Factory - novos armazenamentos de dados](http://azure.microsoft.com/blog/2015/03/30/azure-data-factory-update-new-data-stores/) para obter mais informações, incluindo exemplos.

## Notas da versão de 27/02/2015 da Data Factory

### Novos aprimoramentos
- **Editor da Azure Data Factory**. O Editor Data Factory, que faz parte do Portal de visualização do Azure, permite criar, editar e implantar arquivos JSON que definem os pipelines, conjuntos de dados e serviços vinculados. O principal objetivo do editor é fornecer uma IU (interface do usuário) rápida e leve para criar artefatos da Azure Data Factory sem exigir que você instale o PowerShell do Azure, podendo usar mais prontamente os cmdlets do PowerShell. Consulte a postagem do blog [Editor da Azure Data Factory - um editor Web leve][adf-editor-blog] para uma visão geral e um vídeo sobre o Editor do Data Factory. Para uma visão geral detalhada do editor, consulte o artigo [Editor Data Factory][adf-editor].          

### Alterações

## Notas da versão de 26/01/2015 da Data Factory ##

### Alterações
- Atualizado **Gateway de gerenciamento de dados**, pacote de instalação foi lançado no [Centro de Download da Microsoft][adf-gateway-download]. A partir desta versão, você pode encontrar o Gateway de gerenciamento de dados mais recente para usar com a Azure Data Factory nesse local de download. Esse pacote de instalação atende à Azure Data Factory e ao Power BI para serviços do Office 365. Se você estiver usando o serviços, observe que gateways para Data Factory e Power BI devem ser instalados em computadores diferentes e configurados diferentemente, de acordo com a orientação da documentação da Data Factory ou Power BI.
- A **Atividade de Cópia** agora dá suporte para copiar dados entre o banco de dados do SQL Server local e um banco de dados SQL do Azure. Consulte [Atividade de Cópia][adf-copy-activity] para obter detalhes e [GitHub][adf-github-samples] para obter exemplos JSON.
- **SqlSink** dá suporte a uma nova propriedade: **WriteBatchTimeout**. Essa propriedade oferece a flexibilidade para configurar quanto tempo esperar para que a operação de inserção em lotes seja concluída antes da operação expirar. Para obter uma cópia híbrida (operação de cópia que envolve uma fonte de dados local e uma fonte de dados na nuvem), você deve ter o gateway da versão 1.4 ou superior para usar esta propriedade. 
- O **serviço vinculado do SQL Server** agora dá suporte a **autenticação do Windows**. 
	- Ao criar um serviço vinculado do SQL Server usando o portal, você agora pode optar por usar a autenticação do Windows e definir as credenciais apropriadas. Isso requer que você tenha o gateway de versão 1.4 ou superior. 
	- Ao criar um serviço vinculado do SQL Server usando o PowerShell do Azure, você pode especificar informações de conexão em texto sem formatação ou criptografar as informações de conexão usando um [cmdlet New-AzureDataFactoryEncryptValue][adf-encrypt-value-cmdlet] atualizado e, em seguida, usar a cadeia de caracteres criptografada para a propriedade Cadeia de Conexão no serviço vinculado JSON. Consulte [Serviços vinculados][adf-msdn-linked-services] para obter detalhes sobre como definir um serviço vinculado em JSON. O recurso de criptografia ainda não tem suporte pelo cmdlet New-AzureDataFactoryEncryptValue. 

## Notas da versão de 12/11/2014 da Data Factory ##

### Novos aprimoramentos

- Integração de Aprendizado de Máquina do Azure
	- Esta versão do serviço Azure Data Factory permite que você integre a Azure Data Factory com o AM (Aprendizado de Máquina) do Azure usando **AzureMLLinkedService** e **AzureMLBatchScoringActivity**. Consulte [Criar pipelines de previsão usando a Data Factory e o Aprendizado de Máquina do Azure][adf-azure-ml] para obter detalhes. 
- É fornecido o status da versão do gateway
	- O status de "NewVersionAvailable" será mostrado no Portal de Visualização do Azure e na saída do cmdlet Get-AzureDataFactoryGateway, se houver uma versão mais recente do gateway disponível do que a que está instalada no momento. Você pode seguir o trajeto do portal para baixar o novo arquivo de instalação (. msi) e executá-lo para instalar o gateway mais recente. Não é necessária nenhuma configuração adicional.

### Alterações

- O JobsContainer em HdInsightOnDemandLinkedService é removido.
	- Na definição de JSON para um HDInsightOnDemandLinkedService, você não precisa mais especificar a propriedade **jobsContainer**. Se você tiver a propriedade especificada para um serviço vinculado sob demanda, a propriedade será ignorada. Você pode remover a propriedade da definição do JSON para o serviço vinculado e atualizar a definição de serviço vinculada usando o cmdlet New-AzureDataFactoryLinkedService.
- Parâmetros de configuração opcional para HDInsightOnDemandLinkedService
	- Esta versão apresenta suporte para alguns parâmetros de configuração opcional para HDInsightOnDemandLinked (cluster de HDInsight sob demanda). Consulte [Propriedades ClusterCreateParameters][on-demand-hdi-parameters] para obter detalhes.
- O gateway local foi removido
	- Ao criar um gateway do Data Factory do Azure por meio do portal ou do PowerShell (New-AzureDataFactoryGateway), você não precisa mais especificar o local para o gateway. A região do Data Factory será herdada. Da mesma forma, para configurar um serviço vinculado do SQL Server usando JSON, a propriedade "gatewayLocation" não é mais necessária. O SDK do .NET do Data Factory também é atualizado para refletir essas alterações.
	- Se você usar uma versão anterior do SDK e do PowerShell do Azure, ainda será necessário que você forneça a configuração local.
 
     

#### Alterações de última hora
	
- CustomActivity para DotNetActivity
	- A interface **ICustomActivity** é renomeada para **IDotNetActivity**. Você precisará atualizar os pacotes do NuGet do Data Factory e alterar o ICustomActivity para IDotNetActivity no código-fonte para a atividade personalizada.  
	- O tipo de atividade personalizada na definição de JSON para atividade personalizada deve ser alterado de **CustomActivity** para **DotNetActivity**. 
	- As classes **CustomActivity** e **CustomActivityProperties** foram renomeadas para **DotNetActivity** e **DotNetActivityProperties** com o mesmo conjunto de propriedades.

		Se você usar a versão mais antiga do SDK e do PowerShell do Azure, você pode continuar usando o CustomActivity em vez de DotNetActivity.
    
  		Consulte [Usar atividades personalizadas em um pipeline da Azure Data Factory][adf-custom-activities] para obter uma explicação passo a passo sobre como criar uma atividade personalizada e usá-la em um pipeline da Azure Data Factory.

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



 

<!---HONumber=62-->