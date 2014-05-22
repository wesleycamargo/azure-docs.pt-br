<properties linkid="manage-services-hdinsight-debug-error-messages" urlDisplayName="Depurar Erros do HDInsight" pageTitle="Depurar o HDInsight: mensagens de erro | Azure" metaKeywords="dinsight, serviço do hdinsight, azure hdinsight, depurar, mensagens de erro, erros" description="Saiba quais são as mensagens de erro que você pode receber ao administrar o HDInsight usando o PowerShell, e as etapas que podem ser executadas para recuperação" services="hdinsight" title="Depurar o HDInsight: mensagens de erro" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" authors="bradsev" />

# Depurar o HDInsight: mensagens de erro

##Introdução
As mensagens de erro discriminadas neste tópico são fornecidas para ajudar os usuários do Azure HDInsight a entender possíveis condições de erro que podem encontrar ao administrar o serviço usando o PowerShell do Azure e para avisá-los sobre as etapas que podem ser executadas para recuperação do erro. 

Algumas dessas mensagens de erro também podem ser vistas no portal do Azure ao gerenciar clusters HDInsight. Mas outras mensagens de erro que você pode encontrar são menos granulares devido às restrições nas ações corretivas possíveis neste contexto. Outras mensagens de erro são fornecidas nos contextos onde a atenuação é óbvia. Por exemplo, se as restrições nos parâmetros forem violadas, a mensagem aparecerá no lado direito da caixa onde o valor foi inserido. Este é um caso onde vários nós de dados foram solicitados. A solução é reduzir o número para um valor permitido que seja 22 ou inferior.

![HDI.Debugging.ErrorMessages.Portal][image-hdi-debugging-error-messages-portal]

Os erros que um usuário pode encontrar no PowerShell do Azure ou no Portal do Azure são listados em ordem alfabética por nome na seção [Erros do HDInsight](#hdinsight-error-messages) onde são vinculados a uma entrada na seção [Descrição e atenuação de erros](#discription-mitigation-errors) que fornece as seguintes informações sobre o erro:
 	
- **Descrição**: a mensagem de erro que os usuários veem	
- **Atenuação**: quais etapas podem ser tomadas para recuperação do erro. 

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



<h2><a id="discription-mitigation-errors"></a>Diagnóstico e atenuação de erros</h2> 


<h3><a id="AtleastOneSqlMetastoreMustBeProvided"></a>AtleastOneSqlMetastoreMustBeProvided</h3>
- **Descrição**: forneça os detalhes do Banco de Dados SQL do Azure para pelo menos um componente para usar as configurações personalizadas para as metastores do Hive e do Oozie.   
- **Atenuação**: o usuário precisa fornecer um metastore válido do SQL Azure e repetir a solicitação.  

<h3><a id="AzureRegionNotSupported"></a>AzureRegionNotSupported</h3>
- **Descrição**: não foi possível criar o cluster na região *nameOfYourRegion*. Use uma região do HDInsight válida e repita a solicitação.   
- **Atenuação**: o cliente deve criar a região do cluster que atualmente oferece suporte a ele: Norte da Europa, Leste dos EUA ou Oeste dos EUA.  

<h3><a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound</h3>
- **Descrição**: o servidor não pôde localizar o registro do cluster solicitado.  
- **Atenuação**: repita a operação. 

<h3><a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord</h3>
- **Descrição**: o nome DNS do cluster *yourDnsName* é inválido. O nome deve começar e terminar com caracteres alfanuméricos e pode conter apenas o caractere especial '-'  
- **Atenuação**: verifique se você usou um nome DNS válido para seu cluster que comece e termine com alfanuméricos e que não contenha caracteres especiais além do traço '-' e, em seguida, repita a operação.

<h3><a id="ClusterNameUnavailable"></a>ClusterNameUnavailable</h3>
- **Descrição**: o nome do Cluster *yourClusterName* não está disponível. Escolha outro nome.  
- **Atenuação**: o usuário deve especificar um clustername exclusivo e que não exista e repetir. Se o usuário estiver usando o portal, a interface do usuário irá notificá-lo se um nome de cluster já estiver sendo usado durante as etapas de criação. 
 

<h3><a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid</h3>
- **Descrição**: a senha do cluster é inválida. A senha deve ter, pelo menos, 10 caracteres e deve conter, no mínimo, um número, uma letra maiúscula, uma letra minúscula e um caractere especial sem espaços e não pode contar o nome do usuário.  
- **Atenuação**: forneça uma senha válida do cluster e repita a operação. 

<h3><a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid</h3>
- **Description**: o nome de usuário do cluster é inválido. Verifique se o nome do usuário contém caracteres especiais ou espaços.  
- **Atenuação**: forneça um nome de usuário válido e repita a operação.

<h3><a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord</h3>
- **Description**: o nome DNS do cluster *yourDnsClusterName* é inválido. O nome deve começar e terminar com caracteres alfanuméricos e pode conter apenas o caractere especial '-'  
- **Atenuação**: forneça um nome de usuário DNS do cluster válido e repita a operação.

<h3><a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName</h3>
- **Descrição**: o nome do contêiner no URI *yourcontainerURI* e o nome DNS *yourDnsName* no corpo da solicitação devem ser o mesmo.  
- **Atenuação**: verifique se o nome do contêiner e o seu nome DNS são os mesmos e repita a operação.

<h3><a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound</h3>
- **Descrição**: a configuração do cluster é inválida. Não é possível encontrar nenhuma definição de nó de dados no tamanho do nó.  
- **Atenuação**: repita a operação.

<h3><a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure</h3> 	
- **Descrição**: falha na exclusão da implantação do cluster  
- **Atenuação**: repita a operação de exclusão.

<h3><a id="DnsMappingNotFound"></a>DnsMappingNotFound</h3> 
- **Descrição**: erro de configuração de serviço. Informação de mapeamento de DNS não encontrada.  
- **Atenuação**: exclua e crie um novo cluster.

<h3><a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest</h3>
- **Descrição**: tentativa de criação de contêiner de cluster duplicado. Existe um registro para *nameOfYourContainer* mas as Etags não coincidem.   
- **Atenuação**: forneça um nome exclusivo para o contêiner e repita a operação de criação. 

<h3><a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService</h3>
- **Descrição**: o serviço hospedado *nameOfYourHostedService* já contém um cluster. Um serviço hospedado não pode conter vários clusters  
- **Atenuação**: hospede o cluster em outro serviço hospedado. 

<h3><a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus</h3>
- **Descrição**: o servidor não pôde atualizar o estado da implantação do cluster.  
- **Atenuação**: repita a operação. Se isso acontecer várias vezes, entre em contato com o CSS. 

<h3><a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered</h3>
- **Descrição**: o cluster *yourClusterName* foi excluído como parte da manutenção. Recrie o cluster.     
- **Atenuação**: recrie o cluster.

<h3><a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound</h3>
- **Descrição**: a configuração do cluster é inválida. Configuração de nó de cabeçalho necessária não encontrada nos tamanhos de nós.
- **Atenuação**: repita a operação.

<h3><a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure</h3>
- **Descrição**: não é possível criar o serviço hospedado *nameOfYourHostedService*. Tente novamente a solicitação.  
- **Atenuação**: repita a solicitação.

<h3><a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment</h3>
- **Descrição**: o serviço hospedado *nameOfYourHostedService* já contém uma implantação em produção. Um serviço hospedado não pode conter várias implantações em produção. Tente novamente a solicitação com um nome de cluster diferente.   
- **Atenuação**: use outro nome de cluster e repita a solicitação.

<h3><a id="HostedServiceNotFound"></a>HostedServiceNotFound</h3>
- **Descrição**: o serviço hospedado *nameOfYourHostedService* do cluster não pôde ser encontrado.  
- **Atenuação**: se o cluster estiver em estado de erro, exclua-o e tente novamente. 

<h3><a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment</h3>
- **Descrição**: o serviço hospedado *nameOfYourHostedService* não tem uma implantação associada.  
- **Atenuação**: se o cluster estiver em estado de erro, exclua-o e tente novamente. 

<h3><a id="InsufficientResourcesCores"></a>InsufficientResourcesCores</h3>
- **Descrição**: a SubscriptionId *yourSubscriptionId* não tem núcleos restantes para criar o cluster *yourClusterName*. Obrigatório: *resourcesRequired*, disponível: *resourcesAvailable*.  
- **Atenuação**: libere os recursos em sua assinatura ou aumente os recursos disponíveis para a assinatura e tente criar o cluster novamente.

<h3><a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices</h3>
- **Descrição**: a ID de assinatura *yourSubscriptionId* não tem cota para um novo HostedService para criar o cluster *yourClusterName*.  
- **Atenuação**: libere os recursos em sua assinatura ou aumente os recursos disponíveis para a assinatura e tente criar o cluster novamente.

<h3><a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest</h3>
- **Descrição**: o servidor encontrou um erro interno. Tente novamente a solicitação.  
- **Atenuação**: repita a solicitação. 

<h3><a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation</h3>
- **Descrição**: o local de armazenamento do Azure *dataRegionName* não é um local válido. Verifique se a região está correta e tente novamente a solicitação.   
- **Atenuação**: selecione um local de armazenamento que ofereça suporte ao HDInsight, verifique se o cluster está colocalizado e repita a operação. 

<h3><a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode</h3>
- **Descrição**: tamanho da VM inválido para nós de dados. Somente o tamanho 'VM Grande' tem suporte para todos os nós de dados.  
- **Atenuação**: especifique o tamanho de nó com suporte para o nó de dados e repita a operação. 

<h3><a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode</h3>
- **Descrição**: tamanho da VM inválido para nó de cabeçalho. Apenas o tamanho 'VM Extragrande' tem suporte para o nó de cabeçalho.  
- **Atenuação**: especifique o tamanho de nó com suporte para o nó de cabeçalho e repita a operação.

<h3><a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion</h3>
- **Descrição**: a ID de assinatura de *yourSubscriptionId* que está sendo usada não tem permissões suficientes para executar a operação de exclusão para o cluster *yourClusterName*.  
- **Atenuação**: se o cluster estiver em estado de erro, remova-o e tente novamente.  

<h3><a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName</h3>
- **Descrição**: o nome do contêiner de blob da conta de armazenamento externa *yourContainerName* é inválido. Verifique se o nome começa com uma letra e contém somente letras minúsculas, números e hífen.  
- **Atenuação**: especifique um nome de contêiner de blob da conta de armazenamento válido e repita a operação.

<h3><a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey</h3>
- **Descrição**: a configuração da conta de armazenamento externa *yourStorageAccountName* é necessária para que os detalhes da chave secreta sejam definidos.  
- **Atenuação**: especifique uma chave secreta válida para a conta de armazenamento e repita a operação.

<h3><a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat</h3>
- **Descrição**: a versão do cabeçalho *yourVersionHeader* não está no formato válido de aaaa-mm-dd.  
- **Atenuação**: especifique um formato válido para a versão do cabeçalho e repita a solicitação. 

<h3><a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode</h3>
- **Descrição**: a configuração do cluster é inválida. Encontrada mais de uma configuração de nó de cabeçalho.  
- **Atenuação**: edite a configuração para que apenas um nó de cabeçalho seja especificado. 

<h3><a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest</h3>
- **Descrição**: a operação não pôde ser concluída dentro do tempo permitido ou do número máximo de tentativas possível. Tente novamente a solicitação.  
- **Atenuação**: repita a solicitação. 

<h3><a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty</h3>
- **Descrição**: o parâmetro *yourParameterName* não pode ser nulo ou vazio.  
- **Atenuação**: especifique um valor válido para o parâmetro. 

<h3><a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure</h3>
- **Descrição**: uma ou mais das entradas se solicitação de criação de cluster não são válidas. Verifique se os valores de entrada estão corretos e tente novamente a solicitação.  
- **Atenuação**: verifique se os valores de entrada estão corretos e repita a solicitação. 

<h3><a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable</h3>
- **Descrição**: a capacidade da região não está disponível para a região *yourRegionName* e para a ID da assinatura *yourSubscriptionId*.  
- **Atenuação**: especifique uma região que ofereça suporte a clusters HDInsight. As regiões que têm suporte publicamente são: Leste dos EUA, Oeste dos EUA, Norte da Europa 

<h3><a id="StorageAccountNotColocated"></a>StorageAccountNotColocated</h3>
- **Descrição**: a conta de armazenamento *yourStorageAccountName* está na região *currentRegionName*. Deveria ser igual à região do cluster *yourClusterRegionName*.  
- **Atenuação**: especifique uma conta de armazenamento na mesma região em que seu cluster está ou, se os seus dados já estiverem na conta de armazenamento, crie um novo cluster na mesma região da conta de armazenamento existente. Se você estiver usando o portal, a interface do usuário irá notificá-lo sobre esse problema com antecedência. 

<h3><a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive</h3>
- **Descrição**: a ID da assinatura *yourSubscriptionId* fornecida não está ativa.  
- **Atenuação**: reative sua assinatura ou obtenha uma nova assinatura válida.

<h3><a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound</h3>
- **Descrição**: a ID da assinatura *yourSubscriptionId* não pôde ser encontrada.  
- **Atenuação**: verifique se sua ID de assinatura é válida e repita a operação. 

<h3><a id="UnableToResolveDNS"></a>UnableToResolveDNS</h3>
- **Descrição**: não é possível resolver o *yourDnsUrl* do DNS. Verifique se foi fornecida a URL totalmente qualificada do ponto de extremidade do blob.  
- **Atenuação**: forneça uma URL de blob válida. A URL deve ser totalmente válida, incluindo começar com *http://* e terminar com *.com*. A URL totalmente qualificada geralmente pode ser encontrada na guia de armazenamento do portal manage.windowsazure.com.  

<h3><a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource</h3>
- **Descrição**: não é possível verificar a localização do recurso *yourDnsUrl*. Verifique se foi fornecida a URL totalmente qualificada do ponto de extremidade do blob.  
- **Atenuação**: forneça uma URL de blob válida. A URL deve ser totalmente válida, incluindo começar com *http://* e terminar com *.com*. A URL totalmente qualificada geralmente pode ser encontrada na guia de armazenamento do portal manage.windowsazure.com. 

<h3><a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable</h3>
- **Descrição**: a capacidade da versão não está disponível para a versão *specifiedVersion* e para a ID da assinatura *yourSubscriptionId*.  
- **Atenuação**: escolha uma versão que esteja disponível e repita a operação. 

<h3><a id="VersionNotSupported"></a>VersionNotSupported</h3>
- **Descrição**: a versão *specifiedVersion* não é suportada.   
- **Atenuação**: escolha uma versão que tenha suporte e repita a operação.

<h3><a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion</h3>
- **Descrição**: a versão *specifiedVersion* não está disponível na região do Azure *specifiedRegion*.  
- **Atenuação**: escolha uma versão que tenha suporte na região especificada e repita a operação. 

<h3><a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound</h3>
- **Descrição**: a configuração do cluster é inválida. Configuração da conta WASB necessária não encontrada em contas externas.  
- **Atenuação**: verifique se a conta existe e está corretamente especificada na configuração e repita a operação. 

<h2><a id="resources"></a>Recursos adicionais de depuração</h2> 

* [Documentação do SDK do Azure HDInsight][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/pt-br/library/dn479185.aspx

[image-hdi-debugging-error-messages-portal]: ./media/hdinsight-debug-jobs/hdi-debug-errormessages-portal.png






