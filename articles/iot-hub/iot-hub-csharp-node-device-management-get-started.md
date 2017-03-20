---
title: "Introdução ao gerenciamento de dispositivo de Hub IoT do Azure (.NET/Node) | Microsoft Docs"
description: "Como usar o gerenciamento de dispositivos do Hub IoT do Azure para iniciar uma reinicialização do dispositivo remoto. Use o SDK do dispositivo IoT do Azure para Node.js para implementar um aplicativo de dispositivo simulado que inclui um método direto e o SDK do serviço do Azure IoT para .NET para implementar um aplicativo de serviço que invoca o método direto."
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: e044006d-ffd6-469b-bc63-c182ad066e31
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/17/2016
ms.author: juanpere
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: f03b8d192255a3c93284f3c5e898f68a1234644f
ms.lasthandoff: 03/07/2017


---
# <a name="get-started-with-device-management-netnode"></a>Introdução ao gerenciamento de dispositivos (.NET/Node)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]
## <a name="introduction"></a>Introdução
Os aplicativos de back-end podem usar primitivos no Hub IoT do Azure, ou seja, o dispositivo gêmeo e métodos diretos, para iniciar remotamente e monitorar as ações de gerenciamento de dispositivo nos dispositivos.  Este artigo fornece código e diretrizes sobre como dispositivos e aplicativos de back-end funcionam juntos para iniciar e monitorar uma reinicialização do dispositivo remoto usando o Hub IoT.

Para iniciar e monitorar remotamente as ações de gerenciamento do dispositivo em seus dispositivos a partir de um aplicativo baseado em nuvem e de back-end, use os primitivos do Hub IoT do Azure como o [dispositivo gêmeo][lnk-devtwin] e [métodos diretos][lnk-c2dmethod]. Este tutorial mostra como um aplicativo de back-end e um dispositivo podem trabalhar juntos para permitir que você inicie e monitore uma reinicialização remota do dispositivo por meio do Hub IoT.

Use um método direto para iniciar as ações de gerenciamento do dispositivo (como a reinicialização, redefinição de fábrica e atualização do firmware) a partir de um aplicativo de back-end na nuvem. O dispositivo é responsável por:

* Lidar com a solicitação do método enviada a partir do Hub IoT.
* Iniciar a ação específica do dispositivo correspondente no dispositivo.
* Fornecer atualizações de status por meio das propriedades relatadas para o Hub IoT.

Você pode usar um aplicativo de back-end na nuvem para executar consultas do dispositivo gêmeo para relatar o progresso de suas ações de gerenciamento do dispositivo.

Este tutorial mostra como:

* Usar o portal do Azure para criar um Hub IoT e criar uma identidade de dispositivo em seu Hub IoT.
* Crie um aplicativo de dispositivo simulado contendo um método direto que reinicia o dispositivo. Métodos diretos são invocados da nuvem.
* Criar um aplicativo de console .NET que chama um método direto de reinicialização no aplicativo de dispositivo simulado por meio do Hub IoT.

Ao final deste tutorial, você terá um aplicativo de dispositivo de console Node.js e um aplicativo de back-end do console .NET (C#):

**dmpatterns_getstarted_device.js**, que conecta seu hub IoT com a identidade do dispositivo criada anteriormente, recebe um método direto de reinicialização, simula uma reinicialização física e informa a hora da última reinicialização.

**TriggerReboot**, que chama um método direto no aplicativo de dispositivo simulado, exibe a resposta e exibe as propriedades relatadas atualizadas.

Para concluir este tutorial, você precisará do seguinte:

* Visual Studio 2015 ou Visual Studio 2017.
* Node.js versão 0.12.x ou posterior. <br/>  [Preparar o ambiente de desenvolvimento][lnk-dev-setup] descreve como instalar o Node.js para este tutorial no Windows ou no Linux.
* Uma conta ativa do Azure. (Se você não tem uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Disparar uma reinicialização remota no dispositivo usando um método direto
Nesta seção, você criará um aplicativo do console .NET (usando C#) que inicia uma reinicialização remota em um dispositivo usando um método direto. O aplicativo usa consultas de dispositivo gêmeo para descobrir o último horário de reinicialização para esse dispositivo.

1. No Visual Studio, adicione um projeto da Área de Trabalho Clássica do Windows no Visual C# a uma nova solução usando o modelo de projeto **Aplicativo do Console (.NET Framework)**. Verifique se a versão do .NET Framework é 4.5.1 ou posterior. Nomeie o projeto como **TriggerReboot**.

    ![Novo projeto da Área de Trabalho Clássica do Windows no Visual C#][img-createapp]

2. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **TriggerReboot** e, em seguida, clique em **Gerenciar Pacotes NuGet**.
3. Na janela **Gerenciador de Pacotes Nuget**, selecione **Procurar**, procure **microsoft.azure.devices**, selecione **Instalar** para instalar o pacote **Microsoft.Azure.Devices** e aceite os termos de uso. O procedimento baixa, instala e adiciona uma referência ao [pacote Nuget do SDK do Dispositivo IoT do Azure][lnk-nuget-service-sdk] e suas dependências.

    ![Janela do Gerenciador de Pacotes NuGet][img-servicenuget]
4. Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs** :
   
        using Microsoft.Azure.Devices;
        
5. Adicione os seguintes campos à classe **Program** . Substitua o valor do espaço reservado pela cadeia de conexão do Hub IoT criado na seção anterior e no dispositivo de destino.
   
        static RegistryManager registryManager;
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static JobClient jobClient;
        static string targetDevice = "{deviceIdForTargetDevice}";
        
6. Adicione o seguinte método à classe **Programa**.  Esse código obtém o dispositivo gêmeo para o dispositivo de reinicialização e gera as propriedades relatadas.
   
        public static async Task QueryTwinRebootReported()
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);
            Console.WriteLine(twin.Properties.Reported.ToJson());
        }
        
7. Adicione o seguinte método à classe **Programa**.  Esse código inicia uma reinicialização no dispositivo usando um método direto.

        public static async Task StartReboot()
        {
            client = ServiceClient.CreateFromConnectionString(connString);
            CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
            method.ResponseTimeout = TimeSpan.FromSeconds(30);

            CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

            Console.WriteLine("Invoked firmware update on device.");
        }

7. Por fim, adicione as seguintes linhas ao método **Main** :
   
        registryManager = RegistryManager.CreateFromConnectionString(connString);
        StartReboot().Wait();
        QueryTwinRebootReported().Wait();
        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
        
8. Compilar a solução.

## <a name="create-a-simulated-device-app"></a>Criar um aplicativo de dispositivo simulado
Nesta seção, você irá

* Criar um aplicativo de console do Node.js que responde a um método direto chamado pela nuvem
* Disparar uma reinicialização do dispositivo simulado
* Usar as propriedades relatadas para habilitar consultas de dispositivo gêmeo para identificar dispositivos e a última reinicialização

1. Criar uma nova pasta vazia denominada **manageddevice**.  Na pasta **manageddevice**, crie um arquivo package.json usando o comando a seguir no prompt de comando.  Aceite todos os padrões:
   
    ```
    npm init
    ```
2. No prompt de comando na pasta **manageddevice**, execute o seguinte comando para instalar o pacote **azure-iot-device** do SDK do Dispositivo e o pacote **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Usando um editor de texto, crie um novo arquivo **dmpatterns_getstarted_device.js** na pasta **manageddevice**.
4. Adicione as seguintes instruções ‘require’ no início do arquivo **dmpatterns_getstarted_device.js**:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Adicione uma variável **connectionString** e use-a para criar um **Cliente** do dispositivo.  Substitua a cadeia de conexão de dispositivo pela cadeia de conexão do seu dispositivo.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Adicione a seguinte função para implementar o método direto no dispositivo
   
    ```
    var onReboot = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };
   
        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });
   
        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```
7. Adicione o seguinte código para abrir a conexão com o Hub IoT e inicie o ouvinte do método direto:
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```
8. Salve e feche o arquivo **dmpatterns_getstarted_device.js**.
   
> [!NOTE]
> Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, implemente políticas de repetição (como uma retirada exponencial), como sugerido no artigo [Tratamento de falhas transitórias][lnk-transient-faults] do MSDN.


## <a name="run-the-apps"></a>Executar os aplicativos
Agora você está pronto para executar os aplicativos.

1. No prompt de comando da pasta **manageddevice**, execute o seguinte comando para iniciar a escutar o método direto de reinicialização.
   
    ```
    node dmpatterns_getstarted_device.js
    ```
2. Execute o aplicativo de console do C# **TriggerReboot**. Clique com o botão direito do mouse no projeto **TriggerReboot**, selecione **Depurar** e, em seguida, **Iniciar nova instância**.

3. Você verá a resposta do dispositivo para o método direto no console.

## <a name="customize-and-extend-the-device-management-actions"></a>Personalizar e estender as ações de gerenciamento do dispositivo
As soluções de IoT podem expandir o conjunto definido de padrões de gerenciamento do dispositivo ou habilitar padrões personalizados usando o dispositivo gêmeo e os primitivos do método da nuvem para o dispositivo. Outros exemplos de ações de gerenciamento do dispositivo incluem a redefinição de fábrica, atualização do firmware, atualização do software, gerenciamento de energia, gerenciamento da rede e da conectividade, e criptografia dos dados.

## <a name="device-maintenance-windows"></a>Janelas de manutenção do dispositivo
Normalmente, você pode configurar os dispositivos para executar ações em um horário que minimiza as interrupções e a inatividade.  As janelas de manutenção do dispositivo são um padrão usado para definir a hora em que um dispositivo deve atualizar sua configuração. As soluções de back-end podem usar as propriedades desejadas do dispositivo gêmeo para definir e ativar uma política no dispositivo que permite uma janela de manutenção. Quando um dispositivo recebe a política da janela de manutenção, ele pode usar a propriedade relatada do dispositivo gêmeo para informar o status da política. O aplicativo de back-end pode usar as consultas do dispositivo gêmeo para atestar a conformidade dos dispositivos e cada política.

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você usou um método direto para disparar uma reinicialização remota em um dispositivo. Você usou as propriedades relatadas para relatar a hora da última reinicialização do dispositivo e consultou o dispositivo gêmeo para descobrir a hora da última reinicialização do dispositivo na nuvem.

Para continuar com a introdução ao Hub IoT e aos padrões de gerenciamento de dispositivo como remoto por meio da atualização de firmware de ar, consulte:

[Tutorial: Como realizar uma atualização de firmware][lnk-fwupdate]

Para saber como estender sua solução de IoT e agendar chamadas de método em vários dispositivos, confira o tutorial [Agendar e difundir trabalhos][lnk-tutorial-jobs].

Para continuar a introdução ao Hub IoT, confira [Introdução ao SDK do Gateway IoT][lnk-gateway-SDK].

<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png
[img-servicenuget]: media/iot-hub-csharp-node-device-management-get-started/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-device-management-get-started/createnetapp.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
