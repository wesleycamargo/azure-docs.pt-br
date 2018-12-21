---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/7/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 1a750a97cdc940c0f0a3d7e33d6be0d33f811425
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53108015"
---
Um dos principais recursos do Azure IoT Edge é a possibilidade de implantar módulos em seus dispositivos IoT Edge na nuvem. Um módulo IoT Edge é um pacote executável implementado como contêiner. Nesta seção, implementaremos um módulo criado previamente na [seção Módulos do IoT Edge do Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Este módulo gera uma telemetria para seu dispositivo simulado.

1. No portal do Azure, insira `Simulated Temperature Sensor` na pesquisa e abra o resultado do Marketplace.

   ![Sensor de temperatura simulado na pesquisa do portal do Azure](./media/iot-edge-deploy-module/search-for-temperature-sensor.png)

2. No campo **Assinatura**, selecione a assinatura com o Hub IoT que você está usando se ainda não a selecionou.

3. No campo **Hub IoT**, selecione o nome do Hub IoT que você está usando se ainda não o selecionou.

4. Clique em **Localizar dispositivo**, selecione seu dispositivo IoT Edge (denominado `myEdgeDevice`), em seguida, selecione **Criar**.

5. Na etapa **Adicionar módulos** do assistente, clique no módulo **SimulatedTemperatureSensor** para verificar suas configurações, clique em **Salvar** e selecione **Próximo**.

6. Na etapa **Especificar rotas** do assistente, verifique se as rotas foram devidamente configuradas com a rota padrão que envia todas as mensagens de todos os módulos para o Hub IoT (`$upstream`). Caso contrário, adicione o código a seguir e selecione **Avançar**.

   ```json
    {
    "routes": {
        "route": "FROM /messages/* INTO $upstream"
        }
    }
   ```

7. No assistente, na etapa **Revisar implantação**, selecione **Enviar**.

8. Volte para a página de detalhes do dispositivo e selecione **Atualizar**. Além do módulo edgeAgent, criado quando você iniciou o serviço pela primeira vez, você poderá ver outro módulo de tempo de execução chamado **edgeHub** e o módulo **SimulatedTemperatureSensor** listados.

   Pode demorar alguns minutos até que os novos módulos apareçam. O dispositivo IoT Edge tem de recuperar suas novas informações de implantação da nuvem, iniciar os contêineres e depois relatar o novo status de volta ao Hub IoT. 

   ![Exibir SimulatedTemperatureSensor na lista de módulos implantados](./media/iot-edge-deploy-module/deployed-modules-marketplace-temp.png)
