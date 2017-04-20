---
title: "Conectar o Intel Edison (Nó) ao IoT do Azure - Lição 4: piscar o LED| Microsoft Docs"
description: Personalize as mensagens para alterar o comportamento liga e desliga do LED.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: controlar led com arduino
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-node-get-started
ms.assetid: 387cd97e-b05e-43c4-b252-f68ad45d524a
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: 9a2c6dfe5d85a4da9714f9b8942cb2baf1a98466
ms.lasthandoff: 01/25/2017


---
# <a name="change-the-on-and-off-behavior-of-the-led"></a>Alterar o comportamento de ativar e desativar do LED
## <a name="what-you-will-do"></a>O que você fará
Personalize as mensagens para alterar o comportamento liga e desliga do LED. Se você tiver problemas, procure por soluções na [página de solução de problemas][troubleshooting].

## <a name="what-you-will-learn"></a>O que você aprenderá
Utilizar funções adicionais para alterar o comportamento de ligar e desligar do LED.

## <a name="what-you-need"></a>O que você precisa
Você deve ter concluído com sucesso [Executar um aplicativo de exemplo no Intel Edison para receber mensagens da nuvem para o dispositivo][receive-cloud-to-device-messages].

## <a name="add-functions-to-appjs-and-gulpfilejs"></a>Adicionar funções ao app.js e ao gulpfile.js
1. Abra o aplicativo de exemplo no Visual Studio Code, executando os seguintes comandos:

   ```bash
   cd Lesson4
   code .
   ```
2. Abra o arquivo `app.js` e, em seguida, adicione as seguintes funções após a função blinkLED():

   ```javascript
   function turnOnLED() {
     myLed.write(1);
   }

   function turnOffLED() {
     myLed.write(0);
   }
   ```

   ![arquivo app.js com funções adicionais](media/iot-hub-intel-edison-lessons/lesson4/updated_app_node.png)
3. Adicione as seguintes condições antes do caso “blink” no bloco de mudança de casos da função `receiveMessageCallback`:

   ```javascript
   case 'on':
     turnOnLED();
     break;
   case 'off':
     turnOffLED();
     break;
   ```

   Agora você configurou o aplicativo de exemplo para responder a mais instruções por meio de mensagens. A instrução "on" ativa o LED e a instrução "off" desativa o LED.
4. Abra o arquivo gulpfile.js e, em seguida, adicione uma nova função antes da função `sendMessage`:

   ```javascript
   var buildCustomMessage = function (messageId) {
     if ((messageId & 1) && (messageId < MAX_MESSAGE_COUNT)) {
       return new Message(JSON.stringify({ command: 'on', messageId: messageId }));
     } else if (messageId < MAX_MESSAGE_COUNT) {
       return new Message(JSON.stringify({ command: 'off', messageId: messageId }));
     } else {
       return new Message(JSON.stringify({ command: 'stop', messageId: messageId }));
     }
   }
   ```

   ![Arquivo Gulpfile.js com funções adicionais][gulpfile]
5. Na função `sendMessage`, substitua a linha `var message = buildMessage(sentMessageCount);` com a nova linha mostrada no trecho a seguir:

   ```javascript
   var message = buildCustomMessage(sentMessageCount);
   ```
6. Salve todas as alterações.

### <a name="deploy-and-run-the-sample-application"></a>Implantar e executar o aplicativo de exemplo
Implante e execute o aplicativo de exemplo no Edison executando o seguinte comando:

```bash
gulp deploy && gulp run
```

Você deve ver o LED ativar por dois segundos e, em seguida, desativar por outros dois segundos. A última mensagem "stop" interrompe a execução do aplicativo de exemplo.

![liga e desliga][on-and-off]

Parabéns! Você personalizou com sucesso as mensagens que são enviadas do Hub IoT para o Edison.

### <a name="summary"></a>Resumo
Essa seção opcional demonstra como personalizar as mensagens para que o aplicativo de exemplo controle o comportamento liga e desliga do LED de maneira diferente.

<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[receive-cloud-to-device-messages]: iot-hub-intel-edison-kit-node-lesson4-send-cloud-to-device-messages.md
[gulpfile]: media/iot-hub-intel-edison-lessons/lesson4/updated_gulpfile_node.png
[on-and-off]: media/iot-hub-intel-edison-lessons/lesson4/gulp_on_and_off_node.png

