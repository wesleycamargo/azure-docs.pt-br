---
title: "Provisionar o Raspberry Pi para monitoramento remoto usando C – Azure | Microsoft Docs"
description: "Descreve como conectar um dispositivo Raspberry Pi à solução de monitoramento remoto pré-configurada Azure IoT Suite usando um aplicativo escrito em C."
services: iot-suite
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: fc50a33f-9fb9-42d7-b1b8-eb5cff19335e
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2017
ms.author: dobett
ms.openlocfilehash: 2f5915093a0d7984f0350af21aa438cdd588bbf2
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2017
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-preconfigured-solution-c"></a>Conectar o dispositivo Raspberry Pi à solução de monitoramento remoto pré-configurada (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Este tutorial mostra como conectar um dispositivo físico à solução de monitoramento remoto pré-configurada. Assim como acontece com a maioria dos aplicativos inseridos que são executados em dispositivos restritos, o código do cliente do aplicativo do dispositivo Raspberry Pi é escrito em C. Neste tutorial, você cria o aplicativo em um Raspberry Pi executando o sistema operacional Raspbian.

### <a name="required-hardware"></a>Requisitos de hardware

Um computador desktop para que você possa se conectar remotamente à linha de comando no Raspberry Pi.

[Kit de início de IoT da Microsoft para Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) ou componentes equivalentes. Este tutorial usa os seguintes itens do kit:

- Raspberry Pi 3
- Cartão MicroSD (com NOOBS)
- Um cabo USB Mini
- Um cabo Ethernet

### <a name="required-desktop-software"></a>Software de área de trabalho necessário

É necessário um cliente SSH em seu computador desktop para que você possa acessar remotamente a linha de comando no Raspberry Pi.

- O Windows não inclui um cliente SSH. Recomendamos o uso de [PuTTY](http://www.putty.org/).
- A maioria das distribuições do Linux e Mac OS incluem o utilitário de linha de comando do SSH. Para obter mais informações, consulte [SSH usando o Linux ou Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

### <a name="required-raspberry-pi-software"></a>Software do Raspberry Pi necessário

As etapas a seguir mostram como preparar seu Raspberry Pi para criar um aplicativo C que se conecta à solução pré-configurada:

1. Conecte-se ao seu Raspberry Pi usando `ssh`. Para obter mais informações, consulte [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) no [site do Raspberry Pi](https://www.raspberrypi.org/).

1. Use o seguinte comando para atualizar o Raspberry Pi:

    ```sh
    sudo apt-get update
    ```

1. Use o seguinte comando para adicionar as bibliotecas e ferramentas de desenvolvimento necessárias em seu Raspberry Pi:

    ```sh
    sudo apt-get install g++ make cmake gcc git
    ```

1. Use os comandos a seguir para instalar as bibliotecas clientes do Hub IoT:

    ```sh
    grep -q -F 'deb http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' /etc/apt/sources.list || sudo sh -c "echo 'deb http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' >> /etc/apt/sources.list"
    grep -q -F 'deb-src http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' /etc/apt/sources.list || sudo sh -c "echo 'deb-src http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' >> /etc/apt/sources.list"
    sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys FDA6A393E4C2257F
    sudo apt-get update
    sudo apt-get install -y azure-iot-sdk-c-dev cmake libcurl4-openssl-dev git-core
    ```

1. Clone o analisador JSON Parson para seu Raspberry Pi usando os seguintes comandos:

    ```sh
    cd ~
    git clone https://github.com/kgabis/parson.git
    ```

## <a name="create-a-project"></a>Criar um projeto

Conclua as etapas a seguir usando a conexão `ssh` com seu Raspberry Pi:

1. Crie uma pasta chamada `remote_monitoring` na pasta base do Raspberry Pi. Navegue até essa pasta na linha de comando:

    ```sh
    cd ~
    mkdir remote_monitoring
    cd remote_monitoring
    ```

1. Crie os quatro arquivos `main.c`, `remote_monitoring.c`, `remote_monitoring.h` e `CMakeLists.txt` na pasta `remote_monitoring`.

1. Crie uma pasta chamada `parson` na pasta `remote_monitoring`.

1. Copie os arquivos `parson.c` e `parson.h` da sua cópia local do repositório Parson para a pasta `remote_monitoring/parson`.

1. Em um editor de texto, abra o arquivo `remote_monitoring.c`. No Raspberry Pi, você pode usar o editor de texto `nano` ou `vi`. Adicione as seguintes declarações de `#include` :

    ```c
    #include "iothubtransportmqtt.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer_devicetwin.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    #include "parson.h"
    ```

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="add-code-to-run-the-app"></a>Adicionar código para executar o aplicativo

Em um editor de texto, abra o arquivo `remote_monitoring.h`. Adicione os códigos a seguir:

```c
void remote_monitoring_run(void);
```

Em um editor de texto, abra o arquivo `main.c`. Adicione os códigos a seguir:

```c
#include "remote_monitoring.h"

int main(void)
{
  remote_monitoring_run();

  return 0;
}
```

## <a name="build-and-run-the-application"></a>Compile e execute o aplicativo

As etapas a seguir descrevem como usar *CMake* para compilar o aplicativo cliente.

1. Em um editor de texto, abra o arquivo **CMakeLists.txt** na pasta `remote_monitoring`.

1. Adicione as seguintes instruções para definir como criar o aplicativo cliente:

    ```cmake
    macro(compileAsC99)
      if (CMAKE_VERSION VERSION_LESS "3.1")
        if (CMAKE_C_COMPILER_ID STREQUAL "GNU")
          set (CMAKE_C_FLAGS "--std=c99 ${CMAKE_C_FLAGS}")
          set (CMAKE_CXX_FLAGS "--std=c++11 ${CMAKE_CXX_FLAGS}")
        endif()
      else()
        set (CMAKE_C_STANDARD 99)
        set (CMAKE_CXX_STANDARD 11)
      endif()
    endmacro(compileAsC99)

    cmake_minimum_required(VERSION 2.8.11)
    compileAsC99()

    set(AZUREIOT_INC_FOLDER "${CMAKE_SOURCE_DIR}" "${CMAKE_SOURCE_DIR}/parson" "/usr/include/azureiot" "/usr/include/azureiot/inc")

    include_directories(${AZUREIOT_INC_FOLDER})

    set(sample_application_c_files
        ./parson/parson.c
        ./remote_monitoring.c
        ./main.c
    )

    set(sample_application_h_files
        ./parson/parson.h
        ./remote_monitoring.h
    )

    add_executable(sample_app ${sample_application_c_files} ${sample_application_h_files})

    target_link_libraries(sample_app
        serializer
        iothub_client
        iothub_client_mqtt_transport
        aziotsharedutil
        umqtt
        pthread
        curl
        ssl
        crypto
        m
    )
    ```

1. Na pasta `remote_monitoring`, crie uma pasta para armazenar os arquivos *make* que o CMake gera. Em seguida, execute os comandos **cmake** e **make** da seguinte maneira:

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Execute o aplicativo cliente e envie telemetria ao Hub IoT:

    ```sh
    ./sample_app
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
