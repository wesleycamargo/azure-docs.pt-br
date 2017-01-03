---
title: "Introdução aos dispositivos gêmeos | Microsoft Docs"
description: "Este tutorial mostra como usar dispositivos gêmeos"
services: iot-hub
documentationcenter: node
author: fsautomata
manager: timlt
editor: 
ms.assetid: f7e23b6e-bfde-4fba-a6ec-dbb0f0e005f4
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: 00746fa67292fa6858980e364c88921d60b29460
ms.openlocfilehash: 4b90b2529fa6d763ed3ce9c3b4da07afeb860f0e


---
# <a name="tutorial-get-started-with-device-twins"></a>Tutorial: Introdução aos dispositivos gêmeos
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

No fim deste tutorial, você terá dois aplicativos de console, um .NET e um Node.js:

* **AddTagsAndQuery.sln**, um aplicativo .NET que deve ser executado do back-end e que adiciona marcas e consultas aos dispositivos gêmeos.
* **TwinSimulatedDevice.js**, um aplicativo Node.js que simula um dispositivo que se conecta ao seu Hub IoT com a identidade do dispositivo criada anteriormente e reporta sua condição de conectividade.

> [!NOTE]
> O artigo [SDKs do IoT do Azure][lnk-hub-sdks] fornece informações sobre os SDKs do IoT do Azure que você pode usar para compilar dispositivos e aplicativos back-end.
> 
> 

Para concluir este tutorial, você precisará do seguinte:

* Microsoft Visual Studio 2015.
* Node.js versão 0.10.x ou posterior.
* Uma conta ativa do Azure. (Se você não tem uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-the-service-app"></a>Criar o aplicativo do serviço
Nesta seção, você cria um aplicativo de console do Node.js que adiciona metadados de local ao dispositivo gêmeo associado a **myDeviceId**. Ele então consulta os dispositivos gêmeos armazenados no Hub IoT selecionando os dispositivos localizados nos EUA e depois aqueles que indicam uma conexão celular.

1. No Visual Studio, adicione um projeto da Área de Trabalho Clássica do Windows no Visual C# à solução atual usando o modelo de projeto **Aplicativo do Console** . Nomeie o projeto **AddTagsAndQuery**.
   
    ![Novo projeto da Área de Trabalho Clássica do Windows no Visual C#][img-createapp]
2. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **AddTagsAndQuery** e clique em **Gerenciar Pacotes Nuget**.
3. Na janela **Gerenciador de Pacotes Nuget**, selecione **Procurar**, procure **microsoft.azure.devices**, selecione **Instalar** para instalar o pacote **Microsoft.Azure.Devices** e aceite os termos de uso. O procedimento baixa, instala e adiciona uma referência ao pacote Nuget do [SDK de Serviço IoT do Microsoft Azure][lnk-nuget-service-sdk] e suas dependências.
   
    ![Janela Gerenciador de Pacotes Nuget][img-servicenuget]
4. Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs** :
   
        using Microsoft.Azure.Devices;
5. Adicione os seguintes campos à classe **Program** . Substitua o valor do espaço reservado pela cadeia de conexão do hub IoT criado na seção anterior.
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
6. Adicione o seguinte método à classe **Programa** :
   
        public static async Task AddTagsAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch =
                @"{
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                        }
                    }
                }";
            await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);
   
            var query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            var twinsInRedmond43 = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43: {0}", string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));
   
            query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43 using cellular network: {0}", string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
        }
   
    A classe **RegistryManager** expõe todos os métodos necessários para interagir com gêmeos de dispositivo do serviço. O código anterior inicializa primeiro o objeto **registryManager**, depois recupera o dispositivo gêmeo para **myDeviceId** e, finalmente, atualiza as marcações com as informações do local desejado.
   
    Após a atualização, ele executa duas consultas: a primeira seleciona somente os dispositivos gêmeos de dispositivos localizados na fábrica de **Redmond43**, e o segundo aperfeiçoa a consulta para selecionar somente os dispositivos que também estão conectados por meio de rede celular.
   
    Observe que o código anterior, quando ele cria o objeto **query**, especifica um número máximo de documentos retornados. O objeto **query** contém uma propriedade booliana **HasMoreResults** que você pode usar para invocar os métodos **GetNextAsTwinAsync** várias vezes para recuperar todos os resultados. Um método chamado **GetNextAsJson** está disponível para os resultados que não são de dispositivos gêmeos, por exemplo, resultados de consultas de agregação.
7. Por fim, adicione as seguintes linhas ao método **Main** :
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddTagsAndQuery().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();
8. Execute esse aplicativo e você deverá ver um dispositivo nos resultados da consulta que pergunta sobre todos os dispositivos localizados em **Redmond43**, e nenhum para a consulta que restringe os resultados para dispositivos que usam uma rede de celular.
   
    ![Resultados da consulta na janela][img-addtagapp]

Na próxima seção, você criará um aplicativo de dispositivo que reporta as informações de conectividade e altera o resultado da consulta na seção anterior.

## <a name="create-the-device-app"></a>Criar o aplicativo do dispositivo
Nesta seção, você cria um aplicativo de console do Node.js que se conecta ao seu hub como **myDeviceId** e depois atualiza suas propriedades reportadas para conter as informações indicando que ele está conectado usando uma rede celular.

1. Crie uma nova pasta vazia denominada **reportconnectivity**. Na pasta **reportconnectivity**, crie um novo arquivo package.json usando o comando a seguir no prompt de comando. Aceite todos os padrões:
   
    ```
    npm init
    ```
2. No prompt de comando, na pasta **reportconnectivity**, execute o seguinte comando para instalar os pacotes **azure-iot-device** e **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Usando um editor de texto, crie um novo arquivo **ReportConnectivity.js** na pasta **reportconnectivity**.
4. Adicione o seguinte código ao arquivo **ReportConnectivity.js** e substitua o espaço reservado **{device connection string}** pela cadeia de conexão copiada quando você criou a identidade do dispositivo **myDeviceId**:
   
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
   
            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };
   
                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });
   
    O objeto **Client** expõe todos os métodos necessários para você interagir com dispositivos gêmeos a partir do dispositivo. O código anterior, depois de inicializar o objeto **Client**, recupera o dispositivo gêmeo para **myDeviceId** e atualiza suas propriedades reportadas com as informações de conectividade.
5. Executar o aplicativo do dispositivo
   
        node ReportConnectivity.js
   
    Você deve ver a mensagem `twin state reported`.
6. Agora que o dispositivo divulgou suas informações de conectividade, ele deve aparecer em ambas as consultas. Execute o aplicativo .NET **AddTagsAndQuery** para executar as consultas novamente. Desta vez, **myDeviceId** deve aparecer em ambos os resultados da consulta.
   
    ![][img-addtagapp2]

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você configurou um novo hub IoT no portal do Azure e depois criou uma identidade do dispositivo no Registro de identidade do Hub IoT. Você adicionou os metadados do dispositivo como marcações de um aplicativo back-end e escreveu um aplicativo de dispositivo simulado para reportar informações de conectividade do dispositivo no dispositivo gêmeo. Você também aprendeu a consultar essas informações usando a linguagem de consulta do Hub IoT semelhante ao SQL.

Veja os recursos a seguir para saber como:

* Enviar telemetria de dispositivos com o tutorial [Introdução ao Hub IoT][lnk-iothub-getstarted].
* Configurar dispositivos usando as propriedades desejadas do dispositivo gêmeo com o tutorial [Usar propriedades desejadas para configurar dispositivos][lnk-twin-how-to-configure].
* Controlar dispositivos interativamente (como ativar uma ventoinha de um aplicativo controlado pelo usuário), com o tutorial [Uso de métodos diretos][lnk-methods-tutorial].

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-twin-getstarted/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-twin-getstarted/createnetapp.png
[img-addtagapp]: media/iot-hub-csharp-node-twin-getstarted/addtagapp.png
[img-addtagapp2]: media/iot-hub-csharp-node-twin-getstarted/addtagapp2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-twin-how-to-configure]: iot-hub-csharp-node-twin-how-to-configure.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md




<!--HONumber=Nov16_HO5-->


