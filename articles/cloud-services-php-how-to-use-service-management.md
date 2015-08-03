<properties
	pageTitle="Como usar as APIs de gerenciamento de serviços do Azure (PHP)"
	description="Saiba como usar as APIs de Gerenciamento de Serviços do PHP do Azure para gerenciar serviços de nuvem e outros aplicativos do Azure."
	services="web-sites"
	documentationCenter="php"
	authors="tfitzmac"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="11/17/2014"
	ms.author="tomfitz"/>

# Como usar o Gerenciamento de Serviços do PHP

Este guia mostrará como executar tarefas de gerenciamento de serviços comuns de forma programática no PHP. A classe [ServiceManagementRestProxy] no [SDK do Azure para PHP][download-SDK-PHP] oferece suporte a acesso programático para grande parte da funcionalidade relacionada ao gerenciamento de serviços que está disponível no [Portal de Gerenciamento][management-portal] (como **criação, atualização e exclusão de serviços de nuvem, implantações, serviços de armazenamento e grupos de afinidade**). Essa funcionalidade pode ser útil na criação de aplicativos que precisam de acesso programático ao gerenciamento de serviços.

## O que é o Gerenciamento de Serviços
A API de Gerenciamento de Serviços fornece acesso programático a grande parte da funcionalidade do gerenciamento de serviços disponível por meio do [Portal de Gerenciamento][management-portal]. O SDK do Azure para PHP permite que você gerencie os serviços de nuvem, as contas de armazenamento e os grupos de afinidade.

Para usar a API de Gerenciamento de Serviços, será necessário [criar uma conta do Azure][win-azure-account].

## Conceitos
O SDK do Azure para PHP encapsula a [API de Gerenciamento de Serviços do Azure][svc-mgmt-rest-api], que é uma API REST. Todas as operações da API são executadas por meio do SSL e mutuamente autenticadas usando certificados X.509 v3. O serviço de gerenciamento pode ser acessado em um serviço em execução no Azure ou diretamente pela Internet em qualquer aplicativo que possa enviar uma solicitação HTTPS e receber uma resposta HTTPS.

## Criar um aplicativo PHP

O único requisito para a criação de um aplicativo PHP que usa o Gerenciamento de Serviços é a referência das classes no SDK do Azure para PHP em seu código. Você pode usar as ferramentas de desenvolvimento para criar seu aplicativo, incluindo o bloco de notas.

Neste guia, você usará os recursos de serviços que podem ser chamados em um aplicativo PHP localmente ou no código em execução dentro de uma função web do Azure, uma função de trabalho ou um site.

## Obter as bibliotecas de cliente do Azure

[AZURE.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

## Como: conectar-se ao gerenciamento de serviços

Para conectar-se ao ponto de extremidade do Gerenciamento de Serviços, você precisa da ID de sua assinatura do Azure e do caminho para um certificado de gerenciamento válido. Você pode obter sua ID de assinatura por meio do [Portal de Gerenciamento][management-portal] e criar certificados de gerenciamento de várias formas. Neste guia o [OpenSSL](http://www.openssl.org/) é usado, que você pode [baixar para o Windows (a página pode estar em inglês)](http://www.openssl.org/related/binaries.html) e executar em um console.

Na verdade, você precisa criar dois certificados, um para o servidor (um arquivo `.cer`) e um para o cliente (um arquivo `.pem`). Para criar o arquivo `.pem`, execute isto:

	`openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem`

Para criar o certificado `.cer`, execute isto:

	`openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer`

Para obter mais informações sobre certificados do Azure, consulte [Visão geral dos certificados no Azure (a página pode estar em inglês)](http://msdn.microsoft.com/library/azure/gg981929.aspx). Para obter uma descrição completa dos parâmetros do OpenSSL, consulte a documentação em [http://www.openssl.org/docs/apps/openssl.html](http://www.openssl.org/docs/apps/openssl.html).

Se você baixou e importou o seu arquivo de configurações de publicação usando a [Interface de linha de comando do Azure][Azure CLI], você pode usar o arquivo `.pem` criado pelas ferramentas criam em vez de criar o seu próprio arquivo. As ferramentas criam um arquivo `.cer` para você e o atualizam no Azure, colocam o arquivo `.pem` correspondente no diretório do `.azure` em seu computador (em seu diretório de usuário).

Depois de criar esses arquivos, você precisará carregar o arquivo `.cer` no Azure por meio do [portal de gerenciamento][management-portal], e precisará anotar o local onde salvou o arquivo `.pem`.

Depois de ter obtido a ID de sua assinatura, criado um certificado e carregado o arquivo `.cer` no Azure, você poderá conectar-se ao ponto de extremidade de gerenciamento do Azure criando uma cadeia de conexão e passando-a para o método **createServiceManagementService** na classe **ServicesBuilder**:

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$conn_string = "SubscriptionID=<your_subscription_id>;CertificatePath=<path_to_.pem_certificate>";

	$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

No exemplo acima, `$serviceManagementRestProxy` é um objeto [ServiceManagementRestProxy]. A classe **ServiceManagementRestProxy** é a classe primária usada para gerenciar serviços do Azure.

## Como listar os locais disponíveis

Para listar os locais que estão disponíveis para hospedar serviços, use o método **ServiceManagementRestProxy->listLocations**:

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	try{
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

		$result = $serviceManagementRestProxy->listLocations();

		$locations = $result->getLocations();

		foreach($locations as $location){
		      echo $location->getName()."<br />";
		}
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Quando cria um serviço de nuvem, um serviço de armazenamento ou um grupo de afinidade, você precisa fornecer um local válido. O método **listLocations** sempre retornará uma lista atualizada dos locais disponíveis no momento. Na data da criação deste artigo, os locais disponíveis são:

- Em qualquer lugar dos EUA
- Em qualquer lugar da Europa
- Europa Ocidental
- Em qualquer lugar da Ásia
- Sudeste Asiático
- Ásia Oriental
- Centro-Norte dos EUA
- Norte da Europa
- Centro-Sul dos Estados Unidos
- Oeste dos EUA
- Leste dos EUA

Nos exemplos de código que seguem, os locais são passados para métodos como cadeias de caracteres. No entanto, você também pode passar locais como enumerações usando a classe <code>WindowsAzure\\ServiceManagement\\Models\\Locations</code>. Por exemplo, em vez de passar "Oeste dos EUA" para um método que aceita um local, você pode passar <code>Locations::WEST_US</code>.

## Como: criar um serviço de nuvem

Quando você cria um aplicativo e o executa no Azure, o código e a configuração, juntos, são chamados de [serviço de nuvem] do Azure (conhecido como *serviço hospedado* em versões anteriores do Azure). O método **createHostedServices** permite que você crie um novo serviço hospedado, fornecendo um nome de serviço hospedado (que deve ser exclusivo no Azure), um rótulo (um nome de serviço hospedado codificado na base 64) e um objeto **CreateServiceOptions**. O objeto [CreateServiceOptions] permite que você defina o local *ou* o grupo de afinidade para o seu serviço.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\ServiceManagement\Models\CreateServiceOptions;
	use WindowsAzure\Common\ServiceException;

	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

        $name = "myhostedservice";
        $label = base64_encode($name);
        $options = new CreateServiceOptions();
        $options->setLocation('West US');
		// Instead of setLocation, you can use setAffinityGroup
		// to set an affinity group.

        $result = $serviceManagementRestProxy->createHostedService($name, $label, $options);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Você pode listar todos os serviços hospedados para sua assinatura com o método **listHostedServices**, que retorna um objeto [ListHostedServicesResult]. A chamada do método **getHostedServices** permite que você execute um loop por uma matriz de objetos [HostedServices] e recupere as propriedades de serviço:

	$listHostedServicesResult = $serviceManagementRestProxy->listHostedServices();

	$hosted_services = $listHostedServicesResult->getHostedServices();

	foreach($hosted_services as $hosted_service){
		echo "Service name: ".$hosted_service->getName()."<br />";
		echo "Management URL: ".$hosted_service->getUrl()."<br />";
		echo "Affinity group: ".$hosted_service->getAffinityGroup()."<br />";
		echo "Location: ".$hosted_service->getLocation()."<br />";
		echo "------<br />";
		}

Se desejar obter informações sobre um determinado serviço hospedado, você pode fazê-lo passando o nome do serviço hospedado para o método **getHostedServiceProperties**:

	$getHostedServicePropertiesResult = $serviceManagementRestProxy->getHostedServiceProperties("myhostedservice");

	$hosted_service = $getHostedServicePropertiesResult->getHostedService();

	echo "Service name: ".$hosted_service->getName()."<br />";
	echo "Management URL: ".$hosted_service->getUrl()."<br />";
	echo "Affinity group: ".$hosted_service->getAffinityGroup()."<br />";
	echo "Location: ".$hosted_service->getLocation()."<br />";

Depois de criar um serviço de nuvem, você pode implantar seu código de serviço com o método [createDeployment](#CreateDeployment).

##<a id="DeleteCloudService"></a>Como excluir um serviço de nuvem

Você pode excluir um serviço de nuvem passando o nome do serviço para o método **deleteHostedService**:

	$serviceManagementRestProxy->deleteHostedService("myhostedservice");

Observe que para poder excluir um serviço, todas as implantações do serviço devem ser excluídas primeiro. (Consulte [Como excluir uma implantação](#DeleteDeployment) para obter detalhes.)

## Como criar uma implantação

O método **createDeployment** carrega um novo [pacote de serviço] e cria uma nova implantação no ambiente de preparo ou de produção. Os parâmetros para esse método são os seguintes:

* **$name**: o nome do serviço hospedado.
* **$deploymentName**: o nome da implantação.
* **$slot**: uma enumeração indicando o slot de preparo ou de produção.
* **$packageUrl**: a URL do pacote de implantação (um arquivo .cspgk). O arquivo do pacote deve ser armazenado em uma conta de Armazenamento de Blob do Azure sob a mesma assinatura que o serviço hospedado para o qual o pacote está sendo carregado. Você pode criar um pacote de implantação com os [cmdlets do PowerShell do Azure] ou com a [ferramenta de linha de comando cspack].
* **$configuration**: o arquivo de configuração de serviço (arquivo .cscfg).
* **$label**: o nome do serviço hospedado codificado na base 64.

O exemplo a seguir cria uma nova implantação no slot de produção de um serviço hospedado chamado `myhostedservice`:


	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
	use WindowsAzure\Common\ServiceException;

	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

        $name = "myhostedservice";
		$deploymentName = "v1";
        $slot = DeploymentSlot::PRODUCTION;
		$packageUrl = "URL_for_.cspkg_file";
		$configuration = base64_encode(file_get_contents('path_to_.cscfg_file'));
		$label = base64_encode($name);

        $result = $serviceManagementRestProxy->createDeployment($name,
														 $deploymentName,
														 $slot,
														 $packageUrl,
														 $configuration,
														 $label);

		$status = $serviceManagementRestProxy->getOperationStatus($result);
		echo "Operation status: ".$status->getStatus()."<br />";
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Observe no exemplo acima que o status da operação **createDeployment** pode ser recuperado passando o resultado retornado por **createDeployment** para o método **getOperationStatus**.

Você pode acessar as propriedades de implantação com o método **getDeployment**. O exemplo a seguir recupera uma implantação especificando o slot de implantação no objeto [GetDeploymentOptions], mas, em vez disso, você pode especificar o nome da implantação. O exemplo também reitera por todas as instâncias da implantação:

	$options = new GetDeploymentOptions();
	$options->setSlot(DeploymentSlot::PRODUCTION);

	$getDeploymentResult = $serviceManagementRestProxy->getDeployment("myhostedservice", $options);
	$deployment = $getDeploymentResult->getDeployment();

	echo "Name: ".$deployment->getName()."<br />";
	echo "Slot: ".$deployment->getSlot()."<br />";
	echo "Private ID: ".$deployment->getPrivateId()."<br />";
	echo "Status: ".$deployment->getStatus()."<br />";
	echo "Instances: <br />";
	foreach($deployment->getroleInstanceList() as $roleInstance){
		echo "Instance name: ".$roleInstance->getInstanceName()."<br />";
		echo "Instance status: ".$roleInstance->getInstanceStatus()."<br />";
		echo "Instance size: ".$roleInstance->getInstanceSize()."<br />";
	}
	echo "------<br />";

## Como atualizar uma implantação

Uma implantação pode ser atualizada usando o método **changeDeploymentConfiguration** ou o método **updateDeploymentStatus**.

O método **changeDeploymentConfiguration** permite carregar um novo arquivo de configuração de serviço (`.cscfg`), que alterará qualquer uma das várias configurações de serviço (incluindo o número de instâncias em uma implantação). Para obter mais informações, consulte [Esquema de configuração de serviço do Azure (.cscfg)]. O exemplo a seguir demonstra como carregar um novo arquivo de configuração de serviço:

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\ServiceManagement\Models\ChangeDeploymentConfigurationOptions;
	use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
	use WindowsAzure\Common\ServiceException;

	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

        $name = "myhostedservice";
		$configuration = base64_encode(file_get_contents('path to .cscfg file'));
		$options = new ChangeDeploymentConfigurationOptions();
		$options->setSlot(DeploymentSlot::PRODUCTION);

        $result = $serviceManagementRestProxy->changeDeploymentConfiguration($name, $configuration, $options);

		$status = $serviceManagementRestProxy->getOperationStatus($result);
		echo "Operation status: ".$status->getStatus()."<br />";
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Observe no exemplo acima que o status da operação **changeDeploymentConfiguration** pode ser recuperado passando o resultado retornado por **changeDeploymentConfiguration** para o método **getOperationStatus**.

O método **updateDeploymentStatus** permite que você defina o status de uma implantação como EXECUTANDO ou SUSPENSO. O exemplo a seguir demonstra como definir o status para EXECUTANDO para uma implantação no slot de produção de um serviço hospedado chamado `myhostedservice`:

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\ServiceManagement\Models\DeploymentStatus;
	use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
	use WindowsAzure\ServiceManagement\Models\GetDeploymentOptions;
	use WindowsAzure\Common\ServiceException;

	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

		$options = new GetDeploymentOptions();
		$options->setSlot(DeploymentSlot::PRODUCTION);

        $result = $serviceManagementRestProxy->updateDeploymentStatus("myhostedservice", DeploymentStatus::RUNNING, $options);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## Como mover implantações entre o preparo e a produção

O Azure fornece dois ambientes de implantação: preparo e produção. Normalmente, um serviço é implantado no ambiente de preparo para que seja testado antes da implantação do serviço para o ambiente de produção. Quando for a hora de promover o serviço em preparo para o ambiente de produção, você pode fazê-lo sem reimplantar o serviço. Isso pode ser feito alternando as implantações. (Para obter mais informações sobre como permutar implantações, consulte [Visão geral do gerenciamento de implantações no Azure].)

O exemplo a seguir mostra como usar o método **swapDeployment** para permutar duas implantações (com os nomes de implantação `v1` e `v2`). No exemplo, antes de chamar o método **swapDeployment**, a implantação `v1` está no slot de produção e a implantação `v2` está no slot de preparo. Após chamar **swapDeployment**, a implantação `v2` está em produção e a implantação `v1` está em preparo.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

		$result = $serviceManagementRestProxy->swapDeployment("myhostedservice", "v2", "v1");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## Como: excluir uma implantação

Para excluir uma implantação, use o método **deleteDeployment**. O exemplo a seguir mostra como excluir uma implantação no ambiente de preparo usando o método **setSlot** em um objeto [GetDeploymentOptions] e passá-lo para **deleteDeployment**. Em vez de especificar uma implantação por slot, você pode usar o método **setName** na classe [GetDepolymentOptions] para especificar uma implantação pelo nome da implantação.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\ServiceManagement\Models\GetDeploymentOptions;
	use WindowsAzure\ServiceManagement\Models\DeploymentSlot;
	use WindowsAzure\Common\ServiceException;

	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

		$options = new GetDeploymentOptions();
		$options->setSlot(DeploymentSlot::STAGING);

		$result = $serviceManagementRestProxy->deleteDeployment("myhostedservice", $options);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## Como criar um serviço de armazenamento

Um [serviço de armazenamento] fornece acesso a [Blobs][azure-blobs], [Tabelas][azure-tables] e [Filas][azure-queues] do Azure. Para criar um serviço de armazenamento, você precisa de um nome para o serviço (com 3 a 24 caracteres minúsculos e exclusivo no Azure), um rótulo (um nome com até 100 caracteres codificado em base 64 para o serviço) e um local ou um grupo de afinidade. Fornecer uma descrição para o serviço é opcional. O local, o grupo de afinidade e a descrição são definidos em um objeto [CreateServiceOptions], que é passado para o método **createStorageService**. O exemplo a seguir mostra como criar um serviço de armazenamento especificando um local. Se desejar usar um grupo de afinidade, você precisará primeiro criar um grupo de afinidade (consulte [Como criar um grupo de afinidade](#CreateAffinityGroup)) e defini-lo com o método **CreateServiceOptions->setAffinityGroup**.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\ServiceManagement\Models\CreateServiceOptions;
	use WindowsAzure\Common\ServiceException;


	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

        $name = "mystorageaccount";
        $label = base64_encode($name);
        $options = new CreateServiceOptions();
        $options->setLocation('West US');
		$options->setDescription("My storage account description.");

        $result = $serviceManagementRestProxy->createStorageService($name, $label, $options);

		$status = $serviceManagementRestProxy->getOperationStatus($result);
		echo "Operation status: ".$status->getStatus()."<br />";
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Observe no exemplo acima que o status da operação **createStorageService** pode ser recuperada passando o resultado retornado por **createStorageService** para o método **getOperationStatus**.

Você pode listar suas contas de armazenamento e suas propriedades com o método **listStorageServices**:

	// Create REST proxy.
	$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);
	$getStorageServicesResult = $serviceManagementRestProxy->listStorageServices();
	$storageServices = $getStorageServicesResult->getStorageServices();

	foreach($storageServices as $storageService){
		echo "Service name: ".$storageService->getName()."<br />";
		echo "Service URL: ".$storageService->getUrl()."<br />";
		echo "Affinity group: ".$storageService->getAffinityGroup()."<br />";
		echo "Location: ".$storageService->getLocation()."<br />";
		echo "------<br />";
	}

## Como excluir um serviço de armazenamento

Você pode excluir um serviço de armazenamento passando o nome do serviço para o método **deleteStorageService**. A exclusão de um serviço de armazenamento excluirá todos os dados armazenados no serviço (blobs, tabelas e filas).

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

		$serviceManagementRestProxy->deleteStorageService("mystorageservice");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## Como criar um grupo de afinidade

Um grupo de afinidade é um agrupamento lógico dos serviços do Azure que informa ao Azure para localizar os serviços para obter desempenho otimizado. Por exemplo, você pode criar um grupo de afinidade no local "Oeste dos Estados Unidos" e, em seguida, criar um [serviço de nuvem](#CreateCloudService) nesse grupo de afinidade. Se você criar um serviço de armazenamento no mesmo grupo de afinidade, o Azure saberá colocá-lo no local "Oeste dos Estados Unidos" e otimizá-lo no data center para obter melhor desempenho com os serviços de nuvem no mesmo grupo de afinidade.

Para criar um grupo de afinidade, você precisa de um nome, de um rótulo (nome codificado em base 64), e de um local. Opcionalmente, você pode fornecer uma descrição:

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\ServiceManagement\Models\CreateAffinityGroupOptions;
	use WindowsAzure\Common\ServiceException;

	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

        $name = "myAffinityGroup";
        $label = base64_encode($name);
        $location = "West US";

        $options = new CreateAffinityGroupOptions();
		$options->setDescription = "My affinity group description.";

        $serviceManagementRestProxy->createAffinityGroup($name, $label, $location, $options);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Depois de criar um grupo de afinidade, você pode especificar o grupo (em vez de um local) ao [criar um serviço de armazenamento](#CreateStorageService).

Você pode listar grupos de afinidade e inspecionar suas propriedades chamando o método **listAffinityGroups** e, em seguida, chamando os métodos apropriados na classe [AffinityGroup]:

	$result = $serviceManagementRestProxy->listAffinityGroups();

	$groups = $result->getAffinityGroups();

	foreach($groups as $group){
		echo "Name: ".$group->getName()."<br />";
		echo "Description: ".$group->getDescription()."<br />";
		echo "Location: ".$group->getLocation()."<br />";
		echo "------<br />";
	}

## Como excluir um grupo de afinidade

Você pode excluir um grupo de afinidade passando o nome do grupo para o método **deleteAffinityGroup**. Observe que para poder excluir um grupo de afinidade, o grupo de afinidade deve estar desassociado de qualquer serviço (ou os serviços que usam o grupo de afinidade devem ser excluídos).

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	try{
		// Create REST proxy.
		$serviceManagementRestProxy = ServicesBuilder::getInstance()->createServiceManagementService($conn_string);

		// An affinity group must be disassociated from all services
		// before it can be deleted.
		$serviceManagementRestProxy->deleteAffinityGroup("myAffinityGroup");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/windowsazure/ee460801
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

[ServiceManagementRestProxy]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/ServiceManagementRestProxy.php
[management-portal]: https://manage.windowsazure.com/
[svc-mgmt-rest-api]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx
[win-azure-account]: /pricing/free-trial/
[storage-account]: storage/storage-create-storage-account.md

[download-SDK-PHP]: php-download-sdk.md
[Azure CLI]: virtual-machines/virtual-machines-command-line-tools.md
[Composer]: http://getcomposer.org/
[ServiceManagementSettings]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/ServiceManagementSettings.php

[serviço de nuvem]: cloud-services-what-is.md
[CreateServiceOptions]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/CreateServiceOptions.php
[ListHostedServicesResult]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/ListHostedServicesResult.php

[pacote de serviço]: http://msdn.microsoft.com/library/windowsazure/gg433093
[cmdlets do PowerShell do Azure]: install-configure-powershell.md
[ferramenta de linha de comando cspack]: http://msdn.microsoft.com/library/windowsazure/gg432988.aspx
[GetDeploymentOptions]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/GetDeploymentOptions.php
[ListHostedServicesResult]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/GetDeploymentOptions.php

[Visão geral do gerenciamento de implantações no Azure]: http://msdn.microsoft.com/library/windowsazure/hh386336.aspx
[serviço de armazenamento]: storage-whatis-account.md
[azure-blobs]: storage/storage-php-how-to-use-blobs.md
[azure-tables]: storage/storage-php-how-to-use-table-storage.md
[azure-queues]: storage/storage-php-how-to-use-queues.md
[AffinityGroup]: https://github.com/WindowsAzure/azure-sdk-for-php/blob/master/WindowsAzure/ServiceManagement/Models/AffinityGroup.php


[Esquema de configuração de serviço do Azure (.cscfg)]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx

<!---HONumber=July15_HO4-->