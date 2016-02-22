<properties
	pageTitle="Criar um Hub IoT usando um modelo do Gerenciador de Recursos | Microsoft Azure"
	description="Siga este tutorial para começar a usar os modelos do Gerenciador de Recursos para criar um Hub IoT com um programa em C# ou com o PowerShell."
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="11/23/2015"
     ms.author="dobett"/>

# Tutorial: criar um hub IoT usando um programa do C#

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## Introdução

Você pode usar o Gerenciador de Recursos do Azure para criar e gerenciar hubs IoT do Azure de forma programática. Este tutorial mostra como usar um modelo do gerenciador de recursos para criar um hub IoT de um programa do C#.

> [AZURE.NOTE] O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e clássico](../resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação do Gerenciador de Recursos.

Para concluir este tutorial, você precisará do seguinte:

- Microsoft Visual Studio 2015.
- Uma conta ativa do Azure. <br/>Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos Para obter detalhes, consulte [Avaliação gratuita do Azure][lnk-free-trial].
- [Microsoft Azure PowerShell 1.0][lnk-powershell-install] ou posterior.

> [AZURE.TIP] Este artigo descreve como criar um novo Hub IoT usando um programa em C# e um modelo de ARM. Você também pode usar o [modelo ARM](#submit-a-template-to-create-an-iot-hub) no artigo atual com um script do PowerShell em vez de um programa C#. O artigo [Usando o Azure PowerShell com o Gerenciador de Recursos do Azure][lnk-powershell-arm] descreve como escrever um script do PowerShell que usa um modelo de ARM para criar um recurso do Azure como um Hub IoT.

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## Preparar seu projeto do Visual Studio

1. No Visual Studio, crie um novo projeto do Windows do Visual C# usando o modelo de projeto de **Aplicativo do Console**. Nomeie o projeto **CreateIoTHub**.

2. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto e clique em **Gerenciar Pacotes NuGet**.

3. No Gerenciador de Pacotes NuGet, pesquise **Microsoft.Azure.Management.Resources**. Selecione versão **2.18.11-preview**. Clique em **Instalar**, em **Examinar Alterações**, em **OK** e em **Aceito** para aceitar as licenças.

4. No Gerenciador de Pacotes do NuGet, pesquise por **Microsoft.IdentityModel.Clients.ActiveDirectory**. Selecione a versão **2.19.208020213**. Clique em **Instalar**, em **Examinar Alterações**, em **OK** e em **Aceito** para aceitar a licença.

5. No Gerenciador de Pacotes NuGet, pesquise por **Microsoft.Azure.Common**. Selecione a versão **2.1.0**. Clique em **Instalar**, em **Examinar Alterações**, em **OK** e em **Aceito** para aceitar as licenças.

6. Em Program.cs, substitua as instruções **using** existentes pelo seguinte:

    ```
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http.Headers;
    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```
    
7. Em Program.cs, adicione as variáveis estáticas a seguir, substituindo os valores de espaço reservado. Você fez uma anotação de **ApplicationId**, **SubscriptionId**, **TenantId** e **Password** anteriormente neste tutorial. **Nome do grupo de recursos** é o nome do grupo de recursos que você usará ao criar o Hub IoT; ele pode ser um grupo de recursos pré-existente ou um novo. **Nome do Hub IoT** é o nome do Hub IoT que você criará, como **MyIoTHub**. **Nome de implantação** é um nome para a implantação, como **Deployment\_01**.

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    
    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name}";
    static string deploymentName = "{Deployment name}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## Enviar um modelo para criar um hub IoT

Use um modelo JSON para criar um novo hub IoT em seu grupo de recursos. Você também pode usar um modelo para fazer alterações em um hub IoT existente.

1. No Gerenciador de Soluções, clique com botão direito do mouse no projeto, clique em **Adicionar** e em **Novo Item**. Adicione um novo arquivo JSON chamado **template.json** ao seu projeto.

2. No Gerenciador de Soluções, selecione **template.json** e, em **Propriedades**, defina **Copiar para Diretório de Saída** para **Copiar sempre**.

3. Substitua o conteúdo de **template.json** pela seguinte definição de recurso para adicionar um novo hub IoT padrão à região **Leste dos EUA**:

    ```
    {
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",

    "resources": [
      {
        "apiVersion": "2015-08-15-preview",
        "type": "Microsoft.Devices/Iothubs",
        "name": "[IotHubName]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      }
    ]
    }
    ```

4. Adicione o seguinte método ao Program.cs:
    
    ```
    static bool CreateIoTHub(ResourceManagementClient client)
    {
        
    }
    ```

5. Adicione o seguinte código ao método **CreateIoTHub** para carregar o arquivo de modelo, adicione o nome do hub IoT e envie o modelo para o gerenciador de recursos do Azure:

    ```
    string template = File.ReadAllText("template.json");
    template = template.Replace("[IotHubName]", iotHubName);
    var createResponse = client.Deployments.CreateOrUpdateAsync(
      rgName,
      deploymentName,
      new Deployment()
      {
        Properties = new DeploymentProperties
        {
          Mode = DeploymentMode.Incremental,
          Template = template
        }
      }).Result;
    ```

6. Adicione o seguinte código ao método **CreateIoTHub**, que aguarda até que a implantação seja concluída com êxito:

    ```
    string state = createResponse.Deployment.Properties.ProvisioningState;
    while (state != "Succeeded" && state != "Failed")
    {
      var getResponse = client.Deployments.GetAsync(
        rgName,
        deploymentName).Result;

      state = getResponse.Deployment.Properties.ProvisioningState;
      Console.WriteLine("Deployment state: {0}", state);
    }

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
      return false;
    }
    return true;
    ```

[AZURE.INCLUDE [iot-hub-retrieve-keys](../../includes/iot-hub-retrieve-keys.md)]

## Compilar e executar o aplicativo

Agora você pode concluir o aplicativo chamando os métodos **CreateIoTHub** e **ShowIoTHubKeys** antes de compilar e executá-lo.

1. Adicione o seguinte código ao final do método **Main**:

    ```
    if (CreateIoTHub(client))
        ShowIoTHubKeys(client, token.AccessToken);
    Console.ReadLine();
    ```
    
2. Clique em **Criar** e **Compilar Solução**. Corrija todos os erros.

3. Clique em **Depurar** e, em seguida, **Iniciar Depuração** para executar o aplicativo. Pode levar vários minutos para que a implantação seja executada.

4. Você pode verificar se o seu aplicativo adicionou o novo hub IoT visitando o [portal][lnk-azure-portal] e exibindo sua lista de recursos ou usando o cmdlet **Get-AzureRmResource** do PowerShell.

> [AZURE.NOTE] Este aplicativo de exemplo adiciona um IoT Hub de padrão S1 pelo qual você será cobrado. É possível excluir o hub IoT por meio do [portal][lnk-azure-portal] ou usando o cmdlet **Remove-AzureRmResource**do PowerShell quando tiver terminado.

## Próximas etapas

- Explore os recursos da [API REST do Provedor de Recursos do Hub IoT][lnk-rest-api].
- Leia a [Visão geral do Gerenciador de Recursos do Azure][lnk-azure-rm-overview] para saber mais sobre os recursos do Gerenciador de Recursos do Azure.

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-powershell-install]: https://azure.microsoft.com/pt-BR/blog/azps-1-0-pre/
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ./resource-group-overview.md
[lnk-powershell-arm]: ./powershell-azure-resource-manager.md

<!---HONumber=AcomDC_0211_2016-->