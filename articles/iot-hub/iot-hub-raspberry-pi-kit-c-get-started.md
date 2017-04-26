---
title: "Raspberry Pi à nuvem (C) – conectar o Raspberry Pi ao Hub IoT do Azure | Microsoft Docs"
description: Conectar o Raspberry Pi ao Hub IoT do Azure para que o Raspberry Pi envie dados para a nuvem do Azure.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: raspberry pi azure iot, hub iot raspberry pi, raspberry pi enviar dados para a nuvem, raspberry pi para nuvem
ms.assetid: 68c0e730-1dc8-4e26-ac6b-573b217b302d
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/13/2017
ms.author: xshi
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 387dcace5be29de52b465bc53fa81a3dbf876390
ms.lasthandoff: 04/18/2017


---

# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Conectar o Raspberry Pi ao Hub IoT do Azure (C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Neste tutorial, você começará aprendendo as noções básicas de como trabalhar com o Raspberry Pi que está executando o Raspbian. Em seguida, aprenderá a conectar seus dispositivos diretamente à nuvem usando o [Hub IoT do Azure](iot-hub-what-is-iot-hub.md). Para obter exemplos do Windows 10 IoT Core, acesse o [Centro de Desenvolvimento do Windows](http://www.windowsondevices.com/).

## <a name="what-you-do"></a>O que fazer

* Instale o Raspberry Pi.
* Crie um Hub IoT.
* Registre um dispositivo para o Pi em seu Hub IoT.
* Execute um aplicativo de exemplo no Pi para enviar os dados do sensor para o Hub IoT.

Conecte o Raspberry Pi a um Hub IoT criado por você. Em seguida, execute um aplicativo de exemplo no Pi para coletar dados de temperatura e umidade de um sensor BME280. Por fim, você envia os dados do sensor para o Hub IoT.

## <a name="what-you-learn"></a>O que você aprenderá

* Como criar um Hub IoT do Azure e obter sua nova cadeia de conexão do dispositivo.
* Como conectar o Pi a um sensor BME280.
* Como coletar dados de sensor executando um aplicativo de exemplo no Pi.
* Como enviar dados de sensor ao Hub IoT.

## <a name="what-you-need"></a>O que você precisa

![O que você precisa](media/iot-hub-raspberry-pi-kit-c-get-started/0_starter_kit.jpg)

* Da placa do Raspberry Pi 2 ou do Raspberry Pi 3.
* Uma assinatura ativa do Azure. Se não tiver uma conta do Azure, [crie uma conta de avaliação gratuita do Azure](https://azure.microsoft.com/free/) em apenas alguns minutos.
* Um monitor, um teclado USB e mouse que se conectam ao Pi.
* Um Mac ou PC que esteja executando Windows ou Linux.
* Uma conexão com a Internet.
* Um cartão microSD de 16 GB ou superior.
* Um adaptador USB-SD ou um cartão microSD para gravar a imagem do sistema operacional no cartão microSD.
* Uma fonte de alimentação de 5 volts e 2 amperes com o cabo micro USB de 2,7 metros.

Os itens a seguir são opcionais:

* Um sensor de umidade, temperatura e pressão Adafruit BME280 montado.
* Uma placa universal.
* Cabos de jumper fêmea/macho de 15,2 cm.
* Um LED de 10 mm difuso.


> [!NOTE] 
Esses itens são opcionais porque o exemplo de código dá suporte a dados simulados de sensor.


[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="setup-raspberry-pi"></a>Instalar o Raspberry Pi

### <a name="install-the-raspbian-operating-system-for-pi"></a>Instalar o sistema operacional Raspbian para o Pi

Preparar o cartão microSD para instalação da imagem do Raspbian.

1. Baixe o Raspbian.
   1. [Baixe o Raspbian Jessie com Pixel](https://www.raspberrypi.org/downloads/raspbian/) (o arquivo .zip).
   1. Extraia a imagem do Raspbian em uma pasta no computador.
1. Instale o Raspbian no cartão microSD.
   1. [Baixe e instale o utilitário gravador de cartão SD Etcher](https://etcher.io/).
   1. Execute o Etcher e selecione a imagem do Raspbian extraída na etapa 1.
   1. Selecione a unidade de cartão microSD. Observação que o Etcher talvez já tenha selecionado a unidade correta.
   1. Clique em Flash para instalar o Raspbian no cartão microSD.
   1. Remova o cartão microSD do computador após a conclusão. É seguro remover o cartão microSD diretamente porque o Etcher ejeta ou desmonta automaticamente o cartão microSD após a conclusão.
   1. Insira o cartão microSD no Pi.

### <a name="enable-ssh-and-spi"></a>Habilitar SSH e SPI

1. Conecte o Pi ao monitor, ao teclado e ao mouse, inicie o Pi e, em seguida, faça logon no Raspbian usando `pi` como o nome de usuário e `raspberry` como a senha.
1. Clique no ícone do Raspberry > **Preferências** > **Configuração do Raspberry Pi**.

   ![O menu de Preferências do Raspbian](media/iot-hub-raspberry-pi-kit-c-get-started/1_raspbian-preferences-menu.png)

1. Na guia **Interfaces**, defina **SPI** e **SSH** como **Habilitar** e, em seguida, clique em **OK**.

   ![Habilitar SPI e SSH no Raspberry Pi](media/iot-hub-raspberry-pi-kit-c-get-started/2_enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE] 
Para habilitar o SSH e o SPI, você pode encontrar mais documentos de referência em [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) e [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).

### <a name="connect-the-sensor-to-pi"></a>Conectar o sensor ao Pi

Use a placa universal e os cabos de jumper para conectar um LED e um BME280 ao Pi, da seguinte maneira. Se você não tiver o sensor, ignore esta seção.

![A conexão do Raspberry Pi e do sensor](media/iot-hub-raspberry-pi-kit-c-get-started/3_raspberry-pi-sensor-connection.png)

Use a seguinte fiação para os pinos do sensor:

| Início (Sensor e LED)     | End (quadro)            | Cor de cabo   |
| -----------------------  | ---------------------- | ------------: |
| LED VDD (pino 5G)         | GPIO 4 (pino 7)         | Cabo branco   |
| LED GND (pino 6G)         | GND (pino 6)            | Cabo preto   |
| VDD (pino 18F)            | 3,3 v PWR (pino 17)      | Cabo branco   |
| GND (pino 20F)            | GND (pino 20)           | Cabo preto   |
| SCK (pino 21F)            | SPI0 SCLK (pino 23)     | Cabo laranja  |
| SDO (pino 22F)            | SPI0 MISO (pino 21)     | Cabo amarelo  |
| SDI (pino 23F)            | SPI0 MOSI (pino 19)     | Cabo verde   |
| CS (pino 24F)             | SPI0 CS (pino 24)       | Cabo azul    |

Clique para exibir os [mapeamentos de pinos do Raspberry Pi 2 e 3](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) para referência.

Depois de conectar com êxito o BME280 ao Raspberry Pi, ele deve ficar semelhante à imagem abaixo.

![Pi e BME280 conectados](media/iot-hub-raspberry-pi-kit-c-get-started/4_connected-pi.jpg)

Ligue o Pi usando o cabo micro USB e a fonte de alimentação. Use o cabo Ethernet para conectar o Pi à sua rede com fio ou siga as [instruções da Raspberry Pi Foundation](https://www.raspberrypi.org/learning/software-guide/wifi/) para conectar o Pi à sua rede sem fio.

![Conectado à rede com fio](media/iot-hub-raspberry-pi-kit-c-get-started/5_power-on-pi.jpg)


## <a name="run-a-sample-application-on-pi"></a>Executar um aplicativo de exemplo no Pi

### <a name="install-the-prerequisite-packages"></a>Instale os pacotes de pré-requisito

1. Use um dos seguintes clientes SSH do seu computador host para se conectar ao Raspberry Pi.
    - [PuTTY](http://www.putty.org/) para Windows.
    - O cliente SSH interno no Ubuntu ou macOS.

1. Instale os pacotes de pré-requisito do SDK do dispositivo IoT do Microsoft Azure para C e Cmake, executando os seguintes comandos:

   ```bash
   grep -q -F 'deb http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' /etc/apt/sources.list || sudo sh -c "echo 'deb http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' >> /etc/apt/sources.list"
   grep -q -F 'deb-src http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' /etc/apt/sources.list || sudo sh -c "echo 'deb-src http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' >> /etc/apt/sources.list"
   sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys FDA6A393E4C2257F
   sudo apt-get update
   sudo apt-get install -y azure-iot-sdk-c-dev cmake
   ```


### <a name="configure-the-sample-application"></a>Configurar o aplicativo de exemplo

1. Clone o aplicativo de exemplo executando o seguinte comando:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-client-app
   ```
1. Abra o arquivo de configuração executando os seguintes comandos:

   ```bash
   cd iot-hub-c-raspberry-pi-clientapp
   nano config.json
   ```

   ![Arquivo de configuração](media/iot-hub-raspberry-pi-kit-c-get-started/6_config-file.png)

   Há duas macros que você pode configurar nesse arquivo. A primeira é a `INTERVAL`, que define o intervalo de tempo entre duas mensagens que são enviadas para a nuvem. A segunda, a `SIMULATED_DATA`, é um valor booliano para definir se os dados simulados de sensor serão usados ou não.

   Se você **não tiver o sensor**, defina o valor `SIMULATED_DATA` como `1` para fazer com que o aplicativo de exemplo crie e use dados simulados de sensor.

1. Salve e saia pressionando CTRL+O > ENTER > CTRL+X.

### <a name="build-and-run-the-sample-application"></a>Crie e execute o aplicativo de exemplo

1. Crie o aplicativo de exemplo, executando o seguinte comando:

   ```bash
   cmake . && make
   ```
   ![Saída do build](media/iot-hub-raspberry-pi-kit-c-get-started/7_build-output.png)

1. Execute o aplicativo de exemplo com seguinte comando:

   ```bash
   sudo ./app '<device connection string>'
   ```

   > [!NOTE] 
   Verifique se você copiou e colou a cadeia de conexão do dispositivo em aspas simples.


Você deverá ver a seguinte saída, mostrando os dados do sensor e as mensagens que são enviadas ao seu Hub IoT.

![Saída – dados de sensor enviados do Raspberry Pi para o seu Hub IoT](media/iot-hub-raspberry-pi-kit-c-get-started/8_run-output.png)

## <a name="next-steps"></a>Próximas etapas

Você executou um aplicativo de exemplo para coletar dados de sensor e enviá-los ao seu Hub IoT.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

