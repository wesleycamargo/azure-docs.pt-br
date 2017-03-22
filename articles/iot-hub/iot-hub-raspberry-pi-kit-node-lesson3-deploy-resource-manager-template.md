---
title: "Conectar o Raspberry Pi (Nó) ao IoT do Azure - Lição 3: implantação de modelo | Microsoft Docs"
description: "O aplicativo de funções do Azure escuta os eventos de Hub IoT do Azure, processa as mensagens recebidas e grava-as no armazenamento de Tabelas do Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "armazenar dados na nuvem, dados armazenados na nuvem, serviço de nuvem de iot"
ms.assetid: 6c58de85-c5c4-4989-bb5e-08c45c549966
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: a3a7ec4c81556e4cb530f32c9997d8701db68b2c
ms.lasthandoff: 01/24/2017


---
# <a name="create-an-azure-function-app-and-azure-storage-account"></a>Criar um aplicativo de funções do Azure e uma conta de armazenamento do Azure
O [Azure Functions](../azure-functions/functions-overview.md) é uma solução para executar facilmente *funções* (pequenos trechos de código) na nuvem. Um aplicativo de funções do Azure hospeda a execução de suas funções no Azure.

## <a name="what-you-will-do"></a>O que você fará
Use um modelo do Azure Resource Manager para criar um aplicativo de funções do Azure e uma conta de Armazenamento do Azure. O aplicativo de funções do Azure escuta os eventos de Hub IoT do Azure, processa as mensagens recebidas e grava-as no armazenamento de Tabelas do Azure. Se você tiver problemas, procure as soluções na [página de solução de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="what-you-will-learn"></a>O que você aprenderá
Neste artigo, você aprenderá:

* Como usar o [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) para implantar recursos do Azure.
* Como usar um aplicativo de funções do Azure para processar mensagens do Hub IoT e gravá-las em uma tabela no armazenamento de Tabelas do Azure.

## <a name="what-you-need"></a>O que você precisa
Você deve ter concluído com sucesso:
* [Introdução ao Raspberry Pi 3](iot-hub-raspberry-pi-kit-node-get-started.md)
* [Criar seu Hub IoT do Azure](iot-hub-raspberry-pi-kit-node-get-started.md)

## <a name="open-the-sample-app"></a>Abrir o aplicativo de exemplo
Abra o projeto de exemplo no Visual Studio Code executando os seguintes comandos:

```bash
cd Lesson3
code .
```

![Estrutura do repositório](media/iot-hub-raspberry-pi-lessons/lesson3/repo_structure.png)

* O arquivo `app.js` na subpasta `app` é o arquivo de origem chave. Esse arquivo de origem contém o código para enviar uma mensagem 20 vezes para o hub IoT e piscar o LED para cada mensagem que ele envia.
* O arquivo `arm-template.json` é o modelo do Azure Resource Manager que contém um aplicativo de funções do Azure e uma conta de Armazenamento do Azure.
* O arquivo `arm-template-param.json` é o arquivo de configuração usado pelo modelo do Azure Resource Manager.
* A subpasta `ReceiveDeviceMessages` contém o código do Node.js para a função do Azure.

## <a name="configure-azure-resource-manager-templates-and-create-resources-in-azure"></a>Configurar modelos do Azure Resource Manager e criar recursos no Azure
Atualize o arquivo `arm-template-param.json` no Visual Studio Code.

![Parâmetros do modelo do Azure Resource Manager](media/iot-hub-raspberry-pi-lessons/lesson3/arm_para.png)

* Substitua **[seu nome de Hub IoT]** pelo **{meu nome de hub}** que foi especificado quando você [criou o Hub IoT e registrou o Raspberry Pi 3](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md).
* Substitua **[cadeia de caracteres de prefixo para novos recursos]** pelo prefixo que quiser. O prefixo garante que o nome do recurso seja globalmente exclusivo para evitar conflitos. Não use um traço nem número inicial no prefixo.

Depois de atualizar o arquivo `arm-template-param.json`, implante os recursos do Azure executando o seguinte comando:

```bash
az group deployment create --template-file arm-template.json --parameters @arm-template-param.json -g iot-sample
```

Levará cerca de cinco minutos para criar esses recursos. Embora a criação de recursos esteja em andamento, você pode passar para o próximo artigo.

## <a name="summary"></a>Resumo
Você criou o aplicativo de funções do Azure para processar as mensagens de hub IoT e uma conta de Armazenamento do Azure para armazenar essas mensagens. Agora você pode implantar e executar o exemplo para enviar mensagens do dispositivo para a nuvem no Pi.

## <a name="next-steps"></a>Próximas etapas
[Executar o aplicativo de exemplo para enviar mensagens do dispositivo para a nuvem no Raspberry Pi 3](iot-hub-raspberry-pi-kit-node-lesson3-run-azure-blink.md)


