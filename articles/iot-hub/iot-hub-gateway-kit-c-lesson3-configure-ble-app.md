---
title: "Dispositivo SensorTag e Gateway do IoT do Azure - Lição 3: executar aplicativo de exemplo| Microsoft Docs"
description: Execute um aplicativo de exemplo BLE para receber dados do BLE SensorTag em seu Hub IoT.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: aplicativo ble, aplicativo de monitor de sensor, coleta de dados do sensor, dados de sensores, dados de sensor para a nuvem
ms.assetid: b33e53a1-1df7-4412-ade1-45185aec5bef
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: a9f689c0b231af3cdf9257e2179bf86ff7bc9a31
ms.lasthandoff: 01/25/2017


---
# <a name="configure-and-run-a-ble-sample-application"></a>Configurar e executar um aplicativo de exemplo BLE

## <a name="what-you-will-do"></a>O que você fará

- Clone o repositório de exemplo. 
- Configure a conectividade entre SensorTag e NUC da Intel. 
- Use a CLI do Azure para obter informações do seu Hub IoT e SensorTag para um aplicativo de exemplo BLE (Bluetooth de Baixa Energia). Configure e execute o aplicativo de exemplo BLE. 

Se você tiver problemas, procure as soluções na [página de solução de problemas](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>O que você aprenderá

Neste artigo, você aprenderá:

- Como configurar e executar o aplicativo de exemplo BLE.

## <a name="what-you-need"></a>O que você precisa

Você deve ter concluído com sucesso

- [Criar um Hub IoT e registrar o SensorTag](iot-hub-gateway-kit-c-lesson2-register-device.md)

## <a name="clone-the-sample-repository-to-the-host-computer"></a>Clonar o repositório de exemplo para o computador host

Para clonar o repositório de exemplo, siga estas etapas no computador host:

1. Abra uma janela de Prompt de comando no Windows ou um terminal no macOS ou Ubuntu.
2. Execute os seguintes comandos:

   ```bash
   git clone https://github.com/Azure-samples/iot-hub-c-intel-nuc-gateway-getting-started
   cd iot-hub-c-intel-nuc-gateway-getting-started
   ```

## <a name="set-up-the-connectivity-between-sensortag-and-intel-nuc"></a>Configurar a conectividade entre SensorTag e NUC da Intel

Para configurar a conectividade, siga estas etapas no computador host:

1. Inicialize o arquivo de configuração executando os seguintes comandos:

   ```bash
   cd Lesson3
   npm install
   gulp init
   ```

2. Abra `config-gateway.json` no Visual Studio Code executando o seguinte comando:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-gateway.json
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-gateway.json
   ```

3. Localize a seguinte linha de código e substitua `[device hostname or IP address]` pelo nome de host ou endereço IP do NUC da Intel.
   ![instantâneo do gateway de configuração](media/iot-hub-gateway-kit-lessons/lesson3/config_gateway.png)

4. Instale ferramentas de auxílio no NUC da Intel executando o seguinte comando:

   ```bash
   gulp install-tools
   ```

5. Ative o SensorTag pressionando o botão de energia como na imagem a seguir e o LED verde deverá piscar.

   ![ativar Sensor Tag](media/iot-hub-gateway-kit-lessons/lesson3/turn on_off sensortag.jpg)

6. Verificar dispositivos SensorTag executando os seguintes comandos:

   ```bash
   gulp discover-sensortag
   ```

7. Teste a conectividade entre a SensorTag e o NUC da Intel executando o seguinte comando:

   ```bash
   gulp test-connectivity --mac {mac address}
   ```

   Substitua `{mac address}` pelo endereço MAC obtido na etapa anterior.

## <a name="get-the-connection-string-of-sensortag"></a>Obter a cadeia de conexão da SensorTag

Para obter a cadeia de conexão do Hub IoT do Azure da SensorTag, execute o seguinte comando no computador host:

```bash
az iot device show-connection-string --hub-name {IoT hub name} --device-id mydevice --resource-group iot-gateway
```

`{IoT hub name}` é o nome do hub IoT usado. Use o gateway IoT como o valor de `{resource group name}` e use mydevice como o valor de `{device id}` se você não tiver alterado o valor na Lição 2.

## <a name="configure-the-ble-sample-application"></a>Configurar o aplicativo de exemplo BLE

Para configurar e executar o aplicativo de exemplo BLE, siga estas etapas no computador host:

1. Abra `config-sensortag.json` no Visual Studio Code executando o seguinte comando:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-sensortag.json
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-sensortag.json
   ```

   ![instantâneo da configuração da sensortag](media/iot-hub-gateway-kit-lessons/lesson3/config_sensortag.png)

2. Faça as seguintes substituições no código:
   - Substitua `[IoT hub name]` pelo nome do Hub IoT usado.
   - Substitua `[IoT device connection string]` pela cadeia de conexão da SensorTag que você obteve.
   - Substitua `[device_mac_address]` pelo endereço MAC da SensorTag que você obteve.

3. Execute o aplicativo de exemplo BLE.

   Para executar o aplicativo de exemplo BLE, siga estas etapas no computador host:

   1. Ative a SensorTag.

   2. Implante e execute o aplicativo de exemplo BLE no NUC da Intel executando o seguinte comando:
   
      ```bash
      gulp run
      ```

## <a name="verify-that-the-ble-sample-application-works"></a>Verificar se o aplicativo de exemplo BLE funciona

Você verá agora algo semelhante ao mostrado a seguir:

![Saída do aplicativo de exemplo BLE](media/iot-hub-gateway-kit-lessons/lesson3/BLE_running.png)

O aplicativo de exemplo continua coletando dados de temperatura e enviando-os para o Hub IoT. O aplicativo de exemplo é encerrado automaticamente após 40 segundos.

## <a name="summary"></a>Resumo

Você configurou com êxito a conectividade entre a SensorTag e o NUC da Intel e executou um aplicativo de exemplo BLE que coleta e envia dados da SensorTag para o Hub IoT. Você está pronto para aprender a verificar se o seu Hub IoT recebeu os dados.

## <a name="next-steps"></a>Próximas etapas
[Ler mensagens de seu Hub IoT](iot-hub-gateway-kit-c-lesson3-read-messages-from-hub.md)
