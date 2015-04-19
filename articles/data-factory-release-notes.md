<properties title="Azure Data Factory - Release Notes" pageTitle="Data Factory - Notas de Versão | Azure" description="Notas da versão do Data Factory" metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/04/2014" ms.author="spelluru" />

# Notas de versão do Data Factory do Azure

## Notas de versão 11/12/2014 do Data Factory # #

### Novos aprimoramentos

- Integração de Aprendizado de Máquina do Azure
	- Esta versão de serviço do Data Factory do Azure permite que você integre o Data Factory do Azure com o Aprendizado de Máquina do Azure (AM) usando **AzureMLLinkedService** e **AzureMLBatchScoringActivity**. Consulte [Criar pipelines de previsão usando o Data Factory e Aprendizado de Máquina do Azure][adf-azure-ml] para obter detalhes. 
- É fornecido o status da versão do gateway
	- O status de "NewVersionAvailable" será mostrado no Portal de Visualização do Azure e na saída do cmdlet Get-AzureDataFactoryGateway, se houver uma versão mais recente do gateway disponível do que a que está instalada no momento. Você pode seguir o trajeto do portal para baixar o novo arquivo de instalação (. msi) e executá-lo para instalar o gateway mais recente. Não é necessária nenhuma configuração adicional.

### Alterações

- O JobsContainer em HdInsightOnDemandLinkedService é removido.
	- Na definição de JSON para um HDInsightOnDemandLinkedService, você não precisa mais especificar a propriedade **jobsContainer**. Se você tiver a propriedade especificada para um serviço vinculado sob demanda, a propriedade será ignorada. Você pode remover a propriedade da definição do JSON para o serviço vinculado e atualizar a definição de serviço vinculada usando o cmdlet New-AzureDataFactoryLinkedService.
- Parâmetros de configuração opcional para HDInsightOnDemandLinkedService
	- Esta versão apresenta suporte para alguns parâmetros de configuração opcional para HDInsightOnDemandLinked (cluster de HDInsight sob demanda). Consulte [ClusterCreateParameters propriedades][on-demand-hdi-parameters] para obter detalhes.
- O gateway local foi removido
	- Ao criar um gateway do Data Factory do Azure por meio do portal ou do PowerShell (New-AzureDataFactoryGateway), você não precisa mais especificar o local para o gateway. A região do Data Factory será herdada. Da mesma forma, para configurar um serviço vinculado do SQL Server usando JSON, a propriedade "gatewayLocation" não é mais necessária. O SDK do .NET do Data Factory também é atualizado para refletir essas alterações.
	- Se você usar uma versão anterior do SDK e do PowerShell do Azure, ainda será necessário que você forneça a configuração local.
 
     

#### Alterações de última hora
	
- CustomActivity para DotNetActivity
	- **ICustomActivity** interface é renomeada para **IDotNetActivity**. Você precisará atualizar os pacotes do NuGet do Data Factory e alterar o ICustomActivity para IDotNetActivity no código-fonte para a atividade personalizada.  
	- O tipo de atividade personalizada na definição de JSON para atividade personalizada deve ser alterado de **CustomActivity** para **DotNetActivity**. 
	- As classes **CustomActivity** e **CustomActivityProperties** foram renomeadas para **DotNetActivity** e **DotNetActivityProperties** com o mesmo conjunto de propriedades.

		Se você usar a versão mais antiga do SDK e do PowerShell do Azure, você pode continuar usando o CustomActivity em vez de DotNetActivity.
    
  		Consulte [Usar atividades personalizadas em uma pipeline do Data Factory do Azure][adf-custom-activities] para obter uma explicação passo a passo sobre como criar uma atividade personalizada e usá-la em uma pipeline do Data Factory do Azure.  

[adf-azure-ml]: ../data-factory-create-predictive-pipelines
[adf-custom-activities]: ../data-factory-use-custom-activities
[on-demand-hdi-parameters]: http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clustercreateparameters_properties.aspx



<!--HONumber=35.2-->
