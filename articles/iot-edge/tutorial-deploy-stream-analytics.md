---
title: Implantar o Azure Stream Analytics com o Azure IoT Edge | Microsoft Docs
description: "Implantar o Azure Stream Analytics como um módulo em um dispositivo de borda"
services: iot-edge
keywords: 
author: msebolt
manager: timlt
ms.author: v-masebo
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 0d19d1142cf15221f84692f7e613edd6b46b4083
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2017
---
# <a name="deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>Implantar o Azure Stream Analytics como um módulo do IoT Edge – versão prévia

Os dispositivos IoT podem gerar grandes quantidades de dados. Às vezes, esses dados precisam ser analisados ou processados antes de chegarem à nuvem, a fim de reduzir o tamanho dos dados carregados ou eliminar a latência da viagem de ida e volta de um insight acionável.

O [ASA][azure-stream] (Azure Stream Analytics) fornece uma sintaxe de consulta estruturada de modo sofisticado para a análise de dados na nuvem e em dispositivos do IoT Edge. Para obter mais informações sobre o ASA no IoT Edge, consulte a [documentação do ASA](../stream-analytics/stream-analytics-edge.md).

Este tutorial orienta você pela criação de um trabalho do Azure Stream Analytics e sua implantação em um dispositivo do IoT Edge, a fim de processar um fluxo de telemetria local diretamente no dispositivo e, em seguida, gerar alertas para conduzir uma ação imediata no dispositivo.  Há dois módulos envolvidos neste tutorial. Um módulo de sensor de temperatura simulado (tempSensor) que gera dados de temperatura de 20 a 120 graus, incrementados em 1 a cada 5 segundos e um módulo do ASA que filtra temperaturas maiores que 100 graus. O módulo do ASA também redefine o tempSensor quando a média de 30 segundos atinge 100.

Você aprenderá como:

> [!div class="checklist"]
> * Criar um trabalho do ASA para processar dados no Edge
> * Conectar o novo trabalho do ASA com outros módulos do IoT Edge
> * Implantar o trabalho do ASA em um dispositivo do IoT Edge

## <a name="prerequisites"></a>Pré-requisitos

* Um Hub IoT 
* O dispositivo que você criou e configurou no guia de início rápido ou em Implantar o Azure IoT Edge em um dispositivo simulado no [Windows][lnk-tutorial1-win] e no [Linux][lnk-tutorial1-lin].
* Docker em seu dispositivo do IoT Edge
    * [Instale o Docker no Windows][lnk-docker-windows] e verifique se ele está em execução.
    * [Instale o Docker no Linux][lnk-docker-linux] e verifique se ele está em execução.
* Python 2.7.x em seu dispositivo do IoT Edge
    * [Instale o Python 2.7 no Windows][lnk-python].
    * A maioria das distribuições Linux, incluindo o Ubuntu, já tem o Python 2.7 instalado.  Use o seguinte comando para verificar se o Pip está instalado: `sudo apt-get install python-pip`.

> [!NOTE]
> Observe que a cadeia de conexão do dispositivo e a ID do dispositivo do IoT Edge serão necessárias para este tutorial.

O IoT Edge aproveita os módulos pré-criados do IoT Edge do Serviço do Azure para implantação rápida e o ASA (Azure Stream Analytics) é um módulo desse tipo. Crie um trabalho do ASA em seu portal e, em seguida, acesse o portal do Hub IoT para implantá-lo como um Módulo do IoT Edge.  

Para obter mais informações sobre o Azure Stream Analytics, consulte a seção **Visão geral** da [Documentação do Stream Analytics][azure-stream].

## <a name="create-an-asa-job"></a>Criar um trabalho do ASA

Nesta seção, você cria um trabalho do Azure Stream Analytics para extrair dados de seu hub IoT, consultar os dados telemétricos enviados de seu dispositivo e encaminhar os resultados para um Contêiner do Armazenamento do Azure (BLOB). Para obter mais informações, consulte a seção **Visão geral** da [Documentação do Stream Analytics][azure-stream]. 

> [!NOTE]
> Uma conta de Armazenamento do Azure é necessária para fornecer um ponto de extremidade a ser usado como uma saída no trabalho do ASA. O exemplo abaixo usa o tipo de armazenamento de BLOBs.  Para obter mais informações, consulte a seção **Blobs** da [Documentação do Armazenamento do Azure][azure-storage].

1. No portal do Azure, navegue para **Criar um recurso -> Armazenamento**, clique em **Ver todos** e clique em **Conta de armazenamento – blob, arquivo, tabela, fila**.

2. Insira um nome para a conta de armazenamento e selecione o mesmo local em que o Hub IoT está armazenado. Clique em **Criar**. Anote o nome para usá-lo mais tarde.

    ![nova conta de armazenamento][1]

3. No portal do Azure, navegue para a conta de armazenamento recém-criada. Clique em **Procurar blobs** em **Serviço Blob**. 
4. Crie um novo contêiner para o módulo do ASA para armazenar dados. Defina o nível de acesso como _Contêiner_. Clique em **OK**.

    ![configurações de armazenamento][10]

5. No portal do Azure, navegue para **Criar um recurso** > **Internet das Coisas** e selecione **Trabalho do Stream Analytics**.

2. Insira um nome, escolha **Edge** como o ambiente de Hospedagem e use os valores padrão restantes.  Clique em **Criar**.

    >[!NOTE]
    >Atualmente, trabalhos ASA na borda IoT não têm suporte na região US West 2. Selecione um local diferente.

    ![criação do ASA][5]

2. Vá para o trabalho criado, em **Topologia do Trabalho**, selecione **Entradas** e clique em **Adicionar**.

3. Insira o nome `temperature`, escolha **Fluxo de dados** como o tipo de fonte e use os padrões para os outros parâmetros. Clique em **Criar**.

    ![entrada do ASA][2]

    > [!NOTE]
    > As entradas adicionais podem incluir pontos de extremidade específicos ao IoT Edge.

4. Em **Topologia do Trabalho**, selecione **Saídas** e clique em **Adicionar**.

5. Insira o nome `alert` e use os padrões. Clique em **Criar**.

    ![saída do ASA][3]

6. Em **Topologia do Trabalho**, selecione **Consulta** e insira o seguinte:

    ```sql
    SELECT  
        'reset' AS command 
    INTO 
       alert 
    FROM 
       temperature TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second,30) 
    HAVING Avg(machine.temperature) > 100
    ```

## <a name="deploy-the-job"></a>Implantar o trabalho

Agora você está pronto para implantar o trabalho do ASA em seu dispositivo do IoT Edge.

1. No portal do Azure, no Hub IoT, navegue para **IoT Edge (versão prévia)** e abra a folha da *{deviceId}*.

1. Selecione **Definir módulos** e, em seguida, **Importar Módulo do IoT Edge do Serviço do Azure**.

1. Selecione a assinatura e o trabalho do ASA no Edge criado. Em seguida, selecione sua conta de armazenamento. Clique em **Salvar**.

    ![definir módulo][6]

1. Clique em **Adicionar Módulo do IoT Edge** para adicionar o módulo de sensor de temperatura. Insira _tempSensor_ como o nome e `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview` como a URL da Imagem. Deixe as outras configurações inalteradas e clique em **Salvar**.

    ![módulo de temperatura][11]

1. Copie o nome do módulo do ASA. Clique em **Avançar** para configurar rotas.

1. Copie o seguinte em **Rotas**.  Substitua _{moduleName}_ pelo nome do módulo copiado:

    ```json
    {
        "routes": {                                                               
          "telemetryToCloud": "FROM /messages/modules/tempSensor/* INTO $upstream", 
          "alertsToCloud": "FROM /messages/modules/{moduleName}/* INTO $upstream", 
          "alertsToReset": "FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")", 
          "telemetryToAsa": "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/{moduleName}/inputs/temperature\")" 
        }
    }
    ```

1. Clique em **Avançar**.

1. Na etapa **Examinar Modelo**, clique em **Enviar**.

1. Volte para a página de detalhes do dispositivo e clique em **Atualizar**.  Você deverá ver o novo módulo _{moduleName}_ executado juntamente com o módulo **agente do IoT Edge** e o **hub do IoT Edge**.

    ![saída do módulo][7]

## <a name="view-data"></a>Exibir dados

Agora você pode acessar o dispositivo do IoT Edge para verificar a interação entre o módulo do ASA e o módulo tempSensor.

1. Em um prompt de comando, configure o tempo de execução com a cadeia de conexão do dispositivo do IoT Edge:

    ```cmd/sh
    iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords  
    ```

1. Execute o comando para iniciar o tempo de execução:

    ```cmd/sh
    iotedgectl start  
    ```

1. Execute o comando para ver os módulos em execução:

    ```cmd/sh
    docker ps  
    ```

    ![saída do Docker][8]

1. Execute o comando para ver todos os logs do sistema e dados de métricas. Use o nome do módulo acima:

    ```cmd/sh
    docker logs -f {moduleName}  
    ```

    ![log do Docker][9]

1. No portal do Azure, em sua conta de armazenamento, em **Serviço Blob**, clique em **Procurar blobs**, selecione seu contêiner e o arquivo JSON recém-criado.

1. Clique em **Baixar** e exiba os resultados.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou um contêiner do Armazenamento do Azure e um trabalho do Stream Analytics para analisar dados de seu dispositivo do IoT Edge.  Em seguida, você carregou um módulo personalizado do ASA para mover dados de seu dispositivo, por meio do fluxo, para um BLOB para download.  Continue com os outros tutoriais para saber mais sobre como o Azure IoT Edge pode criar soluções para seus negócios.

> [!div class="nextstepaction"] 
> [Implantar um modelo do Azure Machine Learning como um módulo][lnk-ml-tutorial]

<!-- Images. -->
[1]: ./media/tutorial-deploy-stream-analytics/storage.png
[2]: ./media/tutorial-deploy-stream-analytics/asa_input.png
[3]: ./media/tutorial-deploy-stream-analytics/asa_output.png
[4]: ./media/tutorial-deploy-stream-analytics/add_device.png
[5]: ./media/tutorial-deploy-stream-analytics/asa_job.png
[6]: ./media/tutorial-deploy-stream-analytics/set_module.png
[7]: ./media/tutorial-deploy-stream-analytics/module_output.png
[8]: ./media/tutorial-deploy-stream-analytics/docker_output.png
[9]: ./media/tutorial-deploy-stream-analytics/docker_log.png
[10]: ./media/tutorial-deploy-stream-analytics/storage_settings.png
[11]: ./media/tutorial-deploy-stream-analytics/temp_module.png


<!-- Links -->
[lnk-what-is-iot-edge]: what-is-iot-edge.md
[lnk-module-dev]: module-development.md
[iot-hub-get-started-create-hub]: ../../includes/iot-hub-get-started-create-hub.md
[azure-iot]: https://docs.microsoft.com/en-us/azure/iot-hub/
[azure-storage]: https://docs.microsoft.com/en-us/azure/storage/
[azure-stream]: https://docs.microsoft.com/en-us/azure/stream-analytics/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-module-tutorial]: tutorial-csharp-module.md
[lnk-ml-tutorial]: tutorial-deploy-machine-learning.md

[lnk-docker-windows]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-linux]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/
[lnk-python]: https://www.python.org/downloads/