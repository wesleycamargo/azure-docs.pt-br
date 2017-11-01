---
title: "Atualização de firmware do dispositivo com o Hub IoT do Azure (.NET/.NET) | Microsoft Docs"
description: "Como usar o gerenciamento de dispositivos no Hub IoT do Azure para iniciar uma atualização de firmware do dispositivo. Use o SDK do dispositivo IoT do Azure para .NET para implementar um aplicativo de dispositivo simulado e o SDK do serviço do IoT do Azure para .NET para implementar um aplicativo de serviço que dispara a atualização de firmware."
services: iot-hub
documentationcenter: .net
author: JimacoMS2
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/19/2017
ms.author: v-jamebr
ms.openlocfilehash: bd0a227861d75dc66af8fb4865a17a3b6d0f70ba
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2017
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-netnet"></a>Use o gerenciamento de dispositivos para iniciar uma atualização de firmware do dispositivo (.NET/.NET)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

## <a name="introduction"></a>Introdução
No tutorial [Introdução ao gerenciamento de dispositivo][lnk-dm-getstarted], você viu como usar os primitivos [dispositivo gêmeo][lnk-devtwin] e [métodos diretos][lnk-c2dmethod] para reiniciar remotamente um dispositivo. Este tutorial usa os mesmos primitivos do Hub IoT e mostra como fazer uma atualização de firmware simulada de ponta a ponta.  Esse padrão é usado na implementação da atualização de firmware para o [exemplo de implementação de dispositivo Raspberry Pi][lnk-rpi-implementation].

Este tutorial mostra como:

* Criar um aplicativo de console .NET que chama o método direto **firmwareUpdate** no aplicativo de dispositivo simulado por meio do Hub IoT.
* Criar um aplicativo de dispositivo simulado que implementa um método direto **firmwareUpdate**. Esse método inicia um processo de várias etapas que aguarda o download, baixa e aplica a imagem do firmware. Durante cada etapa da atualização, o dispositivo usa as propriedades relatadas para informar sobre o progresso.

Ao final deste tutorial, você terá um aplicativo de dispositivo de console .NET (C#) e um aplicativo de back-end do console .NET (C#):

* **SimulatedDeviceFwUpdate**, que se conecta ao seu Hub IoT com a identidade do dispositivo criada anteriormente, recebe um método direto **firmwareUpdate**, é executado por meio de um processo de vários estados para simular uma atualização de firmware, incluindo: aguardar o download da imagem, baixar a nova imagem e, por fim, aplicar a imagem.

* **TriggerFWUpdate**, que usa o SDK do serviço para invocar remotamente o método direto **firmwareUpdate** no dispositivo simulado, exibe a resposta e periodicamente (a cada 500 ms) exibe as propriedades relatadas do dispositivo atualizadas.

Para concluir este tutorial, você precisará do seguinte:

* Visual Studio 2015 ou Visual Studio 2017.
* Uma conta ativa do Azure. (Se você não tem uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

Consulte o artigo [Introdução ao gerenciamento de dispositivo](iot-hub-csharp-csharp-device-management-get-started.md) para criar seu hub IoT e obter a cadeia de conexão dele.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Disparar uma atualização de firmware remoto no dispositivo usando um método direto
Nesta seção, você criará um aplicativo do console .NET (usando C#) que inicia uma atualização remota de firmware em um dispositivo. O aplicativo utiliza um método direto para iniciar a atualização e usa consultas em dispositivos gêmeos para receber periodicamente o status da atualização do firmware ativo.

1. No Visual Studio, adicione um projeto da Área de Trabalho Clássica do Windows no Visual C# à solução atual usando o modelo de projeto **Aplicativo do Console** . Nomeie o projeto como **TriggerFWUpdate**.

    ![Novo projeto da Área de Trabalho Clássica do Windows no Visual C#][img-createserviceapp]

2. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **TriggerFWUpdate**, em seguida, clique em **Gerenciar Pacotes NuGet**.
3. Na janela **Gerenciador de Pacotes Nuget**, selecione **Procurar**, procure **microsoft.azure.devices**, selecione **Instalar** para instalar o pacote **Microsoft.Azure.Devices** e aceite os termos de uso. O procedimento baixa, instala e adiciona uma referência ao [pacote Nuget do SDK do Dispositivo IoT do Azure][lnk-nuget-service-sdk] e suas dependências.

    ![Janela do Gerenciador de Pacotes NuGet][img-servicenuget]
4. Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs** :

    ```csharp   
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Adicione os seguintes campos à classe **Program** . Substitua os valores múltiplos de espaço reservado pela cadeia de conexão do Hub IoT criado na seção anterior e a ID do dispositivo.
   
    ```csharp   
    static RegistryManager registryManager;
    static string connString = "{iot hub connection string}";
    static ServiceClient client;
    static string targetDevice = "{deviceIdForTargetDevice}";
    ```
        
6. Adicione o seguinte método à classe **Programa**. Esse método sonda o dispositivo gêmeo para obter o status atualizado a cada 500 milissegundos. Ele grava no console somente quando o status realmente é alterado. Neste exemplo, para evitar o consumo de mensagens extras do Hub IoT na sua assinatura, a sondagem é interrompida quando o dispositivo relata um status **applyComplete** ou um erro.  
   
    ```csharp   
    public static async Task QueryTwinFWUpdateReported(DateTime startTime)
    {
        DateTime lastUpdated = startTime;

        while (true)
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);

            if (twin.Properties.Reported.GetLastUpdated().ToUniversalTime() > lastUpdated.ToUniversalTime())
            {
                lastUpdated = twin.Properties.Reported.GetLastUpdated().ToUniversalTime();
                Console.WriteLine("\n" + twin.Properties.Reported["iothubDM"].ToJson());

                var status = twin.Properties.Reported["iothubDM"]["firmwareUpdate"]["status"].Value;
                if ((status == "downloadFailed") || (status == "applyFailed") || (status == "applyComplete"))
                {
                    Console.WriteLine("\nStop polling.");
                    return;
                }
            }
            await Task.Delay(500);
        }
    }
    ```
        
7. Adicione o seguinte método à classe **Programa** :

    ```csharp   
    public static async Task StartFirmwareUpdate()
    {
        client = ServiceClient.CreateFromConnectionString(connString);
        CloudToDeviceMethod method = new CloudToDeviceMethod("firmwareUpdate");
        method.ResponseTimeout = TimeSpan.FromSeconds(30);
        method.SetPayloadJson(
            @"{
               fwPackageUri : 'https://someurl'
            }");

        CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

        Console.WriteLine("Invoked firmware update on device.");
    }
    ```

8. Por fim, adicione as seguintes linhas ao método **Main** . Isso cria um gerenciador de registro com o qual o dispositivo gêmeo será lido, inicia a tarefa de sondagem em um thread de trabalho e dispara a atualização do firmware.
   
    ```csharp   
    registryManager = RegistryManager.CreateFromConnectionString(connString);

    Task queryTask = Task.Run(() => (QueryTwinFWUpdateReported(DateTime.Now)));

    StartFirmwareUpdate().Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```
        
9. Compilar a solução.

## <a name="create-a-simulated-device-app"></a>Criar um aplicativo de dispositivo simulado
Nesta seção, você:

* Criar um aplicativo de console do .NET que responde a um método direto chamado pela nuvem
* Simular uma atualização de firmware disparada por um serviço de back-end por meio de um método direto
* Usar as propriedades relatadas para habilitar consultas de dispositivo gêmeo para identificar dispositivos e quando foi a última atualização de firmware concluída

1. No Visual Studio, adicione um projeto da Área de Trabalho Clássica do Windows no Visual C# à solução atual usando o modelo de projeto **Aplicativo do Console** . Nomeie o projeto como **SimulateDeviceFWUpdate**.
   
    ![Novo aplicativo de dispositivo Visual C# Windows clássico][img-createdeviceapp]
    
2. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **SimulateDeviceFWUpdate** e clique em **Gerenciar Pacotes NuGet**.
3. Na janela **Gerenciador de Pacotes NuGet**, selecione **Procurar** e pesquise por **microsoft.azure.devices.client**. Selecione **Instalar** para instalar o pacote **Microsoft.Azure.Devices.Client** e aceite os termos de uso. O procedimento baixa, instala e adiciona uma referência ao [pacote NuGet do SDK do Dispositivo IoT do Azure][lnk-nuget-client-sdk] e suas dependências.
   
    ![Aplicativo de cliente de janela do Gerenciador de Pacotes NuGet][img-clientnuget]
4. Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs** :
   
    ```csharp   
    using Newtonsoft.Json.Linq;
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Adicione os seguintes campos à classe **Program** . Substitua o valor de espaço reservado pela cadeia de conexão do dispositivo que você anotou na seção **Criar uma identidade de dispositivo**.
   
    ```csharp   
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

6. Adicione o seguinte método para relatar o status de volta para a nuvem por meio do dispositivo gêmeo: 

    ```csharp   
    static async Task reportFwUpdateThroughTwin(Twin twin, TwinCollection fwUpdateValue)
    {
        try
        {
            TwinCollection patch = new TwinCollection();
            TwinCollection iothubDM = new TwinCollection();

            iothubDM["firmwareUpdate"] = fwUpdateValue;
            patch["iothubDM"] = iothubDM;

            await Client.UpdateReportedPropertiesAsync(patch);
            Console.WriteLine("Twin state reported: {0}", fwUpdateValue["status"]);
        }
        catch 
        {
            Console.WriteLine("Error updating device twin");
            throw;
        }
    }
    ```

7. Adicione o seguinte método para simular o download da imagem do firmware:
        
    ```csharp   
    static async Task<byte[]> simulateDownloadImage(string imageUrl)
    {
        var image = "[fake image data]";

        Console.WriteLine("Downloading image from " + imageUrl);

        await Task.Delay(4000);

        return Encoding.ASCII.GetBytes(image);
            
    }
    ```

8. Adicione o seguinte método para simular a aplicação da imagem ao dispositivo:
        
    ```csharp   
    static async Task simulateApplyImage(byte[] imageData)
    {
        if (imageData == null)
        {
            throw new ArgumentNullException();
        }

        await Task.Delay(4000);

    }
    ```
 
9.  Adicione o seguinte método para simular a espera do download da imagem do firmware. Atualize o status para **em espera** e desmarque as outras propriedades de atualização de firmware no gêmeo. Essas propriedades estão desmarcadas para remover todos os valores existentes de atualizações de firmware anteriores. Isso é necessário porque as propriedades relatadas são enviadas como uma operação de PATCH (um delta) e não uma operação PUT (um conjunto completo de propriedades que substitui todos os valores anteriores). Normalmente, os dispositivos serão informados sobre uma atualização disponível e uma política definida pelo administrador fará com que o dispositivo inicie o download e aplique a atualização. Essa função é onde a lógica para habilitar essa política deve ser executado. 
        
    ```csharp   
    static async Task waitToDownload(Twin twin, string fwUpdateUri)
    {
        var now = DateTime.Now;
        TwinCollection status = new TwinCollection();
        status["fwPackageUri"] = fwUpdateUri;
        status["status"] = "waiting";
        status["error"] = null;
        status["startedWaitingTime"] = DateTime.Now;
        status["downloadCompleteTime"] = null;
        status["startedApplyingImage"] = null;
        status["lastFirmwareUpdate"] = null;

        await reportFwUpdateThroughTwin(twin, status);

        await Task.Delay(2000);
    }
    ```

10. Adicione o seguinte método para realizar o download. Ele atualiza o status para **baixando** por meio do dispositivo gêmeo, chama o método de download simulado e relata um status **downloadComplete** ou **downloadFailed** por meio do gêmeo dependendo do resultado da operação de download. 
        
    ```csharp   
    static async Task<byte[]> downloadImage(Twin twin, string fwUpdateUri)
    {
        try
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloading";
            await reportFwUpdateThroughTwin(twin, statusUpdate);

            byte[] imageData = await simulateDownloadImage(fwUpdateUri);

            statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloadComplete";
            statusUpdate["downloadCompleteTime"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            return imageData;
        }
        catch (Exception ex)
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloadFailed";
            statusUpdate["error"] = new TwinCollection();
            statusUpdate["error"]["code"] = ex.GetType().ToString();
            statusUpdate["error"]["message"] = ex.Message;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            throw;
        }
    }
    ```

11. Adicione o método a seguir para aplicar a imagem. Ele atualiza o status para **aplicando** por meio do dispositivo gêmeo, chama o método de aplicação de imagem simulado e atualizado o status para **applyComplete** ou **applyFailed** por meio do gêmeo dependendo do resultado da operação de aplicação. 
        
    ```csharp   
    static async Task applyImage(Twin twin, byte[] imageData)
    {
        try
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applying";
            statusUpdate["startedApplyingImage"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);

            await simulateApplyImage(imageData);

            statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applyComplete";
            statusUpdate["lastFirmwareUpdate"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
        }
        catch (Exception ex)
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applyFailed";
            statusUpdate["error"] = new TwinCollection();
            statusUpdate["error"]["code"] = ex.GetType().ToString();
            statusUpdate["error"]["message"] = ex.Message;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            throw;
        }
    }
    ```

12. Adicione o seguinte método para progredir a operação de atualização de firmware da espera para o download da imagem por meio da aplicação da imagem ao dispositivo:
        
    ```csharp   
    static async Task doUpdate(string fwUpdateUrl)
    {
        try
        {
            Twin twin = await Client.GetTwinAsync();
            await waitToDownload(twin, fwUpdateUrl);
            byte[] imageData = await downloadImage(twin, fwUpdateUrl);
            await applyImage(twin, imageData);
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error during update: {0}", ex.Message);
        }
    }
    ```

13. Adicione o seguinte método para manipular o método direto **updateFirmware** da nuvem. Ele extrai a URL para a atualização do firmware da carga da mensagem e a transmite para a tarefa **doUpdate**, que é iniciada em outro thread do pool de threads. Em seguida, ele retorna imediatamente a resposta do método para a nuvem.
        
    ```csharp   
    static Task<MethodResponse> onFirmwareUpdate(MethodRequest methodRequest, object userContext)
    {
        string fwUpdateUrl = (string)JObject.Parse(methodRequest.DataAsJson)["fwPackageUri"];
        Console.WriteLine("\nMethod: {0} triggered by service, URI is: {1}", methodRequest.Name, fwUpdateUrl);

        Task updateTask = Task.Run(() => (doUpdate(fwUpdateUrl)));

        string result = "'FirmwareUpdate started.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }
    ```
> [!NOTE]
> Esse método dispara a atualização simulada para ser executada como um **Tarefa** e imediatamente responde à chamada do método, informando o serviço que a atualização do firmware foi iniciada. O status e a conclusão da atualização serão enviados para o serviço por meio das propriedades relatadas do dispositivo gêmeo. Respondemos à chamada do método ao iniciar a atualização, em vez de após sua conclusão, porque:
> * Um processo de atualização real provavelmente demorará mais do que o tempo limite da chamada de método.
> * Um processo de atualização real muito provavelmente exigirá uma reinicialização, o que reiniciaria este aplicativo e tornaria o objeto **MetodRequest** indisponível. (Contudo, é possível atualizar propriedades relatadas mesmo após uma reinicialização.) 

14. Por fim, adicione o seguinte código ao método **Principal** para abrir a conexão para o hub IoT e inicializar o ouvinte do método:
   
    ```csharp   
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
        
        // setup callback for "firmware update" method
        Client.SetMethodHandlerAsync("firmwareUpdate", onFirmwareUpdate, null).Wait();
        Console.WriteLine("Waiting for firmware update direct method call\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        // as a good practice, remove the firmware update handler
        Client.SetMethodHandlerAsync("firmwareUpdate", null, null).Wait();
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
        
15. Compilar a solução.       

> [!NOTE]
> Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, implemente políticas de repetição (como uma retirada exponencial), como sugerido no artigo [Tratamento de falhas transitórias][lnk-transient-faults] do MSDN.


## <a name="run-the-apps"></a>Executar os aplicativos
Agora você está pronto para executar os aplicativos.
1. Execute o aplicativo do dispositivo .NET **SimulatedDeviceFWUpdate**.  Clique com o botão direito do mouse no projeto **SimulatedDeviceFWUpdate**, selecione **Depurar** e, em seguida, **Iniciar nova instância**. Ele deve iniciar a escuta para chamadas de método do seu Hub IoT: 

2. Depois que o dispositivo estiver conectado e aguardando as invocações de método, execute o aplicativo **TriggerFWUpdate** .NET para invocar o método de atualização de firmware no aplicativo do dispositivo simulado. Clique com o botão direito do mouse no projeto **TriggerFWUpdate**, selecione **Depurar** e, em seguida, **Iniciar nova instância**. Você deverá ver a sequência de atualização indicada no console **SimulatedDeviceFWUpdate**, bem como a sequência relatada por meio das propriedades relatadas do dispositivo no console **TriggerFWUpdate**. Observe que o processo leva alguns segundos para ser concluído. 
   
    ![Execução de aplicativo de serviço e dispositivo][img-combinedrun]


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você usou um método direto para disparar uma atualização remota de firmware em um dispositivo e utilizou as propriedades relatadas para acompanhar o andamento da atualização do firmware.

Para saber como estender sua solução de IoT e agendar chamadas de método em vários dispositivos, confira o tutorial [Agendar e difundir trabalhos][lnk-tutorial-jobs].

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-csharp-firmware-update/servicesdknuget.png
[img-clientnuget]: media/iot-hub-csharp-csharp-firmware-update/clientsdknuget.png
[img-createserviceapp]: media/iot-hub-csharp-csharp-firmware-update/createserviceapp.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-firmware-update/createdeviceapp.png
[img-combinedrun]: media/iot-hub-csharp-csharp-firmware-update/combinedrun.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-csharp-csharp-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-csharp-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/