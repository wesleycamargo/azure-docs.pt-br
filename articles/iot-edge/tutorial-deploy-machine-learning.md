---
title: Implantar o Azure Machine Learning com o Azure IoT Edge | Microsoft Docs
description: "Implantar o Azure Machine Learning como um módulo em um dispositivo de borda"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 9b8475dcc51fb24fadd1faa4a2008b25a4464080
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-azure-machine-learning-as-an-iot-edge-module---preview"></a>Implantar o Azure Machine Learning como um módulo do IoT Edge – versão prévia

Use os módulos do IoT Edge para implantar um código que implementa a lógica de negócios diretamente em seus dispositivos do IoT Edge. Este tutorial orienta você pela implantação de um módulo do Azure Machine Learning que prevê quando um dispositivo falha com base nos dados de sensor do dispositivo simulado do IoT Edge criado nos tutoriais Implantar o Azure IoT Edge em um dispositivo simulado no [Windows][lnk-tutorial1-win] ou [Linux][lnk-tutorial1-lin]. Você aprenderá como: 

> [!div class="checklist"]
> * Implantar um módulo do Azure Machine Learning no dispositivo do IoT Edge
> * Exibir os dados gerados

Quando desejar usar seu próprio modelo do [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/preview/) na solução, você [implantará um modelo](https://aka.ms/aml-iot-edge-doc) no IoT Edge e o hospedará em um registro de contêiner como o [Registro de Contêiner do Azure](../container-registry/index.yml) ou o Docker.

## <a name="prerequisites"></a>Pré-requisitos

* O dispositivo do Azure IoT Edge criado no guia de início rápido ou no primeiro tutorial.
* A cadeia de conexão do Hub IoT ao qual o dispositivo do IoT Edge se conecta.
* O contêiner do Azure ML

## <a name="create-the-azure-ml-container"></a>Criar o contêiner do Azure ML
Para criar o contêiner do Azure ML, siga as instruções do [Kit de ferramentas de IA para o Azure IoT Edge](https://aka.ms/aml-iot-edge-anomaly-detection).

## <a name="run-the-solution"></a>Executar a solução

1. No [portal do Azure](https://portal.azure.com), navegue para o hub IoT.
1. Acesse **IoT Edge (versão prévia)** e selecione o dispositivo do IoT Edge.
1. Selecione **Definir módulos**.
1. Selecione **Adicionar módulo do IoT Edge**.
1. No campo **Nome**, insira `tempSensor`.
1. No campo **URI de Imagem**, insira `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
1. Deixe as outras configurações inalteradas e selecione **Salvar**.
1. Ainda na etapa **Adicionar Módulos**, selecione **Adicionar módulo do IoT Edge** novamente.
1. No campo **Nome**, insira o nome do contêiner criado na seção anterior. Consulte o [Kit de ferramentas de IA para o Azure IoT Edge](https://aka.ms/aml-iot-edge-anomaly-detection) para obter ajuda para encontrar o nome.
1. No campo **Imagem**, insira o URI de imagem do contêiner criado na seção anterior. Consulte o [Kit de ferramentas de IA para o Azure IoT Edge](https://aka.ms/aml-iot-edge-anomaly-detection) para obter ajuda para encontrar a imagem.
1. Clique em **Salvar**.
1. De volta à etapa **Adicionar Módulos**, clique em **Avançar**.
1. Atualize as rotas para o módulo:
1. Na etapa **Especificar Rotas**, copie o JSON abaixo na caixa de texto. Os módulos publicam todas as mensagens no tempo de execução do Edge. As regras declarativas no tempo de execução definem o local em que as mensagens fluem. Neste tutorial, você precisa de duas rotas. A primeira rota transporta as mensagens do sensor de temperatura para o módulo de aprendizado de máquina por meio do ponto de extremidade “mlInput”, que é o ponto de extremidade usado por todos os módulos do Azure Machine Learning. A segunda rota transporta as mensagens do módulo de aprendizado de máquina para o Hub IoT. Nesta rota, “mlOutput” é o ponto de extremidade usado por todos os módulos do Azure Machine Learning para gerar dados e “upstream” é um destino especial que faz com que o Hub do Edge envie mensagens ao Hub IoT. 

    ```json
    {
        "routes": {
            "sensorToMachineLearning":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/machinelearningmodule/inputs/amlInput\")",
            "machineLearningToIoTHub": "FROM /messages/modules/machinelearningmodule/outputs/amlOutput INTO $upstream"
        }
    }
    ``` 

1. Clique em **Avançar**. 
1. Na etapa “Examinar Modelo”, clique em “Enviar”. 
1. Volte para a página de detalhes do dispositivo e clique em “Atualizar”.  Você deverá ver o novo “machinelearningmodule” em execução junto com o “módulo tempSensor” e o “tempo de execução do IoT Edge”.

## <a name="view-generated-data"></a>Exibir os dados gerados

 No VS Code, use o comando de menu **Exibir | Paleta de Comandos... | IoT: Iniciar o Monitoramento de Mensagens D2C** para monitorar os dados recebidos no Hub IoT. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você implantou um módulo do IoT Edge da plataforma Azure Machine Learning. Continue com um dos outros tutoriais para saber mais sobre outras formas pelas quais o Azure IoT Edge pode ajudá-lo a transformar dados em informações de negócios na borda.

> [!div class="nextstepaction"]
> [Implantar um Azure Function como um módulo](tutorial-deploy-function.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md