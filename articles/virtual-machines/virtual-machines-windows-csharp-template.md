<properties
	pageTitle="Implantar uma VM usando C# e um modelo do Resource Manager | Microsoft Azure"
	description="Saiba como usar o C# e um modelo do Resource Manager para implantar uma VM do Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/18/2016"
	ms.author="davidmu"/>

# Implantar uma Máquina Virtual do Azure usando C# e um modelo do Resource Manager

Usando grupos de recursos e modelos, você pode gerenciar todos os recursos juntos que dão suporte ao seu aplicativo. Este artigo mostra como configurar a autenticação e o armazenamento usando o Azure PowerShell e, em seguida, criar e implantar um modelo usando C# para criar recursos do Azure.

Para concluir este tutorial, você precisará:

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)
- [Um token de autenticação](../resource-group-authenticate-service-principal.md)

São necessários cerca de 30 minutos para a conclusão destas etapas.

## Etapa 1: instalar o PowerShell do Azure

Consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter informações sobre como instalar a versão mais recente do Azure PowerShell, selecionar a assinatura que você deseja usar e entrar na sua conta do Azure.
    
## Etapa 2: Criar um grupo de recursos para o modelo de armazenamento

Todos os recursos devem estar implantados em um grupo de recursos. Consulte a [Visão geral do Azure Resource Manager](../resource-group-overview.md) para obter mais informações.

1. Obtenha uma lista dos locais disponíveis nos quais os recursos podem ser criados.

	    Get-AzureLocation | sort Name | Select Name

2. Substitua o valor **$locName** por um local na lista, por exemplo, **EUA Central**. Crie a variável.

        $locName = "location name"
        
3. Substitua o valor **$rgName** pelo nome do novo grupo de recursos. Crie a variável e o grupo de recursos.

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
        
    Você deverá ver algo assim:
    
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myrg1
    
## Etapa 3: Criar uma conta de armazenamento e o contêiner de modelos

Uma conta de armazenamento é necessária para armazenar o modelo que você pretende criar e implantar.

1. Substitua o valor $stName pelo nome (letras minúsculas e números somente) da conta de armazenamento. Teste a exclusividade do nome.

        $stName = "storage account name"
        Test-AzureName -Storage $stName

    Se esse comando retornar **False**, o nome proposto será exclusivo.
    
2. Agora, execute o comando para criar a conta de armazenamento.
    
        New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $stName -Type "Standard_LRS" -Location $locName
        
3. Substitua {blob-storage-endpoint} pelo ponto de extremidade do armazenamento de blobs em sua conta. Substitua {storage-account-name} pelo nome da sua conta de armazenamento. Substitua {primary-storage-key} pela chave de acesso primária. Execute estes comandos para criar o contêiner no qual os arquivos estão armazenados. Você pode obter os valores de ponto de extremidade e de chave do Portal do Azure.

        $ConnectionString = "DefaultEndpointsProtocol=http;BlobEndpoint={blob-storage-endpoint};AccountName={storage-account-name};AccountKey={primary-storage-key}"
        $ctx = New-AzureStorageContext -ConnnectionString $ConnectionString
        New-AzureStorageContainer -Name "templates" -Permission Blob -Context $ctx

## Etapa 3: Criar o projeto do Visual Studio, o arquivo de modelo e o arquivo de parâmetros

### Criar o arquivo de modelo

Um modelo do Gerenciador de Recursos do Azure permite implantar e gerenciar recursos do Azure juntos usando uma descrição JSON dos recursos e parâmetros de implantação associados. O modelo que você cria neste tutorial é semelhante a um modelo que pode ser encontrado na Galeria de modelos. Para saber mais, consulte [Implantar uma VM simples do Windows no Oeste dos EUA](https://azure.microsoft.com/documentation/templates/101-vm-simple-windows/).

No Visual Studio, faça o seguinte:

1. Clique em **Arquivo** > **Novo** > **Projeto**.

2. Em **Modelos** > **Visual C#**, selecione **Aplicativo de Console**, digite o nome e o local do projeto e, em seguida, clique em **OK**.

3. Clique com o botão direito do mouse no nome do projeto no Gerenciador de Soluções e, em seguida, clique em **Adicionar** > **Novo Item**.

4. Clique em Web, selecione Arquivo JSON, digite *VirtualMachineTemplate.json* para o nome e clique em **Adicionar**.

5. Nos colchetes de abertura e fechamento do arquivo VirtualMachineTemplate.json, adicione o elemento de esquema e o elemento contentVersion obrigatórios:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
        }

6. [Parâmetros](../resource-group-authoring-templates.md#parameters) nem sempre são necessários, mas eles facilitam o gerenciamento de modelos. Eles descrevem o tipo do valor, o valor padrão, se necessário e, possivelmente, os valores permitidos do parâmetro. Neste tutorial, os parâmetros que são usados para criar uma máquina virtual, uma conta de armazenamento e uma rede virtual são adicionados ao modelo. Adicione o elemento parameters e seus elementos filho após o elemento contentVersion:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": { "type": "string" },
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "dnsNameForPublicIP": { "type": "string" }
          },
        }

7. [Variáveis](../resource-group-authoring-templates.md#variables) podem ser usadas em um modelo para especificar valores que podem ser alterados com frequência ou que precisam ser criados com base em uma combinação de valores de parâmetros. Adicione o elemento variables após a seção de parâmetros:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": { "type": "string" },
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "dnsNameForPublicIP": { "type": "string" },
          },
          "variables": {
            "location": "West US",
            "imagePublisher": "MicrosoftWindowsServer",
            "imageOffer": "WindowsServer",
            "windowsOSVersion": "2012-R2-Datacenter",
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

8. [Recursos](../resource-group-authoring-templates.md#resources) como a máquina virtual, a rede virtual e a conta de armazenamento são definidos no modelo em seguida. Adicione a seção de recursos após a seção de variáveis:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "newStorageAccountName": { "type": "string" },
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "dnsNameForPublicIP": { "type": "string" }
          },
          "variables": {
            "location": "West US",
            "imagePublisher": "MicrosoftWindowsServer",
            "imageOffer": "WindowsServer",
            "OSDiskName": "osdiskforwindowssimple",
            "windowsOSVersion": "2012-R2-Datacenter",
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
            "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
          },
          "resources": [
            {
              "apiVersion": "2015-06-15",
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[parameters('newStorageAccountName')]",
              "location": "[variables('location')]",
              "properties": {
                "accountType": "[variables('storageAccountType')]"
              }
            },
            {
              "apiVersion": "2016-03-30",
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
              "apiVersion": "2016-03-30",
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
              "apiVersion": "2016-03-30",
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
              "apiVersion": "2016-03-30",
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
                    "sku": "[variables('windowsOSVersion')]",
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
      
9. Salve o arquivo de modelo que você criou.

### Criar o arquivo de parâmetros

Para especificar valores para os parâmetros de recursos que foram definidos no modelo, você cria um arquivo de parâmetros que contém os valores e o envia ao Gerenciador de Recursos com o modelo. No Visual Studio, faça o seguinte:

1. Clique com o botão direito do mouse do mouse no nome do projeto no Gerenciador de Soluções e, em seguida, clique em **Adicionar** e **Novo Item**.

2. Clique em Web, selecione Arquivo JSON, digite *Parameters.json* para o Nome e clique em **Adicionar**.

3. Abra o arquivo parameters.json e adicione o seguinte conteúdo JSON:

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

    >[AZURE.NOTE] Este artigo cria uma máquina virtual executando uma versão do sistema operacional do Windows Server. Para saber mais sobre a seleção de outras imagens, consulte [Navegar e selecionar imagens de máquina virtual do Azure com o Windows PowerShell e a CLI do Azure](virtual-machines-linux-cli-ps-findimage.md).

4. Salve o arquivo de parâmetros que você criou.

### Carregar os arquivos e definir permissão para usá-los 

O arquivo de modelo e o arquivo de parâmetros são acessados pelo Gerenciador de Recursos do Azure por meio de uma conta de armazenamento do Azure. Para colocar os arquivos no primeiro armazenamento que você criou, faça o seguinte:

1. Abra o Gerenciador de Nuvem e navegue até o contêiner de modelos na sua conta de armazenamento que você criou anteriormente.

2. Na janela do contêiner de modelos, clique no ícone Carregar Blob no canto superior direito, navegue até o arquivo VirtualMachineTemplate.json que você criou e clique em **Abrir**.

3. Clique no ícone Carregar Blob novamente, navegue até o arquivo Parameters.json que você criou e, em seguida, clique em **Abrir**.

## Etapa 4: Instalar as bibliotecas

Os pacotes NuGet são a maneira mais fácil de instalar as bibliotecas de que você precisa para concluir este tutorial. Você deve instalar a Biblioteca de Gerenciamento de Recursos do Azure e a Biblioteca de Autenticação do Active Directory do Azure. Para obter essas bibliotecas no Visual Studio, faça o seguinte:

1. Clique com o botão direito do mouse no nome no Gerenciador de Soluções e clique em **Gerenciar Pacotes NuGet**.

2. Digite *Active Directory* na caixa de pesquisa, clique em **Instalar** para o pacote da Biblioteca de Autenticação do Active Directory e siga as instruções para instalar o pacote.

4. Na parte superior da página, selecione **Incluir Pré-lançamento**. Digite *Microsoft.Azure.ResourceManager* na caixa de pesquisa, clique em **Instalar** para as Bibliotecas de Gerenciamento de Recursos do Microsoft Azure e siga as instruções para instalar o pacote.

Agora você está pronto para começar a usar as bibliotecas para criar seu aplicativo.

##Etapa 5: Criar as credenciais que são usadas para autenticar solicitações

O aplicativo do Azure Active Directory foi criado e a biblioteca de autenticação está instalada, agora você pode formatar as informações do aplicativo em credenciais que são usadas para autenticar solicitações para o Azure Resource Manager. Faça o seguinte:

1. Abra o arquivo Program.cs para o projeto que você criou e, em seguida, adicione o seguinte usando instruções na parte superior do arquivo:

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.Resources;
        using Microsoft.Azure.Management.Resources.Models;
        using Microsoft.Rest;

2.	Adicione este método à classe Programa para obter o token necessário para criar as credenciais:

        private static string GetAuthorizationHeader()
        {
          ClientCredential cc = new ClientCredential("{application-id}", "{password}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var result = context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (result == null)
          {
            throw new InvalidOperationException("Failed to obtain the JWT token");
          }

          string token = result.Result.AccessToken;

          return token;
        }

    Substitua {id-aplicativo} pelo identificador do aplicativo que você registrou anteriormente, {senha} pela senha que você escolheu para o aplicativo AD e {id-locatário} pelo identificador do locatário para sua assinatura. Você pode encontrar a ID do locatário executando Get-AzureRmSubscription.

3. Adicione este código ao método Main no arquivo Program.cs para criar as credenciais:

        var token = GetAuthorizationHeader();
        var credential = new TokenCredentials(token);

4. Salve o arquivo Program.cs.

## Etapa 6: Adicionar o código para implantar o modelo

Nesta etapa, você usará as classes [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) e [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) para criar o grupo de recursos em que os recursos são implantados.

1. Adicione variáveis ao método Principal da classe Programa para especificar os nomes que você deseja usar para os recursos, o local dos recursos, como "EUA Central", informações da conta de administrador e o identificador da assinatura:

        var groupName = "resource group name";
        var storageName = "storage account name";
        var vmName = "virtual machine name";  
        var deploymentName = "deployment name";
        var adminName = "administrator account name";
        var adminPassword = "administrator account password";
        var location = "location name";
        var subscriptionId = "subsciption id";

    Substitua todos os valores de variáveis pelos nomes e identificador que você deseja usar. Você pode encontrar o identificador da assinatura executando Get-AzureRmSubscription. O valor da variável storageName é o nome da conta de armazenamento na qual o modelo foi armazenado.
    
2. Adicione este método à classe Program para criar o grupo de recursos:

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
          Console.WriteLine(rgResult.Properties.ProvisioningState);
        }

2. Adicione este código ao método Main para chamar o método que você acabou de adicionar:

        CreateResourceGroup(
          credential,
          groupName,
          subscriptionId,
          location);
        Console.ReadLine();

3. Adicione este método à classe Programa para implantar os recursos no grupo de recursos usando o modelo que você definiu:

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
              Uri = "https://" + storageName + ".blob.core.windows.net/templates/VirtualMachineTemplate.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = "https://" + storageName + ".blob.core.windows.net/templates/Parameters.json"
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

4. Adicione este código ao método Main para chamar o método que você acabou de adicionar:

        CreateTemplateDeployment(
          credential,
          groupName",
          storageName,
          deploymentName,
          subscriptionId);
        Console.ReadLine();

##Etapa 7: Adicionar código para excluir os recursos

Como você é cobrado pelos recursos usados no Azure, sempre é uma boa prática excluir os recursos que não são mais necessários. Você não precisa excluir cada recurso separadamente de um grupo de recursos. Você pode excluir o grupo de recursos e todos os seus recursos serão excluídos automaticamente.

1.	Adicione este método à classe Programa para excluir o grupo de recursos:

        public static void DeleteResourceGroup(
          TokenCredentials credential,
          string groupName)
        {
          Console.WriteLine("Deleting resource group...");
          var resourceGroupClient = new ResourceManagementClient(credential);
          resourceGroupClient.ResourceGroups.DeleteAsync(groupName);
        }

2.	Adicione este código ao método Main para chamar o método que você acabou de adicionar:

        DeleteResourceGroup(
          credential,
          groupName);
        Console.ReadLine();

##Etapa 8: Executar o aplicativo de console

1.	Para executar o aplicativo de console, clique em **Iniciar** no Visual Studio e, em seguida, entre no AD do Azure usando o mesmo nome de usuário e senha que você usa com sua assinatura.

2.	Pressione **Enter** depois que o status Aceito for exibido.

	Devem ser necessários cerca de cinco minutos para o aplicativo de console executar completamente do início ao fim. Antes de pressionar Enter para iniciar a exclusão de recursos, você pode levar alguns minutos para verificar a criação de recursos no portal do Azure antes de excluí-los.

3. Procure os Logs de Auditoria no portal do Azure para ver o status dos recursos:

	![Procurar logs de auditoria no Portal do Azure](./media/virtual-machines-windows-csharp-template/crpportal.png)

## Próximas etapas

- Se houver problemas com a implantação, uma próxima etapa será examinar [Solucionando os problemas de implantações do grupo de recursos com o Portal do Azure](../resource-manager-troubleshoot-deployments-portal.md)
- Saiba como gerenciar a máquina virtual que você acabou de criar examinando [Gerenciar as máquinas virtuais usando o Azure Resource Manager e o PowerShell](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0615_2016-->