---
title: "Dispositivo SensorTag e Gateway do IoT do Azure - Lição 4: criar aplicativo de funções | Microsoft Docs"
description: Salve mensagens da NUC da Intel para o hub IoT, grave-as no Armazenamento de Tabelas do Azure e, em seguida, leia-as na nuvem.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "armazenar dados na nuvem, dados armazenados na nuvem, serviço de nuvem de iot"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-gateway-kit-c-lesson1-set-up-nuc
ms.assetid: f84f9a85-e2c4-4a92-8969-f65eb34c194e
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 3672804218dbbe3a545536b8c4baccb2b9171cdc
ms.lasthandoff: 01/25/2017


---
# <a name="create-an-azure-function-app-and-storage-account"></a>Criar um aplicativo de funções do Azure e uma conta de armazenamento

O Azure Functions é uma solução para executar _funções_ (pequenos trechos de código) com facilidade na nuvem. Um aplicativo de funções do Azure hospeda a execução de suas funções no Azure. 

## <a name="what-you-will-do"></a>O que você fará

- Use um modelo do Azure Resource Manager para criar um aplicativo de funções do Azure e uma conta de Armazenamento do Azure. O aplicativo de funções do Azure escuta os eventos de Hub IoT do Azure, processa as mensagens recebidas e grava-as no armazenamento de Tabelas do Azure.

Se você tiver problemas, procure as soluções na [página de solução de problemas](iot-hub-gateway-kit-c-troubleshooting.md).


## <a name="what-you-will-learn"></a>O que você aprenderá

Nesta lição, você aprenderá a:

- Como usar o Azure Resource Manager para implantar recursos do Azure.
- Como usar um aplicativo de funções do Azure para processar mensagens do Hub IoT e gravá-las em uma tabela no armazenamento de Tabelas do Azure.

## <a name="what-you-need"></a>O que você precisa

Você deve ter concluído com sucesso as lições anteriores:

- [Lesson 1: Set up Intel NUC as an IoT gateway](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md) (Lição 1: Configurar a NUC da Intel como um gateway IoT)
- [Lesson 2: Get your host computer and Azure IoT hub ready](iot-hub-gateway-kit-c-lesson2-get-the-tools-win32.md) (Lição 2: Prepare seu computador host e hub do IoT do Azure)
- [Lição 3: Receber mensagens de SensorTag e ler mensagens do Hub IoT](iot-hub-gateway-kit-c-lesson3-configure-ble-app.md)

## <a name="open-a-sample-app"></a>Abrir um aplicativo de exemplo

Vá para sua pasta do repositório `iot-hub-c-intel-nuc-gateway-getting-started`, inicialize os arquivos de configuração e, em seguida, abra o projeto de exemplo no código do Visual Studio, executando o seguinte comando:

```bash
cd Lesson4
npm install
gulp init
code .
```

![estrutura do repositório](media/iot-hub-gateway-kit-lessons/lesson4/arm_template.png)

- O arquivo `arm-template.json` é o modelo do Azure Resource Manager que contém um aplicativo de funções do Azure e uma conta de Armazenamento do Azure.
- O arquivo `arm-template-param.json` é o arquivo de configuração usado pelo modelo do Azure Resource Manager.
- A subpasta `ReceiveDeviceMessages` contém o código do Node.js para a função do Azure.

## <a name="configure-azure-resource-manager-templates-and-create-resources-in-azure"></a>Configurar modelos do Azure Resource Manager e criar recursos no Azure

Atualize o arquivo `arm-template-param.json` no Visual Studio Code.

![arm template json](media/iot-hub-gateway-kit-lessons/lesson4/arm_template_param.png)

- Substitua `[your IoT Hub name]` pelo `{my hub name}` especificado na Lição 2.

Depois de atualizar o arquivo `arm-template-param.json`, implante os recursos do Azure executando o seguinte comando:

```bash
az group deployment create --template-file arm-template.json --parameters @arm-template-param.json -g iot-gateway
```

Use `iot-gateway` como o valor de `{resource group name}` se não tiver alterado o valor na Lição 2.

## <a name="summary"></a>Resumo

Você criou o aplicativo de funções do Azure para processar as mensagens de hub IoT e uma conta de Armazenamento do Azure para armazenar essas mensagens. Agora você pode ler as mensagens enviadas pelo gateway para o hub IoT.

## <a name="next-steps"></a>Próximas etapas
[Read messages persisted in Azure Table storage](iot-hub-gateway-kit-c-lesson4-read-table-storage.md) (Ler as mensagens persistidas no Armazenamento de Tabelas do Azure).

