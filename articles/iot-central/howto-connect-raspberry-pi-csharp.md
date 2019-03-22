---
title: Conectar um Raspberry Pi ao aplicativo Azure IoT Central (C#) | Microsoft Docs
description: Como um desenvolvedor de dispositivos, saiba como conectar um Raspberry Pi ao aplicativo Azure IoT Central usando C#.
author: viv-liu
ms.author: viviali
ms.date: 10/31/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 6330e941f3308920ff4d5404663824633484146a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58108351"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-c"></a>Conectar um Raspberry Pi ao aplicativo Azure IoT Central (C#)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

Este artigo descreve como um desenvolvedor de dispositivos conecta um Raspberry Pi ao aplicativo Microsoft Azure IoT Central usando a linguagem de programação C#.

## <a name="before-you-begin"></a>Antes de começar

Para concluir as etapas deste artigo, você precisa dos seguintes componentes:

* [.NET Core 2](https://www.microsoft.com/net) instalado no computador de desenvolvimento. Também é necessário ter um editor de código adequado, como [Visual Studio Code](https://code.visualstudio.com/).
* Um aplicativo Azure IoT Central criado a partir do modelo de aplicativo de **Devkits de Exemplo**. Para obter mais informações, consulte o [Criar um início rápido de aplicativo](quick-deploy-iot-central.md).
* Um dispositivo Raspberry Pi executando o sistema operacional Raspbian.


## <a name="sample-devkits-application"></a>Aplicativo **Devkits de exemplo**

Um aplicativo criado a partir do modelo de aplicativo de **Devkits de Exemplo** inclui um modelo de dispositivo **Raspberry Pi** com as características a seguir: 

- Telemetria, que inclui as seguintes medidas que o dispositivo coletará:
    - Umidade
    - Temperatura
    - Pressão
    - Magnetômetro (X, Y, Z)
    - Acelerômetro (X, Y, Z)
    - Giroscópio (X, Y, Z)
- Configurações
    - Voltagem
    - Atual
    - Velocidade da ventoinha
    - Alternar IR.
- propriedades
    - Propriedade do dispositivo número de dado
    - Propriedade de localização da nuvem 

Para obter detalhes completos sobre a configuração do modelo de dispositivo, veja os [detalhes do modelo de Dispositivo do Raspberry PI](howto-connect-raspberry-pi-csharp.md#raspberry-pi-device-template-details)


## <a name="add-a-real-device"></a>Adicionar um dispositivo real

No aplicativo Azure IoT Central, adicione um dispositivo real do modelo de dispositivo **Raspberry Pi** e anote a cadeia de conexão do dispositivo. Para obter mais informações, consulte [Adicionar um dispositivo real ao aplicativo Azure IoT Central](tutorial-add-device.md).

### <a name="create-your-net-application"></a>Criar o aplicativo .NET

Você cria e testa o aplicativo do dispositivo no computador desktop.

Para concluir as etapas a seguir, você pode usar o Visual Studio Code. Para saber mais, consulte [Trabalhando com C#](https://code.visualstudio.com/docs/languages/csharp).

> [!NOTE]
> Se preferir, você pode concluir as etapas a seguir usando um editor de código diferente.

1. Para inicializar o projeto .NET e adicionar os pacotes necessários do NuGet, execute os comandos a seguir:

   ```cmd/sh
   mkdir pisample
   cd pisample
   dotnet new console
   dotnet add package Microsoft.Azure.Devices.Client
   dotnet restore
   ```

1. Abra a pasta `pisample` no Visual Studio Code. Em seguida, abra o arquivo de projeto **pisample.csproj**. Adicione a marca `<RuntimeIdentifiers>` mostrada no seguinte snippet de código:

    ```xml
    <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.0</TargetFramework>
        <RuntimeIdentifiers>win-arm;linux-arm</RuntimeIdentifiers>
      </PropertyGroup>
      <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Devices.Client" Version="1.5.2" />
      </ItemGroup>
    </Project>
    ```

    > [!NOTE]
    > O número de versão do pacote **Microsoft.Azure.Devices.Client** pode ser maior que o mostrado.

1. Salve o **pisample.csproj**. Se o Visual Studio Code solicitar que você execute o comando de restauração, escolha **Restaurar**.

1. Abra **Program.cs** e substitua o conteúdo pelo código a seguir:

    ```csharp
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;

    namespace pisample
    {
      class Program
      {
        static string DeviceConnectionString = "{your device connection string}";
        static DeviceClient Client = null;
        static TwinCollection reportedProperties = new TwinCollection();
        static CancellationTokenSource cts;
        static double baseTemperature = 60;
        static double basePressure = 500;
        static double baseHumidity = 50;
        static void Main(string[] args)
        {
          Console.WriteLine("Raspberry Pi Azure IoT Central example");

          try
          {
            InitClient();
            SendDeviceProperties();

            cts = new CancellationTokenSource();
            SendTelemetryAsync(cts.Token);

            Console.WriteLine("Wait for settings update...");
            Client.SetDesiredPropertyUpdateCallbackAsync(HandleSettingChanged, null).Wait();
            Console.ReadKey();
            cts.Cancel();
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        public static void InitClient()
        {
          try
          {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        public static async void SendDeviceProperties()
        {
          try
          {
            Console.WriteLine("Sending device properties:");
            Random random = new Random();
            TwinCollection telemetryConfig = new TwinCollection();
            reportedProperties["dieNumber"] = random.Next(1, 6);
            Console.WriteLine(JsonConvert.SerializeObject(reportedProperties));

            await Client.UpdateReportedPropertiesAsync(reportedProperties);
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        private static async void SendTelemetryAsync(CancellationToken token)
        {
          try
          {
            Random rand = new Random();

            while (true)
            {
              double currentTemperature = baseTemperature + rand.NextDouble() * 20;
              double currentPressure = basePressure + rand.NextDouble() * 100;
              double currentHumidity = baseHumidity + rand.NextDouble() * 20;

              var telemetryDataPoint = new
              {
                humidity = currentHumidity,
                pressure = currentPressure,
                temp = currentTemperature
              };
              var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
              var message = new Message(Encoding.ASCII.GetBytes(messageString));

              token.ThrowIfCancellationRequested();
              await Client.SendEventAsync(message);

              Console.WriteLine("{0} > Sending telemetry: {1}", DateTime.Now, messageString);

              await Task.Delay(1000);
            }
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Intentional shutdown: {0}", ex.Message);
          }
        }

        private static async Task HandleSettingChanged(TwinCollection desiredProperties, object userContext)
        {
          try
          {
            Console.WriteLine("Received settings change...");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            string setting = "fanSpeed";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "setVoltage";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "setCurrent";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "activateIR";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            await Client.UpdateReportedPropertiesAsync(reportedProperties);
          }

          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        private static void AcknowledgeSettingChange(TwinCollection desiredProperties, string setting)
        {
          reportedProperties[setting] = new
          {
            value = desiredProperties[setting]["value"],
            status = "completed",
            desiredVersion = desiredProperties["$version"],
            message = "Processed"
          };
        }
      }
    }
    ```

    > [!NOTE]
    > Você atualiza o espaço reservado `{your device connection string}` na próxima etapa.

## <a name="run-your-net-application"></a>Executar o aplicativo .NET

Adicione a cadeia de conexão específica do dispositivo ao código do dispositivo para autenticação no Azure IoT Central. Você fez uma anotação dessa cadeia de conexão quando adicionou o dispositivo real ao aplicativo Azure IoT Central.

  > [!NOTE]
   > Azure IoT Central fez a transição para usar o serviço de provisionamento de dispositivos do Azure IoT Hub (DPS) para todas as conexões de dispositivo, siga estas instruções para [obter a cadeia de caracteres de conexão do dispositivo](concepts-connectivity.md#get-a-connection-string) e continue com o restante do tutorial.

1. Substitua `{your device connection string}` no **Program.cs** com a cadeia de conexão que você anotou anteriormente.

1. Execute o comando a seguir no ambiente de linha de comando:

   ```cmd/sh
   dotnet restore
   dotnet publish -r linux-arm
   ```

1. Copie a pasta `pisample\bin\Debug\netcoreapp2.0\linux-arm\publish` ao dispositivo Raspberry Pi. É possível usar o comando **scp** para copiar os arquivos, por exemplo:

    ```cmd/sh
    scp -r publish pi@192.168.0.40:publish
    ```

    Para obter mais informações, consulte [Acesso remoto do Raspberry Pi](https://www.raspberrypi.org/documentation/remote-access/).

1. Entre no dispositivo Raspberry Pi e execute os comandos a seguir em um shell:

    ```cmd/sh
    sudo apt-get update
    sudo apt-get install libc6 libcurl3 libgcc1 libgssapi-krb5-2 liblttng-ust0 libstdc++6 libunwind8 libuuid1 zlib1g
    ```

1. No Raspberry Pi, execute os comandos a seguir:

    ```cmd/sh
    cd publish
    chmod 777 pisample
    ./pisample
    ```

    ![O programa inicia](./media/howto-connect-raspberry-pi-csharp/device_begin.png)

1. No aplicativo Azure IoT Central, é possível ver como o código em execução no Raspberry Pi interage com o aplicativo:

   * Na página **Medidas** do dispositivo real, é possível ver a telemetria.
   * Na página **Propriedades**, é possível ver o valor da propriedade do **Número Impresso**.
   * Na página **Configurações**, é possível alterar várias configurações no Raspberry Pi, como voltagem e velocidade da ventoinha.

     A captura de tela a seguir mostra o Raspberry Pi recebendo a alteração de configuração:

     ![Raspberry Pi recebe a alteração de configuração](./media/howto-connect-raspberry-pi-csharp/device_switch.png)


## <a name="raspberry-pi-device-template-details"></a>Detalhes de modelo do Dispositivo Raspberry PI

Um aplicativo criado a partir do modelo de aplicativo de **Devkits de Exemplo** inclui um modelo de dispositivo **Raspberry Pi** com as características a seguir:

### <a name="telemetry-measurements"></a>Medidas de telemetria

| Nome do campo     | Unidades  | Mínimo | Máximo | Casas decimais |
| -------------- | ------ | ------- | ------- | -------------- |
| umidade       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressão       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>Configurações

Configurações numéricas

| Nome de exibição | Nome do campo | Unidades | Casas decimais | Mínimo | Máximo | Inicial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Voltagem      | setVoltage | Volts | 0              | 0       | 240     | 0       |
| Atual      | setCurrent | Amps  | 0              | 0       | 100     | 0       |
| Velocidade da ventoinha    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

Configurações de alternância

| Nome de exibição | Nome do campo | Texto ativado | Texto desativado | Inicial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ATIVADO      | DESATIVADO      | Desativar     |

### <a name="properties"></a>propriedades

| Type            | Nome de exibição | Nome do campo | Tipo de dados |
| --------------- | ------------ | ---------- | --------- |
| Propriedade de dispositivo | Número impresso   | dieNumber  | número    |
| Texto            | Local padrão     | location   | N/D       |

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a conectar um Raspberry Pi ao aplicativo Azure IoT Central, as próximas etapas sugeridas são apresentadas:

* [Conectar um aplicativo cliente Node.js genérico ao Azure IoT Central](howto-connect-nodejs.md)
