<properties
	pageTitle="Implantar um aplicativo Web usando o MSDeploy com um nome de host e um certificado ssl"
	description="Use um modelo do Gerenciador de Recursos do Azure para implantar um aplicativo Web usando o MSDeploy e configurando um nome de host personalizado e um certificado SSL"
	services="app-service\web"
	documentationCenter=""
	authors="jodehavi"
	/>

<tags
	ms.service="app-service-web"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/22/2015"
	ms.author="john.dehavilland"/>

# Implantar um aplicativo Web com o MSDeploy, um nome de host personalizado e um certificado SSL

Este guia orienta você durante a criação de uma implantação ponta a ponta para um Aplicativo Web do Azure usando o MSDeploy e adicionando um nome de host personalizado e um certificado SSL ao modelo ARM.

Para obter mais informações sobre a criação de modelos, consulte [Criação de Modelos do Gerenciador de Recursos do Azure](../resource-group-authoring-templates.md).

###Criar o aplicativo de exemplo

Você implantará um aplicativo Web ASP.NET. A primeira etapa é criar um aplicativo Web simples (ou você pode optar por usar um existente — nesse caso, ignore esta etapa).

Abra o Visual Studio 2015 e escolha Arquivo > Novo Projeto. Na caixa de diálogo que aparecer, escolha Web > aplicativo Web ASP.NET. Em Modelos, escolha Web e escolha o modelo MVC. Selecione _Alterar tipo de autenticação_ para _Sem Autenticação_. Isso é apenas para simplificar ao máximo o aplicativo de exemplo.

Neste ponto, você terá um aplicativo Web ASP.Net básico pronto para uso como parte do processo de implantação.

###Criar um pacote do MSDeploy

A próxima etapa será criar o pacote para implantar o aplicativo Web no Azure. Para fazer isso, salve seu projeto e execute o seguinte na linha de comando:

	msbuild yourwebapp.csproj /t:Package /p:PackageLocation="path\to\package.zip"

Isso criará um pacote compactado na pasta PackageLocation. O aplicativo está pronto para ser implantado; agora você pode compilar um modelo do Gerenciador de Recursos do Azure para fazer isso.

###Criar um modelo ARM
Primeiro, vamos começar com um modelo ARM básico que criará um aplicativo Web e um plano de hospedagem (observe que os parâmetros e as variáveis não serão mostrados para tudo ficar mais conciso).

	{
		"name": "[parameters('appServicePlanName')]",
		"type": "Microsoft.Web/serverfarms",
		"location": "[resourceGroup().location]",
		"apiVersion": "2014-06-01",
		"dependsOn": [ ],
		"tags": {
		    "displayName": "appServicePlan"
		},
		"properties": {
		    "name": "[parameters('appServicePlanName')]",
		    "sku": "[parameters('appServicePlanSKU')]",
		    "workerSize": "[parameters('appServicePlanWorkerSize')]",
		    "numberOfWorkers": 1
		}
	},
	{
		"name": "[variables('webAppName')]",
		"type": "Microsoft.Web/sites",
		"location": "[resourceGroup().location]",
		"apiVersion": "2015-08-01",
		"dependsOn": [
		    "[concat('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
		],
		"tags": {
		    "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]": "Resource",
		    "displayName": "webApp"
		},
		"properties": {
		    "name": "[variables('webAppName')]",
		    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
		}
	}

Em seguida, você precisará modificar o recurso de aplicativo Web para que ele obtenha um recurso aninhado do MSDeploy. Isso permitirá que você referencie o pacote criado anteriormente e diga ao Gerenciador de Recursos do Azure para usar o MSDeploy para implantar o pacote no Aplicativo Web do Azure. A seguir, o recurso Microsoft.Web/sites com o recurso aninhado do MSDeploy:

    {
        "name": "[variables('webAppName')]",
        "type": "Microsoft.Web/sites",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-08-01",
        "dependsOn": [
            "[concat('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        ],
        "tags": {
            "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]": "Resource",
            "displayName": "webApp"
        },
        "properties": {
            "name": "[variables('webAppName')]",
            "serverFarmId": "[resourceId('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        },
        "resources": [
            {
                "name": "MSDeploy",
                "type": "extensions",
                "location": "[resourceGroup().location]",
                "apiVersion": "2015-08-01",
                "dependsOn": [
                    "[concat('Microsoft.Web/sites/', variables('webAppName'))]"
                ],
                "tags": {
                    "displayName": "webDeploy"
                },
                "properties": {
                    "packageUri": "[concat(parameters('_artifactsLocation'), '/', parameters('webDeployPackageFolder'), '/', parameters('webDeployPackageFileName'), parameters('_artifactsLocationSasToken'))]",
                    "dbType": "None",
                    "connectionString": "",
                    "setParameters": {
                        "IIS Web Application Name": "[variables('webAppName')]"
                    }
                }
            }
        ]
    }

Agora você observará que o recurso do MSDeploy obtém uma propriedade **packageUri**, definida desta forma:

	"packageUri": "[concat(parameters('_artifactsLocation'), '/', parameters('webDeployPackageFolder'), '/', parameters('webDeployPackageFileName'), parameters('_artifactsLocationSasToken'))]"

Essa **packageUri** obtém o uri da conta de armazenamento que aponta para a conta de armazenamento para onde você carregará o pacote compactado. O Gerenciador de Recursos do Azure aproveitará as [Assinaturas de Acesso Compartilhado](../storage/storage-dotnet-shared-access-signature-part-1.md) para obter o pacote localmente da conta de armazenamento durante a implantação do modelo. Este processo será automatizado por meio de um script do PowerShell que carregará o pacote e chamará a API de Gerenciamento do Azure para criar as chaves necessárias e passá-las para o modelo como parâmetros (*\_artifactsLocation* e *\_artifactsLocationSasToken*). Será necessário definir parâmetros para a pasta em que o pacote será carregado no contêiner de armazenamento e para o nome de arquivo desse pacote.

Em seguida, adicione outro recurso aninhado para configurar as associações de nome de host para aproveitar um domínio personalizado. Primeiro, você precisará garantir que você possui o nome de host e configurá-lo para que o Azure verifique você o possui - consulte [Configurar um nome de domínio personalizado no Serviço de Aplicativo do Azure](web-sites-custom-domain-name.md). Depois disso, você poderá adicionar o seguinte ao seu modelo na seção do recurso Microsoft.Web/sites:

	{
		"apiVersion": "2015-08-01",
		"type": "hostNameBindings",
		"name": "www.yourcustomdomain.com",
		"dependsOn": [
		    "[concat('Microsoft.Web/sites/', variables('webAppName'))]"
		],
		"properties": {
		    "domainId": null,
		    "hostNameType": "Verified",
		    "siteName": "variables('webAppName')"
		}
	}

Por fim, adicione outro recurso de nível superior, Microsoft.Web/certificates. Esse recurso conterá o certificado SSL e existirá no mesmo nível do aplicativo Web e do plano de hospedagem.

	{
	    "name": "[parameters('certificateName')]",
	    "apiVersion": "2014-04-01",
	    "type": "Microsoft.Web/certificates",
	    "location": "[resourceGroup().location]",
	    "properties": {
	        "pfxBlob": "pfx base64 blob",
	        "password": "some pass"
	    }
	}

Você precisará ter um certificado SSL válido para configurar esse recurso. Quando você tiver um certificado válido, será necessário extrair os bytes pfx como uma cadeia de caracteres de base64. Uma opção para extrair isso é usar o seguinte comando PowerShell:

	$fileContentBytes = get-content 'C:\path\to\cert.pfx' -Encoding Byte

	[System.Convert]::ToBase64String($fileContentBytes) | Out-File 'pfx-bytes.txt'

Em seguida, você poderia passar isso como um parâmetro para o modelo de implantação ARM.

Neste ponto, o modelo ARM estará pronto.

###Implantar o modelo

As etapas finais são juntar tudo isso em uma implantação completa ponta a ponta. Para facilitar a implantação, você pode aproveitar o script **Deploy-AzureResourceGroup.ps1** do PowerShell adicionado durante a criação de um projeto do Grupo de Recursos do Azure no Visual Studio para ajudar no carregamento de todos os artefatos necessários ao modelo. Ele exige a criação prévia de uma conta de armazenamento que você deseja usar. Neste exemplo, criei uma conta de armazenamento compartilhada para o pacote.zip a ser carregado. O script utilizará o AzCopy para carregar o pacote para a conta de armazenamento. Você passa o local da pasta do artefato e o script carregará automaticamente todos os arquivos desse diretório para o contêiner de armazenamento nomeado. Depois de chamar Deploy-AzureResourceGroup.ps1, você deverá atualizar as associações SSL para mapear o nome de host personalizado com o certificado SSL.

O PowerShell a seguir mostra a implantação completa chamando Deploy-AzureResourceGroup.ps1:

	#Set resource group name
	$rgName = "Name-of-resource-group"

	#call deploy-azureresourcegroup script to deploy web app

	.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation "East US" `
									-ResourceGroupName $rgName `
									-UploadArtifacts "container-name" `
									-StorageAccountName "name-of-storage-acct-for-package" `
									-StorageAccountResourceGroupName "resource-group-name-storage-acct" `
									-TemplateFile "web-app-deploy.json" `
									-TemplateParametersFile "web-app-deploy-parameters.json" `
									-ArtifactStagingDirectory "C:\path\to\packagefolder"

	#update web app to bind ssl certificate to hostname. This has to be done after creation above.

	$cert = Get-PfxCertificate -FilePath C:\path\to\certificate.pfx

	$ar = Get-AzureRmResource -Name nameofwebsite -ResourceGroupName $rgName -ResourceType Microsoft.Web/sites -ApiVersion 2014-11-01

	$props = $ar.Properties

	$props.HostNameSslStates[2].'SslState' = 1
	$props.HostNameSslStates[2].'thumbprint' = $cert.Thumbprint
	$props.hostNameSslStates[2].'toUpdate' = $true

	Set-AzureRmResource -ApiVersion 2014-11-01 -Name nameofwebsite -ResourceGroupName $rgName -ResourceType Microsoft.Web/sites -PropertyObject $props

Neste ponto, seu aplicativo deve ter sido implantado e você deve ser capaz de localizá-lo por meio de https://www.yourcustomdomain.com

<!---HONumber=AcomDC_0107_2016-->