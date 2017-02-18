---
title: "Conecte o Raspberry Pi (Nó) ao IoT do Azure - Lição 4: modificar aplicativo | Microsoft Docs"
description: Personalize as mensagens para alterar o comportamento liga e desliga do LED.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: led de controle com raspberry pi, controle de led de raspberry pi, led de controle de raspberry pi
ms.assetid: 3b42a4ad-0197-42f6-8ca9-04c883e879e8
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 53c9408bedc6c61cdb0e755b46b3090e8723e271


---
# <a name="change-the-on-and-off-behavior-of-the-led"></a>Alterar o comportamento de ativar e desativar do LED
## <a name="what-you-will-do"></a>O que você fará
Personalize as mensagens para alterar o comportamento liga e desliga do LED. Se você tiver problemas, procure as soluções na [página de solução de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="what-you-will-learn"></a>O que você aprenderá
Utilizar funções adicionais do Node.js para alterar o comportamento liga e desliga do LED.

## <a name="what-you-need"></a>O que você precisa
Você deve ter concluído com sucesso [Executar um aplicativo de exemplo no Raspberry Pi para receber mensagens da nuvem para o dispositivo](iot-hub-raspberry-pi-kit-node-lesson4-send-cloud-to-device-messages.md).

## <a name="add-nodejs-functions"></a>Adicionar funções do Node.js
1. Abra o aplicativo de exemplo no Visual Studio Code, executando os seguintes comandos:
   
   ```bash
   cd Lesson4
   code .
   ```
2. Abra o arquivo `app.js` e, em seguida, adicione as seguintes funções no final:
   
   ```javascript
   function turnOnLED() {
     wpi.digitalWrite(CONFIG_PIN, 1);
   }
   
   function turnOffLED() {
     wpi.digitalWrite(CONFIG_PIN, 0);
   }
   ```
   
   ![Arquivo App.js com funções adicionais](media/iot-hub-raspberry-pi-lessons/lesson4/updated_app_js.png)
3. Adicione as seguintes condições antes do padrão um do bloco de caso de opção da função `receiveMessageCallback`:
   
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
   
   ![Arquivo Gulpfile.js com funções adicionais](media/iot-hub-raspberry-pi-lessons/lesson4/updated_gulpfile.png)
5. Na função `sendMessage`, substitua a linha `var message = buildMessage(sentMessageCount);` com a nova linha mostrada no trecho a seguir:
   
   ```javascript
   var message = buildCustomMessage(sentMessageCount);
   ```
6. Salve todas as alterações.

### <a name="deploy-and-run-the-sample-application"></a>Implantar e executar o aplicativo de exemplo
Implante e execute o aplicativo de exemplo no Pi executando o seguinte comando:

```bash
gulp deploy && gulp run
```

Você deve ver o LED ativar por dois segundos e, em seguida, desativar por outros dois segundos. A última mensagem "stop" interrompe a execução do aplicativo de exemplo.

![Exemplo de aplicativo com mensagens de logon e logoff](media/iot-hub-raspberry-pi-lessons/lesson4/gulp_on_and_off.png)

Parabéns! Você personalizou com sucesso as mensagens que são enviadas do Hub IoT para o Pi.

### <a name="summary"></a>Resumo
Essa seção opcional demonstra como personalizar as mensagens para que o aplicativo de exemplo controle o comportamento liga e desliga do LED de maneira diferente.




<!--HONumber=Jan17_HO4-->


