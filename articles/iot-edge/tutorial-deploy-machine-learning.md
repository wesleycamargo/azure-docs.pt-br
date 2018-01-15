---
title: Implantar o Azure Machine Learning com o Azure IoT Edge | Microsoft Docs
description: "Implantar o Azure Machine Learning como um módulo em um dispositivo de borda"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 12/13/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: a0131fdbbf926d59eae06089cde109649a1433b8
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2018
---
# <a name="deploy-azure-machine-learning-as-an-iot-edge-module---preview"></a>Implantar o Azure Machine Learning como um módulo do IoT Edge – versão prévia

Use os módulos do IoT Edge para implantar um código que implementa a lógica de negócios diretamente em seus dispositivos IoT Edge. Este tutorial orienta você pela implantação de um módulo do Azure Machine Learning que prevê quando um dispositivo falha com base nos dados de sensor do dispositivo simulado do IoT Edge criado nos tutoriais Implantar o Azure IoT Edge em um dispositivo simulado no [Windows][lnk-tutorial1-win] ou [Linux][lnk-tutorial1-lin]. 

Neste tutorial, você aprenderá como: 

> [!div class="checklist"]
> * Criar um módulo do Azure Machine Learning
> * Enviar por push a um contêiner de módulo para um Registro de Contêiner do Azure
> * Implantar um módulo do Azure Machine Learning no dispositivo do IoT Edge
> * Exibir os dados gerados

O módulo do Azure Machine Learning criado neste tutorial lê os dados de temperatura gerados pelo dispositivo e envia apenas mensagens upstream para o Hub IoT do Azure quando prevê uma falha (chamada de uma anomalia). 


## <a name="prerequisites"></a>Pré-requisitos

* O dispositivo do Azure IoT Edge criado no guia de início rápido ou no primeiro tutorial.
* A cadeia de conexão do Hub IoT para o hub IoT ao qual o dispositivo IoT Edge se conecta.
* Uma conta do Azure Machine Learning. Para criar uma conta, siga as instruções em [Criar contas do Azure Machine Learning e instalar o Azure Machine Learning Workbench](../machine-learning/preview/quickstart-installation.md#create-azure-machine-learning-accounts). Você não precisa instalar o aplicativo workbench para este tutorial. 
* Gerenciamento de módulo para Azure ML no seu computador. Para configurar seu ambiente e criar uma conta, siga as instruções em [Configuração de gerenciamento de modelo](https://docs.microsoft.com/azure/machine-learning/preview/deployment-setup-configuration).

## <a name="create-the-azure-ml-container"></a>Criar o contêiner do Azure ML
Nesta seção, você pode fazer o download dos arquivos de modelo treinado e convertê-los em um contêiner do Azure ML.  

No computador executando o módulo de gerenciamento do Azure ML, faça o download e salve [iot_score.py](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/iot_score.py) e [model.pkl](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/model.pkl) do Kit de ferramentas IoT do Azure ML no GitHub. Esses arquivos definem o modelo de machine learning treinado que você implantará em seu dispositivo IoT Edge. 

Use o modelo treinado para criar um contêiner que pode ser implantado em dispositivos IoT Edge.

```cmd
az ml service create realtime --model-file model.pkl -f iot_score.py -n machinelearningmodule -r python
```
O nome do serviço, *machinelearningmodule* neste exemplo, se torna o nome da imagem de contêiner do docker.

### <a name="view-the-container-repository"></a>Exibição do repositório de contêiner

Verifique se a imagem de seu contêiner foi criada com êxito e armazenada no repositório do contêiner do Azure que está associado ao seu ambiente de machine learning.

1. No [portal do Azure](https://portal.azure.com), vá para **Todos os serviços** e selecione **Registros de contêiner**.
2. Selecione seu registro. O nome deve começar com **mlcr** e pertence ao grupo de recursos, local e assinatura que você usou para configurar o Gerenciamento de Módulo.
3. Selecione **Chaves de acesso**
4. Copie o **Servidor de logon**, **Nome de usuário** e **Senha**.  É necessário para acessar o registro dos seus dispositivos Edge.
5. Selecione **Repositórios**
6. Selecione **machinelearningmodule**
7. Agora você tem o caminho completo da imagem do contêiner. Anote esse caminho de imagem para a próxima seção. Ele deve se parecer com:  **<registry_name>.azureacr.io/machinelearningmodule:1**

## <a name="add-registry-credentials-to-your-edge-device"></a>Adicionar credenciais de registro ao dispositivo Edge

Adicione as credenciais do seu registro ao tempo de execução do Edge no computador em que você está executando o dispositivo Edge. Esse comando dá acesso ao tempo de execução para efetuar pull no contêiner.

Linux:
   ```cmd
   sudo iotedgectl login --address <registry-login-server> --username <registry-username> --password <registry-password> 
   ```

Windows:
   ```cmd
   iotedgectl login --address <registry-login-server> --username <registry-username> --password <registry-password> 
   ```

## <a name="run-the-solution"></a>Executar a solução

1. No [portal do Azure](https://portal.azure.com), navegue para o hub IoT.
1. Acesse **IoT Edge (versão prévia)** e selecione o dispositivo do IoT Edge.
1. Selecione **Definir módulos**.
1. Se você implantou o módulo tempSensor no seu dispositivo IoT Edge, talvez ele seja automaticamente populado. Se ainda não estiver na lista de módulos, adicioná-lo.
    1. Selecione **Adicionar Módulo do IoT Edge**.
    2. No campo **Nome**, insira `tempSensor`.
    3. No campo **URI da Imagem**, insira `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Selecione **Salvar**.
1. Adicione a módulo de machine learning que você criou.
    1. Selecione **Adicionar Módulo do IoT Edge**.
    1. No campo **Nome**, insira `machinelearningmodule`
    1. No campo **imagem**, insira seu endereço de imagem, por exemplo, `<registry_name>.azurecr.io/machinelearningmodule:1`.
    1. Selecione **Salvar**.
1. De volta à etapa **Adicionar módulos**, clique em **Avançar**.
1. Na etapa **Especificar Rotas**, copie o JSON abaixo na caixa de texto. A primeira rota transporta as mensagens do sensor de temperatura para o módulo de aprendizado de máquina por meio do ponto de extremidade “amlInput”, que é o ponto de extremidade usado por todos os módulos do Azure Machine Learning. A segunda rota transporta as mensagens do módulo de aprendizado de máquina para o Hub IoT. Nesta rota, ''amlOutput'' é o endpoint usado por todos os módulos do Azure Machine Learning para dados de saída e ''$upstream'' denota o IoT Hub. 

    ```json
    {
        "routes": {
            "sensorToMachineLearning":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/machinelearningmodule/inputs/amlInput\")",
            "machineLearningToIoTHub": "FROM /messages/modules/machinelearningmodule/outputs/amlOutput INTO $upstream"
        }
    }
    ``` 

1. Selecione **Avançar**. 
1. Na etapa **Revisar Modelo**, selecione **Enviar**. 
1. Volte para a página de detalhes do dispositivo e selecione **Atualizar**.  Você deverá ver o novo **machinelearningmodule** em execução junto com o módulo **tempSensor** e os módulos tempo de execução do IoT Edge.

## <a name="view-generated-data"></a>Exibir os dados gerados

Você pode exibir as mensagens de dispositivo para a nuvem que seu dispositivo IoT Edge envia usando a extensão do Kit de ferramentas do Azure IoT para o Visual Studio Code. 

1. No Visual Studio Code, selecione **Dispositivos de Hub IoT**. 
2. Selecione **...** , em seguida, selecione **Definir cadeia de Conexão de Hub IoT** no menu. 

   ![Dispositivos de Hub IoT mais menu](./media/tutorial-deploy-machine-learning/set-connection.png)

3. Na caixa de texto que é aberta na parte superior da página, insira a cadeia de caracteres de conexão iothubowner para seu Hub IoT. Seu dispositivo Edge IoT deverá aparecer na lista de dispositivos de Hub IoT.
4. Selecione **...**  novamente, em seguida, selecione **Iniciar o monitoramento de mensagem D2C**.
5. Observe as mensagens provenientes do tempSensor a cada cinco segundos, qual machinelearningmodule anexa sua avaliação da integridade do dispositivo. 

   ![Resposta do Azure ML no corpo da mensagem](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você implantou um módulo do IoT Edge da plataforma Azure Machine Learning. Continue com um dos outros tutoriais para saber mais sobre outras formas pelas quais o Azure IoT Edge pode ajudá-lo a transformar dados em informações de negócios na borda.

> [!div class="nextstepaction"]
> [Implantar um Azure Function como um módulo](tutorial-deploy-function.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
