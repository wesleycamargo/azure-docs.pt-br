---
title: Implantar o Azure Stream Analytics com o Azure IoT Edge | Microsoft Docs
description: "Implantar o Azure Stream Analytics como um módulo em um dispositivo de borda"
services: iot-edge
keywords: 
author: msebolt
manager: timlt
ms.author: v-masebo
ms.date: 11/28/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 5a143bbf7abb5304ac51782d517c02ec184a05a2
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/29/2017
---
# <a name="deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>Implantar o Azure Stream Analytics como um módulo do IoT Edge – versão prévia

Os dispositivos IoT podem gerar grandes quantidades de dados. Às vezes, esses dados precisam ser analisados ou processados antes de chegarem à nuvem, a fim de reduzir o tamanho dos dados carregados ou eliminar a latência da viagem de ida e volta de um insight acionável.

O IoT Edge aproveita os módulos pré-criados do IoT Edge do Serviço do Azure para implantação rápida e o ASA ([Azure Stream Analytics][azure-stream]) é um módulo desse tipo. Crie um trabalho do ASA em seu portal e, em seguida, acesse o portal do Hub IoT para implantá-lo como um Módulo do IoT Edge.  

O Azure Stream Analytics fornece uma sintaxe de consulta estruturada de modo sofisticado para a análise de dados na nuvem e em dispositivos do IoT Edge. Para obter mais informações sobre o ASA no IoT Edge, consulte a [documentação do ASA](../stream-analytics/stream-analytics-edge.md).

Este tutorial orienta você pela criação de um trabalho do Azure Stream Analytics e sua implantação em um dispositivo do IoT Edge, a fim de processar um fluxo de telemetria local diretamente no dispositivo e, em seguida, gerar alertas para conduzir uma ação imediata no dispositivo.  Há dois módulos envolvidos neste tutorial. Um módulo de sensor de temperatura simulada (tempSensor) gera dados de temperatura de 20 a 120 graus, incrementados em 1 a cada 5 segundos. O módulo do Stream Analytics redefine o tempSensor quando a média de 30 segundos atinge 70. Em um ambiente de produção, essa funcionalidade pode ser usada para desligar uma máquina ou tomar medidas preventivas quando a temperatura alcançar níveis perigosos. 

Você aprenderá como:

> [!div class="checklist"]
> * Criar um trabalho do ASA para processar dados no Edge
> * Conectar o novo trabalho do ASA com outros módulos do IoT Edge
> * Implantar o trabalho do ASA em um dispositivo do IoT Edge

## <a name="prerequisites"></a>Pré-requisitos

* Um Hub IoT 
* O dispositivo que você criou e configurou no guia de início rápido ou em Implantar o Azure IoT Edge em um dispositivo simulado no [Windows][lnk-tutorial1-win] e no [Linux][lnk-tutorial1-lin]. Você precisa conhecer a chave de conexão do dispositivo e a ID do dispositivo. 
* Docker em execução no seu dispositivo IoT Edge
    * [Instalar o Docker no Windows][lnk-docker-windows]
    * [Instalar o Docker no Linux][lnk-docker-linux]
* Python 2.7.x em seu dispositivo do IoT Edge
    * [Instale o Python 2.7 no Windows][lnk-python].
    * A maioria das distribuições Linux, incluindo o Ubuntu, já tem o Python 2.7 instalado.  Use o seguinte comando para verificar se o Pip está instalado: `sudo apt-get install python-pip`.


## <a name="create-an-asa-job"></a>Criar um trabalho do ASA

Nesta seção, você cria um trabalho do Azure Stream Analytics para extrair dados de seu hub IoT, consultar os dados telemétricos enviados de seu dispositivo e encaminhar os resultados para um Contêiner do Armazenamento do Azure (BLOB). Para obter mais informações, consulte a seção **Visão geral** da [Documentação do Stream Analytics][azure-stream]. 

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Uma conta de Armazenamento do Azure é necessária para fornecer um ponto de extremidade a ser usado como uma saída no trabalho do ASA. O exemplo abaixo usa o tipo de armazenamento de BLOBs.  Para obter mais informações, consulte a seção **Blobs** da [Documentação do Armazenamento do Azure][azure-storage].

1. No portal do Azure, navegue até **Criar um recurso** e insira `Storage account` na barra de pesquisa. Selecione **Conta de armazenamento - blob, arquivo, tabela, fila**.

2. Insira um nome para a conta de armazenamento e selecione o mesmo local em que o Hub IoT está localizado. Clique em **Criar**. Lembre-se do nome para usá-lo mais tarde.

    ![nova conta de armazenamento][1]

3. Navegue para a conta de armazenamento recém-criada. Clique em **Procurar blobs**. 
4. Crie um novo contêiner para o módulo do ASA para armazenar dados. Defina o nível de acesso como **Contêiner**. Clique em **OK**.

    ![configurações de armazenamento][10]

### <a name="create-a-stream-analytics-job"></a>Criar um trabalho de Stream Analytics

1. No portal do Azure, navegue para **Criar um recurso** > **Internet das Coisas** e selecione **Trabalho do Stream Analytics**.

2. Insira um nome, escolha **Edge** como o ambiente de Hospedagem e use os valores padrão restantes.  Clique em **Criar**.

    >[!NOTE]
    >Atualmente, os trabalhos do ASA no IoT Edge não têm suporte na região Oeste dos EUA 2. 

3. Vá para o trabalho criado. Selecione **Entradas** e então clique em **Adicionar**.

4. Para o alias de entrada, insira `temperature`, defina o tipo de origem como **Fluxo de dados** e use os padrões para os outros parâmetros. Clique em **Criar**.

   ![entrada do ASA](./media/tutorial-deploy-stream-analytics/asa_input.png)

5. Selecione **Saídas** e então clique em **Adicionar**.

6. Para o alias de saída, insira `alert` e use os padrões para os outros parâmetros. Clique em **Criar**.

   ![saída do ASA](./media/tutorial-deploy-stream-analytics/asa_output.png)


7. Selecione **Consulta**.
8. Substitua o texto padrão pela seguinte consulta:

    ```sql
    SELECT  
        'reset' AS command 
    INTO 
       alert 
    FROM 
       temperature TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second,30) 
    HAVING Avg(machine.temperature) > 70
    ```
9. Clique em **Salvar**.

## <a name="deploy-the-job"></a>Implantar o trabalho

Agora você está pronto para implantar o trabalho do ASA em seu dispositivo do IoT Edge.

1. No portal do Azure, em seu hub IoT, navegue até **IoT Edge (versão prévia)** e abra a página de detalhes para o dispositivo IoT Edge.
1. Selecione **Definir módulos**.
1. Se você tiver implantado o módulo tempSensor neste dispositivo, talvez ele seja automaticamente populado. Caso contrário, utilize as etapas a seguir para adicionar esse módulo:
   1. Clique em **Adicionar Módulo do IoT Edge**
   1. Insira `tempSensor` como o nome e `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview` para o URI da Imagem. 
   1. Deixe as outras configurações inalteradas e clique em **Salvar**.
1. Para adicionar seu trabalho ASA Edge, selecione **Importar Módulo IoT Edge do Azure Stream Analytics**.
1. Selecione a assinatura e o trabalho do ASA Edge criado. 
1. Selecione sua assinatura e a conta de armazenamento que você criou. Clique em **Salvar**.

    ![definir módulo][6]

1. Copie o nome que foi gerado automaticamente para o seu módulo ASA. 

    ![módulo de temperatura][11]

1. Clique em **Avançar** para configurar rotas.
1. Copie o seguinte em **Rotas**.  Substitua _{nomedoMódulo}_ pelo nome do módulo copiado:

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

1. Volte para a página de detalhes do dispositivo e clique em **Atualizar**.  Você deverá ver o novo módulo do Stream Analytics executado juntamente com o módulo **agente do IoT Edge** e o **hub do IoT Edge**.

    ![saída do módulo][7]

## <a name="view-data"></a>Exibir dados

Agora você pode acessar o dispositivo do IoT Edge para verificar a interação entre o módulo do ASA e o módulo tempSensor.

Verifique se todos os módulos estão em execução no Docker:

   ```cmd/sh
   docker ps  
   ```

   ![saída do Docker][8]

Ver todos os logs do sistema e os dados de métricas. Use o nome do módulo do Stream Analytics:

   ```cmd/sh
   docker logs -f {moduleName}  
   ```

Você poderá observar a temperatura da máquina aumentando gradualmente até atingir 70 graus por 30 segundos. Em seguida, o módulo do Stream Analytics dispara uma redefinição e a temperatura da máquina cair para 21. 

   ![log do Docker][9]


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou um contêiner do Armazenamento do Azure e um trabalho do Stream Analytics para analisar dados de seu dispositivo do IoT Edge.  Em seguida, você carregou um módulo personalizado do ASA para mover dados de seu dispositivo, por meio do fluxo, para um BLOB para download.  Continue com os outros tutoriais para saber mais sobre como o Azure IoT Edge pode criar soluções para seus negócios.

> [!div class="nextstepaction"] 
> [Implantar um modelo do Azure Machine Learning como um módulo][lnk-ml-tutorial]

<!-- Images. -->
[1]: ./media/tutorial-deploy-stream-analytics/storage.png
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