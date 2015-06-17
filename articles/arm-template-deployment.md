<properties 
	pageTitle="Implantar recursos do Azure usando um modelo" 
	description="Saiba como usar alguns dos clientes disponíveis na Biblioteca de Gerenciamento de Recursos do Azure para implantar uma máquina virtual, rede virtual e conta de armazenamento" 
	services="virtual-machines,virtual-networks,storage" 
	documentationCenter="" 
	authors="davidmu1" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="davidmu"/>

# Implantar recursos do Azure usando bibliotecas .NET e um modelo

Usando grupos de recursos e modelos, você pode gerenciar em conjunto todos os recursos que dão suporte a seu aplicativo. Este tutorial mostra como usar alguns dos clientes disponíveis na Biblioteca de Gerenciamento de Recursos do Azure e como criar um modelo para implantar uma máquina virtual, rede virtual e conta de armazenamento.

[AZURE.INCLUDE [free-trial-note](../includes/free-trial-note.md)]

Para concluir este tutorial, você também precisa do seguinte:

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Conta de armazenamento do Azure](storage/storage-create-storage-account.md)
- [Windows Management Framework 3.0](http://www.microsoft.com/pt-br/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/pt-br/download/details.aspx?id=40855)
- [PowerShell do Azure](install-configure-powershell.md)

São necessários cerca de 30 minutos para a conclusão destas etapas.

## Etapa 1: adicionar um aplicativo ao AD do Azure e definir permissões

Para usar o AD do Azure para autenticar solicitações ao Gerenciador de Recursos do Azure, um aplicativo deve ser adicionado ao diretório padrão. Faça o seguinte para adicionar um aplicativo:

1. Abra um prompt de comando do Azure PowerShell e, em seguida, execute este comando:

        Switch-AzureMode –Name AzureResourceManager

2. Defina a conta do Azure que você deseja usar para este tutorial. Execute esse comando e digite as credenciais da sua assinatura quando solicitado:

	    Add-AzureAccount

3. Substitua {password} no comando a seguir pelo que você deseja usar e, em seguida, execute-o para criar o aplicativo:

	    New-AzureADApplication -DisplayName "My AD Application 1" -HomePage "https://myapp1.com" -IdentifierUris "https://myapp1.com"  -Password "{password}"

4. Registre o valor de ApplicationId na resposta da etapa anterior. Você precisará dele mais tarde neste tutorial:

	![Criar um aplicativo do AD](./media/arm-template-deployment/azureapplicationid.png)

	>[AZURE.NOTE]Você também pode encontrar o identificador do aplicativo no campo de id de cliente do aplicativo no Portal de Gerenciamento.

5. Substitua {application-id} pelo identificador que acabou de registrar e, em seguida, crie a entidade de serviço para o aplicativo:

        New-AzureADServicePrincipal -ApplicationId {application-id} 

6. Defina a permissão para usar o aplicativo:

	    New-AzureRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName "https://myapp1.com"

## Etapa 2: criar o projeto do Visual Studio, o arquivo de modelo e o arquivo de parâmetros

###Criar o arquivo de modelo

Um modelo do Gerenciador de Recursos do Azure permite implantar e gerenciar recursos do Azure juntos usando uma descrição JSON dos recursos e parâmetros de implantação associados. No Visual Studio, faça o seguinte:

1. Clique em **Arquivo** > **Novo** > **Projeto**.

2. Em **Modelos** > **Visual C#**, selecione **Aplicativo de Console**, digite o nome e o local do projeto e, em seguida, clique em **OK**.

3. Clique com o botão direito do mouse no nome do projeto no Gerenciador de Soluções e, em seguida, clique em **Adicionar** > **Novo Item**.

4.	Na janela Adicionar Novo Item, selecione **Arquivo de Texto**, digite *VirtualMachineTemplate.json* para o nome e clique em **Adicionar**.

5.	Abra o arquivo VirtualMachineTemplate.json e, em seguida, adicione os colchetes de abertura e fechamento, o elemento de esquema necessário e o elemento contentVersion necessário:

        {
            "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
            "contentVersion": "1.0.0.0",
        }

6. [Parâmetros](https://msdn.microsoft.com/library/azure/dn835138.aspx#parameters) nem sempre são necessários, mas eles facilitam o gerenciamento de modelos. Eles descrevem o tipo do valor, o valor padrão, se necessário e, possivelmente, os valores permitidos do parâmetro. Neste tutorial, os parâmetros que são usados para criar uma máquina virtual, uma conta de armazenamento e uma rede virtual são adicionados ao modelo.

    Adicione o elemento parameters e seus elementos filho após o elemento contentVersion:

		{
		  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
		  "contentVersion": "1.0.0.0",
		  "parameters": {
		    "location": { "type": "String", "defaultValue" : "West US", "allowedValues": [ "West US", "East US" ] },
            "newStorageAccountName": { "type": "string" },
            "storageAccountType": { "type": "string", "defaultValue": "Standard_LRS", "allowedValues": [ "Standard_LRS", "Standard_GRS" ] },
            "publicIPAddressName": { "type": "string" },
            "publicIPAddressType" : { "type" : "string", "defaultValue" : "Dynamic", "allowedValues" : [ "Dynamic" ] },
            "vmStorageAccountContainerName": { "type": "string", "defaultValue": "vhds" },
            "vmName": { "type": "string" },
            "vmSize": { "type": "string", "defaultValue": "Standard_A0", "allowedValues" : [ "Standard_A0", "Standard_A1" ] },
            "vmSourceImageName": { "type": "string" },
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "virtualNetworkName":{ "type" : "string" },
            "addressPrefix": { "type" : "string", "defaultValue" : "10.0.0.0/16" },
            "subnet1Name": { "type" : "string", "defaultValue" : "Subnet-1" },
            "subnet2Name": { "type" : "string", "defaultValue" : "Subnet-2" },
            "subnet1Prefix" : { "type" : "string", "defaultValue" : "10.0.0.0/24" },
            "subnet2Prefix" : { "type" : "string", "defaultValue" : "10.0.1.0/24" },
            "dnsName" : { "type" : "string" },
            "subscriptionId": { "type" : "string" },
            "nicName": { "type" : "string" }
          },
        }

7.	[Variáveis](https://msdn.microsoft.com/library/azure/dn835138.aspx#variables) podem ser usadas em um modelo para especificar valores que podem ser alterados com frequência ou que precisam ser criados com base em uma combinação de valores de parâmetros.

    Adicione o elemento variables após a seção de parâmetros:

		{
		  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
		  "contentVersion": "1.0.0.0",
		  "parameters": {
		    "location": { "type": "String", "defaultValue" : "West US", "allowedValues": [ "West US", "East US" ] },
            "newStorageAccountName": { "type": "string" },
            "storageAccountType": { "type": "string", "defaultValue": "Standard_LRS", "allowedValues": [ "Standard_LRS", "Standard_GRS" ] },
            "publicIPAddressName": { "type": "string" },
            "publicIPAddressType" : { "type" : "string", "defaultValue" : "Dynamic", "allowedValues" : [ "Dynamic" ] },
            "vmStorageAccountContainerName": { "type": "string", "defaultValue": "vhds" },
            "vmName": { "type": "string" },
            "vmSize": { "type": "string", "defaultValue": "Standard_A0", "allowedValues" : [ "Standard_A0", "Standard_A1" ] },
            "vmSourceImageName": { "type": "string" },
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "virtualNetworkName":{ "type" : "string" },
            "addressPrefix": { "type" : "string", "defaultValue" : "10.0.0.0/16" },
            "subnet1Name": { "type" : "string", "defaultValue" : "Subnet-1" },
            "subnet2Name": { "type" : "string", "defaultValue" : "Subnet-2" },
            "subnet1Prefix" : { "type" : "string", "defaultValue" : "10.0.0.0/24" },
            "subnet2Prefix" : { "type" : "string", "defaultValue" : "10.0.1.0/24" },
            "dnsName" : { "type" : "string" },
            "subscriptionId": { "type" : "string" },
            "nicName": { "type" : "string" }
          },
          "variables": {
            "sourceImageName" : "[concat('/',parameters('subscriptionId'),'/services/images/',parameters('vmSourceImageName'))]",
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
            "subnet1Ref" : "[concat(variables('vnetID'),'/subnets/',parameters('subnet1Name'))]"
          },
        }

8.	[Recursos](https://msdn.microsoft.com/library/azure/dn835138.aspx#resources) como a máquina virtual, a rede virtual e a conta de armazenamento são definidos no modelo em seguida.

    Adicione a seção de recursos após a seção de variáveis:

		{
		  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
		  "contentVersion": "1.0.0.0",
		  "parameters": {
		    "location": { "type": "String", "defaultValue" : "West US", "allowedValues": [ "West US", "East US" ] },
            "newStorageAccountName": { "type": "string" },
            "storageAccountType": { "type": "string", "defaultValue": "Standard_LRS", "allowedValues": [ "Standard_LRS", "Standard_GRS" ] },
            "publicIPAddressName": { "type": "string" },
            "publicIPAddressType" : { "type" : "string", "defaultValue" : "Dynamic", "allowedValues" : [ "Dynamic" ] },
            "vmStorageAccountContainerName": { "type": "string", "defaultValue": "vhds" },
            "vmName": { "type": "string" },
            "vmSize": { "type": "string", "defaultValue": "Standard_A0", "allowedValues" : [ "Standard_A0", "Standard_A1" ] },
            "vmSourceImageName": { "type": "string" },
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "virtualNetworkName":{ "type" : "string" },
            "addressPrefix": { "type" : "string", "defaultValue" : "10.0.0.0/16" },
            "subnet1Name": { "type" : "string", "defaultValue" : "Subnet-1" },
            "subnet2Name": { "type" : "string", "defaultValue" : "Subnet-2" },
            "subnet1Prefix" : { "type" : "string", "defaultValue" : "10.0.0.0/24" },
            "subnet2Prefix" : { "type" : "string", "defaultValue" : "10.0.1.0/24" },
            "dnsName" : { "type" : "string" },
            "subscriptionId": { "type" : "string" },
            "nicName": { "type" : "string" }
          },
          "variables": {
            "sourceImageName" : "[concat('/',parameters('subscriptionId'),'/services/images/',parameters('vmSourceImageName'))]",
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
            "subnet1Ref" : "[concat(variables('vnetID'),'/subnets/',parameters('subnet1Name'))]"
          },
          "resources": [ {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('newStorageAccountName')]",       
            "location": "[parameters('location')]",
            "properties": { "accountType": "[parameters('storageAccountType')]" }
          },
          {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[parameters('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": { 
              "publicIPAllocationMethod": "[parameters('publicIPAddressType')]",
              "dnsSettings": { "domainNameLabel": "[parameters('dnsName')]" }
            }
          },
          {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('virtualNetworkName')]",
            "location": "[parameters('location')]",
            "properties": {
              "addressSpace": { "addressPrefixes": [ "[parameters('addressPrefix')]" ] },
              "subnets": [ {
                "name": "[parameters('subnet1Name')]",
                "properties": { "addressPrefix": "[parameters('subnet1Prefix')]" }
              },
              {
                "name": "[parameters('subnet2Name')]",
                "properties": { "addressPrefix": "[parameters('subnet2Prefix')]" }
              } ]
            }
          },
          {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/networkInterfaces",
            "name": "[parameters('nicName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
              "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
            ],
            "properties": {
              "ipConfigurations": [ {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": {
                    "id": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddressName'))]"
                  },
                  "subnet": { "id": "[variables('subnet1Ref')]" }
                }
              } ]
            }
          },
          {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[parameters('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[concat('Microsoft.Storage/storageAccounts/', parameters('newStorageAccountName'))]",
              "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
            ],
            "properties": {
              "hardwareProfile": { "vmSize": "[parameters('vmSize')]" },
              "osProfile": {
                "computername": "[parameters('vmName')]",
                "adminUsername": "[parameters('adminUsername')]",
                "adminPassword": "[parameters('adminPassword')]",
                "windowsProfile": { "provisionVMAgent": "true" }
              },
              "storageProfile": { 
                "sourceImage": { "id": "[variables('sourceImageName')]" },
                "destinationVhdsContainer" : "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmStorageAccountContainerName'),'/')]"
              },
              "networkProfile": {
                "networkInterfaces" : [ {
                  "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                } ]
              }
            }
          } ]
        }

9.	Salve o arquivo de modelo que você criou.

###Criar o arquivo de parâmetros

Para especificar valores para os parâmetros de recursos que foram definidos no modelo, você cria um arquivo de parâmetros que contém os valores e o envia ao Gerenciador de Recursos com o modelo. No Visual Studio, faça o seguinte:

1.	Clique com o botão direito do mouse do mouse no nome do projeto no Gerenciador de Soluções e, em seguida, clique em **Adicionar** e **Novo Item**.

2.	Na janela Adicionar Novo Item, selecione **Arquivo de Texto**, digite *Parameters.json* para o nome e clique em **Adicionar**.

3.	Abra o arquivo parameters.json e adicione o seguinte conteúdo JSON:

        {
          "contentVersion": "1.0.0.0",
          "parameters": { 
            "vmName": { "value": "mytestvm1" },
            "newStorageAccountName": { "value": "mytestsa1" },
            "storageAccountType": { "value": "Standard_LRS" },
            "publicIPaddressName": { "value": "mytestip1" },
            "location": { "value": "West US" },
            "vmStorageAccountContainerName": { "value": "vhds" },
            "vmSize": { "value": "Standard_A1" },
            "subscriptionId": { "value": "{subscription-id}" },
            "vmSourceImageName": { "value": "{source-image-name}" },
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" },
            "virtualNetworkName": { "value": "mytestvn1" },
            "dnsName": { "value": "mytestdns1" }, 
            "nicName": { "value": "mytestnic1" } 
          }
        }

    >[AZURE.NOTE]Os nomes vhd de imagem são alterados regularmente na galeria de imagens, portanto, você precisa obter um nome de imagem atual para implantar a máquina virtual. Para fazer isso, consulte [Gerenciar imagens do Windows usando o Windows PowerShell](https://msdn.microsoft.com/library/azure/dn790330.aspx) e substitua {source-image-name} pelo nome do arquivo vhd que você deseja usar. Por exemplo, "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201412.01-en.us-127GB.vhd". Substitua {subscription-id} pelo identificador da sua assinatura.


4.	Salve o arquivo de parâmetros que você criou.

O arquivo de modelo e o arquivo de parâmetros são acessados pelo Gerenciador de Recursos do Azure por meio de uma conta de armazenamento do Azure. Para colocar os arquivos no armazenamento, faça o seguinte:

1.	Abra o Gerenciador de Servidores e navegue até o contêiner em sua conta de armazenamento em que deseja colocar o arquivo. Para este tutorial, o contêiner em que o modelo se encontra é denominado modelos.

2.	No canto superior direito do painel do contêiner de modelos, clique no ícone Carregar Blob, navegue até o arquivo VirtualMachineTemplate.json que você criou e clique em **Abrir**.

3. Clique no ícone Carregar Blob novamente, navegue até o arquivo Parameters.json que você criou e, em seguida, clique em **Abrir**.

##Etapa 3: instalar as bibliotecas

Os pacotes NuGet são a maneira mais fácil de instalar as bibliotecas de que você precisa para concluir este tutorial. Você deve instalar a Biblioteca de Gerenciamento de Recursos do Azure e a Biblioteca de Autenticação do Active Directory do Azure. Para obter essas bibliotecas no Visual Studio, faça o seguinte:

1.	Clique com o botão direito do mouse no nome no Gerenciador de Soluções e clique em **Gerenciar Pacotes NuGet**.

2.	Digite *Active Directory* na caixa de pesquisa, clique em **Instalar** para o pacote da Biblioteca de Autenticação do Active Directory e siga as instruções para instalar o pacote.

3.	Na parte superior da página, selecione **Incluir Pré-lançamento**. Digite *Gerenciamento de Recursos do Azure* na caixa de pesquisa, clique em **Instalar** para as Bibliotecas de Gerenciamento de Recursos do Microsoft Azure e siga as instruções para instalar o pacote.

Agora você está pronto para começar a usar as bibliotecas para criar seu aplicativo.

##Etapa 4: criar as credenciais que são usadas para autenticar solicitações

Agora que o aplicativo Active Directory do Azure foi criado e a biblioteca de autenticação está instalada, você pode formatar as informações do aplicativo em credenciais que são usadas para autenticar solicitações para o Gerenciador de Recursos do Azure. Faça o seguinte:

1.	Abra o arquivo Program.cs para o projeto que você criou e, em seguida, adicione o seguinte usando instruções na parte superior do arquivo:

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.Resources;
        using Microsoft.Azure.Management.Resources.Models;

2.	Adicione o seguinte método à classe Program para obter o token necessário para criar as credenciais:

        private static string GetAuthorizationHeader()
        {
          ClientCredential cc = new ClientCredential("{application-id}", "{password}");
            var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
            var result = context.AcquireToken("https://management.azure.com/", cc);
          if (result == null)
          {
            throw new InvalidOperationException("Failed to obtain the JWT token");
          }

          string token = result.AccessToken;

          return token;
        }

	Substitua {application-id} pelo identificador do aplicativo que você registrou anteriormente, {password} pela senha que você escolheu para o aplicativo AD e {tenant-id} pelo identificador do locatário para sua assinatura. Você pode encontrar a id do locatário executando Get-AzureSubscription.

3.	Adicione o seguinte código ao método Main no arquivo Program.cs para criar as credenciais:

		var token = GetAuthorizationHeader();
		var credential = new TokenCloudCredentials("{subscription-id}", token);

4.	Salve o arquivo Program.cs.


##Etapa 5: adicionar o código para implantar o modelo

Os recursos sempre são implantados de um modelo em um grupo de recursos. Você usa o [Grupo de Recursos](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) e classes [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) para criar o grupo de recursos em que os recursos são implantados.

1.	Adicione o seguinte método à classe Program para criar o grupo de recursos:

		public async static void CreateResourceGroup(TokenCloudCredentials credential)
		{
		  Console.WriteLine("Creating the resource group...");
		  var resourceGroup = new ResourceGroup { Location = "West US" };
		  using (var resourceManagementClient = new ResourceManagementClient(credential))
		  {
		    var rgResult = await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync("mytestrg1", resourceGroup);
		    Console.WriteLine(rgResult.StatusCode);
		  }
		}

2.	Adicione o seguinte código ao método Main para chamar o método que você acabou de adicionar:

		CreateResourceGroup(credential);
		Console.ReadLine();

3.	Adicione o seguinte método à classe de Programa para implantar os recursos no grupo de recursos usando o modelo que você definiu:

		public async static void CreateTemplateDeployment(TokenCloudCredentials credential)
		{
		  Console.WriteLine("Creating the template deployment...");
		  var deployment = new Deployment();
          deployment.Properties = new DeploymentProperties
		  {
		    Mode = DeploymentMode.Incremental,
		    TemplateLink = new TemplateLink
		    {
		      Uri = new Uri("https://{storage-account-name}.blob.core.windows.net/templates/VirtualMachineTemplate.json")
			},
			ParametersLink = new ParametersLink
			{
			  Uri = new Uri("https://{storage-account-name}.blob.core.windows.net/templates/Parameters.json")
			}
		  };
		  using (var templateDeploymentClient = new ResourceManagementClient(credential))
		  {
		    var dpResult = await templateDeploymentClient.Deployments.CreateOrUpdateAsync("mytestrg1", "mytestdp1", deployment);
			Console.WriteLine(dpResult.StatusCode);
		  }
		}

	Substitua {storage-account-name} pelo nome da conta em que você colocou os arquivos anteriormente.

4.	Adicione o seguinte código ao método Main para chamar o método que você acabou de adicionar:

		CreateTemplateDeployment(credential);
		Console.ReadLine();

##Etapa 6: adicionar código para excluir os recursos

Como você é cobrado pelos recursos usados no Azure, sempre é uma boa prática excluir os recursos que não são mais necessários. Você não precisa excluir cada recurso separadamente de um grupo de recursos. Você pode excluir o grupo de recursos, e todos os seus recursos serão excluídos automaticamente.

1.	Adicione o seguinte método à classe Program para excluir o grupo de recursos:

		public async static void DeleteResourceGroup(TokenCloudCredentials credential)
		{
		  using (var resourceGroupClient = new ResourceManagementClient(credential))
		  {
		    var rgResult = await resourceGroupClient.ResourceGroups.DeleteAsync("mytestrg1");
			Console.WriteLine(rgResult.StatusCode);
		  }
		}

2.	Adicione o seguinte código ao método Main para chamar o método que você acabou de adicionar:

		DeleteResourceGroup(credential);
		Console.ReadLine();

##Etapa 7: executar o aplicativo de console

1.	Para executar o aplicativo de console, clique em **Iniciar** no Visual Studio e, em seguida, entre no AD do Azure usando o mesmo nome de usuário e senha que você usa com sua assinatura.

2.	Pressione **Enter** após cada código de status ser retornado para criar cada recurso. Depois que a máquina virtual for criada, execute a próxima etapa antes de pressionar Enter para excluir todos os recursos.

	Devem ser necessários cerca de cinco minutos para o aplicativo de console executar completamente do início ao fim. Antes de pressionar Enter para iniciar a exclusão de recursos, você pode levar alguns minutos para verificar a criação de recursos no portal de visualização do Azure antes de excluí-los.

3. Procure os logs de auditoria no portal de visualização do Azure para ver o status dos recursos:

	![Criar um aplicativo do AD](./media/arm-template-deployment/crpportal.png)

<!---HONumber=58-->