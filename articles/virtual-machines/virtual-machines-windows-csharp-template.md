---
title: Implantar uma VM usando C# e um modelo do Resource Manager | Microsoft Docs
description: Saiba como usar o C# e um modelo do Resource Manager para implantar uma VM do Azure.
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: bfba66e8-c923-4df2-900a-0c2643b81240
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: e04b6871a119c9022aeb42a8e3063a224ff50db0


---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>Implantar uma Máquina Virtual do Azure usando C# e um modelo do Resource Manager
Usando grupos de recursos e modelos, você pode gerenciar todos os recursos juntos que dão suporte ao seu aplicativo. Este artigo mostra como usar o Visual Studio e o C# para configurar a autenticação, criar um modelo e, em seguida, implantar recursos do Azure usando o modelo que você criou.

Primeiro, você precisa realizar as seguintes etapas:

* Instalar o [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
* Verificar a instalação do [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)
* Obter um [token de autenticação](../resource-group-authenticate-service-principal.md)
* Criar um grupo de recursos usando [Azure PowerShell](../resource-group-template-deploy.md), [CLI do Azure](../resource-group-template-deploy-cli.md), ou [portal do Azure](../resource-group-template-deploy-portal.md).

São necessários cerca de 30 minutos para a conclusão destas etapas.

## <a name="step-1-create-the-visual-studio-project-the-template-file-and-the-parameters-file"></a>Etapa 1: Criar o projeto do Visual Studio, o arquivo de modelo e o arquivo de parâmetros
### <a name="create-the-template-file"></a>Criar o arquivo de modelo
Um modelo do Azure Resource Manager possibilita implantar e gerenciar recursos do Azure ao mesmo tempo. O modelo é uma descrição JSON dos recursos e parâmetros de implantação associados.

No Visual Studio, siga estas etapas:

1. Clique em **Arquivo** > **Novo** > **Projeto**.
2. Em **Modelos** > **Visual C#**, selecione **Aplicativo de Console**, digite o nome e o local do projeto e, em seguida, clique em **OK**.
3. Clique com o botão direito do mouse no nome do projeto no Gerenciador de Soluções, clique em **Adicionar** > **Novo Item**.
4. Clique em Web, selecione Arquivo JSON, digite *VirtualMachineTemplate.json* para o nome e clique em **Adicionar**.
5. Nos colchetes de abertura e fechamento do arquivo VirtualMachineTemplate.json, adicione o elemento de esquema e o elemento contentVersion obrigatórios:
   
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
        }
6. [Parâmetros](../resource-group-authoring-templates.md#parameters) nem sempre são necessários, mas eles fornecem uma maneira de entrar valores quando o modelo é implantado. Adicione o elemento parameters e seus elementos filho após o elemento contentVersion:
   
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
        }
7. [Variáveis](../resource-group-authoring-templates.md#variables) podem ser usadas em um modelo para especificar valores que podem ser alterados com frequência ou que precisam ser criados com base em uma combinação de valores de parâmetros. Adicione o elemento variables após a seção de parâmetros:
   
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"  
          },
        }
8. [Recursos](../resource-group-authoring-templates.md#resources) como a máquina virtual, a rede virtual e a conta de armazenamento são definidos no modelo em seguida. Adicione a seção de recursos após a seção de variáveis:
   
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "mystorage1",
              "apiVersion": "2015-06-15",
              "location": "[resourceGroup().location]",
              "properties": { "accountType": "Standard_LRS" }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/publicIPAddresses",
              "name": "myip1",
              "location": "[resourceGroup().location]",
              "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": { "domainNameLabel": "mydns1" }
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/virtualNetworks",
              "name": "myvnet1",
              "location": "[resourceGroup().location]",
              "properties": {
                "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
                "subnets": [ {
                  "name": "mysn1",
                  "properties": { "addressPrefix": "10.0.0.0/24" }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/networkInterfaces",
              "name": "mync1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/publicIPAddresses/myip1",
                "Microsoft.Network/virtualNetworks/myvn1"
              ],
              "properties": {
                "ipConfigurations": [ {
                  "name": "ipconfig1",
                  "properties": {
                    "privateIPAllocationMethod": "Dynamic",
                    "publicIPAddress": {
                      "id": "[resourceId('Microsoft.Network/publicIPAddresses', 'myip1')]"
                    },
                    "subnet": { "id": "[variables('subnetRef')]" }
                  }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Compute/virtualMachines",
              "name": "myvm1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/networkInterfaces/mync1",
                "Microsoft.Storage/storageAccounts/mystorage1"
              ],
              "properties": {
                "hardwareProfile": { "vmSize": "Standard_A1" },
                "osProfile": {
                  "computerName": "myvm1",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                  "imageReference": {
                    "publisher": "MicrosoftWindowsServer",
                    "offer": "WindowsServer",
                    "sku": "2012-R2-Datacenter",
                    "version" : "latest"
                  },
                  "osDisk": {
                    "name": "myosdisk1",
                    "vhd": {
                      "uri": "https://mystorage1.blob.core.windows.net/vhds/myosdisk1.vhd"
                    },
                    "caching": "ReadWrite",
                    "createOption": "FromImage"
                  }
                },
                "networkProfile": {
                  "networkInterfaces" : [ {
                    "id": "[resourceId('Microsoft.Network/networkInterfaces','mync1')]"
                  } ]
                }
              }
            } ]
          }
9. Salve o arquivo de modelo que você criou.

### <a name="create-the-parameters-file"></a>Criar o arquivo de parâmetros
Para especificar valores para os parâmetros de recursos que foram definidos no modelo, você cria um arquivo de parâmetros que contém os valores usados quando o modelo é implantado. No Visual Studio, siga estas etapas:

1. Clique com o botão direito do mouse no nome do projeto no Gerenciador de Soluções, clique em **Adicionar** > **Novo Item**.
2. Clique em Web, selecione Arquivo JSON, digite *Parameters.json* para o Nome e clique em **Adicionar**.
3. Abra o arquivo parameters.json e adicione o seguinte conteúdo JSON:
   
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" }
          }
        }
   
   > [!NOTE]
   > Este artigo cria uma máquina virtual executando uma versão do sistema operacional do Windows Server. Para saber mais sobre a seleção de outras imagens, veja [Navegar e selecionar imagens de máquina virtual do Azure com o Windows PowerShell e a CLI do Azure](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
   > 
   > 
4. Salve o arquivo de parâmetros que você criou.

## <a name="step-2-install-the-libraries"></a>Etapa 2: Instalar as bibliotecas
Os pacotes NuGet são a maneira mais fácil de instalar as bibliotecas de que você precisa para concluir este tutorial. Você precisa instalar a Biblioteca de Gerenciamento de Recursos do Azure e a Biblioteca de Autenticação do Azure Active Directory para criar os recursos. Para obter essas bibliotecas no Visual Studio, siga estas etapas:

1. Clique com o botão direito do mouse no nome no Gerenciador de Soluções, clique em **Gerenciar Pacotes NuGet** e, em seguida, clique em Procurar.
2. Digite *Active Directory* na caixa de pesquisa, clique em **Instalar** para o pacote da Biblioteca de Autenticação do Active Directory e siga as instruções para instalar o pacote.
3. Na parte superior da página, selecione **Incluir Pré-lançamento**. Digite *Microsoft.Azure.Management.ResourceManager* na caixa de pesquisa, clique em **Instalar** para as Bibliotecas de Gerenciamento de Recursos do Microsoft Azure e siga as instruções para instalar o pacote.

Agora você está pronto para começar a usar as bibliotecas para criar seu aplicativo.

## <a name="step-3-create-the-credentials-that-are-used-to-authenticate-requests"></a>Etapa 3: criar as credenciais que são usadas para autenticar solicitações
O aplicativo do Azure Active Directory é criado e a biblioteca de autenticação está instalada. Agora você pode formatar as informações do aplicativo em credenciais que são usadas para autenticar solicitações para o Azure Resource Manager.

1. Abra o arquivo Program.cs para o projeto que você criou e, em seguida, adicione o seguinte usando instruções na parte superior do arquivo:
   
        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.Azure.Management.ResourceManager.Models;
        using Microsoft.Rest;
        using System.IO;
2. Adicione este método à classe Programa para obter o token necessário para criar as credenciais:
   
     private static async Task<AuthenticationResult> GetAccessTokenAsync()   {
   
       var cc = new ClientCredential("{client-id}", "{client-secret}");
       var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
       var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
       if (token == null)
       {
         throw new InvalidOperationException("Could not get the token.");
       }
       return token;
     }
   
   Substitua {client-id} pelo identificador do aplicativo do Azure Active Directory, {client-secret} pela chave de acesso do aplicativo do AD e {tenant-id} pelo identificador do locatário da sua assinatura. Você pode encontrar a ID do locatário executando Get-AzureRmSubscription. Você pode encontrar a chave de acesso usando o portal do Azure.
3. Para criar as credenciais, adicione este código ao método Main no arquivo Program.cs:
   
        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);
4. Salve o arquivo Program.cs.

## <a name="step-4-deploy-the-template"></a>Etapa 4: Implantar o modelo
Nesta etapa, você usa o grupo de recursos que você criou anteriormente, mas você também pode criar um grupo de recursos usando as classes [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) e [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx).

1. Adicione variáveis ao método Principal da classe Programa para especificar os nomes dos recursos que você criou anteriormente, o nome da implantação e o identificador da assinatura:
   
        var groupName = "resource group name";
        var subscriptionId = "subsciption id";
        var deploymentName = "deployment name";
   
    Substitua o valor groupName pelo nome do novo grupo de recursos. Substitua o valor de deploymentName pelo nome que você deseja usar para a implantação. Você pode encontrar o identificador da assinatura executando Get-AzureRmSubscription.
2. Adicione este método à classe Programa para implantar os recursos no grupo de recursos usando o modelo que você definiu:
   
        public static async Task<DeploymentExtended> CreateTemplateDeploymentAsync(
          TokenCredentials credential,
          string groupName,
          string deploymentName,
          string subscriptionId)
        {
          Console.WriteLine("Creating the template deployment...");
          var deployment = new Deployment();
          deployment.Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            Template = File.ReadAllText("..\\..\\VirtualMachineTemplate.json"),
            Parameters = File.ReadAllText("..\\..\\Parameters.json")
          };
          var resourceManagementClient = new ResourceManagementClient(credential) 
            { SubscriptionId = subscriptionId };
          return await resourceManagementClient.Deployments.CreateOrUpdateAsync(
            groupName,
            deploymentName,
            deployment);
        }
   
    Se você quiser implantar o modelo de uma conta de armazenamento, você pode substituir a propriedade Template pela propriedade TemplateLink.
3. Para chamar o método que você acabou de adicionar, adicione este código ao método Main:
   
        var dpResult = CreateTemplateDeploymentAsync(
          credential,
          groupName,
          deploymentName,
          subscriptionId);
        Console.WriteLine(dpResult.Result.Properties.ProvisioningState);
        Console.ReadLine();

## <a name="step-5-delete-the-resources"></a>Etapa 5: Excluir os recursos
Como você é cobrado pelos recursos usados no Azure, sempre é uma boa prática excluir os recursos que não são mais necessários. Você não precisa excluir cada recurso separadamente de um grupo de recursos. Exclua o grupo de recursos e todos os seus recursos serão excluídos automaticamente.

1. Para excluir o grupo de recursos, adicione este método à classe Programa:
   
     public static async void DeleteResourceGroupAsync(
   
       TokenCredentials credential,
       string groupName,
       string subscriptionId)
     {
   
       Console.WriteLine("Deleting resource group...");
       var resourceManagementClient = new ResourceManagementClient(credential)
         { SubscriptionId = subscriptionId };
       await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
     }
2. Para chamar o método que você acabou de adicionar, adicione este código ao método Main:
   
     DeleteResourceGroupAsync(
   
       credential,
       groupName,
       subscriptionId);
     Console.ReadLine();

## <a name="step-6-run-the-console-application"></a>Etapa 6: executar o aplicativo de console
1. Para executar o aplicativo de console, clique em **Iniciar** no Visual Studio e, em seguida, entre no AD do Azure usando as mesmas credenciais que você usa com sua assinatura.
2. Pressione **Enter** depois que o status Aceito for exibido.
   
   Devem ser necessários cerca de cinco minutos para o aplicativo de console executar completamente do início ao fim. Antes de pressionar Enter para iniciar a exclusão de recursos, você pode levar alguns minutos para verificar a criação de recursos no portal do Azure antes de excluí-los.
3. Para ver o status dos recursos, Procure os Logs de Auditoria no portal do Azure:
   
    ![Procurar logs de auditoria no Portal do Azure](./media/virtual-machines-windows-csharp-template/crpportal.png)

## <a name="next-steps"></a>Próximas etapas
* Se houver problemas com a implantação, uma próxima etapa será examinar [Solucionando problemas de implantações do grupo de recursos com o portal do Azure](../resource-manager-troubleshoot-deployments-portal.md).
* Saiba como gerenciar a máquina virtual que você criou examinando [Gerenciar Máquinas Virtuais usando o Azure Resource Manager e o PowerShell](virtual-machines-windows-csharp-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




<!--HONumber=Nov16_HO3-->


