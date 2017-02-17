---
title: Agendar trabalhos com o Hub IoT do Azure (.NET/Node) | Microsoft Docs
description: "Como agendar um trabalho do Hub IoT do Azure para invocar um método direto em vários dispositivos. Use o SDK do dispositivo IoT do Azure para Node.js para implementar os aplicativos de dispositivo e o SDK do serviço do IoT do Azure para .NET para implementar um aplicativo de serviço que executa o trabalho."
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: 2233356e-b005-4765-ae41-3a4872bda943
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/17/2016
ms.author: juanpere
translationtype: Human Translation
ms.sourcegitcommit: a243e4f64b6cd0bf7b0776e938150a352d424ad1
ms.openlocfilehash: fd53e73d6a686581ea2b807ae66716fc36a99ad4


---
# <a name="schedule-and-broadcast-jobs"></a>Agendar e transmitir trabalhos
[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

## <a name="introduction"></a>Introdução
O Hub IoT do Azure é um serviço completamente gerenciado que permite que um aplicativo back-end crie e controle trabalhos que agendam e atualizam milhões de dispositivos.  Os trabalhos podem ser usados para as seguintes ações:

* Atualizar as propriedades desejadas
* Marcas de atualização
* Chamar métodos diretos

Conceitualmente, um trabalho encapsula uma dessas ações e rastreia o progresso da execução com relação a um conjunto de dispositivos, definido por uma consulta do dispositivo gêmeo.  Por exemplo, usando um trabalho, um aplicativo back-end pode chamar um método de reinicialização em 10.000 dispositivos, especificado por uma consulta do dispositivo gêmeo e agendada no futuro.  Esse aplicativo pode acompanhar o progresso à medida que cada um desses dispositivos recebem e executam o método de reinicialização.

Saiba mais sobre cada um desses recursos nestes artigos:

* Dispositivo gêmeo e propriedades: [Introdução os dispositivos gêmeos][lnk-get-started-twin] e [Tutorial: Como usar as propriedades do dispositivo gêmeo][lnk-twin-props]
* métodos diretos: [Guia do desenvolvedor do Hub IoT – métodos diretos][lnk-dev-methods] e [Tutorial: Usar métodos diretos][lnk-c2d-methods]

Este tutorial mostra como:

* Criar um aplicativo dispositivo simulado que tem um método direto que habilita o **lockDoor** que pode ser chamado pelo aplicativo back-end.
* Crie um aplicativo de console .NET que chama o método direto **lockDoor** no aplicativo de dispositivo simulado usando um trabalho e atualiza as propriedades desejadas usando um trabalho de dispositivo.

Ao final deste tutorial, você terá um aplicativo de dispositivo de console Node.js e um aplicativo de back-end do console .NET (C#):

**simDevice.js**, que se conecta ao seu hub IoT com a identidade do dispositivo e recebe um método direto **lockDoor**.

**ScheduleJob**, que chama um método direto no aplicativo de dispositivo simulado e atualiza as propriedades desejadas do dispositivo gêmeo usando um trabalho.

Para concluir este tutorial, você precisará do seguinte:

* Microsoft Visual Studio 2015.
* Node.js versão 0.12.x ou posterior. <br/>  [Preparar o ambiente de desenvolvimento][lnk-dev-setup] descreve como instalar o Node.js para este tutorial no Windows ou no Linux.
* Uma conta ativa do Azure. (Se você não tem uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Agendar trabalhos para chamar um método direto e atualizar as propriedades do dispositivo gêmeo
Nesta seção, é possível criar um aplicativo de console .NET (usando C#) que inicia um **lockDoor** remoto em um dispositivo usando um método direto e atualizar as propriedades do dispositivo gêmeo.

1. No Visual Studio, adicione um projeto da Área de Trabalho Clássica do Windows no Visual C# à solução atual usando o modelo de projeto **Aplicativo do Console** . Nomeie o projeto como **ScheduleJob**.

    ![Novo projeto da Área de Trabalho Clássica do Windows no Visual C#][img-createapp]

2. No Gerenciador de Soluções, clique com o botão direito no projeto **ScheduleJob** e em **Gerenciar Pacotes NuGet**.
3. Na janela **Gerenciador de Pacotes Nuget**, selecione **Procurar**, procure **microsoft.azure.devices**, selecione **Instalar** para instalar o pacote **Microsoft.Azure.Devices** e aceite os termos de uso. O procedimento baixa, instala e adiciona uma referência ao [pacote Nuget do SDK do Dispositivo IoT do Azure][lnk-nuget-service-sdk] e suas dependências.

    ![Janela do Gerenciador de Pacotes NuGet][img-servicenuget]
4. Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs** :
   
        using Microsoft.Azure.Devices;
        
5. Adicione os seguintes campos à classe **Program** . Substitua o espaço reservado pela cadeia de conexão do Hub IoT criado na seção anterior.
   
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static JobClient jobClient;
        
6. Adicione o seguinte método à classe **Programa** :
   
        public static async Task MonitorJob(string jobId)
        {
            JobResponse result;
            do
            {
                result = await jobClient.GetJobAsync(jobId);
                Console.WriteLine("Job Status : " + result.Status.ToString());
                Thread.Sleep(2000);
            } while ((result.Status != JobStatus.Completed) && (result.Status != JobStatus.Failed));
        }
                
7. Adicione o seguinte método à classe **Programa** :

        public static async Task StartMethodJob(string jobId)
        {
            CloudToDeviceMethod directMethod = new CloudToDeviceMethod("lockDoor", TimeSpan.FromSeconds(5), TimeSpan.FromSeconds(5));

            JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
                "SELECT * from DEVICES",
                directMethod,
                DateTime.Now,
                10);

            Console.WriteLine("Started Method Job");
        }

8. Adicione o seguinte método à classe **Programa** :

        public static async Task StartTwinUpdateJob(string jobId)
        {
            var twin = new Twin();
            twin.Properties.Desired["Building"] = "43";
            twin.Properties.Desired["Floor"] = "3";
            twin.ETag = "*";

            JobResponse result = await jobClient.ScheduleTwinUpdateAsync(jobId,
                "SELECT * from DEVICES", 
                twin,
                DateTime.Now,
                10);

            Console.WriteLine("Started Twin Update Job");
        }
 

9. Por fim, adicione as seguintes linhas ao método **Main** :
   
        jobClient = JobClient.CreateFromConnectionString(connString);

        string methodJobId = Guid.NewGuid().ToString();

        StartMethodJob(methodJobId);
        MonitorJob(methodJobId).Wait();
        Console.WriteLine("Press ENTER to run the next job.");
        Console.ReadLine();

        string twinUpdateJobId = Guid.NewGuid().ToString();

        StartTwinUpdateJob(twinUpdateJobId);
        MonitorJob(twinUpdateJobId).Wait();
        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
                   
10. Compilar a solução.

## <a name="create-a-simulated-device-app"></a>Criar um aplicativo de dispositivo simulado
Nesta seção, você cria um aplicativo de console do Node.js que responde a um método direto chamado pela nuvem, que dispara uma reinicialização de dispositivo simulado e usa propriedades relatadas para habilitar consultas em dispositivos gêmeos e identificar os dispositivos e quando eles foram reiniciados pela última vez.

1. Crie uma nova pasta vazia denominada **simDevice**.  Na pasta **simDevice**, crie um arquivo package.json usando o comando a seguir no seu prompt de comando.  Aceite todos os padrões:
   
    ```
    npm init
    ```
2. No prompt de comando, na pasta **simDevice**, execute o seguinte comando para instalar o pacote **azure-iot-device** do SDK do Dispositivo e o pacote **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Usando um editor de texto, crie um novo arquivo **SimDevice.js** na pasta **simDevice**.
4. Adicione as seguintes instruções "require" no início do arquivo **simDevice.js**:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Adicione uma variável **connectionString** e use-a para criar um **Cliente** do dispositivo.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Adicione a seguinte função para manipular o método **lockDoor**.
   
    ```
    var onLockDoor = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        console.log('Locking Door!');
    };
    ```
7. Adicione o seguinte código para registrar o manipulador do método **lockDoor**.
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub.  Waiting for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
    });
    ```
8. Salve e feche o arquivo **simDevice.js**.

> [!NOTE]
> Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, implemente políticas de repetição (como uma retirada exponencial), como sugerido no artigo [Tratamento de falhas transitórias][lnk-transient-faults] do MSDN.
> 
> 

## <a name="run-the-apps"></a>Executar os aplicativos
Agora você está pronto para executar os aplicativos.

1. No prompt de comando, na pasta **simDevice**, execute o seguinte comando para começar a escutar o método direto de reinicialização.
   
    ```
    node simDevice.js
    ```
2. Execute o aplicativo de console C# **ScheduleJob**- clique com botão direito no projeto **ScheduleJob**, selecione **Depurar** e **Iniciar nova instância**.

3. Você verá a saída do dispositivo e dos aplicativos back-end.

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você usou um trabalho para agendar um método direto para um dispositivo e a atualização das propriedades do twin do dispositivo.

Para continuar com a introdução ao Hub IoT e aos padrões de gerenciamento de dispositivo como remoto por meio da atualização de firmware de ar, consulte:

[Tutorial: Como realizar uma atualização de firmware][lnk-fwupdate]

Para continuar a introdução ao Hub IoT, confira [Introdução ao SDK do Gateway IoT][lnk-gateway-SDK].

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-schedule-jobs/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-schedule-jobs/createnetapp.png

[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-props]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/



<!--HONumber=Dec16_HO1-->


