---
title: "Conectar o Arduino (C) ao IoT do Azure - Lição 3: implantação de modelo | Microsoft Docs"
description: "O aplicativo de funções do Azure escuta os eventos de Hub IoT do Azure, processa as mensagens recebidas e grava-as no armazenamento de Tabelas do Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "armazenar dados na nuvem, dados armazenados na nuvem, serviço de nuvem de iot"
ms.assetid: 9c8f4cd1-9511-4601-ad7e-51761a986753
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: cf666d17406a49036082a6a4dd15bad89c04aa8f


---

# <a name="create-an-azure-function-app-and-azure-storage-account"></a>Criar um aplicativo de funções do Azure e uma conta de armazenamento do Azure
O [Azure Functions](../../articles/azure-functions/functions-overview.md) é uma solução para executar facilmente *funções* (pequenos trechos de código) na nuvem. Um aplicativo de funções do Azure hospeda a execução de suas funções no Azure.

## <a name="what-will-you-do"></a>O que você fará
Use um modelo do Azure Resource Manager para criar um aplicativo de funções do Azure e uma conta de Armazenamento do Azure. O aplicativo de funções do Azure escuta os eventos de Hub IoT do Azure, processa as mensagens recebidas e grava-as no armazenamento de Tabelas do Azure.

Se tiver problemas, procure por soluções na [página de solução de problemas de sua placa Adafruit Feather M0 WiFi Arduino](iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md).

## <a name="what-will-you-learn"></a>O que você aprenderá
Neste artigo, você aprenderá:
* Como usar o [Azure Resource Manager](../../articles/azure-resource-manager/resource-group-overview.md) para implantar recursos do Azure.
* Como usar um aplicativo de funções do Azure para processar mensagens do Hub IoT e gravá-las em uma tabela no armazenamento de Tabelas do Azure.

## <a name="what-do-you-need"></a>Do que você precisa
Você deve ter concluído com sucesso:
- [Comece com uma placa Arduino][get-started]
- [Criar seu Hub Azure IoT][create-iot-hub]

## <a name="open-the-sample-app"></a>Abrir o aplicativo de exemplo
Abra o projeto de exemplo no Visual Studio Code executando os seguintes comandos:

```bash
cd Lesson3
code .
```

![Estrutura do repositório][repo-structure]

* O arquivo `app.ino` na subpasta `app` é o arquivo de origem chave. Esse arquivo de origem contém o código para enviar uma mensagem 20 vezes para o hub IoT e piscar o LED para cada mensagem que ele envia.
* O `config.json` contém as configurações necessárias.
* O arquivo `arm-template.json` é o modelo do Azure Resource Manager que contém um aplicativo de funções do Azure e uma conta de Armazenamento do Azure.
* O arquivo `arm-template-param.json` é o arquivo de configuração usado pelo modelo do Azure Resource Manager.
* A subpasta `ReceiveDeviceMessages` contém o código do Node.js para a função do Azure.

## <a name="configure-azure-resource-manager-templates-and-create-resources-in-azure"></a>Configurar modelos do Azure Resource Manager e criar recursos no Azure
Atualize o arquivo `arm-template-param.json` no Visual Studio Code.

![Parâmetros do modelo do Azure Resource Manager][arm-template-params]

* Substitua **[seu nome de Hub IoT]** pelo **{meu nome de hub}** que foi especificado quando você [criou o Hub IoT e registrou sua placa Arduino][created-iot-hub-and-registered-arduino-board].
* Substitua **[cadeia de caracteres de prefixo para novos recursos]** pelo prefixo que quiser. O prefixo garante que o nome do recurso seja globalmente exclusivo para evitar conflitos. Não use um traço nem número inicial no prefixo.

Depois de atualizar o arquivo `arm-template-param.json`, implante os recursos do Azure executando o seguinte comando:

```bash
az group deployment create --template-file arm-template.json --parameters @arm-template-param.json -g iot-sample
```

Levará cerca de cinco minutos para criar esses recursos. Embora a criação de recursos esteja em andamento, você pode passar para o próximo artigo.

## <a name="summary"></a>Resumo
Você criou o aplicativo de funções do Azure para processar as mensagens de hub IoT e uma conta de Armazenamento do Azure para armazenar essas mensagens. Agora você pode implantar e executar o exemplo para enviar mensagens do dispositivo para a nuvem na placa Arduino.

## <a name="next-steps"></a>Próximas etapas
[Executar um aplicativo de exemplo para enviar mensagens do dispositivo para a nuvem na sua placa Arduino][send-device-to-cloud-messages]

<!-- Images and links -->

[get-started]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[create-iot-hub]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md
[repo-structure]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/repo_structure_c.png
[arm-template-params]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/arm_para_arduino.png
[created-iot-hub-and-registered-arduino-board]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md
[send-device-to-cloud-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-run-azure-blink.md


<!--HONumber=Jan17_HO4-->


