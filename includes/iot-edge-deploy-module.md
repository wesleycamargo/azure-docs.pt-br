---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 08/14/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 983c65ba6e8b87f1dd66fcfdb50eac088ffab5d0
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49960182"
---
Um dos principais recursos do Azure IoT Edge é a possibilidade de implantar módulos em seus dispositivos IoT Edge na nuvem. Um módulo IoT Edge é um pacote executável implementado como contêiner. Nesta seção, você implanta um módulo que gera uma telemetria para seu dispositivo simulado.

1. No portal do Azure, navegue para o hub IoT.
1. Acesse o **IoT Edge** em **Gerenciamento de Dispositivo Automático** e selecione seu dispositivo IoT Edge.
1. Selecione **Definir Módulos**.
1. Na seção **Módulos de Implantação** da etapa **Adicionar Módulos**, clique em **Adicionar** e selecione **Módulo IoT Edge**.
1. No campo **Nome**, insira `tempSensor`.
1. No campo **URI da Imagem**, insira `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`.
1. Deixe as outras configurações inalteradas e selecione **Salvar**.

   ![Salve o módulo do IoT Edge depois de inserir o nome e o URI da imagem](./media/iot-edge-deploy-module/name-image.png)

1. De volta à etapa **Adicionar módulos**, clique em **Avançar**.
1. Na etapa **Especificar rotas**, você deve ter uma rota padrão que envie todas as mensagens de todos os módulos para o Hub IoT. Caso contrário, adicione o código a seguir e selecione **Avançar**.

   ```json
   {
       "routes": {
           "route": "FROM /messages/* INTO $upstream"
       }
   }
   ```

1. Na etapa **Revisar Implantação**, selecione **Enviar**.
1. Volte para a página de detalhes do dispositivo e selecione **Atualizar**. Além de módulo edgeAgent, criado quando você iniciou o serviço pela primeira vez, poderá ser visto outro módulo de tempo de execução, chamado **edgeHub**, e o módulo **tempSensor** listados.

   ![Exibir o TempSensor na lista de módulos implantados](./media/iot-edge-deploy-module/deployed-modules.png)
