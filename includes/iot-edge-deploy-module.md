---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 10/14/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 536857a5fe3ec3cc037f21835a4152f93197bcb8
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2018
ms.locfileid: "51977386"
---
Um dos principais recursos do Azure IoT Edge é a possibilidade de implantar módulos em seus dispositivos IoT Edge na nuvem. Um módulo IoT Edge é um pacote executável implementado como contêiner. Nesta seção, você implanta um módulo que gera uma telemetria para seu dispositivo simulado.

1. No portal do Azure, navegue para o hub IoT.

2. Acesse o **IoT Edge** em **Gerenciamento de Dispositivo Automático** e selecione seu dispositivo IoT Edge.

3. Selecione **Definir Módulos**. Um assistente de três etapas é aberto no portal, que orienta você por meio da adição de módulos, especificando as rotas e analisando a implantação. 

4. Na etapa **Adicionar módulos** do assistente, localize a seção **Módulos de implantação**. Clique em **Adicionar**, depois selecione **Módulo do IoT Edge**.

   ![Adicionar um novo módulo do IoT Edge](./media/iot-edge-deploy-module/add-module.png)

5. No campo **Nome**, insira `tempSensor`.

6. No campo **URI da Imagem**, insira `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`.

7. Deixe as outras configurações inalteradas e selecione **Salvar**.

   ![Salve o módulo do IoT Edge depois de inserir o nome e o URI da imagem](./media/iot-edge-deploy-module/name-image.png)

8. Na primeira etapa do assistente, selecione **Avançar**.

9. No assistente, na etapa **Especificar rotas**, você deve ter uma rota padrão que envie todas as mensagens de todos os módulos para o Hub IoT. Caso contrário, adicione o código a seguir e selecione **Avançar**.

   ```json
   {
       "routes": {
           "route": "FROM /messages/* INTO $upstream"
       }
   }
   ```

10. No assistente, na etapa **Revisar implantação**, selecione **Enviar**.

11. Volte para a página de detalhes do dispositivo e selecione **Atualizar**. Além de módulo edgeAgent, criado quando você iniciou o serviço pela primeira vez, poderá ser visto outro módulo de tempo de execução, chamado **edgeHub**, e o módulo **tempSensor** listados.

   Pode demorar alguns minutos até que os novos módulos apareçam. O dispositivo IoT Edge tem de recuperar suas novas informações de implantação da nuvem, iniciar os contêineres e depois relatar o novo status de volta ao Hub IoT. 

   ![Exibir o TempSensor na lista de módulos implantados](./media/iot-edge-deploy-module/deployed-modules.png)
