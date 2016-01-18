<properties
	pageTitle="Implantar recursos do Azure usando um modelo | Microsoft Azure"
	description="Saiba como usar alguns dos clientes disponíveis na Biblioteca de Gerenciamento de Recursos do Azure para implantar uma máquina virtual, rede virtual e conta de armazenamento"
	services="virtual-machines,virtual-networks,storage"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="davidmu"/>

# Implantar recursos do Azure usando bibliotecas .NET e um modelo

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.


Usando grupos de recursos e modelos, você pode gerenciar em conjunto todos os recursos que dão suporte a seu aplicativo. Este tutorial mostra como usar alguns dos clientes disponíveis na Biblioteca de Gerenciamento de Recursos do Azure e como criar um modelo para implantar uma máquina virtual, rede virtual e conta de armazenamento.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Para concluir este tutorial, você também precisa do seguinte:

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Conta de armazenamento do Azure](../storage-create-storage-account.md)
- [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)

>[AZURE.NOTE]A conta de armazenamento que você cria neste ponto é usada para armazenar o modelo. Outra conta de armazenamento é criada quando você implanta o modelo que é usado para armazenar o disco da máquina virtual. Crie um contêiner nessa conta de armazenamento chamado modelos.

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

São necessários cerca de 30 minutos para a conclusão destas etapas.

## Etapa 1: adicionar um aplicativo ao AD do Azure e definir permissões

Para usar o AD do Azure para autenticar solicitações ao Gerenciador de Recursos do Azure, um aplicativo deve ser adicionado ao diretório padrão. Faça o seguinte para adicionar um aplicativo:

1. Abra um prompt do Azure PowerShell, execute este prompt de comando e digite as credenciais de sua assinatura quando solicitado:

			Login-AzureRmAccount

2. Substitua {senha} no comando a seguir pelo que você deseja usar e, em seguida, execute-o para criar o aplicativo:

			New-AzureRmADApplication -DisplayName "My AD Application 1" -HomePage "https://myapp1.com" -IdentifierUris "https://myapp1.com"  -Password "{password}"

	>[AZURE.NOTE]Anote o identificador do aplicativo retornado depois que o aplicativo é criado, pois você precisará dele para a próxima etapa. Você também pode encontrar o identificador do aplicativo no campo de ID de cliente do aplicativo na seção Active Directory do portal do Azure.

3. Substitua {id-aplicativo} pelo identificador que acabou de registrar e, em seguida, crie a entidade de serviço para o aplicativo:

			New-AzureRmADServicePrincipal -ApplicationId {application-id}

4. Defina a permissão para usar o aplicativo:

			New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName "https://myapp1.com"

## Etapa 2: criar o projeto do Visual Studio, o arquivo de modelo e o arquivo de parâmetros

### Criar o arquivo de modelo

Um modelo do Gerenciador de Recursos do Azure permite implantar e gerenciar recursos do Azure juntos usando uma descrição JSON dos recursos e parâmetros de implantação associados. O modelo que você cria neste tutorial é semelhante a um modelo que pode ser encontrado na Galeria de modelos. Para saber mais, consulte [Implantar uma VM simples do Windows no Oeste dos EUA](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/).

No Visual Studio, faça o seguinte:

1. Clique em **Arquivo** > **Novo** > **Projeto**.

2. Em **Modelos** > **Visual C#**, selecione **Aplicativo de Console**, digite o nome e o local do projeto e, em seguida, clique em **OK**.

3. Clique com o botão direito do mouse no nome do projeto no Gerenciador de Soluções e, em seguida, clique em **Adicionar** > **Novo Item**.

4. Na janela Adicionar Novo Item, selecione **Arquivo de Texto**, digite *VirtualMachineTemplate.json* para o nome e clique em **Adicionar**.

5. Abra o arquivo VirtualMachineTemplate.json e, em seguida, adicione os colchetes de abertura e fechamento, o elemento de esquema necessário e o elemento contentVersion necessário:

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
	}
	```

6. [Parâmetros](../resource-group-authoring-templates.md#parameters) nem sempre são necessários, mas eles facilitam o gerenciamento de modelos. Eles descrevem o tipo do valor, o valor padrão, se necessário e, possivelmente, os valores permitidos do parâmetro. Neste tutorial, os parâmetros que são usados para criar uma máquina virtual, uma conta de armazenamento e uma rede virtual são adicionados ao modelo.

    Adicione o elemento parameters e seus elementos filho após o elemento contentVersion:

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"newStorageAccountName": { "type": "string" },
			"adminUserName": { "type": "string" },
			"adminPassword": { "type": "securestring" },
			"dnsNameForPublicIP": { "type": "string" },
			"windowsOSVersion": {
				"type": "string",
				"defaultValue": "2012-R2-Datacenter",
				"allowedValues": [ "2008-R2-SP1", "2012-Datacenter", "2012-R2-Datacenter" ]
			}
		},
 	}
	```

7. [Variáveis](../resource-group-authoring-templates.md#variables) podem ser usadas em um modelo para especificar valores que podem ser alterados com frequência ou que precisam ser criados com base em uma combinação de valores de parâmetros.

    Adicione o elemento variables após a seção de parâmetros:

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"newStorageAccountName": { "type": "string" },
			"adminUsername": { "type": "string" },
			"adminPassword": { "type": "securestring" },
			"dnsNameForPublicIP": { "type": "string" },
			"windowsOSVersion": {
				"type": "string",
				"defaultValue": "2012-R2-Datacenter",
				"allowedValues": ["2008-R2-SP1", "2012-Datacenter", "2012-R2-Datacenter"]
			}
		},
		"variables": {
			"location": "West US",
			"imagePublisher": "MicrosoftWindowsServer",
			"imageOffer": "WindowsServer",
			"OSDiskName": "osdiskforwindowssimple",
			"nicName": "myVMnic1",
			"addressPrefix": "10.0.0.0/16",
			"subnetName": "Subnet",
			"subnetPrefix": "10.0.0.0/24",
			"storageAccountType": "Standard_LRS",
			"publicIPAddressName": "myPublicIP",
			"publicIPAddressType": "Dynamic",
			"vmStorageAccountContainerName": "vhds",
			"vmName": "MyWindowsVM",
			"vmSize": "Standard_A2",
			"virtualNetworkName": "MyVNET",
			"vnetID":"[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
			"subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]"
		},
	}
	```

8. [Recursos](../resource-group-authoring-templates.md#resources) como a máquina virtual, a rede virtual e a conta de armazenamento são definidos no modelo em seguida.

    Adicione a seção de recursos após a seção de variáveis:

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"newStorageAccountName": { "type": "string" },
			"adminUsername": { "type": "string" },
			"adminPassword": { "type": "securestring" },
			"dnsNameForPublicIP": { "type": "string" },
			"windowsOSVersion": {
				"type": "string",
				"defaultValue": "2012-R2-Datacenter",
				"allowedValues": [ "2008-R2-SP1", "2012-Datacenter", "2012-R2-Datacenter"]
			}
		},
		"variables": {
			"location": "West US",
			"imagePublisher": "MicrosoftWindowsServer",
			"imageOffer": "WindowsServer",
			"OSDiskName": "osdiskforwindowssimple",
			"nicName": "myVMnic1",
			"addressPrefix": "10.0.0.0/16",
			"subnetName": "Subnet",
			"subnetPrefix": "10.0.0.0/24",
			"storageAccountType": "Standard_LRS",
			"publicIPAddressName": "myPublicIP",
			"publicIPAddressType": "Dynamic",
			"vmStorageAccountContainerName": "vhds",
			"vmName": "MyWindowsVM",
			"vmSize": "Standard_A2",
			"virtualNetworkName": "MyVNET",
			"vnetID":"[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
			"subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]"
		},
		"resources": [
			{
  			"apiVersion": "2015-05-01-preview",
				"type": "Microsoft.Storage/storageAccounts",
				"name": "[parameters('newStorageAccountName')]",
				"location": "[variables('location')]",
				"properties": {
					"accountType": "[variables('storageAccountType')]"
				}
			},
			{
				"apiVersion": "2015-05-01-preview",
				"type": "Microsoft.Network/publicIPAddresses",
				"name": "[variables('publicIPAddressName')]",
				"location": "[variables('location')]",
				"properties": {
					"publicIPAllocationMethod": "[variables('publicIPAddressType')]",
					"dnsSettings": {
						"domainNameLabel": "[parameters('dnsNameForPublicIP')]"
					}
				}
			},
			{
				"apiVersion": "2015-05-01-preview",
				"type": "Microsoft.Network/virtualNetworks",
				"name": "[variables('virtualNetworkName')]",
				"location": "[variables('location')]",
				"properties": {
					"addressSpace": {
						"addressPrefixes": [ "[variables('addressPrefix')]" ]
					},
					"subnets": [ {
						"name": "[variables('subnetName')]",
						"properties": {
							"addressPrefix": "[variables('subnetPrefix')]"
						}
					} ]
				}
			},
			{
				"apiVersion": "2015-05-01-preview",
				"type": "Microsoft.Network/networkInterfaces",
				"name": "[variables('nicName')]",
				"location": "[variables('location')]",
				"dependsOn": [
					"[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
					"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
				],
				"properties": {
					"ipConfigurations": [ {
						"name": "ipconfig1",
						"properties": {
							"privateIPAllocationMethod": "Dynamic",
							"publicIPAddress": {
								"id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))]"
							},
							"subnet": {
								"id": "[variables('subnetRef')]"
							}
						}
					} ]
				}
			},
			{
				"apiVersion": "2015-06-15",
				"type": "Microsoft.Compute/virtualMachines",
				"name": "[variables('vmName')]",
				"location": "[variables('location')]",
				"dependsOn": [
					"[concat('Microsoft.Storage/storageAccounts/', parameters('newStorageAccountName'))]",
					"[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
				],
				"properties": {
					"hardwareProfile": {
						"vmSize": "[variables('vmSize')]"
					},
					"osProfile": {
						"computerName": "[variables('vmName')]",
						"adminUsername": "[parameters('adminUsername')]",
						"adminPassword": "[parameters('adminPassword')]",
					},
					"storageProfile": {
						"imageReference": {
							"publisher": "[variables('imagePublisher')]",
							"offer": "[variables('imageOffer')]",
							"sku": "[parameters('windowsOSVersion')]",
							"version" : "latest"
						},
						"osDisk": {
							"name": "osdisk",
							"vhd": {
								"uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
							},
							"caching": "ReadWrite",
							"createOption": "FromImage"
						}
					},
					"networkProfile": {
						"networkInterfaces" : [ {
							"id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
						} ]
					}
				}
			} ]
		}
		```

9. Salve o arquivo de modelo que você criou.

### Criar o arquivo de parâmetros

Para especificar valores para os parâmetros de recursos que foram definidos no modelo, você cria um arquivo de parâmetros que contém os valores e o envia ao Gerenciador de Recursos com o modelo. No Visual Studio, faça o seguinte:

1. Clique com o botão direito do mouse do mouse no nome do projeto no Gerenciador de Soluções e, em seguida, clique em **Adicionar** e **Novo Item**.

2. Na janela Adicionar Novo Item, selecione **Arquivo de Texto**, digite *Parameters.json* para o nome e clique em **Adicionar**.

3. Abra o arquivo parameters.json e adicione o seguinte conteúdo JSON:

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"newStorageAccountName": { "value": "mytestsa1" },
			"adminUserName": { "value": "mytestacct1" },
			"adminPassword": { "value": "mytestpass1" },
			"dnsNameForPublicIP": { "value": "mytestdns1" }
		}
	}
	```

    >[AZURE.NOTE]Este tutorial cria uma máquina virtual executando uma versão do sistema operacional do Windows Server. Para saber mais sobre a seleção de outras imagens, confira [Navegar e selecionar imagens da máquina virtual do Azure com o Windows PowerShell e a CLI do Azure](resource-groups-vm-searching.md).


4. Salve o arquivo de parâmetros que você criou.

### Carregar os arquivos

O arquivo de modelo e o arquivo de parâmetros são acessados pelo Gerenciador de Recursos do Azure por meio de uma conta de armazenamento do Azure. Para colocar os arquivos no primeiro armazenamento que você criou, faça o seguinte:

1. Abra o Gerenciador de Servidores e navegue até o contêiner em sua conta de armazenamento em que deseja colocar o arquivo. Para este tutorial, o contêiner em que o modelo se encontra é denominado modelos.

2. No canto superior direito do painel do contêiner de modelos, clique no ícone Carregar Blob, navegue até o arquivo VirtualMachineTemplate.json que você criou e clique em **Abrir**.

3. Clique no ícone Carregar Blob novamente, navegue até o arquivo Parameters.json que você criou e, em seguida, clique em **Abrir**.

## Etapa 3: instalar as bibliotecas

Os pacotes NuGet são a maneira mais fácil de instalar as bibliotecas de que você precisa para concluir este tutorial. Você deve instalar a Biblioteca de Gerenciamento de Recursos do Azure e a Biblioteca de Autenticação do Active Directory do Azure. Para obter essas bibliotecas no Visual Studio, faça o seguinte:

1. Clique com o botão direito do mouse no nome no Gerenciador de Soluções e clique em **Gerenciar Pacotes NuGet**.

2. Digite *Active Directory* na caixa de pesquisa, clique em **Instalar** para o pacote da Biblioteca de Autenticação do Active Directory e siga as instruções para instalar o pacote.

3. Na parte superior da página, selecione **Incluir Pré-lançamento**. Digite *Microsoft.Azure.Management.Resources* na caixa de pesquisa, clique em **Instalar** para as Bibliotecas de Gerenciamento de Recursos do Microsoft Azure e siga as instruções para instalar o pacote.

Agora você está pronto para começar a usar as bibliotecas para criar seu aplicativo.

##Etapa 4: criar as credenciais que são usadas para autenticar solicitações

Agora que o aplicativo Active Directory do Azure foi criado e a biblioteca de autenticação está instalada, você pode formatar as informações do aplicativo em credenciais que são usadas para autenticar solicitações para o Gerenciador de Recursos do Azure. Faça o seguinte:

1. Abra o arquivo Program.cs para o projeto que você criou e, em seguida, adicione o seguinte usando instruções na parte superior do arquivo:

	```
	using Microsoft.Azure;
	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using Microsoft.Azure.Management.Resources;
	using Microsoft.Azure.Management.Resources.Models;
	using Microsoft.Rest;
	```

2.	Adicione o seguinte método à classe Program para obter o token necessário para criar as credenciais:

	```
	private static string GetAuthorizationHeader()
	{
		ClientCredential cc = new ClientCredential("{application-id}", "{password}");
		var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
		var result = context.AcquireTokenAsync("https://management.azure.com/", cc);
		if (result == null)
		{
			throw new InvalidOperationException("Failed to obtain the JWT token");
		}

		string token = result.AccessToken;

		return token;
	}
	```

	Substitua {id-aplicativo} pelo identificador do aplicativo que você registrou anteriormente, {senha} pela senha que você escolheu para o aplicativo AD e {id-locatário} pelo identificador do locatário para sua assinatura. Você pode encontrar a id do locatário executando Get-AzureSubscription.

3. Adicione o seguinte código ao método Main no arquivo Program.cs para criar as credenciais:

	```
	var token = GetAuthorizationHeader();
	var credential = new TokenCredentials(token);
	```

4. Salve o arquivo Program.cs.


## Etapa 5: adicionar o código para implantar o modelo

Os recursos sempre são implantados de um modelo em um grupo de recursos. Você usa o [Grupo de Recursos](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) e classes [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) para criar o grupo de recursos em que os recursos são implantados.

1. Adicione o seguinte método à classe Program para criar o grupo de recursos:

	```
	public static void CreateResourceGroup(
		TokenCredentials credential,
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the resource group...");
		var resourceManagementClient = new ResourceManagementClient(credential);
		resourceManagementClient.SubscriptionId = subscriptionId;
		var resourceGroup = new ResourceGroup {
			Location = location
		};
		var rgResult = resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, resourceGroup);
		Console.WriteLine(rgResult.StatusCode.Properties.ProvisioningState);
	}
	```

2. Adicione o seguinte código ao método Main para chamar o método que você acabou de adicionar:

	```
	CreateResourceGroup(
		credential,
		"{group-name}",
		"{subscription-id}",
		"{location}");
		Console.ReadLine();
	```

	Substitua {group-name} pelo nome que você deseja usar para o grupo de recursos. Substitua {subscription-id} pelo identificador de assinatura. Você pode obtê-lo executando Get-AzureSubscription. Substitua o local pela região em que você deseja criar os recursos, como "Oeste dos EUA".

3. Adicione o seguinte método à classe de Programa para implantar os recursos no grupo de recursos usando o modelo que você definiu:

	```
	public static void CreateTemplateDeployment(
		TokenCredentials credential,
		string groupName,
		string storageName,
		string deploymentName,
		string subscriptionId)
	{
		Console.WriteLine("Creating the template deployment...");
		var deployment = new Deployment();
		deployment.Properties = new DeploymentProperties
		{
			Mode = DeploymentMode.Incremental,
			TemplateLink = new TemplateLink
			{
				Uri = "https://{storage-name}.blob.core.windows.net/templates/VirtualMachineTemplate.json"
			},
			ParametersLink = new ParametersLink
			{
				Uri = "https://{storage-name}.blob.core.windows.net/templates/Parameters.json"
			}
		};
		var resourceManagementClient = new ResourceManagementClient(credential);
		resourceManagementClient.SubscriptionId = subscriptionId;
		var dpResult = resourceManagementClient.Deployments.CreateOrUpdate(
			groupName,
			deploymentName,
			deployment);
		Console.WriteLine(dpResult.Properties.ProvisioningState);
	}
	```

	Substitua {storage-name} pelo nome da conta em que você colocou os arquivos anteriormente.

4. Adicione o seguinte código ao método Main para chamar o método que você acabou de adicionar:

	```
	CreateTemplateDeployment(
		credential,
		"{group-name}",
		"{storage-name}",
		"{deployment-name}",
		"{subscription-id}");
	Console.ReadLine();
	```

    Substitua {group-name} pelo nome do grupo de recursos que deseja usar. Substitua {storage-name} pelo nome da conta de armazenamento em que colocou os arquivos do modelo. Substitua {deployment-name} pelo nome que você deseja usar para identificar o conjunto de recursos de implantação. Substitua {subscription-id} pelo identificador de assinatura. Você pode obtê-lo executando Get-AzureSubscription.

##Etapa 6: adicionar código para excluir os recursos

Como você é cobrado pelos recursos usados no Azure, sempre é uma boa prática excluir os recursos que não são mais necessários. Você não precisa excluir cada recurso separadamente de um grupo de recursos. Você pode excluir o grupo de recursos, e todos os seus recursos serão excluídos automaticamente.

1.	Adicione o seguinte método à classe Program para excluir o grupo de recursos:

	```
	public static void DeleteResourceGroup(
		TokenCredentials credential,
		string groupName,
		string subscriptionId)
	{
		Console.WriteLine("Deleting resource group...");
		var resourceGroupClient = new ResourceManagementClient(credential);
		resourceGroupClient.ResourceGroups.DeleteAsync(groupName);
	}
	```

2.	Adicione o seguinte código ao método Main para chamar o método que você acabou de adicionar:

	```
	DeleteResourceGroup(
		credential,
		"{group-name}",
		"{subscription-id}");
	Console.ReadLine();
	```
    Substitua {group-name} pelo nome do grupo de recursos que deseja usar. Substitua {subscription-id} pelo identificador de assinatura. Você pode obtê-lo executando Get-AzureSubscription.

##Etapa 7: executar o aplicativo de console

1.	Para executar o aplicativo de console, clique em **Iniciar** no Visual Studio e, em seguida, entre no AD do Azure usando o mesmo nome de usuário e senha que você usa com sua assinatura.

2.	Pressione **Enter** depois que o status Aceito for exibido.

	Devem ser necessários cerca de cinco minutos para o aplicativo de console executar completamente do início ao fim. Antes de pressionar Enter para iniciar a exclusão de recursos, você pode levar alguns minutos para verificar a criação de recursos no portal do Azure antes de excluí-los.

3. Procure os Logs de Auditoria no portal do Azure para ver o status dos recursos:

	![Criar um aplicativo do AD](./media/arm-template-deployment/crpportal.png)

<!---HONumber=AcomDC_0107_2016-->