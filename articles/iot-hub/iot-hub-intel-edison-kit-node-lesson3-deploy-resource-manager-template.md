---
title: "Conectar o Intel Edison (Nó) ao IoT do Azure - Lição 3: criar aplicativo de funções| Microsoft Docs"
description: "O aplicativo de funções do Azure escuta os eventos de Hub IoT do Azure, processa as mensagens recebidas e grava-as no armazenamento de Tabelas do Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "armazenar dados na nuvem, dados armazenados na nuvem, serviço de nuvem de iot"
ms.assetid: 37ee5962-95ce-40e8-8162-17e735eaec21
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: 72d1496fcf353662fa2f7b003ee3757c45b8181e
ms.lasthandoff: 01/25/2017


---
# <a name="create-an-azure-function-app-and-azure-storage-account"></a>Criar um aplicativo de funções do Azure e uma conta de armazenamento do Azure
O [Azure Functions](../../articles/azure-functions/functions-overview.md) é uma solução para executar facilmente *funções* (pequenos trechos de código) na nuvem. Um aplicativo de funções do Azure hospeda a execução de suas funções no Azure.

## <a name="what-will-you-do"></a>O que você fará
Use um modelo do Azure Resource Manager para criar um aplicativo de funções do Azure e uma conta de Armazenamento do Azure. O aplicativo de funções do Azure escuta os eventos de Hub IoT do Azure, processa as mensagens recebidas e grava-as no armazenamento de Tabelas do Azure. A conta de armazenamento é usada para ler as cópias persistentes de mensagens da tabela do Azure. Se você tiver problemas, procure por soluções na [página de solução de problemas][troubleshooting].

## <a name="what-will-you-learn"></a>O que você aprenderá
Neste artigo, você aprenderá:
* Como usar o [Azure Resource Manager](../../articles/azure-resource-manager/resource-group-overview.md) para implantar recursos do Azure.
* Como usar um aplicativo de funções do Azure para processar mensagens do Hub IoT e gravá-las em uma tabela no armazenamento de Tabelas do Azure.

## <a name="what-do-you-need"></a>Do que você precisa
Você deve ter concluído com sucesso:
- [Introdução ao Intel Edison][get-started-with-your-intel-edison]
- [Criar seu Hub IoT do Azure][create-your-azure-iot-hub]

## <a name="open-the-sample-app"></a>Abrir o aplicativo de exemplo
Abra o projeto de exemplo no Visual Studio Code executando os seguintes comandos:

```bash
cd Lesson3
code .
```

![Estrutura do repositório][repo-structure]

* O arquivo na subpasta `app` é o arquivo de origem chave. Esse arquivo de origem contém o código para enviar uma mensagem 20 vezes para o hub IoT e piscar o LED para cada mensagem que ele envia.
* O arquivo `arm-template.json` é o modelo do Azure Resource Manager que contém um aplicativo de funções do Azure e uma conta de Armazenamento do Azure.
* O arquivo `arm-template-param.json` é o arquivo de configuração usado pelo modelo do Azure Resource Manager.
* A subpasta `ReceiveDeviceMessages` contém o código do Node.js para a função do Azure.

## <a name="configure-azure-resource-manager-templates-and-create-resources-in-azure"></a>Configurar modelos do Azure Resource Manager e criar recursos no Azure
Atualize o arquivo `arm-template-param.json` no Visual Studio Code.

![Parâmetros do modelo do Azure Resource Manager][arm-template-parameters]

* Substitua **[o nome de seu Hub IoT]** por **{nome do meu hub}** que foi especificado quando você [criou o Hub IoT e registrou o Intel Edison][created-your-iot-hub-and-registered-intel-edison].
* Substitua **[cadeia de caracteres de prefixo para novos recursos]** pelo prefixo que quiser. O prefixo garante que o nome do recurso seja globalmente exclusivo para evitar conflitos. Não use um traço nem número inicial no prefixo.

Depois de atualizar o arquivo `arm-template-param.json`, implante os recursos do Azure executando o seguinte comando:

```bash
az group deployment create --template-file arm-template.json --parameters @arm-template-param.json -g iot-sample
```

Levará cerca de cinco minutos para criar esses recursos. Embora a criação de recursos esteja em andamento, você pode passar para o próximo artigo.

## <a name="summary"></a>Resumo
Você criou o aplicativo de funções do Azure para processar as mensagens de hub IoT e uma conta de Armazenamento do Azure para armazenar essas mensagens. Agora, é possível implantar e executar o exemplo para enviar mensagens do dispositivo para a nuvem no Edison.

## <a name="next-steps"></a>Próximas etapas
[Executar um aplicativo de exemplo para enviar mensagens do dispositivo para a nuvem no Intel Edison][send-device-to-cloud-messages].
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[get-started-with-your-intel-edison]: iot-hub-intel-edison-kit-node-get-started.md
[create-your-azure-iot-hub]: iot-hub-intel-edison-kit-node-get-started.md
[repo-structure]: media/iot-hub-intel-edison-lessons/lesson3/repo_structure.png
[arm-template-parameters]: media/iot-hub-intel-edison-lessons/lesson3/arm_para.png
[created-your-iot-hub-and-registered-intel-edison]: iot-hub-intel-edison-kit-node-lesson2-prepare-azure-iot-hub.md
[send-device-to-cloud-messages]: iot-hub-intel-edison-kit-node-lesson3-run-azure-blink.md
