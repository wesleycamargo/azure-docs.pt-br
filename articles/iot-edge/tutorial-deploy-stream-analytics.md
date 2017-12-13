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
ms.openlocfilehash: 6cf8e2469a6fe6bac0db6caf9acb182a6349096f
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2017
---
# <a name="deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>Implantar o Azure Stream Analytics como um módulo do IoT Edge – versão prévia

Os dispositivos IoT podem gerar grandes quantidades de dados. Para reduzir a quantidade de dados carregados ou para eliminar a latência de ida e volta de uma ideia acionável, os dados às vezes precisam ser analisados ou processados antes de alcançarem a nuvem.

O Azure IoT Edge tira proveito dos módulos do IoT Edge de serviço do Azure pré-criados para implantação rápida. [O Azure Stream Analytics][azure-stream] é um desses módulos. Você pode criar um trabalho do Azure Stream Analytics no portal e ir para o portal do Hub IoT do Azure a fim de implantá-lo como um módulo do IoT Edge. 

O Azure Stream Analytics fornece uma sintaxe de consulta estruturada de modo sofisticado para a análise de dados na nuvem e em dispositivos do IoT Edge. Para saber mais sobre o Azure Stream Analytics no IoT Edge, confira a [documentação do Azure Stream Analytics](../stream-analytics/stream-analytics-edge.md).

Este tutorial orienta você pela criação de um trabalho do Azure Stream Analytics e sua implantação em um dispositivo do IoT Edge. Isso permite processar um fluxo de telemetria local diretamente no dispositivo e gerar alertas que geram uma ação imediata no dispositivo. 

O tutorial apresenta dois módulos: 
* Um módulo de sensor de temperatura simulada (tempSensor) que gera dados de temperatura de 20 a 120 graus, incrementados em 1 a cada 5 segundos. 
* Um módulo do Stream Analytics que redefine o tempSensor quando a média de 30 segundos atinge 70. Em um ambiente de produção, você pode usar essa funcionalidade para desligar uma máquina ou tomar medidas preventivas quando a temperatura alcançar níveis perigosos. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um trabalho do Azure Stream Analytics para processar dados no edge.
> * Conectar o novo trabalho do Azure Stream Analytics a outros módulos do IoT Edge.
> * Implantar o trabalho do Azure Stream Analytics em um dispositivo do IoT Edge.

## <a name="prerequisites"></a>Pré-requisitos

* Um hub IoT. 
* O dispositivo que você criou e configurou no guia de início rápido ou nos artigos sobre como implantar o Azure IoT Edge em um dispositivo simulado no [Windows][lnk-tutorial1-win] ou no [Linux][lnk-tutorial1-lin]. Você precisa conhecer a chave de conexão do dispositivo e a ID do dispositivo. 
* Docker em execução no seu dispositivo IoT Edge.
    * [Instalar o Docker no Windows][lnk-docker-windows].
    * [Instalar o Docker no Linux][lnk-docker-linux].
* Python 2.7.x em seu dispositivo do IoT Edge.
    * [Instale o Python 2.7 no Windows][lnk-python].
    * A maioria das distribuições Linux, incluindo o Ubuntu, já tem o Python 2.7 instalado. Para verificar se o pip está instalado, use o seguinte comando: `sudo apt-get install python-pip`.

## <a name="create-an-azure-stream-analytics-job"></a>Criar um trabalho do Azure Stream Analytics

Nesta seção, você cria um trabalho do Azure Stream Analytics para extrair dados de seu hub IoT, consultar os dados telemétricos enviados de seu dispositivo e encaminhar os resultados para um contêiner do Armazenamento do Azure. Para saber mais, confira a seção “Visão geral” da [Documentação do Stream Analytics][azure-stream]. 

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Uma conta de Armazenamento do Azure é necessária para fornecer um ponto de extremidade a ser usado como uma saída no trabalho do Azure Stream Analytics. O exemplo nesta seção usa o tipo Armazenamento de blobs. Para saber mais, confira a seção “Blobs” da [Documentação do Armazenamento do Azure][azure-storage].

1. No portal do Azure, vá para **Criar um recurso**, insira **Conta de armazenamento** na caixa de pesquisa e selecione **Conta de armazenamento - blob, arquivo, tabela, fila**.

2. No painel **Criar conta de armazenamento**, digite um nome para sua conta de armazenamento, selecione o mesmo local em que o hub IoT está armazenado e selecione **Criar**. Anote o nome para usá-lo mais tarde.

    ![Criar uma conta de armazenamento][1]

3. Vá para a conta de armazenamento que você acabou de criar e selecione **Procurar blobs**. 

4. Crie um novo contêiner onde o módulo do Stream Analytics possa armazenar os dados, defina o nível de acesso como **Contêiner**e selecione **OK**.

    ![Configurações de armazenamento][10]

### <a name="create-a-stream-analytics-job"></a>Criar um trabalho de Stream Analytics

1. No portal do Azure, vá para **Criar um recurso** > **Internet das Coisas** e selecione **Trabalho do Stream Analytics**.

2. No painel **Novo Trabalho do Stream Analytics**, faça o seguinte:

    a. Na caixa **Nome do trabalho**, digite um nome de trabalho.
    
    b. Em **Ambiente de hospedagem**, selecione **Edge**.
    
    c. Nos campos restantes, use os valores padrão.

    > [!NOTE]
    > Atualmente, os trabalhos do Azure Stream Analytics no IoT Edge não têm suporte na região Oeste dos EUA 2. 

3. Selecione **Criar**.

4. No trabalho criado, em **Topologia do Trabalho**, selecione **Entradas** e selecione **Adicionar**.

5. No painel **Nova entrada**, faça o seguinte:

    a. Em **Alias de entrada**, digite **temperatura**.
    
    b. Na caixa **Tipo de Fonte**, selecione **Fluxo de dados**.
    
    c. Nos campos restantes, use os valores padrão.

   ![Entrada do Azure Stream Analytics](./media/tutorial-deploy-stream-analytics/asa_input.png)

6. Selecione **Criar**.

7. Em **Topologia do Trabalho**, selecione **Saídas** e selecione **Adicionar**.

8. No painel **Nova saída**, faça o seguinte:

    a. Na caixa **Alias de saída**, digite **alerta**.
    
    b. Nos campos restantes, use os valores padrão. 
    
    c. Selecione **Criar**.

   ![Saída do Azure Stream Analytics](./media/tutorial-deploy-stream-analytics/asa_output.png)


9. Em **Topologia do Trabalho**, selecione **Consulta**e substitua o texto padrão pela seguinte consulta:

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

10. Selecione **Salvar**.

## <a name="deploy-the-job"></a>Implantar o trabalho

Agora você está pronto para implantar o trabalho do Azure Stream Analytics em seu dispositivo do IoT Edge.

1. No portal do Azure, em seu hub IoT, vá para **IoT Edge (versão prévia)** e abra a página de detalhes para o dispositivo IoT Edge.

2. Selecione **Definir módulos**.  
    Se você implantou o módulo tempSensor neste dispositivo, talvez ele seja automaticamente preenchido. Se não existir, adicione o módulo fazendo o seguinte:

   a. Selecione **Adicionar Módulo do IoT Edge**.

   b. Como nome, digite **tempSensor**.
    
   c. Como URI da imagem, insira **microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview**. 

   d. Deixe as outras configurações inalteradas.
   
   e. Selecione **Salvar**.

3. Para adicionar seu trabalho Azure Stream Analytics Edge, selecione **Importar Módulo IoT Edge do Azure Stream Analytics**.

4. Selecione sua assinatura e o trabalho de Edge do Azure Stream Analytics que você criou. 

5. Selecione sua assinatura e a conta de armazenamento que você criou; em seguida, selecione **Salvar**.

    ![Definir módulo][6]

6. Copie o nome que foi gerado automaticamente para o módulo do Azure Stream Analytics. 

    ![Módulo de temperatura][11]

7. Para configurar rotas, selecione **Avançar**.

8. Copie o código a seguir em **Rotas**. Substitua _{nomedoMódulo}_ pelo nome do módulo copiado:

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

9. Selecione **Avançar**.

10. Na etapa **Revisar Modelo**, selecione **Enviar**.

11. Volte para a página de detalhes do dispositivo e selecione **Atualizar**.  
    Você deverá ver o novo módulo do Stream Analytics executado juntamente com o módulo agente do IoT Edge e o hub do IoT Edge.

    ![Saída do módulo][7]

## <a name="view-data"></a>Exibir dados

Agora você pode acessar o dispositivo do IoT Edge para verificar a interação entre o módulo do Azure Stream Analytics e o módulo tempSensor.

1. Verifique se todos os módulos estão em execução no Docker:

   ```cmd/sh
   docker ps  
   ```

   ![Saída do Docker][8]

2. Exibir todos os logs do sistema e dados de métricas. Use o nome do módulo do Stream Analytics:

   ```cmd/sh
   docker logs -f {moduleName}  
   ```

Você poderá observar a temperatura da máquina aumentando gradualmente até atingir 70 graus por 30 segundos. Em seguida, o módulo do Stream Analytics dispara uma redefinição e a temperatura da máquina cai para 21. 

   ![Log do Docker][9]


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou um contêiner de armazenamento do Azure e um trabalho do Stream Analytics para analisar dados de seu dispositivo do IoT Edge. Em seguida, você carregou um módulo personalizado do Azure Stream Analytics a fim de mover dados de seu dispositivo, por meio do fluxo, para um blob para download. Continue com os outros tutoriais para saber como o Azure IoT Edge pode criar outras soluções para seus negócios.

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
