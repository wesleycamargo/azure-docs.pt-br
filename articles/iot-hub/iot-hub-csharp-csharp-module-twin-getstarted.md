---
title: Introdução à identidade do módulo e ao módulo gêmeo do Hub IoT do Azure (.NET) | Microsoft Docs
description: Saiba como criar a identidade do módulo e atualizar o módulo gêmeo usando SDKs de IoT para .NET.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: 14fe23df22bde82ad4a711ce516190d728ede11b
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51236594"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-net-back-end-and-net-device"></a>Introdução à identidade do módulo e ao módulo gêmeo do Hub IoT usando back-end .NET e dispositivo .NET

> [!NOTE]
> [As identidades do módulo e os módulos gêmeos](iot-hub-devguide-module-twins.md) são semelhantes à identidade do dispositivo e ao dispositivo gêmeo do Hub IoT do Azure, mas fornecem melhor granularidade. Enquanto a identidade do dispositivo e o dispositivo gêmeo do Hub IoT do Azure permitem que o aplicativo de back-end configure um dispositivo e forneça visibilidade às condições do dispositivo, uma identidade de módulo e módulo gêmeo fornecem esses recursos para componentes individuais de um dispositivo. Em dispositivos habilitados com vários componentes, como dispositivos baseados em sistema operacional ou dispositivos de firmware, permite condições e configuração isolada para cada componente.

No fim deste tutorial, você terá dois aplicativos de console .NET:

* **CreateIdentities**, que cria uma identidade de dispositivo, uma identidade de módulo e uma chave de segurança associada para conectar seus clientes de dispositivo e módulo.

* **UpdateModuleTwinReportedProperties**, que envia propriedades relatadas atualizadas de módulo gêmeo ao Hub IoT.

> [!NOTE]
> Para obter informações sobre os SDKs de IoT do Azure que você pode usar para criar aplicativos executados em dispositivos e no back-end da solução, veja [SDKs de IoT do Azure](iot-hub-devguide-sdks.md).

Para concluir este tutorial, você precisará do seguinte:

* Visual Studio 2017.

* Uma conta ativa do Azure. (Se você não tiver uma conta, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Agora, você criou seu hub IoT e tem o nome de host e a cadeia de conexão de Hub IoT que precisa para concluir o restante deste tutorial.

[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]


## <a name="update-the-module-twin-using-net-device-sdk"></a>Atualizar o módulo gêmeo usando o SDK do dispositivo .NET

Nesta seção, você criará um aplicativo de console .NET em seu dispositivo simulado que atualiza as propriedades relatadas do módulo gêmeo.

1. **Criar um projeto do Visual Studio:** No Visual Studio, adicione um projeto da Área de Trabalho Clássica do Windows no Visual C# à solução existente usando o modelo de projeto **Aplicativo do Console (.NET Framework)**. Verifique se a versão do .NET Framework é 4.6.1 ou posterior. Nomeie o projeto **UpdateModuleTwinReportedProperties**.

    ![Criar um projeto do Visual Studio](./media/iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.JPG)

2. **Instalar o SDK mais recente do dispositivo .NET do Hub IoT do Azure:** a identidade do módulo e o módulo gêmeo estão em visualização pública. Está disponível apenas nos SDKs do dispositivo do Hub IoT pré-lançamento. No Visual Studio, abra ferramentas > gerenciamento de pacotes Nuget > gerenciar pacotes Nuget para solução. Pesquise Microsoft.Azure.Devices.Client. Verifique se você marcou a caixa de seleção de incluir pré-lançamento. Selecione a versão mais recente e instale. Agora você tem acesso a todos os recursos do módulo. 

    ![Instalar o SDK V1.16.0-preview-005 do serviço .NET do Hub IoT](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

3. **Obter a cadeia de conexão do módulo** – agora se fizer logon no [portal do Azure](https://portal.azure.com/). Navegue até seu Hub IoT e clique em Dispositivos IoT. Localize myFirstDevice, abra-o e você verá que myFirstModule foi criado com êxito. Copie a cadeia de conexão do módulo. Ela será necessária na próxima etapa.

    ![Detalhes do módulo do Portal do Azure](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-detail.JPG)

4. **Criar um aplicativo de console UpdateModuleTwinReportedProperties**

    Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs** :

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    ```

    Adicione os seguintes campos à classe **Program** . Substitua o valor de espaço reservado pela cadeia de conexão do módulo.

    ```csharp
    private const string ModuleConnectionString = 
      "<Your module connection string>";
    private static ModuleClient Client = null;
    static void ConnectionStatusChangeHandler(ConnectionStatus status, 
      ConnectionStatusChangeReason reason)
    {
        Console.WriteLine("Connection Status Changed to {0}; the reason is {1}", 
          status, reason);
    }
    ```

    Adicione o seguinte método **OnDesiredPropertyChanged** à classe de **Programa**:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
        {
            Console.WriteLine("desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
            Console.WriteLine("Sending current time as reported property");
            TwinCollection reportedProperties = new TwinCollection
            {
                ["DateTimeLastDesiredPropertyChangeReceived"] = DateTime.Now
            };

            await Client.UpdateReportedPropertiesAsync(reportedProperties).ConfigureAwait(false);
        }
    ```

    Por fim, adicione as seguintes linhas ao método **Principal** :

    ```csharp
    static void Main(string[] args)
    {
        Microsoft.Azure.Devices.Client.TransportType transport = 
          Microsoft.Azure.Devices.Client.TransportType.Amqp;

        try
        {
            Client = 
              ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
            Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

            Console.WriteLine("Retrieving twin");
            var twinTask = Client.GetTwinAsync();
            twinTask.Wait();
            var twin = twinTask.Result;
            Console.WriteLine(JsonConvert.SerializeObject(twin.Properties)); 

            Console.WriteLine("Sending app start time as reported property");
            TwinCollection reportedProperties = new TwinCollection();
            reportedProperties["DateTimeLastAppLaunch"] = DateTime.Now;

            Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (AggregateException ex)
        {
            Console.WriteLine("Error in sample: {0}", ex);
        }

        Console.WriteLine("Waiting for Events.  Press enter to exit...");
        Console.ReadLine();
        Client.CloseAsync().Wait();
    }
    ```

    Este exemplo de código mostra como recuperar o módulo gêmeo e atualizar as propriedades relatadas com o protocolo AMQP. Na versão prévia pública, só há suporte a AMQP para operações de módulo gêmeo.

5. Além do método **Principal** acima, você pode adicionar o bloco de código abaixo para enviar eventos para o Hub IoT do seu módulo:

    ```csharp
    Byte[] bytes = new Byte[2];
    bytes[0] = 0;
    bytes[1] = 1;
    var sendEventsTask = Client.SendEventAsync(new Message(bytes));
    sendEventsTask.Wait();
    Console.WriteLine("Event sent to IoT Hub.");
    ```

## <a name="run-the-apps"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos. No Visual Studio, no Gerenciador de Soluções, clique com o botão direito na solução e clique em **Definir Projetos de Inicialização**. Selecione **Vários projetos de inicialização**e, em seguida, selecione **Iniciar** como ação para o aplicativo do console. E, em seguida, pressione F5 para iniciar o aplicativo. 

## <a name="next-steps"></a>Próximas etapas

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, confira:

* [Introdução ao gerenciamento de dispositivo](iot-hub-node-node-device-management-get-started.md)
* [Introdução ao IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)