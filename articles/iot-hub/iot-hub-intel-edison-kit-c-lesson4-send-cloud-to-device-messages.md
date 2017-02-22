---
title: "Conectar o Intel Edison (C) ao IoT do Azure - Lição 4: receber mensagens| Microsoft Docs"
description: "Um aplicativo de exemplo é executado no Edison e monitora mensagens de entrada de seu Hub IoT. Uma nova tarefa gulp envia mensagens para o Edison de seu Hub IoT para piscar o LED."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: controlar led pela web com arduino, controlar led via web com arduino
ms.assetid: 820d38f3-d3b8-4249-9e2b-f1b9b771e62f
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/8/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: b4d76eeb1bc0b406800c89c4952900350d8eafa8


---
# <a name="run-a-sample-application-to-receive-cloud-to-device-messages"></a>Executar um aplicativo de exemplo para receber mensagens da nuvem para o dispositivo
Neste artigo, você implanta um aplicativo de exemplo no Intel Edison. O aplicativo de exemplo monitora mensagens recebidas do hub IoT. Você também executa uma tarefa gulp no computador para enviar mensagens para o Edison do Hub IoT. Quando o aplicativo de exemplo recebe uma a mensagem, ele pisca o LED. Se você tiver problemas, procure por soluções na [página de solução de problemas][troubleshooting].

## <a name="what-you-will-do"></a>O que você fará
* Conectar o aplicativo de exemplo ao hub IoT.
* Implantar e executar o aplicativo de exemplo.
* Enviar mensagens do Hub IoT para o Edison para piscar o LED.

## <a name="what-you-will-learn"></a>O que você aprenderá
Neste artigo, você aprenderá:
* Como monitorar mensagens recebidas do hub IoT.
* Como enviar mensagens da nuvem para o dispositivo do Hub IoT para o Edison.

## <a name="what-you-need"></a>O que você precisa
* Intel Edison, configurado para uso. Para saber como configurar o Edison, consulte [Configurar seu dispositivo][configure-your-device].
* Um hub IoT criado em sua assinatura do Azure. Para aprender a criar seu Hub IoT, consulte [Criar seu Hub IoT do Azure][create-your-azure-iot-hub].

## <a name="connect-the-sample-application-to-your-iot-hub"></a>Conectar o aplicativo de exemplo ao Hub IoT
1. Verifique se você está na pasta de repositório `iot-hub-c-edison-getting-started`. Abra o aplicativo de exemplo no Visual Studio Code, executando os seguintes comandos:

   ```bash
   cd Lesson4
   code .
   ```

   O arquivo na subpasta `app` é o arquivo de origem principal que contém o código para monitorar mensagens recebidas do Hub IoT. A função `blinkLED` pisca o LED.

   ![Estrutura do repositório no aplicativo de exemplo][repo-structure]
2. Inicialize o arquivo de configuração executando os seguintes comandos:

   ```bash
   npm install
   gulp init
   ```

   Se você concluiu as etapas em [Criar uma conta de armazenamento e aplicativo de funções do Azure][create-an-azure-function-app-and-storage-account] neste computador, todas as configurações serão herdadas, portanto você pode ignorar a tarefa de implantar e executar o aplicativo de exemplo. Se você concluiu as etapas em [Criar uma conta de armazenamento e aplicativo de funções do Azure][create-an-azure-function-app-and-storage-account] em um computador diferente, será necessário substituir os espaços reservados no arquivo `config-edison.json`. O arquivo `config-edison.json` está na subpasta da pasta base.

   ![Conteúdo do arquivo config-edison.json](media/iot-hub-intel-edison-lessons/lesson4/config-edison.png)

   * Substitua **[nome de host ou endereço IP do dispositivo]** pelo endereço IP do dispositivo que você marcou quando configurou seu dispositivo.
   * Substitua **[cadeia de conexão do dispositivo IoT]** pela cadeia de conexão do dispositivo que você obtém executando o comando `az iot device show-connection-string --hub-name {my hub name} --device-id {device id}`.
   * Substitua **[cadeia de conexão do hub IoT]** pela cadeia de conexão do hub IoT que você obtém executando o comando `az iot hub show-connection-string --name {my hub name}`.

   > [!NOTE]
   > Execute **gulp install-tools** e também, se você ainda não fez isso na Lição 1.

## <a name="deploy-and-run-the-sample-application"></a>Implantar e executar o aplicativo de exemplo
Implante e execute o aplicativo de exemplo no Edison executando os seguintes comandos:

```bash
gulp deploy && gulp run
```

O comando gulp implanta o aplicativo de exemplo no Edison. Em seguida, ele executa o aplicativo no Edison e uma tarefa separada no computador host para enviar 20 mensagens de piscar para o Edison do Hub IoT.

Após a execução, o aplicativo de exemplo começa a escutar as mensagens do Hub IoT. Enquanto isso, a tarefa gulp envia várias mensagens de "piscar" do Hub IoT para o Edison. Para cada mensagem de piscar recebida pelo Edison, o aplicativo de exemplo chama a função `blinkLED` para piscar o LED.

Você deve ver o LED piscar a cada dois segundos, uma vez que a tarefa gulp envia 20 mensagens do Hub IoT ao Edison. A última é uma mensagem de “parar” que interrompe a execução do aplicativo.

![Exemplo de aplicativo com o comando gulp e mensagens de piscar][gulp-command-and-blink-messages]

## <a name="summary"></a>Resumo
Você enviou com êxito mensagens do Hub IoT para o Edison para piscar o LED. A próxima tarefa é opcional: alterar o comportamento de liga e desliga do LED.

## <a name="next-steps"></a>Próximas etapas
[Alterar o comportamento de ligar e desligar do LED][change-the-on-and-off-behavior-of-the-led]

<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
[configure-your-device]: iot-hub-intel-edison-kit-c-lesson1-configure-your-device.md
[create-your-azure-iot-hub]: iot-hub-intel-edison-kit-c-lesson2-prepare-azure-iot-hub.md
[repo-structure]: media/iot-hub-intel-edison-lessons/lesson4/repo_structure_c.png
[create-an-azure-function-app-and-storage-account]: iot-hub-intel-edison-kit-c-lesson3-deploy-resource-manager-template.md
[gulp-command-and-blink-messages]: media/iot-hub-intel-edison-lessons/lesson4/gulp_blink_c.png
[change-the-on-and-off-behavior-of-the-led]: iot-hub-intel-edison-kit-c-lesson4-change-led-behavior.md


<!--HONumber=Jan17_HO4-->


