---
title: Conectar um dispositivo usando C no Windows | Microsoft Docs
description: "Descreve como conectar um dispositivo à solução pré-configurada de monitoramento remoto do Azure IoT Suite usando um aplicativo escrito em C em execução no Windows."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 34e39a58-2434-482c-b3fa-29438a0c05e8
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: ba7a47bf66c106c75565682a71f870aa561cd827
ms.openlocfilehash: 61373e3cf9c1630747fc70ab289e125217f4af82


---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-windows"></a>Conectar seu dispositivo à solução pré-configurada de monitoramento remoto (Windows)
[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="create-a-c-sample-solution-on-windows"></a>Criar um exemplo de solução C no Windows
As etapas a seguir mostram como usar o Visual Studio para criar um aplicativo cliente escrito em C que se comunica com a solução pré-configurada de Monitoramento Remoto.

Crie um projeto inicial no Visual Studio 2015 e adicione os pacotes NuGet de cliente do dispositivo Hub IoT:

1. No Visual Studio 2015, crie um aplicativo de console C usando o modelo **Aplicativo do Console Win32** do Visual C++. Dê o nome de **RMDevice**ao projeto.
2. Na página **Configurações de Aplicativos** no **Assistente do Aplicativo Win32**, verifique se **Aplicativo do Console** está selecionado e desmarque **Cabeçalho pré-compilado** e **Verificações do SDL (Security Development Lifecycle)**.
3. No **Gerenciador de Soluções**, exclua os arquivos stdafx.h, targetver.h e stdafx.cpp.
4. No **Gerenciador de Soluções**, renomeie o arquivo RMDevice.cpp para RMDevice.c.
5. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **RMDevice** e clique em **Gerenciar Pacotes NuGet**. Clique em **Procurar**para encontrar e instalar os seguintes pacotes NuGet no projeto:
   
   * Microsoft.Azure.IoTHub.Serializer
   * Microsoft.Azure.IoTHub.IoTHubClient
   * Microsoft.Azure.IoTHub.HttpTransport
6. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **RMDevice** e clique em **Propriedades** para abrir a caixa de diálogo **Páginas de Propriedade**. Para obter detalhes, confira [Configurando as propriedades do projeto no Visual C++][lnk-c-project-properties]. 
7. Clique na pasta **Vinculador** e clique na página de propriedade **Entrada**.
8. Adicione **crypt32.lib** à propriedade **Dependências Adicionais**. Clique em **OK** e **OK** novamente para salvar os valores da propriedade do projeto.

## <a name="specify-the-behavior-of-the-iot-hub-device"></a>Especificar o comportamento do dispositivo Hub IoT
As bibliotecas de cliente do Hub IoT usam um modelo para especificar o formato das mensagens que o dispositivo envia ao Hub IoT e os comandos recebidos do Hub IoT.

1. No Visual Studio, abra o arquivo RMDevice.c. Substitua as instruções `#include` existentes pelo seguinte código:
   
    ```
    #include "iothubtransporthttp.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    ```
2. Adicione as declarações de variável a seguir após as instruções `#include` . Substitua os valores do espaço reservado [ID do Dispositivo] e [Chave do Dispositivo] pelos valores do seu dispositivo no painel da solução de monitoramento remoto. Use o nome de host do Hub IoT no painel para substituir [IoTHub Name]. Por exemplo, se o nome de host do Hub IoT for **contoso.azure-devices.net**, substitua [Nome do HubIoT] por **contoso**:
   
    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "azure-devices.net";
    ```
3. Adicione o código a seguir para definir o modelo que permite ao dispositivo se comunicar com o Hub IoT. Esse modelo especifica que o dispositivo envia temperatura, temperatura externa, umidade e uma ID de dispositivo como telemetria. O dispositivo também envia metadados sobre o dispositivo ao Hub IoT, incluindo uma lista de comandos permitidos pelo dispositivo. Esse dispositivo responde aos comandos **SetTemperature** e **SetHumidity**:
   
    ```
    // Define the Model
    BEGIN_NAMESPACE(Contoso);
   
    DECLARE_STRUCT(SystemProperties,
    ascii_char_ptr, DeviceID,
    _Bool, Enabled
    );
   
    DECLARE_STRUCT(DeviceProperties,
    ascii_char_ptr, DeviceID,
    _Bool, HubEnabledState
    );
   
    DECLARE_MODEL(Thermostat,
   
    /* Event data (temperature, external temperature and humidity) */
    WITH_DATA(int, Temperature),
    WITH_DATA(int, ExternalTemperature),
    WITH_DATA(int, Humidity),
    WITH_DATA(ascii_char_ptr, DeviceId),
   
    /* Device Info - This is command metadata + some extra fields */
    WITH_DATA(ascii_char_ptr, ObjectType),
    WITH_DATA(_Bool, IsSimulatedDevice),
    WITH_DATA(ascii_char_ptr, Version),
    WITH_DATA(DeviceProperties, DeviceProperties),
    WITH_DATA(ascii_char_ptr_no_quotes, Commands),
   
    /* Commands implemented by the device */
    WITH_ACTION(SetTemperature, int, temperature),
    WITH_ACTION(SetHumidity, int, humidity)
    );
   
    END_NAMESPACE(Contoso);
    ```

## <a name="implement-the-behavior-of-the-device"></a>Implementar o comportamento do dispositivo
Agora adicione o código que implementa o comportamento definido no modelo.

1. Adicione as seguintes funções que serão executadas quando o dispositivo receber os comandos **SetTemperature** e **SetHumidity** do Hub IoT:
   
    ```
    EXECUTE_COMMAND_RESULT SetTemperature(Thermostat* thermostat, int temperature)
    {
      (void)printf("Received temperature %d\r\n", temperature);
      thermostat->Temperature = temperature;
      return EXECUTE_COMMAND_SUCCESS;
    }
   
    EXECUTE_COMMAND_RESULT SetHumidity(Thermostat* thermostat, int humidity)
    {
      (void)printf("Received humidity %d\r\n", humidity);
      thermostat->Humidity = humidity;
      return EXECUTE_COMMAND_SUCCESS;
    }
    ```
2. Adicione a seguinte função que envia uma mensagem ao Hub IoT:
   
    ```
    static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
    {
      IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
      if (messageHandle == NULL)
      {
        printf("unable to create a new IoTHubMessage\r\n");
      }
      else
      {
        if (IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, NULL, NULL) != IOTHUB_CLIENT_OK)
        {
          printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
          printf("IoTHubClient accepted the message for delivery\r\n");
        }
   
        IoTHubMessage_Destroy(messageHandle);
      }
    free((void*)buffer);
    }
    ```
3. Adicione a seguinte função que conecta a biblioteca de serialização no SDK:
   
    ```
    static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
      IOTHUBMESSAGE_DISPOSITION_RESULT result;
      const unsigned char* buffer;
      size_t size;
      if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
      {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
      }
      else
      {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
          printf("failed to malloc\r\n");
          result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
          memcpy(temp, buffer, size);
          temp[size] = '\0';
          EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
          result =
            (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
            (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
            IOTHUBMESSAGE_REJECTED;
          free(temp);
        }
      }
      return result;
    }
    ```
4. Adicione a função a seguir para se conectar ao Hub IoT, enviar e receber mensagens e se desconectar do hub. Observe como o dispositivo envia metadados sobre si, incluindo os comandos que ele suporta, ao hub IoT quando ele se conecta. Esses metadados permitem que a solução atualize o status do dispositivo para **Executando** no painel:
   
    ```
    void remote_monitoring_run(void)
    {
      if (serializer_init(NULL) != SERIALIZER_OK)
      {
        printf("Failed on serializer_init\r\n");
      }
      else
      {
        IOTHUB_CLIENT_CONFIG config;
        IOTHUB_CLIENT_HANDLE iotHubClientHandle;
   
        config.deviceId = deviceId;
        config.deviceKey = deviceKey;
        config.iotHubName = hubName;
        config.iotHubSuffix = hubSuffix;
        config.protocol = HTTP_Protocol;
        config.deviceSasToken = NULL;
        config.protocolGatewayHostName = NULL;
        iotHubClientHandle = IoTHubClient_Create(&config);
        if (iotHubClientHandle == NULL)
        {
          (void)printf("Failed on IoTHubClient_CreateFromConnectionString\r\n");
        }
        else
        {
          Thermostat* thermostat = CREATE_MODEL_INSTANCE(Contoso, Thermostat);
          if (thermostat == NULL)
          {
            (void)printf("Failed on CREATE_MODEL_INSTANCE\r\n");
          }
          else
          {
            STRING_HANDLE commandsMetadata;
   
            if (IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, thermostat) != IOTHUB_CLIENT_OK)
            {
              printf("unable to IoTHubClient_SetMessageCallback\r\n");
            }
            else
            {
   
              /* send the device info upon startup so that the cloud app knows
              what commands are available and the fact that the device is up */
              thermostat->ObjectType = "DeviceInfo";
              thermostat->IsSimulatedDevice = false;
              thermostat->Version = "1.0";
              thermostat->DeviceProperties.HubEnabledState = true;
              thermostat->DeviceProperties.DeviceID = (char*)deviceId;
   
              commandsMetadata = STRING_new();
              if (commandsMetadata == NULL)
              {
                (void)printf("Failed on creating string for commands metadata\r\n");
              }
              else
              {
                /* Serialize the commands metadata as a JSON string before sending */
                if (SchemaSerializer_SerializeCommandMetadata(GET_MODEL_HANDLE(Contoso, Thermostat), commandsMetadata) != SCHEMA_SERIALIZER_OK)
                {
                  (void)printf("Failed serializing commands metadata\r\n");
                }
                else
                {
                  unsigned char* buffer;
                  size_t bufferSize;
                  thermostat->Commands = (char*)STRING_c_str(commandsMetadata);
   
                  /* Here is the actual send of the Device Info */
                  if (SERIALIZE(&buffer, &bufferSize, thermostat->ObjectType, thermostat->Version, thermostat->IsSimulatedDevice, thermostat->DeviceProperties, thermostat->Commands) != CODEFIRST_OK)
                  {
                    (void)printf("Failed serializing\r\n");
                  }
                  else
                  {
                    sendMessage(iotHubClientHandle, buffer, bufferSize);
                  }
   
                }
   
                STRING_delete(commandsMetadata);
              }
   
              thermostat->Temperature = 50;
              thermostat->ExternalTemperature = 55;
              thermostat->Humidity = 50;
              thermostat->DeviceId = (char*)deviceId;
   
              while (1)
              {
                unsigned char*buffer;
                size_t bufferSize;
   
                (void)printf("Sending sensor value Temperature = %d, Humidity = %d\r\n", thermostat->Temperature, thermostat->Humidity);
   
                if (SERIALIZE(&buffer, &bufferSize, thermostat->DeviceId, thermostat->Temperature, thermostat->Humidity, thermostat->ExternalTemperature) != CODEFIRST_OK)
                {
                  (void)printf("Failed sending sensor value\r\n");
                }
                else
                {
                  sendMessage(iotHubClientHandle, buffer, bufferSize);
                }
   
                ThreadAPI_Sleep(1000);
              }
            }
   
            DESTROY_MODEL_INSTANCE(thermostat);
          }
          IoTHubClient_Destroy(iotHubClientHandle);
        }
        serializer_deinit();
      }
    }
    ```
   
    Para referência, veja um exemplo de mensagem de **DeviceInfo** enviada ao Hub IoT na inicialização:
   
    ```
    {
      "ObjectType":"DeviceInfo",
      "Version":"1.0",
      "IsSimulatedDevice":false,
      "DeviceProperties":
      {
        "DeviceID":"mydevice01", "HubEnabledState":true
      }, 
      "Commands":
      [
        {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
        { "Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
      ]
    }
    ```
   
    Para referência, veja um exemplo de mensagem de **Telemetria** enviada ao Hub IoT:
   
    ```
    {"DeviceId":"mydevice01", "Temperature":50, "Humidity":50, "ExternalTemperature":55}
    ```
   
    Para referência, veja um exemplo de **Comando** recebido do Hub IoT:
   
    ```
    {
      "Name":"SetHumidity",
      "MessageId":"2f3d3c75-3b77-4832-80ed-a5bb3e233391",
      "CreatedTime":"2016-03-11T15:09:44.2231295Z",
      "Parameters":{"humidity":23}
    }
    ```
5. Substitua a função **main** pelo seguinte código para invocar a função **remote_monitoring_run**:
   
    ```
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```
6. Clique em **Compilar** e em **Compilar Solução** para compilar o aplicativo do dispositivo.
7. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **RMDevice**, clique em **Depurar** e em **Iniciar nova instância** para executar o exemplo. O console exibe mensagens à medida que o aplicativo envia a telemetria de exemplo ao Hub IoT e recebe comandos.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-c-project-properties]: https://msdn.microsoft.com/library/669zx6zc.aspx



<!--HONumber=Jan17_HO1-->


