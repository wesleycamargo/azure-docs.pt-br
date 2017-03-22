---
title: "Conectar o Raspberry Pi (C) ao IoT do Azure - Lição 1: implantar aplicativo | Microsoft Docs"
description: "Clone o aplicativo C de exemplo do GitHub e use gulp para implantar esse aplicativo na placa do Raspberry Pi 3. Esse aplicativo de exemplo pisca o LED conectado à placa a cada dois segundos."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: piscar led raspberry pi, piscar led com raspberry pi
ms.assetid: f601d1e1-2bc3-4cc5-a6b1-0467e5304dcf
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 5b16230178fa82c7f227e3e3f8742fa16b00c719
ms.lasthandoff: 01/24/2017


---
# <a name="create-and-deploy-the-blink-application"></a>Criar e implantar o aplicativo de piscar
## <a name="what-you-will-do"></a>O que você fará
Clone o aplicativo C de exemplo do GitHub e use a ferramenta gulp para implantar o aplicativo de exemplo no Raspberry Pi 3. O aplicativo de exemplo pisca o LED conectado à placa a cada dois segundos. Se você tiver problemas, procure as soluções na [página de solução de problemas](iot-hub-raspberry-pi-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>O que você aprenderá
Neste artigo, você aprenderá:

* Como usar a ferramenta `device-discover-cli` para recuperar informações de rede sobre seu Pi.
* Como implantar e executar o aplicativo de exemplo no Pi.
* Como implantar e depurar aplicativos executados remotamente no Pi.

## <a name="what-you-need"></a>O que você precisa
Você deve ter concluído com sucesso as seções a seguir:

* [Configurar seu dispositivo](iot-hub-raspberry-pi-kit-c-lesson1-configure-your-device.md)
* [Obter as ferramentas](iot-hub-raspberry-pi-kit-c-lesson1-get-the-tools-win32.md)

## <a name="obtain-the-ip-address-and-host-name-of-pi"></a>Obter o nome de host e o endereço IP do Pi
Abra um prompt de comando no Windows ou em um terminal no macOS ou Ubuntu e, em seguida, execute o seguinte comando:

```bash
devdisco list --eth
```

Você deverá ver uma saída semelhante à seguinte:

![Descoberta de dispositivo](media/iot-hub-raspberry-pi-lessons/lesson1/device_discovery.png)

Anote `IP address` e `hostname` do Pi. Você precisará dessas informações mais adiante neste artigo.

> [!NOTE]
> Verifique se o Pi está conectado à mesma rede que o computador. Por exemplo, se o computador estiver conectado a uma rede sem fio enquanto o Pi estiver conectado a uma rede com fio, talvez você não veja o endereço IP na saída devdisco.

## <a name="open-the-sample-application"></a>Abrir o aplicativo de exemplo
Para abrir o aplicativo de exemplo, siga estas etapas:

1. Clone o repositório de exemplo do GitHub executando o seguinte comando:
   
    ```bash
    git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-getting-started.git
    ```
2. Abra o aplicativo de exemplo no Visual Studio Code, executando os seguintes comandos:
   
    ```bash
    cd iot-hub-c-raspberrypi-getting-started
    cd Lesson1
    code .
    ```

![Estrutura do repositório](media/iot-hub-raspberry-pi-lessons/lesson1/vscode-blink-c-mac.png)

O arquivo `main.c` na subpasta `app` é o arquivo de origem principal que contém o código para controlar o LED.

### <a name="install-application-dependencies"></a>Instalar dependências de aplicativos
Instale as bibliotecas e outros módulos necessários para o aplicativo de exemplo executando o seguinte comando:

```bash
npm install
```

## <a name="configure-the-device-connection"></a>Configurar a conexão do dispositivo
Para configurar a conexão do dispositivo, siga estas etapas:

1. Gere o arquivo de configuração do dispositivo executando o seguinte comando:
   
   ```bash
   gulp init
   ```
   
   O arquivo de configuração `config-raspberrypi.json` contém as credenciais do usuário que você usa para fazer logon no Pi. Para evitar a perda de credenciais de usuário, o arquivo de configuração é gerado na subpasta `.iot-hub-getting-started` da pasta base em seu computador.

2. Abra o arquivo de configuração do dispositivo no Visual Studio Code executando o seguinte comando:
   
   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-raspberrypi.json
   
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-raspberrypi.json
   ```

3. Substitua o espaço reservado `[device hostname or IP address]` pelo endereço IP ou o nome do host que você obteve anteriormente em "Obter o nome de host e endereço IP de Pi."
   
   ![Config.json](media/iot-hub-raspberry-pi-lessons/lesson1/vscode-config-mac.png)

> [!NOTE]
> Você pode usar a chave SSH, em vez do nome de usuário e senha ao se conectar ao Raspberry Pi. Para fazer isso, você precisará gerar a chave usando **ssh-keygen** e **ssh-copy-id pi@\<endereço do dispositivo\>**.
>
> No Windows, esses comandos estão disponíveis em **Git Bash**.
>
> No MacOS, você precisa executar **brew install ssh-copy-id**.
>
> Depois de carregar com êxito a chave no Raspberry Pi, substitua **device_password** pela propriedade **device_key_path** no **config raspberrypi.json**.
>
> As linhas atualizadas devem parecer com o seguinte:
> ```javascript
> "device_user_name": "pi",
> "device_key_path": "id_rsa",
> ```

Parabéns! Você criou com êxito o primeiro aplicativo de exemplo para o Pi.

## <a name="deploy-and-run-the-sample-application"></a>Implantar e executar o aplicativo de exemplo
### <a name="install-the-azure-iot-hub-sdk-on-pi"></a>Instalar o SDK do Hub IoT do Azure no Pi
Instale o SDK do Hub IoT do Azure no Pi executando o seguinte comando:

```bash
gulp install-tools
```

A conclusão da execução inicial desta tarefa pode levar alguns minutos.

### <a name="deploy-and-run-the-sample-app"></a>Implantar e executar o aplicativo de exemplo
Implantar e executar o aplicativo de exemplo executando o seguinte comando:

```bash
gulp deploy && gulp run
```

### <a name="verify-the-app-works"></a>Verificar se o aplicativo funciona
O aplicativo de exemplo é encerrado automaticamente após o LED piscar 20 vezes. Se você não vir o LED piscar, consulte o [guia de solução de problemas](iot-hub-raspberry-pi-kit-c-troubleshooting.md) para problemas comuns.
![LED piscando](media/iot-hub-raspberry-pi-lessons/lesson1/led_blinking.jpg)

## <a name="summary"></a>Resumo
Você instalou as ferramentas necessárias para trabalhar com o Pi e implantou um aplicativo de exemplo para o Pi para piscar o LED. Agora você pode criar, implantar e executar outro aplicativo de exemplo que conecta seu Pi ao Hub IoT do Azure para enviar e receber mensagens.

## <a name="next-steps"></a>Próximas etapas
[Obter ferramentas do Azure](iot-hub-raspberry-pi-kit-c-lesson2-get-azure-tools-win32.md)


