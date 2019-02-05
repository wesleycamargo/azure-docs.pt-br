---
title: Tutorial para implantar o Azure Machine Learning em um dispositivo – Azure IoT Edge | Microsoft Docs
description: Neste tutorial, você implantará o Azure Machine Learning como um módulo em um dispositivo de borda
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/15/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 464d16d4bbcbdbefd36ce1132630ad702d7a0c90
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2019
ms.locfileid: "55076955"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>Tutorial: Implantar o Azure Machine Learning como um módulo do IoT Edge (versão prévia)

Use os módulos do IoT Edge para implantar um código que implementa a lógica de negócios diretamente em seus dispositivos IoT Edge. Este tutorial mostra todas as etapas necessárias para implantar um módulo do Azure Machine Learning que prevê quando um dispositivo falhará com base nos dados de temperatura do computador simulado. Para saber mais sobre o Serviço do Azure Machine Learning no IoT Edge, confira a [Documentação do Azure Machine Learning](../machine-learning/service/how-to-deploy-to-iot.md).

O módulo do Azure Machine Learning criado neste tutorial lê os dados ambientais gerados pelo dispositivo e rotula as mensagens como anômalas ou não.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um módulo do Azure Machine Learning
> * Enviar por push a um contêiner de módulo para um Registro de Contêiner do Azure
> * Implantar um módulo do Azure Machine Learning no dispositivo do IoT Edge
> * Exibir os dados gerados

>[!NOTE]
>Os módulos do Azure Machine Learning no Azure IoT Edge são públicos na versão prévia.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo do Azure IoT Edge:

* Você pode usar seu computador de desenvolvimento ou uma máquina virtual como um dispositivo do Edge seguindo as etapas no início rápido para os [dispositivos Linux](quickstart-linux.md) ou [Windows](quickstart.md).
* O módulo de aprendizado de máquina do Azure não oferece suporte a processadores ARM.

Recursos de nuvem:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na camada padrão ou gratuito no Azure.
* Um Workspace do Azure Machine Learning. Siga as instruções em [Preparar para implantar modelos no IoT Edge](../machine-learning/service/how-to-deploy-to-iot.md) para criar um.


### <a name="disable-process-identification"></a>Desabilitar a identificação do processo

>[!NOTE]
>
> Enquanto estiver na versão prévia, o Azure Machine Learning não suporta o recurso de segurança de identificação de processo habilitado por padrão no IoT Edge.
> Abaixo estão as etapas para desabilitá-lo. No entanto isso não é adequado para uso em produção. Essas etapas só são necessárias no Linux, já que você as terá concluído durante a instalação do tempo de execução do Windows Edge.

Para desabilitar a identificação do processo no seu dispositivo do IoT Edge será necessário fornecer o endereço IP e a porta para **workload_uri** e **management_uri** na seção **connect** da configuração do daemon do IoT Edge.

Obtenha primeiro o endereço IP. Digite `ipconfig` na linha de comando e copie o endereço IP da interface **docker0**.

Edite o arquivo de configuração do daemon do IoT Edge:

```cmd/sh
sudo nano /etc/iotedge/config.yaml
```

Atualize a seção **connect** seção da configuração com seu endereço IP. Por exemplo: 
```yaml
connect:
  management_uri: "http://172.17.0.1:15580"
  workload_uri: "http://172.17.0.1:15581"
```

Insira os mesmos endereços na seção **listen** da configuração. Por exemplo: 

```yaml
listen:
  management_uri: "http://172.17.0.1:15580"
  workload_uri: "http://172.17.0.1:15581"
```

Crie uma variável de ambiente IOTEDGE_HOST com o endereço management_uri (para defini-la de forma permanente, adicione-a à `/etc/environment`), por exemplo:

```cmd/sh
export IOTEDGE_HOST="http://172.17.0.1:15580"
```


## <a name="create-the-azure-machine-learning-service-container"></a>Criar o contêiner de Serviço do Azure Machine Learning
Nesta seção, você pode baixar os arquivos de modelo treinados e convertê-los em um contêiner de Serviço do Azure Machine Learning.

Siga as instruções na documentação [Preparar para implantar modelos no IoT Edge](../machine-learning/service/how-to-deploy-to-iot.md) para criar um contêiner do Docker com o seu modelo de machine learning.  Todos os componentes necessários para a imagem do Docker estão no [repositório Git do Kit de Ferramentas de IA para o Azure IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial).

### <a name="view-the-container-repository"></a>Exibição do repositório de contêiner

Verifique se a imagem de seu contêiner foi criada com êxito e armazenada no Registro de Contêiner do Azure que está associado ao seu ambiente de aprendizado de máquina.

1. No [portal do Azure](https://portal.azure.com), vá para **Todos os serviços** e selecione **Registros de contêiner**.
2. Selecione seu registro. O nome deve começar com **mlcr** e pertence ao grupo de recursos, local e assinatura que você usou para configurar o Gerenciamento de Módulo.
3. Selecione **Chaves de acesso**
4. Copie o **Servidor de logon**, **Nome de usuário** e **Senha**.  É necessário para acessar o registro dos seus dispositivos Edge.
5. Selecione **Repositórios**
6. Selecione **machinelearningmodule**
7. Agora você tem o caminho completo da imagem do contêiner. Anote esse caminho de imagem para a próxima seção. Ele deve se parecer com: **<registry_name>.azurecr.io/machinelearningmodule:1**

## <a name="deploy-to-your-device"></a>Implantar no seu dispositivo

1. No [portal do Azure](https://portal.azure.com), navegue para o hub IoT.

1. Vá até o **IoT Edge** e selecione o dispositivo IoT Edge.

1. Selecione **Definir módulos**.

1. Na seção **Configurações do registro**, adicione as credenciais que você copiou de seu registro de contêiner do Azure.

   ![Adicionar credenciais de Registro ao manifesto](./media/tutorial-deploy-machine-learning/registry-settings.png)

1. Se você implantou o módulo tempSensor no seu dispositivo IoT Edge, talvez ele seja automaticamente populado. Se ainda não estiver na lista de módulos, adicioná-lo.

    1. Clique em **Adicionar** e selecione **Módulo do IoT Edge**.
    2. No campo **Nome**, insira `tempSensor`.
    3. No campo **URI da Imagem**, insira `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`.
    4. Clique em **Salvar**.

1. Adicione a módulo de machine learning que você criou.

    1. Clique em **Adicionar** e selecione **Módulo do IoT Edge**.
    1. No campo **Nome**, insira `machinelearningmodule`
    1. No campo **imagem**, insira seu endereço de imagem, por exemplo, `<registry_name>.azurecr.io/machinelearningmodule:1`.
    1. Clique em **Salvar**.

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

1. Na etapa **Revisar Implantação**, selecione **Enviar**.

1. Volte para a página de detalhes do dispositivo e selecione **Atualizar**.  Você deverá ver o novo **machinelearningmodule** em execução junto com o módulo **tempSensor** e os módulos tempo de execução do IoT Edge.

## <a name="view-generated-data"></a>Exibir os dados gerados

Você pode exibir mensagens que são geradas por módulo do IoT Edge, e você pode exibir as mensagens que são entregues para o Hub IoT.

### <a name="view-data-on-your-iot-edge-device"></a>Exibir dados no seu dispositivo IoT Edge

No seu dispositivo IoT Edge, você pode exibir as mensagens que são enviadas de cada módulo individual.

Se você executar esses comandos em um dispositivo do Linux, você precisará usar `sudo` para permissões elevadas.

1. Exibir todos os módulos no seu dispositivo IoT Edge.

   ```cmd/sh
   iotedge list
   ```

2. Exibir as mensagens que estão sendo enviadas de um dispositivo específico. Use o nome do módulo da saída do comando anterior.

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>Exibir dados que chegam ao seu Hub IoT

Você pode exibir as mensagens do dispositivo para nuvem recebidas pelo Hub IoT usando a [extensão do Kit de Ferramentas do Hub IoT do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (anteriormente, extensão do Kit de Ferramentas do Azure IoT).

As etapas a seguir mostram como configurar o Visual Studio Code para monitorar mensagens de dispositivo para a nuvem que chegam em seu Hub IoT.

1. No Visual Studio Code, selecione **Dispositivos de Hub IoT**.

2. Selecione **...** , em seguida, selecione **Definir cadeia de Conexão de Hub IoT** no menu.

   ![Definir cadeia de conexão do Hub IoT](./media/tutorial-deploy-machine-learning/set-connection.png)

3. Na caixa de texto que é aberta na parte superior da página, insira a cadeia de caracteres de conexão iothubowner para seu Hub IoT. Seu dispositivo Edge IoT deverá aparecer na lista de dispositivos de Hub IoT.

4. Selecione **...**  novamente, em seguida, selecione **Iniciar o monitoramento de mensagem D2C**.

5. Observe as mensagens recebidas de tempSensor a cada cinco segundos. O corpo da mensagem contém uma propriedade chamada **anomaly** que fornece ao machinelearningmodule um valor verdadeiro ou falso. A propriedade **AzureMLResponse** contém o valor "OK" se o modelo foi executado com êxito.

   ![Resposta do Serviço do Azure Machine Learning no corpo da mensagem](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se você pretende continuar no próximo artigo recomendado, pode manter os recursos e as configurações já criados e reutilizá-los. Você também pode continuar usando o mesmo dispositivo IoT Edge como um dispositivo de teste.

Caso contrário, você pode excluir as configurações locais e os recursos do Azure criados neste artigo para evitar encargos.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você implantou um módulo do IoT Edge da plataforma Azure Machine Learning. Continue com um dos outros tutoriais para saber mais sobre outras formas pelas quais o Azure IoT Edge pode ajudá-lo a transformar dados em informações de negócios na borda.

> [!div class="nextstepaction"]
> [Classificar imagens com o serviço de Visão Personalizada](tutorial-deploy-custom-vision.md)

