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
     ms.date="05/31/2016"
     ms.author="dobett"/>

# Tutorial: criar um Hub IoT usando um programa em C# e a API REST

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## Introdução

Você pode usar a [API REST do Provedor de Recursos do IoT Hub][lnk-rest-api] para criar e gerenciar hubs IoT do Azure de forma programática. Este tutorial mostra como usar a API de REST do Provedor de Recursos para criar um hub IoT a partir de um programa C#.

> [AZURE.NOTE] O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e clássico](../resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação do Gerenciador de Recursos.

Para concluir este tutorial, você precisará do seguinte:

- Microsoft Visual Studio 2015.
- Uma conta ativa do Azure. <br/>Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][lnk-free-trial].
- [Microsoft Azure PowerShell 1.0][lnk-powershell-install] ou posterior.

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## Preparar seu projeto do Visual Studio

1. No Visual Studio, crie um novo projeto do Windows do Visual C# usando o modelo de projeto de **Aplicativo do Console**. Nomeie o projeto **CreateIoTHubREST**.

2. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto e clique em **Gerenciar Pacotes NuGet**.

3. No Gerenciador de Pacotes NuGet, marque a opção **Incluir pré-lançamento** e procure **Microsoft.Azure.Management.ResourceManager**. Clique em **Instalar**, em **Examinar Alterações**, em **OK** e em **Aceito** para aceitar as licenças.

4. No Gerenciador de Pacotes do NuGet, pesquise por **Microsoft.IdentityModel.Clients.ActiveDirectory**. Clique em **Instalar**, em **Examinar Alterações**, em **OK** e em **Aceito** para aceitar a licença.

6. Em Program.cs, substitua as instruções **using** existentes pelo seguinte:

    ```
    using System;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    using Microsoft.Rest;
    using System.Linq;
    using System.Threading;
    using Newtonsoft.Json;
    ```
    
7. Em Program.cs, adicione as variáveis estáticas a seguir, substituindo os valores de espaço reservado. Você fez uma anotação de **ApplicationId**, **SubscriptionId**, **TenantId**, e **Password** anteriormente neste tutorial. **Nome do grupo de recursos** é o nome do grupo de recursos que você usará ao criar o hub IoT; ele pode ser um grupo de recursos pré-existente ou um novo. **Nome do Hub IoT** é o nome do Hub IoT que você criará, por exemplo **MyIoTHub** (observe que esse nome precisa ser globalmente exclusivo e, por isso, deve incluir seu nome e suas iniciais). **Nome de implantação** é um nome para a implantação, como **Deployment\_01**.

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    
    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name including your initials}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## Use a API REST para criar um hub IoT

Use a [API REST do Hub de IoT][lnk-rest-api] para criar um novo Hub IoT em seu grupo de recursos. Você também pode usar a API REST para fazer alterações em um Hub IoT existente.

1. Adicione o seguinte método ao Program.cs:
    
    ```
    static void CreateIoTHub(string token)
    {
        
    }
    ```

2. Adicione o seguinte código ao método **CreateIoTHub** para criar um **HttpClient** com um token de autenticação nos cabeçalhos:

    ```
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
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
    
    var json = JsonConvert.SerializeObject(description, Formatting.Indented);
    ```

4. Adicione o seguinte código ao método **CreateIoTHub** para enviar a solicitação REST ao Azure, verificar a resposta e recuperar a URL que você pode usar para monitorar o estado da tarefa de implantação:

    ```
    var content = new StringContent(JsonConvert.SerializeObject(description), Encoding.UTF8, "application/json");
    var requestUri = string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var result = client.PutAsync(requestUri, content).Result;
      
    if (!result.IsSuccessStatusCode)
    {
      Console.WriteLine("Failed {0}", result.Content.ReadAsStringAsync().Result);
      return;
    }
    
    var asyncStatusUri = result.Headers.GetValues("Azure-AsyncOperation").First();
    ```

5. Adicione o código a seguir ao final do método **CreateIoTHub** para usar o endereço **asyncStatusUri** recuperado na etapa anterior para aguardar a conclusão da implantação:

    ```
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{"Status":"Running"}");
    ```

6. Adicione o seguinte código ao final do método **CreateIoTHub** para recuperar as chaves do Hub IoT criado e imprimi-las no console:

    ```
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2015-08-15-preview", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;
    
    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```
    
## Compilar e executar o aplicativo

Agora, você pode concluir o aplicativo chamando o método **CreateIoTHub** antes de compilá-lo e de executá-lo.

1. Adicione o seguinte código ao final do método **Main**:

    ```
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```
    
2. Clique em **Criar** e **Compilar Solução**. Corrija todos os erros.

3. Clique em **Depurar** e, em seguida, **Iniciar Depuração** para executar o aplicativo. Pode levar vários minutos para que a implantação seja executada.

4. Você pode verificar se o seu aplicativo adicionou o novo hub IoT visitando o [portal][lnk-azure-portal] e exibindo sua lista de recursos ou usando o cmdlet **Get-AzureRmResource** do PowerShell.

> [AZURE.NOTE] Este aplicativo de exemplo adiciona um IoT Hub de padrão S1 pelo qual você será cobrado. É possível excluir o hub IoT por meio do [portal][lnk-azure-portal] ou usando o cmdlet **Remove-AzureRmResource** do PowerShell quando tiver terminado.

## Próximas etapas

Agora que você implantou um Hub IoT usando a API REST, convém explorar ainda mais:

- Leia sobre as funcionalidades da [API REST do provedor de recursos Hub IoT][lnk-rest-api].
- Leia a [Visão geral do Gerenciador de Recursos do Azure][lnk-azure-rm-overview] para saber mais sobre os recursos do Gerenciador de Recursos do Azure.

Para saber mais sobre como desenvolver para o Hub IoT, consulte o seguinte:

- [Introdução ao SDK de C][lnk-c-sdk]
- [SDKs do Hub IoT][lnk-sdks]

Para explorar melhor as funcionalidades do Hub IoT, consulte:

- [Projetar sua solução][lnk-design]
- [Explorar o gerenciamento de dispositivo usando a interface do usuário de exemplo][lnk-dmui]
- [Simular um dispositivo com o SDK do Gateway][lnk-gateway]
- [Usar o Portal do Azure para gerenciar o Hub IoT][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../resource-group-overview.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-sdks-summary.md

[lnk-design]: iot-hub-guidance.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0713_2016-->