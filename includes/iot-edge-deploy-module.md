---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 06/27/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 3e7fcde204966e5a6a8154ab307807652cfa0419
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2018
ms.locfileid: "37086204"
---
Um dos principais recursos do Azure IoT Edge é a possibilidade de implantar módulos em seus dispositivos IoT Edge na nuvem. Um módulo IoT Edge é um pacote executável implementado como contêiner. Nesta seção, você implanta um módulo que gera uma telemetria para seu dispositivo simulado. 

1. No portal do Azure, navegue para o hub IoT.
1. Vá até o **IoT Edge** e selecione o dispositivo IoT Edge.
1. Selecione **Definir Módulos**.
1. Na seção **Módulos de Implantação** da página, clique em **Adicionar** e então selecione **Módulo IoT Edge**.
1. No campo **Nome**, insira `tempSensor`. 
1. No campo **URI da Imagem**, insira `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`. 
1. Deixe as outras configurações inalteradas e selecione **Salvar**.

   ![Salve o módulo do IoT Edge depois de inserir o nome e o URI da imagem](./media/iot-edge-deploy-module/name-image.png)

1. De volta à etapa **Adicionar módulos**, clique em **Avançar**.
1. Na etapa **Especificar rotas**, você deve ter uma rota padrão que envie todas as mensagens de todos os módulos para o Hub IoT. Caso contrário, adicione o código a seguir e selecione **Avançar**.

   ```json
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```

1. Na etapa **Revisar Implantação**, selecione **Enviar**.
1. Volte para a página de detalhes do dispositivo e selecione **Atualizar**. Além de módulo edgeAgent, criado quando você iniciou o serviço pela primeira vez, poderá ser visto outro módulo de tempo de execução, chamado **edgeHub**, e o módulo **tempSensor** listados. 

   ![Exibir o TempSensor na lista de módulos implantados](./media/iot-edge-deploy-module/deployed-modules.png)
