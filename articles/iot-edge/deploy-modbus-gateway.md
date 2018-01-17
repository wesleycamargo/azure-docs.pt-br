---
title: Implantar o Modbus no Azure IoT Edge | Microsoft Docs
description: "Permitir que os dispositivos que usam o Modbus TCP se comuniquem com o Hub IoT do Azure por meio da criação de um dispositivo de gateway do IoT Edge"
services: iot-Edge
documentationcenter: 
author: kgremban
manager: timlt
editor: chrisgmsft
ms.assetid: 
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2017
ms.author: kgremban
ms.custom: 
ms.openlocfilehash: e239bde48c3da0d899e3c78bdd39f520c4128b95
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2018
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway---preview"></a>Conectar dispositivos Modbus TCP por meio de um gateway de dispositivo do IoT Edge - versão prévia

Se você quiser conectar dispositivos IoT que usam os protocolos Modbus TCP ou RTU a um hub IoT do Azure, use um dispositivo IoT Edge como um gateway. O dispositivo de gateway lê os dados dos dispositivos Modbus e comunica esses dados à nuvem usando um protocolo com suporte. 

![Os dispositivos Modbus se conectam ao Hub IoT por meio do gateway do Edge](./media/deploy-modbus-gateway/diagram.png)

Este artigo trata de como criar sua própria imagem de contêiner para um módulo Modbus (ou você pode usar um exemplo pré-compilado) e, em seguida, implantá-lo no dispositivo IoT Edge que atuará como seu gateway. 

Este artigo pressupõe que você está usando o protocolo TCP Modbus. Para obter mais informações sobre como configurar o módulo para dar suporte ao Modbus RTU, consulte o projeto [Módulo Modbus do Azure IoT Edge](https://github.com/Azure/iot-edge-modbus) no Github. 

## <a name="prerequisites"></a>Pré-requisitos
* Um dispositivo do Azure IoT Edge. Para obter instruções sobre como configurar, veja [Implantar o Azure IoT Edge em um dispositivo simulado no Windows](tutorial-simulate-device-windows.md) ou [no Linux](tutorial-simulate-device-linux.md). 
* A cadeia de caracteres de conexão de chave primária para o dispositivo IoT Edge.
* Um dispositivo Modbus simulado ou físico que oferece suporte ao Modbus TCP.

## <a name="prepare-a-modbus-container"></a>Preparar um contêiner Modbus

Se você quiser testar a funcionalidade de gateway Modbus, a Microsoft tem um exemplo de módulo que pode usar. Para usar o exemplo de módulo, vá à seção [Executar a solução](#run-the-solution) e insira o seguinte como o URI de imagem: 

```URL
microsoft/azureiotedge-modbus-tcp:1.0-preview
```

Se quiser criar seu próprio módulo e personalizá-lo para o seu ambiente, há um projeto de código aberto [Módulo Modbus do Azure IoT Edge](https://github.com/Azure/iot-edge-modbus) no Github. Siga as orientações no projeto para criar sua própria imagem de contêiner. Se você criar sua própria imagem de contêiner, consulte [Desenvolver e implantar um módulo do C# IoT Edge](tutorial-csharp-module.md) para obter instruções sobre como publicar imagens de contêiner em um registro e implantar um módulo personalizado em seu dispositivo. 


## <a name="run-the-solution"></a>Executar a solução
1. No [Portal do Azure](https://portal.azure.com/), vá ao hub IoT.
2. Vá para **IoT Edge (versão prévia)** e selecione o dispositivo IoT Edge.
3. Selecione **Definir módulos**.
4. Adicione o módulo Modbus:
   1. Selecione **Adicionar módulo do IoT Edge**.
   2. No campo **Nome**, digite "modbus".
   3. No campo **Imagem**, digite o URI de imagem do exemplo de contêiner: `microsoft/azureiotedge-modbus-tcp:1.0-preview`.
   4. Marque a caixa **Habilitar** para atualizar as propriedades desejadas do gêmeo do módulo.
   5. Copie o JSON a seguir para a caixa de texto. Altere o valor de **SlaveConnection** para o endereço IPv4 do seu dispositivo Modbus.

      ```JSON
      {  
        "properties.desired":{  
          "PublishInterval":"2000",
          "SlaveConfigs":{  
            "Slave01":{  
              "SlaveConnection":"<IPV4 address>",
              "HwId":"PowerMeter-0a:01:01:01:01:01",
              "Operations":{  
                "Op01":{  
                  "PollingInterval": "1000",
                  "UnitId":"1",
                  "StartAddress":"400001",
                  "Count":"2",
                  "DisplayName":"Voltage"
                }
              }
            }
          }
        }
      }
      ```

   6. Selecione **Salvar**.
5. De volta à etapa **Adicionar módulos**, clique em **Avançar**.
7. Na etapa **Especificar Rotas**, copie o JSON seguinte para a caixa de texto. Essa rota envia todas as mensagens coletadas pelo módulo Modbus ao Hub IoT. Nessa rota, ''modbusOutput'' é o ponto de extremidade usado pelo módulo do Modbus para gerar dados, e ''upstream'' é um destino especial que faz com que o Hub de Borda envie mensagens ao Hub IoT. 
   ```JSON
   {
    "routes": {
      "modbusToIoTHub":"FROM /messages/modules/modbus/outputs/modbusOutput INTO $upstream"
    }
   }
   ```

8. Selecione **Avançar**. 
9. Na etapa **Revisar Modelo**, selecione **Enviar**. 
10. Volte para a página de detalhes do dispositivo e selecione **Atualizar**. Você deverá ver o novo **modbus** em execução junto com o tempo de execução do IoT Edge.

## <a name="view-data"></a>Exibir dados
Exiba os dados provenientes do módulo modbus:
```cmd/sh
docker logs -f modbus
```

Usando a [ferramenta de Gerenciador do Hub IoT](https://github.com/azure/iothub-explorer), você também pode exibir a telemetria sendo enviada pelo dispositivo. 

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como os dispositivos IoT Edge podem agir como gateways, veja [Criar um dispositivo IoT Edge que atua como um gateway transparente](how-to-create-transparent-gateway.md)
- Para obter mais informações sobre como os módulos IoT Edge funcionam, veja [Entendendo os módulos do Azure IoT Edge](iot-edge-modules.md)