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
ms.date: 03/01/2017
ms.author: davidmu
ms.translationtype: Human Translation
ms.sourcegitcommit: 61fd58063063d69e891d294e627ae40cb878d65b
ms.openlocfilehash: daff6ab4c0eaf17d1cb488f1c16aa111b6ed9a88
ms.contentlocale: pt-br
ms.lasthandoff: 06/22/2017

---
<a id="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template" class="xliff"></a>

# Implantar uma Máquina Virtual do Azure usando C# e um modelo do Resource Manager
Este artigo mostra como implantar um modelo do Azure Resource Manager usando o C#. O [modelo](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json) implanta uma única máquina virtual que executa o Windows Server em uma nova rede virtual com uma única sub-rede.

Para obter uma descrição detalhada do recurso de máquina virtual, confira [Virtual machines in an Azure Resource Manager template](template-description.md) (Máquinas virtuais em um modelo do Azure Resource Manager). Para saber mais sobre todos os recursos em um modelo, confira [Passo a passo do modelo do Azure Resource Manager](../../azure-resource-manager/resource-manager-template-walkthrough.md).

São necessários cerca de 10 minutos para a conclusão destas etapas.

<a id="step-1-create-a-visual-studio-project" class="xliff"></a>

## Etapa 1: Criar um projeto do Visual Studio

Nesta etapa, você verifica se o Visual Studio está instalado e cria um aplicativo de console usado para implantar o modelo.

1. Se você ainda não fez isso, instale o [Visual Studio](https://www.visualstudio.com/).
2. No Visual Studio, clique em **Arquivo** > **Novo** > **Projeto**.
3. Em **Modelos** > **Visual C#**, selecione **Aplicativo de Console (.NET Framework)**, digite o nome e a localização do projeto e, em seguida, clique em **OK**.

<a id="step-2-install-libraries" class="xliff"></a>

## Etapa 2: Instalar bibliotecas

Os pacotes NuGet são a maneira mais fácil de instalar as bibliotecas de que você precisa para concluir estas etapas. Você precisa instalar a Biblioteca do Azure Resource Manager e a Biblioteca de Autenticação do Active Directory para criar os recursos. Para obter essas bibliotecas no Visual Studio, siga estas etapas:

1. Clique com o botão direito do mouse no nome do projeto no Gerenciador de Soluções, clique em **Gerenciar Pacotes NuGet para a Solução...** e depois em **Procurar**.
2. Digite *Microsoft.IdentityModel.Clients.ActiveDirectory* na caixa de pesquisa, selecione seu projeto, clique em **Instalar** e siga as instruções para instalar o pacote.
3. Na parte superior da página, selecione **Incluir Pré-lançamento**. Digite *Microsoft.Azure.Management.ResourceManager* na caixa de pesquisa, clique em **Instalar** e siga as instruções para instalar o pacote.

Agora você está pronto para começar a usar as bibliotecas para criar seu aplicativo.

<a id="step-3-create-credentials-used-to-authenticate-requests" class="xliff"></a>

## Etapa 3: Criar as credenciais usadas para autenticar solicitações

Antes de começar essa etapa, verifique se você tem acesso a uma [entidade de serviço do Active Directory](../../resource-group-authenticate-service-principal.md). Na entidade de serviço, você adquire um token para autenticar as solicitações para o Azure Resource Manager.

1. Abra o arquivo Program.cs para o projeto que você criou e, em seguida, adicione o seguinte usando instruções para as instruções existentes na parte superior do arquivo:

    ```
    using Microsoft.Azure;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.Rest;
    using System.IO;
    ```

2. Adicione este método à classe Programa para obter o token necessário para criar as credenciais:

    ```
    private static async Task<AuthenticationResult> GetAccessTokenAsync()
    {
      var cc = new ClientCredential("client-id", "client-secret");
      var context = new AuthenticationContext("https://login.microsoftonline.com/tenant-id");
      var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
      if (token == null)
      {
        throw new InvalidOperationException("Could not get the token.");
      } 
      return token;
    }
    ```

    Substitua esses valores:
    
    - *client-id* com o identificador do aplicativo do Azure Active Directory. É possível encontrar esse identificador na folha Propriedades do aplicativo do AD. Para encontrar o aplicativo do AD no portal do Azure, clique em **Azure Active Directory** no menu de recursos e, em seguida, em **Registros do aplicativo**.
    - *client-secret* com a chave de acesso do aplicativo do AD. É possível encontrar esse identificador na folha Propriedades do aplicativo do AD.
    - *tenant-id* com o identificador de locatário da assinatura. É possível encontrar o identificador de locatário na folha Propriedades do Azure Active Directory no portal do Azure. Ele é rotulado *ID de Diretório*.

3. Para chamar o método que você acabou de adicionar, adicione este código ao método Main:

    ```
    var token = GetAccessTokenAsync();
    var credential = new TokenCredentials(token.Result.AccessToken);
    ```

4. Salve o arquivo Program.cs.

<a id="step-4-create-a-resource-group" class="xliff"></a>

## Etapa 4: Criar um grupo de recursos

Embora seja possível criar um grupo de recursos com base em um modelo, o modelo usado da galeria não cria um. Nesta etapa, você adiciona o código para criar um grupo de recursos.

1. Para especificar valores para o aplicativo, adicione variáveis ao método Main da classe Program:

    ```
    var groupName = "myResourceGroup";
    var subscriptionId = "subsciptionId";
    var deploymentName = "deploymentName";
    var location = "location";
    ```

    Substitua esses valores:
    
    - *myResourceGroup* com o nome do grupo de recursos que está sendo criado.
    - *subscriptionId* com o identificador de assinatura. É possível encontrar o identificador de assinatura na folha Assinaturas do portal do Azure.
    - *deploymentName* com o nome da implantação.
    - *location* com a [região do Azure](https://azure.microsoft.com/regions/) na qual você deseja criar os recursos.

2. Para criar o grupo de recursos, adicione este método à classe Program:

    ```
    public static async Task<ResourceGroup> CreateResourceGroupAsync(
      TokenCredentials credential,
      string groupName,
      string subscriptionId,
      string location)
    {
      var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };

      Console.WriteLine("Creating the resource group...");
      var resourceGroup = new ResourceGroup { Location = location };
      return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(
        groupName, 
        resourceGroup);
    }
    ```

3. Para chamar o método que você acabou de adicionar, adicione este código ao método Main:

    ```
    var rgResult = CreateResourceGroupAsync(
      credential,
      groupName,
      subscriptionId,
      location);
    Console.WriteLine(rgResult.Result.Properties.ProvisioningState);
    Console.ReadLine();
    ```

<a id="step-5-create-a-parameters-file" class="xliff"></a>

## Etapa 5: Criar o arquivo de parâmetros

Para especificar valores para os parâmetros de recursos definidos no modelo, você cria um arquivo de parâmetros que contém os valores. O arquivo de parâmetros é usado quando o modelo é implantado. O modelo que você está usando da galeria espera valores para os parâmetros *adminUserName*, *adminPassword* e *dnsLabelPrefix*.

No Visual Studio, siga estas etapas:

1. Clique com o botão direito do mouse no nome do projeto no Gerenciador de Soluções, clique em **Adicionar** > **Novo Item**.
2. Clique em **Web**, selecione **Arquivo JSON**, digite *Parameters.json* para o nome e clique em **Adicionar**.
3. Abra o arquivo Parameters.json e adicione o seguinte conteúdo JSON:

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUserName": { "value": "mytestacct1" },
        "adminPassword": { "value": "mytestpass1" },
        "dnsLabelPrefix": { "value": "mydns1" }
      }
    }
    ```

    Substitua os valores de parâmetro por valores que funcionam no ambiente.

4. Salve o arquivo Parameters.json.

<a id="step-6-deploy-a-template" class="xliff"></a>

## Etapa 6: Implantar um modelo

Neste exemplo, você implanta um modelo da galeria de modelos do Azure e fornece valores de parâmetro a ele com base no arquivo local criado. 

1. Para implantar o modelo, adicione este método à classe Program:

    ```
    public static async Task<DeploymentExtended> CreateTemplateDeploymentAsync(
      TokenCredentials credential,
      string groupName,
      string deploymentName,
      string subscriptionId)
    {
    
      var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };

      Console.WriteLine("Creating the template deployment...");
      var deployment = new Deployment();
      deployment.Properties = new DeploymentProperties
        {
          Mode = DeploymentMode.Incremental,
          TemplateLink = new TemplateLink("https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json"),
          Parameters = File.ReadAllText("..\\..\\Parameters.json")
        };
      
      return await resourceManagementClient.Deployments.CreateOrUpdateAsync(
        groupName,
        deploymentName,
        deployment
      );
    }
    ```

    Você também pode implantar um modelo de uma pasta local usando a propriedade Template em vez da propriedade TemplateLink.

2. Para chamar o método que você acabou de adicionar, adicione este código ao método Main:

    ```
    var dpResult = CreateTemplateDeploymentAsync(
      credential,
      groupName,
      deploymentName,
      subscriptionId);
    Console.WriteLine(dpResult.Result.Properties.ProvisioningState);
    Console.ReadLine();
    ```

<a id="step-7-delete-the-resources" class="xliff"></a>

## Etapa 7: Excluir os recursos

Como você é cobrado pelos recursos utilizados no Azure, sempre é uma boa prática excluir os recursos que não são mais necessários. Você não precisa excluir cada recurso separadamente de um grupo de recursos. Exclua o grupo de recursos e todos os seus recursos serão excluídos automaticamente.

1. Para excluir o grupo de recursos, adicione este método à classe Programa:

   ```
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
   ```

2. Para chamar o método que você acabou de adicionar, adicione este código ao método Main:

   ```
   DeleteResourceGroupAsync(
     credential,
     groupName,
     subscriptionId);
   Console.ReadLine();
   ```

<a id="step-8-run-the-console-application" class="xliff"></a>

## Etapa 8: Executar o aplicativo de console

Devem ser necessários cerca de cinco minutos para o aplicativo de console executar completamente do início ao fim. 

1. Para executar o aplicativo de console, clique em **Iniciar** no Visual Studio e, em seguida, entre no AD do Azure usando as mesmas credenciais que você usa com sua assinatura.

2. Pressione **Enter** depois que o status *Êxito* for exibido. 

    Você também verá **1 com Êxito** em Implantações, na folha Visão Geral do grupo de recursos no portal do Azure.

3. Antes de pressionar **Enter** para iniciar a exclusão de recursos, reserve alguns minutos para verificar a criação dos recursos no portal do Azure. Clique no status de implantação para ver informações sobre a implantação.

<a id="next-steps" class="xliff"></a>

## Próximas etapas
* Se houver problemas com a implantação, a próxima etapa será examinar [Troubleshoot common Azure deployment errors with Azure Resource Manager](../../resource-manager-common-deployment-errors.md) (Solucionar erros comuns de implantação do Azure com o Azure Resource Manager).
* Saiba como implantar uma máquina virtual e seus recursos de suporte examinando [Implantar uma máquina virtual do Azure usando o C#](csharp.md).

