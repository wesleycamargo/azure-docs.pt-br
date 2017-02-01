---
title: "Implantar o aplicativo de piscar no Kit de início de IoT do Azure | Microsoft Docs"
description: "Clone o aplicativo C de exemplo do GitHub e execute o gulp para implantar esse aplicativo na placa do Intel Edison. Esse aplicativo de exemplo pisca o LED conectado à placa a cada dois segundos."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "projetos led arduino, piscar led arduino, código piscar led arduino, programa de piscar arduino, exemplo de piscar arduino"
ms.assetid: b02dfd3f-28fd-4b52-8775-eb0eaf74d707
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/8/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 604ab48856f3fbc09ca6b566ef3f7e1d18e912c6
ms.openlocfilehash: 6c911297be4fa2c7ce6bd817233e8a8012634f42


---
# <a name="create-and-deploy-the-blink-application"></a>Criar e implantar o aplicativo de piscar
## <a name="what-you-will-do"></a>O que você fará
Clonar o aplicativo C de exemplo do GitHub e usar a ferramenta gulp para implantar o aplicativo de exemplo no Intel Edison. O aplicativo de exemplo pisca o LED conectado à placa a cada dois segundos. Se você tiver problemas, procure por soluções na [página de solução de problemas][troubleshooting].

## <a name="what-you-will-learn"></a>O que você aprenderá
* Como implantar e executar o aplicativo de exemplo no Edison.

## <a name="what-you-need"></a>O que você precisa
Você deve ter concluído com sucesso as seções a seguir:

* [Configurar seu dispositivo][configure-your-device]
* [Obter as ferramentas][get-the-tools]

## <a name="open-the-sample-application"></a>Abrir o aplicativo de exemplo
Para abrir o aplicativo de exemplo, siga estas etapas:

1. Clone o repositório de exemplo do GitHub executando o seguinte comando:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-c-edison-getting-started.git
   ```
2. Abra o aplicativo de exemplo no Visual Studio Code, executando os seguintes comandos:

   ```bash
   cd iot-hub-c-edison-getting-started
   cd Lesson1
   code .
   ```

   ![Estrutura do repositório][repo-structure]

O arquivo na subpasta `app` é o arquivo de origem principal que contém o código para controlar o LED.

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

   O arquivo de configuração `config-edison.json` contém as credenciais do usuário que você usa para fazer logon no Edison. Para evitar a perda de credenciais de usuário, o arquivo de configuração é gerado na subpasta `.iot-hub-getting-started` da pasta base em seu computador.

2. Abra o arquivo de configuração do dispositivo no Visual Studio Code executando o seguinte comando:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-edison.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-edison.json
   ```

3. Substitua o espaço reservado `[device hostname or IP address]` e `[device password]` pelo endereço IP e pela senha que você marcou na lição anterior.

   ![Config.json](media/iot-hub-intel-edison-lessons/lesson1/vscode-config-mac.png)

Parabéns! Você criou com êxito o primeiro aplicativo de exemplo para o Edison.

## <a name="deploy-and-run-the-sample-application"></a>Implantar e executar o aplicativo de exemplo
### <a name="install-the-azure-iot-hub-sdk-on-edison"></a>Instalar o SDK do Hub IoT do Azure no Edison
Instale o SDK do Hub IoT do Azure executando o seguinte comando:

```bash
gulp install-tools
```

Essa tarefa pode levar muito tempo para ser concluída, dependendo da sua conexão de rede. Ela precisa ser executada apenas uma vez para um Edison.

### <a name="deploy-and-run-the-sample-app"></a>Implantar e executar o aplicativo de exemplo
Implantar e executar o aplicativo de exemplo executando o seguinte comando:

```bash
gulp deploy && gulp run
```

### <a name="verify-the-app-works"></a>Verificar se o aplicativo funciona
O aplicativo de exemplo é encerrado automaticamente após o LED piscar 20 vezes. Se você não vir o LED piscar, consulte o [guia de solução de problemas][troubleshooting] para ver soluções de problemas comuns.

![LED piscando][led-blinking]

## <a name="summary"></a>Resumo
Você instalou as ferramentas necessárias para trabalhar com o Edison e implantou um aplicativo de exemplo no Edison para piscar o LED. Agora, você pode criar, implantar e executar outro aplicativo de exemplo que conecta o Edison ao Hub IoT do Azure para enviar e receber mensagens.

## <a name="next-steps"></a>Próximas etapas
[Obter as ferramentas do Azure][get-the-azure-tools]

<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
[Configure-your-device]: iot-hub-intel-edison-kit-c-lesson1-configure-your-device.md
[get-the-tools]: iot-hub-intel-edison-kit-c-lesson1-get-the-tools-win32.md
[repo-structure]: media/iot-hub-intel-edison-lessons/lesson1/repo_structure_c.png
[led-blinking]: media/iot-hub-intel-edison-lessons/lesson1/led_blinking_c.jpg
[get-the-azure-tools]: iot-hub-intel-edison-kit-c-lesson2-get-azure-tools-win32.md



<!--HONumber=Dec16_HO2-->


