---
title: Introdução à identidade do módulo e ao módulo gêmeo do Hub IoT do Azure (portal e .NET) | Microsoft Docs
description: Saiba como criar a identidade do módulo e atualizar o módulo gêmeo usando o portal e .NET.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: dobett
ms.openlocfilehash: 6048ddbcfed4ab929d521693175c456ad91f22e2
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54447093"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>Introdução à identidade do módulo e ao módulo gêmeo do Hub IoT usando o portal e o dispositivo .NET

> [!NOTE]
> [As identidades do módulo e os módulos gêmeos](iot-hub-devguide-module-twins.md) são semelhantes à identidade do dispositivo e ao dispositivo gêmeo do Hub IoT do Azure, mas fornecem melhor granularidade. Enquanto a identidade do dispositivo e o dispositivo gêmeo do Hub IoT do Azure permitem que o aplicativo de back-end configure um dispositivo e forneça visibilidade às condições do dispositivo, uma identidade de módulo e módulo gêmeo fornecem esses recursos para componentes individuais de um dispositivo. Em dispositivos habilitados com vários componentes, como dispositivos baseados em sistema operacional ou dispositivos de firmware, permite condições e configuração isolada para cada componente.
>

Neste tutorial, você irá aprender:

1. Como criar uma identidade de módulo no portal. 

2. Como usar o SDK do dispositivo .NET para atualizar o módulo gêmeo a partir do seu dispositivo.

> [!NOTE]
> Para obter informações sobre os SDKs de IoT do Azure que você pode usar para criar aplicativos executados em dispositivos e no back-end da solução, consulte [SDKs de IoT do Azure][lnk-hub-sdks].
>

Para concluir este tutorial, você precisará do seguinte:

* Visual Studio 2015 ou Visual Studio 2017.
* Uma conta ativa do Azure. (Se você não tem uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Recuperar a cadeia conexão para o hub IoT

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrar um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>Criar uma identidade de módulo no portal

Em uma identidade de dispositivo, você pode criar até 20 identidades de módulo. Clique no botão **Adicionar Identidade de Módulo** na parte superior para criar sua primeira identidade de módulo chamada **myFirstModule**. 

  ![Detalhes do Dispositivo][9]

Salve e clique na identidade do módulo que você acabou de criar. Você pode ver os detalhes de identidade do módulo. Salve a cadeia de conexão - chave primária. Ele será usado na próxima seção, na qual você configurará seu módulo no dispositivo.

  ![Detalhes do Dispositivo][12]

## <a name="update-the-module-twin-using-net-device-sdk"></a>Atualizar o módulo gêmeo usando o SDK do dispositivo .NET

Você criou com êxito a identidade do módulo em seu Hub IoT. Vamos tentar se comunicar com a nuvem pelo dispositivo simulado. Depois de criar uma identidade de módulo, um módulo gêmeo é implicitamente criado no Hub IoT. Nesta seção, você criará um aplicativo de console .NET em seu dispositivo simulado que atualiza as propriedades relatadas do módulo gêmeo.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

No Visual Studio, adicione um projeto da Área de Trabalho Clássica do Windows no Visual C# à solução existente usando o modelo de projeto **Aplicativo do Console (.NET Framework)**. Verifique se a versão do .NET Framework é 4.6.1 ou posterior. Nomeie o projeto **UpdateModuleTwinReportedProperties**.

  ![Criar um projeto do Visual Studio][13]

## <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>Instalar o SDK do dispositivo .NET do Hub IoT do Azure

A identidade do módulo e o módulo gêmeo estão em versão prévia pública. Está disponível apenas nos SDKs do dispositivo do Hub IoT pré-lançamento. No Visual Studio, abra ferramentas > gerenciamento de pacotes Nuget > gerenciar pacotes Nuget para solução. Pesquise Microsoft.Azure.Devices.Client. Verifique se você marcou a caixa de seleção de incluir pré-lançamento. Selecione a versão mais recente e instale. Agora você tem acesso a todos os recursos do módulo. 

  ![Instalar o SDK V1.16.0-preview-005 do serviço .NET do Hub IoT][14]

## <a name="get-your-module-connection-string"></a>Obter a cadeia de conexão do módulo

Logon no [portal do Azure][lnk-portal]. Navegue até seu Hub IoT e clique em Dispositivos IoT. Localize myFirstDevice, abra-o e você verá que myFirstModule foi criado com êxito. Copie a cadeia de conexão do módulo. Ela será necessária na próxima etapa.

  ![Detalhes do módulo do Portal do Azure][15]

## <a name="create-updatemoduletwinreportedproperties-console-app"></a>Criar um aplicativo de console UpdateModuleTwinReportedProperties

Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs** :

```csharp
using Microsoft.Azure.Devices.Client;
using Microsoft.Azure.Devices.Shared;
```

Adicione os seguintes campos à classe **Program** . Substitua o valor de espaço reservado pela cadeia de conexão do módulo.

```csharp
private const string ModuleConnectionString = "<Your module connection string>";
private static ModuleClient Client = null;
```

Adicione o seguinte método **OnDesiredPropertyChanged** à classe de **Programa**:

```csharp
private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
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
    Microsoft.Azure.Devices.Client.TransportType transport = Microsoft.Azure.Devices.Client.TransportType.Amqp;

    try
    {
        Client = ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
        Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
        Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

        Console.WriteLine("Retrieving twin");
        var twinTask = Client.GetTwinAsync();
        twinTask.Wait();
        var twin = twinTask.Result;
        Console.WriteLine(JsonConvert.SerializeObject(twin));

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
    Console.ReadKey();
    Client.CloseAsync().Wait();
}

private static void ConnectionStatusChangeHandler(ConnectionStatus status, ConnectionStatusChangeReason reason)
{
    Console.WriteLine($"Status {status} changed: {reason}");
}
```

Este exemplo de código mostra como recuperar o módulo gêmeo e atualizar as propriedades relatadas com o protocolo AMQP. Na versão prévia pública, só há suporte a AMQP para operações de módulo gêmeo.

## <a name="run-the-apps"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos. No Visual Studio, no Gerenciador de Soluções, clique com o botão direito na solução e clique em **Definir Projetos de Inicialização**. Selecione **Vários projetos de inicialização**e, em seguida, selecione **Iniciar** como ação para o aplicativo do console. Em seguida, pressione F5 para iniciar a execução de ambos os aplicativos. 

## <a name="next-steps"></a>Próximas etapas

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, confira:

* [Introdução à identidade do módulo e ao módulo gêmeo do Hub IoT usando back-up .NET e dispositivo .NET][lnk-csharp-csharp-getstarted]
* [Introdução ao IoT Edge][lnk-iot-edge]


<!-- Images. -->
[9]:./media/iot-hub-portal-csharp-module-twin-getstarted/create-module-id.JPG
[12]:./media/iot-hub-portal-csharp-module-twin-getstarted/module-details.JPG
[13]: ./media/iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.JPG
[14]: ./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png
[15]: ./media/iot-hub-csharp-csharp-module-twin-getstarted/module-detail.JPG
<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-csharp-csharp-getstarted]: iot-hub-csharp-csharp-module-twin-getstarted.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
