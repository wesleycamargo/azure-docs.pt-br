c<properties urlDisplayName="Debug HDInsight Hadoop Errors" pageTitle="Depurar Hadoop no HDInsight: Mensagens de erro | Azure" metaKeywords="hdinsight, hdinsight service, hdinsight azure, debug, error messages, errors" description="Learn about the error messages you might receive when administering HDInsight using PowerShell, and steps you can take to recover." services="hdinsight" title="Debug Hadoop in HDInsight: Error messages" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/10/2014" ms.author="bradsev" />

# Depurar Hadoop no HDInsight: Mensagens de erro

##Introdução
As mensagens de erro detalhadas neste tópico são fornecidas para ajudar os usuários do Hadoop no Azure HDInsight a entenderem possíveis condições de erro que podem encontrar ao administrar o serviço usando o PowerShell do Azure e para avisá-los sobre as etapas que podem ser executadas para se recuperar do erro. 

Algumas dessas mensagens de erro também podem ser vistas no portal do Azure ao gerenciar clusters HDInsight. Mas outras mensagens de erro que você pode encontrar são menos granulares devido às restrições nas ações corretivas possíveis neste contexto. Outras mensagens de erro são fornecidas nos contextos onde a atenuação é óbvia. Por exemplo, se as restrições nos parâmetros forem violadas, a mensagem aparecerá no lado direito da caixa onde o valor foi inserido. Este é um caso onde vários nós de dados foram solicitados. A solução é reduzir o número para um valor permitido que seja 22 ou inferior.

![HDI.Debugging.ErrorMessages.Portal][image-hdi-debugging-error-messages-portal]

Os erros que um usuário pode encontrar no PowerShell do Azure ou no Portal do Azure são listados em ordem alfabética por nome na seção [Erros do HDInsight,](#hdinsight-error-messages) na qual eles estão vinculados a uma entrada na seção [Descrição e redução de erros,](#discription-mitigation-errors) que fornece as seguintes informações do erro:
 	
- **Descrição**: a mensagem de erro que os usuários veem	
- **Redução**: quais etapas podem ser tomadas para recuperação do erro. 

###Erros do HDInsight

[AtleastOneSqlMetastoreMustBeProvided](#AtleastOneSqlMetastoreMustBeProvided)
[AzureRegionNotSupported](#AzureRegionNotSupported)		
[ClusterContainerRecordNotFound](#ClusterContainerRecordNotFound)	 
[ClusterDnsNameInvalidReservedWord](#ClusterDnsNameInvalidReservedWord)		
[ClusterNameUnavailable](#ClusterNameUnavailable)	
[ClusterUserNameInvalid](#ClusterUserNameInvalid)	
[ClusterUserNameInvalidReservedWord](#ClusterUserNameInvalidReservedWord)	
[ContainerNameMisMatchWithDnsName](#ContainerNameMisMatchWithDnsName)	
[DataNodeDefinitionNotFound](#DataNodeDefinitionNotFound)	
[DeploymentDeletionFailure](#DeploymentDeletionFailure)	
[DnsMappingNotFound](#DnsMappingNotFound)	
[DuplicateClusterContainerRequest](#DuplicateClusterContainerRequest)	
[DuplicateClusterInHostedService](#DuplicateClusterInHostedService)		
[FailureToUpdateDeploymentStatus](#FailureToUpdateDeploymentStatus)		
[HdiRestoreClusterAltered](#HdiRestoreClusterAltered)	
[HeadNodeConfigNotFound](#HeadNodeConfigNotFound)	
[HeadNodeConfigNotFound](#HeadNodeConfigNotFound)	 
[HostedServiceCreationFailure](#HostedServiceCreationFailure)	
[HostedServiceHasProductionDeployment](#HostedServiceHasProductionDeployment)	
[HostedServiceNotFound](#HostedServiceNotFound)		
[HostedServiceWithNoDeployment](#HostedServiceWithNoDeployment)		
[InsufficientResourcesCores](#InsufficientResourcesCores)	
[InsufficientResourcesHostedServices](#InsufficientResourcesHostedServices)		
[InternalErrorRetryRequest](#InternalErrorRetryRequest)		
[InvalidAzureStorageLocation](#InvalidAzureStorageLocation)		
[InvalidNodeSizeForDataNode](#InvalidNodeSizeForDataNode)	
[InvalidNodeSizeForHeadNode](#InvalidNodeSizeForHeadNode)	
[InvalidRightsForDeploymentDeletion](#InvalidRightsForDeploymentDeletion)	
[InvalidStorageAccountBlobContainerName](#InvalidStorageAccountBlobContainerName)	
[InvalidStorageAccountConfigurationSecretKey](#InvalidStorageAccountConfigurationSecretKey)	
[InvalidVersionHeaderFormat](#InvalidVersionHeaderFormat)	
[MoreThanOneHeadNode](#MoreThanOneHeadNode)	
[OperationTimedOutRetryRequest](#OperationTimedOutRetryRequest)	
[ParameterNullOrEmpty](#ParameterNullOrEmpty)	
[PreClusterCreationValidationFailure](#PreClusterCreationValidationFailure)	
[RegionCapabilityNotAvailable](#RegionCapabilityNotAvailable)	
[StorageAccountNotColocated](#StorageAccountNotColocated)	
[SubscriptionIdNotActive](#SubscriptionIdNotActive)	
[SubscriptionIdNotFound](#SubscriptionIdNotFound)	
[UnableToResolveDNS](#UnableToResolveDNS)	
[UnableToVerifyLocationOfResource](#UnableToVerifyLocationOfResource)	
[VersionCapabilityNotAvailable](#VersionCapabilityNotAvailable)	
[VersionNotSupported](#VersionNotSupported)	
[VersionNotSupportedInRegion](#VersionNotSupportedInRegion)	
[WasbAccountConfigNotFound](#WasbAccountConfigNotFound)	



<h2><a id="discription-mitigation-errors"></a>Diagnóstico e redução de erros</h2> 


<h3><a id="AtleastOneSqlMetastoreMustBeProvided"></a>AtleastOneSqlMetastoreMustBeProvided</h3>
- **Descrição**:   Forneça os detalhes do banco de dados SQL do Azure para pelo menos um componente, para usar as configurações personalizadas para as metastores do Hive e do Oozie.   
- **Redução**:  O usuário precisa fornecer um metastore válido de SQL do Azure e repetir a solicitação.  

<h3><a id="AzureRegionNotSupported"></a>AzureRegionNotSupported</h3>
- **Descrição**: Não foi possível criar o cluster na região *nameOfYourRegion*.   Use uma região do HDInsight válida e repita a solicitação.   
- **Redução**: O cliente deve criar a região do cluster que atualmente dá suporte a ele:  Sudeste da Ásia, Europa Ocidental, Europa do Norte, Leste dos Estados Unidos ou Oeste dos Estados Unidos.  

<h3><a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound</h3>
- **Descrição**:  O servidor não pôde localizar o registro do cluster solicitado.  
- **Redução**: Tente a operação novamente. 

<h3><a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord</h3>
- **Descrição**: O nome DNS do cluster *yourDnsName é inválido*.  O nome deve começar e terminar com caracteres alfanuméricos e pode conter apenas o caractere especial '-'  
- **Redução**:Verifique se você usou um nome DNS válido para seu cluster que comece e termine com alfanuméricos e que não contenha caracteres especiais além do traço '-' e, em seguida, repita a operação.

<h3><a id="ClusterNameUnavailable"></a>ClusterNameUnavailable</h3>
- **Descrição**: O nome do cluster *yourClusterName* não está disponível.  Escolha outro nome.  
- **Redução**: O usuário deve especificar um clustername exclusivo e que não exista e repetir. Se o usuário estiver usando o portal, a interface do usuário vai notificá-lo se um nome de cluster já estiver sendo usado durante as etapas de criação. 
 

<h3><a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid</h3>
- **Descrição**: A senha do cluster é inválida.  A senha deve ter, pelo menos, 10 caracteres e deve conter, no mínimo, um número, uma letra maiúscula, uma letra minúscula e um caractere especial sem espaços e não pode contar o nome do usuário.  
- **Redução**: Forneça uma senha válida do cluster e repita a operação. 

<h3><a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid</h3>
- **Descrição**: O nome de usuário do cluster é inválido.  Verifique se o nome do usuário não contém caracteres especiais ou espaços.  
- **Redução**:Forneça um nome de usuário do cluster válido e repita a operação.

<h3><a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord</h3>
- **Descrição**: O nome DNS do cluster *yourDnsClusterName* é inválido.  O nome deve começar e terminar com caracteres alfanuméricos e pode conter apenas o caractere especial '-'  
- **Redução**:Forneça um nome de usuário DNS do cluster válido e repita a operação.

<h3><a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName</h3>
- **Descrição**:  O nome do contêiner no URI *yourcontainerURI* e o nome DNS *yourDnsName* no corpo da solicitação devem ser o mesmo.  
- **Redução**:Verifique se o nome do contêiner e o seu nome DNS são os mesmos e repita a operação.

<h3><a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound</h3>
- **Descrição**: Configuração de cluster inválida.  Não é possível encontrar nenhuma definição de nó de dados no tamanho do nó.  
- **Redução**:Tente a operação novamente.

<h3><a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure</h3> 	
- **Descrição**:  Falha na exclusão da implantação do cluster  
- **Redução**:Repita a operação de exclusão.

<h3><a id="DnsMappingNotFound"></a>DnsMappingNotFound</h3> 
- **Descrição**: Erro de configuração do serviço.  Informação de mapeamento de DNS não encontrada.  
- **Redução**:Excluir cluster e criar um novo cluster.

<h3><a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest</h3>
- **Descrição**: Tentativa de criação de contêiner de cluster duplicado.   Existe um registro para *nameOfYourContainer*, mas as Etags não coincidem.   
- **Redução**: Forneça um nome exclusivo para o contêiner e repita a operação de criação. 

<h3><a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService</h3>
- **Descrição**: O serviço hospedado *nameOfYourHostedService* já contém um cluster.  Um serviço hospedado não pode conter vários clusters  
- **Redução**: Hospede o cluster em outro serviço hospedado. 

<h3><a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus</h3>
- **Descrição**:  O servidor não pôde atualizar o estado da implantação do cluster.  
- **Redução**: Tente a operação novamente. Se isso acontecer várias vezes, entre em contato com o CSS. 

<h3><a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered</h3>
- **Descrição**: O cluster *yourClusterName* foi excluído como parte da manutenção.     Recrie o cluster.     
- **Redução**:Recrie o cluster.

<h3><a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound</h3>
- **Descrição**: Configuração de cluster inválida.Configuração de nó de cabeçalho necessária não encontrada nos tamanhos de nós.
- **Redução**:Tente a operação novamente.

<h3><a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure</h3>
- **Descrição**: Não é possível criar o serviço hospedado *nameOfYourHostedService*.  Tente novamente a solicitação.  
- **Redução**:Tente novamente a solicitação.

<h3><a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment</h3>
- **Descrição**: O serviço hospedado *nameOfYourHostedService* já contém uma implantação em produção. Um serviço hospedado não pode conter várias implantações em produção.   Tente novamente a solicitação com um nome de cluster diferente.   
- **Redução**:Use outro nome de cluster e repita a solicitação.

<h3><a id="HostedServiceNotFound"></a>HostedServiceNotFound</h3>
- **Descrição**:  Não foi possível encontrar o serviço hospedado *nameOfYourHostedService* do cluster.  
- **Redução**: Se o cluster estiver em estado de erro, exclua-o e tente novamente. 

<h3><a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment</h3>
- **Descrição**:  O serviço hospedado *nameOfYourHostedService* não tem nenhuma implantação associada.  
- **Redução**: Se o cluster estiver em estado de erro, exclua-o e tente novamente. 

<h3><a id="InsufficientResourcesCores"></a>InsufficientResourcesCores</h3>
- **Descrição**: A ID de assinatura *yourSubscriptionId* não tem núcleos restantes para criar o cluster *yourClusterName*. Obrigatório: *resourcesRequired*, disponível:  *resourcesAvailable*.  
- **Redução**:Libere recursos em sua assinatura ou aumente os recursos disponíveis para a assinatura e tente criar o cluster novamente.

<h3><a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices</h3>
- **Descrição**:  A ID de assinatura *yourSubscriptionId* não tem cota para um novo HostedService criar o cluster *yourClusterName*.  
- **Redução**:Libere recursos em sua assinatura ou aumente os recursos disponíveis para a assinatura e tente criar o cluster novamente.

<h3><a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest</h3>
- **Descrição**: O servidor encontrou um erro interno.  Tente novamente a solicitação.  
- **Redução**: Tente novamente a solicitação. 

<h3><a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation</h3>
- **Descrição**: O local de Armazenamento do Azure *dataRegionName* não é um local válido.   Verifique se a região está correta e tente novamente a solicitação.   
- **Redução**: Selecione um local de armazenamento que ofereça suporte ao HDInsight, verifique se o cluster está colocalizado e repita a operação. 

<h3><a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode</h3>
- **Descrição**: Tamanho da VM inválido para nós de dados.  Somente o tamanho 'VM Grande' tem suporte para todos os nós de dados.  
- **Redução**: Especifique o tamanho do nó com suporte para o nó de dados e repita a operação. 

<h3><a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode</h3>
- **Descrição**: Tamanho da VM inválido para nó de cabeçalho.  Apenas o tamanho 'VM Extragrande' tem suporte para o nó de cabeçalho.  
- **Redução**:Especifique o tamanho do nó com suporte para o nó de cabeçalho e repita a operação

<h3><a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion</h3>
- **Descrição**:  A ID de assinatura *yourSubscriptionId* que está sendo usada não tem permissões suficientes para executar a operação de exclusão do cluster *yourClusterName*.  
- **Redução**:  Se o cluster estiver em estado de erro, remova-o e tente novamente.  

<h3><a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName</h3>
- **Descrição**: O nome do contêiner de blob da conta de armazenamento externa *yourContainerName* é inválido.  Verifique se o nome começa com uma letra e contém somente letras minúsculas, números e hífen.  
- **Redução**:Especifique um nome de contêiner de blob da conta de armazenamento válido e repita a operação.

<h3><a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey</h3>
- **Descrição**:  A configuração da conta de armazenamento externa *yourStorageAccountName* é necessária para que os detalhes da chave secreta sejam definidos.  
- **Redução**:Especifique uma chave secreta válida para a conta de armazenamento e repita a operação.

<h3><a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat</h3>
- **Descrição**:  A versão do cabeçalho *yourVersionHeader* não está no formato válido de aaaa-mm-dd.  
- **Redução**: Especifique um formato válido para a versão do cabeçalho e repita a solicitação. 

<h3><a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode</h3>
- **Descrição**: Configuração de cluster inválida.  Encontrada mais de uma configuração de nó de cabeçalho.  
- **Redução**: Edite a configuração para que apenas um nó de cabeçalho seja especificado. 

<h3><a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest</h3>
- **Descrição**: A operação não pôde ser concluída dentro do tempo permitido ou do número máximo de tentativas possível.  Tente novamente a solicitação.  
- **Redução**: Tente novamente a solicitação. 

<h3><a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty</h3>
- **Descrição**:  O parâmetro *yourParameterName* não pode ser nulo nem vazio.  
- **Redução**: Especifique um valor válido para o parâmetro. 

<h3><a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure</h3>
- **Descrição**: Uma ou mais das entradas se solicitação de criação de cluster não são válidas.  Verifique se os valores de entrada estão corretos e tente novamente a solicitação.  
- **Redução**: Verifique se os valores de entrada estão corretos e tente novamente a solicitação. 

<h3><a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable</h3>
- **Descrição**:  A capacidade de região não está disponível para a região *yourRegionName* e para a ID da assinatura *yourSubscriptionId*.  
- **Redução**: Especifique uma região que ofereça suporte a clusters HDInsight. As regiões publicamente com suporte são: Sudeste da Ásia, Europa Ocidental, Europa do Norte, Leste dos Estados Unidos ou Oeste dos Estados Unidos. 

<h3><a id="StorageAccountNotColocated"></a>StorageAccountNotColocated</h3>
- **Descrição**: A conta de armazenamento *yourStorageAccountName* está na região *currentRegionName*.  Deveria ser igual à região do cluster *yourClusterRegionName*.  
- **Redução**: Especifique uma conta de armazenamento na mesma região em que seu cluster está ou, se os seus dados já estiverem na conta de armazenamento, crie um novo cluster na mesma região da conta de armazenamento existente. Se você estiver usando o portal, a interface do usuário vai notificá-lo sobre esse problema com antecedência. 

<h3><a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive</h3>
- **Descrição**:  A ID da assinatura *yourSubscriptionId* fornecida não está ativa.  
- **Redução**:Reative sua assinatura ou obtenha uma nova assinatura válida.

<h3><a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound</h3>
- **Descrição**:  Não foi possível encontrar a ID da assinatura *yourSubscriptionId*.  
- **Redução**: Verifique se sua ID de assinatura é válida e repita a operação. 

<h3><a id="UnableToResolveDNS"></a>UnableToResolveDNS</h3>
- **Descrição**: Não é possível resolver a *yourDnsUrl* do DNS.  Verifique se foi fornecida a URL totalmente qualificada do ponto de extremidade do blob.  
- **Redução**: Forneça uma URL de blob válida. A URL deve ser totalmente válida, incluindo começar com *http://* e terminar com *.com*.  A URL totalmente qualificada geralmente pode ser encontrada na guia de armazenamento do portal manage.windowsazure.com.  

<h3><a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource</h3>
- **Descrição**: Não é possível verificar a localização do recurso *yourDnsUrl*.  Verifique se foi fornecida a URL totalmente qualificada do ponto de extremidade do blob.  
- **Redução**: Forneça uma URL de blob válida. A URL deve ser totalmente válida, incluindo começar com *http://* e terminar com *.com*. A URL totalmente qualificada geralmente pode ser encontrada na guia de armazenamento do portal manage.windowsazure.com. 

<h3><a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable</h3>
- **Descrição**:  A capacidade de versão não está disponível para a versão *specifiedVersion* e para a ID da assinatura *yourSubscriptionId*.  
- **Redução**: Escolha uma versão que esteja disponível e repita a operação. 

<h3><a id="VersionNotSupported"></a>VersionNotSupported</h3>
- **Descrição**:   Versão *specifiedVersion* sem suporte.   
- **Redução**:Escolha uma versão que tenha suporte e repita a operação.

<h3><a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion</h3>
- **Descrição**:  A versão *specifiedVersion* não está disponível na região do Azure *specifiedRegion*.  
- **Redução**: Escolha uma versão que tenha suporte na região especificada e repita a operação. 

<h3><a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound</h3>
- **Descrição**: Configuração de cluster inválida.  Configuração da conta WASB necessária não encontrada em contas externas.  
- **Redução**: Verifique se a conta existe e está corretamente especificada na configuração e repita a operação. 

<h2><a id="resources"></a>Recursos adicionais de depuração</h2> 

* [Documentação do SDK do Azure HDInsight][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/pt-br/library/dn479185.aspx

[image-hdi-debugging-error-messages-portal]: ./media/hdinsight-debug-jobs/hdi-debug-errormessages-portal.png







<!--HONumber=35_1-->
