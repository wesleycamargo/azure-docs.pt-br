<properties
	pageTitle="Criar um IoT Hub usando a API REST | Microsoft Azure"
	description="Siga este tutorial para começar a usar o REST API e criar um Hub IoT."
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

Você pode usar a [API REST do Provedor de Recursos do IoT Hub][lnk-rest-api] para criar e gerenciar hubs IoT do Azure de forma programática. Este tutorial mostra como usar a API de REST do Provedor de Recursos para criar um hub IoT a partir de um programa C#.

> [AZURE.NOTE]O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e clássico](../resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação do Gerenciador de Recursos.

Para concluir este tutorial, você precisará do seguinte:

- Microsoft Visual Studio 2015.
- Uma conta ativa do Azure. <br/>Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos Para obter detalhes, consulte [Avaliação gratuita do Azure][lnk-free-trial].
- [Microsoft Azure PowerShell 1.0][lnk-powershell-install] ou posterior.

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## Preparar seu projeto do Visual Studio

1. No Visual Studio, crie um novo projeto do Windows do Visual C# usando o modelo de projeto de **Aplicativo do Console**. Nomeie o projeto **CreateIoTHubREST**.

2. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto e clique em **Gerenciar Pacotes NuGet**.

3. No Gerenciador de Pacotes NuGet, marque a opção **Incluir pré-lançamento** e procure por **Microsoft.Azure.Management.Resources**. Selecione versão **2.18.11-preview**. Clique em **Instalar**, em **Examinar Alterações**, em **OK** e em **Aceito** para aceitar as licenças.

4. No Gerenciador de Pacotes do NuGet, pesquise por **Microsoft.IdentityModel.Clients.ActiveDirectory**. Selecione a versão **2.19.208020213**. Clique em **Instalar**, em **Examinar Alterações**, em **OK** e em **Aceito** para aceitar a licença.

5. No Gerenciador de Pacotes NuGet, pesquise por **Microsoft.Azure.Common**. Selecione a versão **2.1.0**. Clique em **Instalar**, em **Examinar Alterações**, em **OK** e em **Aceito** para aceitar as licenças.

6. Em Program.cs, substitua as instruções **using** existentes pelo seguinte:

    ```
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    ```
    
7. Em Program.cs, adicione as variáveis estáticas a seguir, substituindo os valores de espaço reservado. Você fez uma anotação de **ApplicationId**, **SubscriptionId**, **TenantId**, e **Password** anteriormente neste tutorial. **Nome do grupo de recursos** é o nome do grupo de recursos que você usará ao criar o hub IoT; ele pode ser um grupo de recursos pré-existente ou um novo. **Nome do Hub IoT** é o nome do Hub IoT que você criará, como **MyIoTHub**. **Nome de implantação** é um nome para a implantação, como **Deployment\_01**.

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

## Use a API REST para criar um hub IoT

Use a API REST do Provedor de Recursos do Hub de IoT para criar um novo hub IoT em seu grupo de recursos. Você também pode usar a [API de REST][lnk-rest-api] para fazer alterações em um hub IoT existente.

1. Adicione o seguinte método ao Program.cs:
    
    ```
    static bool CreateIoTHub(ResourceManagementClient client, string token)
    {
        
    }
    ```

2. Adicione o seguinte código ao método **CreateIoTHub** para adicionar o token de autenticação à solicitação:

    ```
    client.HttpClient.DefaultRequestHeaders.Authorization = 
      new AuthenticationHeaderValue("Bearer", token);
    ```

3. Adicione o seguinte código ao método **CreateIoTHub** para descrever o hub IoT a fim de criar e gerar uma representação JSON:

    ```
    var description = new
    {
      name = iotHubName,
      location = "East US",
      sku = new
      {
        name = "S1",
        tier = "Standard",
        capacity = 1
      }
    };
    
    var content = new StringContent(
      JsonConvert.SerializeObject(description),
      Encoding.UTF8, "application/json");
    ```

4. Adicione o seguinte código ao método **CreateIoTHub** para enviar a solicitação do REST ao Azure e verifique a resposta:

    ```
    var requestUri = string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}?api-version=2015-08-15-preview",
      subscriptionId, rgName, iotHubName);
    var httpsRepsonse = client.HttpClient.PutAsync(
      requestUri, content).Result;

    if (!httpsRepsonse.IsSuccessStatusCode)
    {
      Console.WriteLine("Failed {0}", httpsRepsonse.Content.ReadAsStringAsync().Result);
      return false;
    }
    ```

5. Adicione o seguinte código ao final do método **CreateIoTHub** para esperar até a implantação ser concluída:

    ```
    ResourceGetResult resourceGetResult = null;
    do
    {
    resourceGetResult = client.Resources.GetAsync(
      rgName,
      new ResourceIdentity()
      {
        ResourceName = iotHubName,
        ResourceProviderApiVersion = "2015-08-15-preview",
        ResourceProviderNamespace = "Microsoft.Devices",
        ResourceType = "IotHubs"
      }).Result;
    Console.WriteLine("IoTHub state {0}", 
      resourceGetResult.Resource.ProvisioningState);
    } while (resourceGetResult.Resource.ProvisioningState != "Succeeded" 
          && resourceGetResult.Resource.ProvisioningState != "Failed");
    
    if (resourceGetResult.Resource.ProvisioningState != "Succeeded")
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
    if (CreateIoTHub(client, token.AccessToken))
        ShowIoTHubKeys(client, token.AccessToken);
    Console.ReadLine();
    ```
    
2. Clique em **Criar** e **Compilar Solução**. Corrija todos os erros.

3. Clique em **Depurar** e, em seguida, **Iniciar Depuração** para executar o aplicativo. Pode levar vários minutos para que a implantação seja executada.

4. Você pode verificar se o seu aplicativo adicionou o novo hub IoT visitando o [portal][lnk-azure-portal] e exibindo sua lista de recursos ou usando o cmdlet **Get-AzureRmResource** do PowerShell.

> [AZURE.NOTE]Este aplicativo de exemplo adiciona um IoT Hub de padrão S1 pelo qual você será cobrado. É possível excluir o hub IoT por meio do [portal][lnk-azure-portal] ou usando o cmdlet **Remove-AzureRmResource**do PowerShell quando tiver terminado.

## Próximas etapas

- Explore os recursos da [API REST do Provedor de Recursos do Hub IoT][lnk-rest-api].
- Leia a [Visão geral do Gerenciador de Recursos do Azure][lnk-azure-rm-overview] para saber mais sobre os recursos do Gerenciador de Recursos do Azure.

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-powershell-install]: https://azure.microsoft.com/pt-BR/blog/azps-1-0-pre/
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: https://azure.microsoft.com/documentation/articles/resource-group-overview/

<!---HONumber=AcomDC_0121_2016-->