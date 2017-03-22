---
title: "Dispositivo SensorTag e Gateway do IoT do Azure - Lição 4: armazenamento de tabelas | Microsoft Docs"
description: Salve mensagens da NUC da Intel para o hub IoT, grave-as no Armazenamento de Tabelas do Azure e, em seguida, leia-as na nuvem.
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "recuperar dados da nuvem, serviço de nuvem de iot"
ms.assetid: 8ca78045-ad92-4a6a-90f1-05f9668e6f0e
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 293343e5db58bebc8c2e27d54d6396212fe6d3a1
ms.lasthandoff: 01/25/2017


---

# <a name="read-messages-persisted-in-azure-table-storage"></a>Ler mensagens mantidas no Armazenamento de Tabelas do Azure

## <a name="what-you-will-do"></a>O que você fará

- Execute o aplicativo de exemplo de gateway no gateway que envia mensagens ao Hub IoT.
- Em seguida, execute um código de exemplo no computador host para ler mensagens no seu Armazenamento de Tabelas do Azure. 

Se você tiver problemas, procure as soluções na [página de solução de problemas](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>O que você aprenderá

Como usar a ferramenta gulp para executar o código de exemplo para ler mensagens no Armazenamento de Tabelas do Azure.

## <a name="what-you-need"></a>O que você precisa

Você realizou as seguintes tarefas com êxito:

- [O aplicativo de funções do Azure e a conta de armazenamento do Azure foram criados](iot-hub-gateway-kit-c-lesson4-deploy-resource-manager-template.md).
- [Executar o aplicativo de exemplo do gateway](iot-hub-gateway-kit-c-lesson3-configure-ble-app.md).
- [Ler mensagens do Hub IoT](iot-hub-gateway-kit-c-lesson3-read-messages-from-hub.md).

## <a name="get-your-azure-storage-connection-strings"></a>Obtenha cadeias de conexão do armazenamento do Azure

No início desta lição, você criou com êxito uma conta de armazenamento do Azure. Para obter a cadeia de conexão da conta de armazenamento do Azure, execute os seguintes comandos:

* Liste todas suas contas de armazenamento.

```bash
az storage account list -g iot-gateway --query [].name
```

* Obtenha a cadeia de conexão do armazenamento do Azure.

```bash
az storage account show-connection-string -g iot-gateway -n {storage name}
```

Use iot-gateway como o valor de `{resource group name}` se não tiver alterado o valor na Lição 2.

## <a name="configure-the-device-connection"></a>Configurar a conexão do dispositivo

Atualize o arquivo `config-azure.json` para que o código de exemplo executado no computador host possa ler mensagens no Armazenamento de Tabelas do Azure. Para configurar a conexão do dispositivo, siga estas etapas:

1. Abra o arquivo de configuração do dispositivo `config-azure.json` executando os seguintes comandos:

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-azure.json
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-azure.json
   ```

   ![configuração](media/iot-hub-gateway-kit-lessons/lesson4/config_azure.png)

2. Substitua `[Azure storage connection string]` pela cadeia de conexão do armazenamento do Azure que você obteve.

   `[IoT hub connection string]` já deve ter sido substituído na seção [Ler mensagens do Hub IoT do Azure](iot-hub-gateway-kit-c-lesson3-read-messages-from-hub.md), na Lição&3;.

## <a name="read-messages-in-your-azure-table-storage"></a>Ler mensagens no Armazenamento de Tabelas do Azure

Execute o aplicativo de exemplo no gateway e leia as mensagens de armazenamento de tabelas do Azure executando o seguinte comando:

```bash
gulp run --table-storage
```

O Hub IoT dispara o aplicativo de Funções do Azure para salvar a mensagem no Armazenamento de Tabelas do Azure quando a nova mensagem chega.
O comando `gulp run` executa o aplicativo de exemplo no gateway que envia mensagens ao Hub IoT. Com o parâmetro `table-storage`, ele também gera um processo filho para receber a mensagem salva no Armazenamento de Tabelas do Azure.

As mensagens que estão sendo enviadas e recebidas são todas exibidas instantaneamente na mesma janela de console no computador host. A instância do aplicativo de exemplo será encerrada automaticamente em 40 segundos.

   ![leitura de gulp](media/iot-hub-gateway-kit-lessons/lesson4/gulp_run_read_table.png)


## <a name="summary"></a>Resumo

Você executou o código de exemplo para ler as mensagens no Armazenamento de Tabelas do Azure salvas pelo aplicativo de Funções do Azure.
