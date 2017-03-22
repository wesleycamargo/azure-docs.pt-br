---
title: "Conectar o Raspberry Pi (C) ao IoT do Azure - Lição 4: modificar aplicativo | Microsoft Docs"
description: Personalize as mensagens para alterar o comportamento liga e desliga do LED.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: led de controle com raspberry pi, controle de led de raspberry pi, led de controle de raspberry pi
ms.assetid: 0201b8ed-d5e6-4445-9a4d-1305003d1eff
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: c1c999e40ecc92a1018067822be66f14b61a8c36
ms.lasthandoff: 01/24/2017


---
# <a name="change-the-on-and-off-behavior-of-the-led"></a>Alterar o comportamento de ativar e desativar do LED
## <a name="what-you-will-do"></a>O que você fará
Personalize as mensagens para alterar o comportamento liga e desliga do LED. Se você tiver problemas, procure as soluções na [página de solução de problemas](iot-hub-raspberry-pi-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>O que você aprenderá
Utilizar funções adicionais do Node.js para alterar o comportamento liga e desliga do LED.

## <a name="what-you-need"></a>O que você precisa
Você deve ter concluído com sucesso [Executar um aplicativo de exemplo no Raspberry Pi para receber mensagens da nuvem para o dispositivo](iot-hub-raspberry-pi-kit-c-lesson4-send-cloud-to-device-messages.md).

## <a name="add-functions-to-mainc-and-gulpfilejs"></a>Adicionar funções ao main.c e ao gulpfile.js
1. Abra o aplicativo de exemplo no Visual Studio Code, executando os seguintes comandos:

   ```bash
   cd Lesson4
   code .
   ```
2. Abra o arquivo `main.c` e, em seguida, adicione as seguintes funções após a função blinkLED():

   ```c
   static void turnOnLED()
   {
     digitalWrite(LED_PIN, HIGH);
   }

   static void turnOffLED()
   {
     digitalWrite(LED_PIN, LOW);
   }
   ```

   ![arquivo main.c com funções adicionais](media/iot-hub-raspberry-pi-lessons/lesson4/updated_app_c.png)
3. Adicione as seguintes condições antes do padrão, uma no bloco `if` da função `receiveMessageCallback`:

   ```c
   else if (0 == strcmp((const char*)value, "\"on\""))
   {
       turnOnLED();
   }
   else if (0 == strcmp((const char*)value, "\"off\""))
   {
       turnOffLED();
   }
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

   ![Arquivo Gulpfile.js com funções adicionais](media/iot-hub-raspberry-pi-lessons/lesson4/updated_gulpfile_c.png)
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

![Exemplo de aplicativo com mensagens de logon e logoff](media/iot-hub-raspberry-pi-lessons/lesson4/gulp_on_and_off_c.png)

Parabéns! Você personalizou com sucesso as mensagens que são enviadas do Hub IoT para o Pi.

### <a name="summary"></a>Resumo
Essa seção opcional demonstra como personalizar as mensagens para que o aplicativo de exemplo controle o comportamento liga e desliga do LED de maneira diferente.

