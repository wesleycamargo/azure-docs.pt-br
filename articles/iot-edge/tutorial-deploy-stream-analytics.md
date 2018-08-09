---
title: 'Tutorial: Implantar trabalhos ASA em dispositivos do Azure IoT Edge | Microsoft Docs'
description: Neste tutorial, você implantará o Azure Stream Analytics como um módulo em um dispositivo IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/25/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: afbdf2171c1fc1eef95514526a509d171e262d4a
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435675"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module-preview"></a>Tutorial: Implantar o Azure Stream Analytics como um módulo do IoT Edge (versão prévia)

Muitas soluções de IoT usam serviços de análise para obter insights sobre dados quando estes chegam na nuvem vindos de dispositivos de IoT. Com o Azure IoT Edge, você pode usar a lógica do [Azure Stream Analytics] [ azure-stream] e movê-la para o próprio dispositivo. Ao processar fluxos de telemetria na borda, você pode reduzir a quantidade de dados carregados e reduzir o tempo necessário para reagir a ideias acionáveis.

O Azure IoT Edge e o Azure Stream Analytics são integrados para que você possa criar um trabalho do Azure Stream Analytics no portal do Azure e implantá-lo como um módulo do IoT Edge sem nenhum código adicional.  

O Azure Stream Analytics fornece uma sintaxe de consulta estruturada de modo sofisticado para a análise de dados na nuvem e em dispositivos do IoT Edge. Para saber mais sobre o Azure Stream Analytics no IoT Edge, confira a [documentação do Azure Stream Analytics](../stream-analytics/stream-analytics-edge.md).

O módulo do Stream Analytics neste tutorial calcula a temperatura média em uma janela ininterrupta de 30 segundos. Quando essa média atinge 70, o módulo envia um alerta para o dispositivo tomar medidas. Nesse caso, a medida é redefinir o sensor de temperatura simulado. Em um ambiente de produção, você pode usar essa funcionalidade para desligar uma máquina ou tomar medidas preventivas quando a temperatura alcançar níveis perigosos. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um trabalho do Azure Stream Analytics para processar dados no edge.
> * Conectar o novo trabalho do Azure Stream Analytics a outros módulos do IoT Edge.
> * Implante o trabalho do Azure Stream Analytics em um dispositivo IoT Edge no portal do Azure.

>[!NOTE]
>Os módulos do Azure Stream Analytics para IoT Edge estão em [versão prévia pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo do Azure IoT Edge:

* Você pode usar seu computador de desenvolvimento ou uma máquina virtual como um dispositivo do Edge seguindo as etapas no início rápido para os [dispositivos Linux](quickstart-linux.md) ou [Windows](quickstart.md).
* O módulo de aprendizado de máquina do Azure não oferece suporte a processadores ARM.

Recursos de nuvem:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na camada padrão no Azure. 


## <a name="create-an-azure-stream-analytics-job"></a>Criar um trabalho do Azure Stream Analytics

Nesta seção, você cria um trabalho do Azure Stream Analytics para extrair dados de seu hub IoT, consultar os dados telemétricos enviados de seu dispositivo e encaminhar os resultados para um contêiner do Armazenamento do Azure. Para saber mais, confira a seção “Visão geral” da [Documentação do Stream Analytics][azure-stream]. 

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Uma conta de Armazenamento do Azure é necessária em trabalhos do Azure Stream Analytics, para atuar como um ponto de extremidade para a saída do trabalho. O exemplo nesta seção usa o tipo Armazenamento de blobs. Para saber mais, confira a seção “Blobs” da [Documentação do Armazenamento do Azure][azure-storage].

1. No portal do Azure, vá para **Criar um recurso**, insira **Conta de armazenamento** na caixa de pesquisa e selecione **Conta de armazenamento - blob, arquivo, tabela, fila**.

1. No painel **Criar conta de armazenamento**, insira um nome para sua conta de armazenamento, selecione o mesmo local em que o Hub IoT está armazenado, selecione o mesmo grupo de recursos do Hub IoT e, em seguida, selecione **Criar**. Anote o nome para usá-lo mais tarde.

    ![Criar uma conta de armazenamento][1]


### <a name="create-a-stream-analytics-job"></a>Criar um trabalho de Stream Analytics

1. No portal do Azure, vá para **Criar um recurso** > **Internet das Coisas** e selecione **Trabalho do Stream Analytics**.

1. No painel **Novo Trabalho do Stream Analytics**, faça o seguinte:

   1. Na caixa **Nome do trabalho**, digite um nome de trabalho.
   
   1. Use o mesmo **Grupo de recursos** e **Local** do hub IoT. 

      > [!NOTE]
      > Atualmente, os trabalhos do Azure Stream Analytics no IoT Edge não têm suporte na região Oeste dos EUA 2. 

   1. Em **Ambiente de hospedagem**, selecione **Edge**.
    
1. Selecione **Criar**.

1. No trabalho criado, em **Topologia do Trabalho**, abra **Entradas**.

   ![Entrada do Azure Stream Analytics](./media/tutorial-deploy-stream-analytics/asa_input.png)

1. Selecione **Adicionar entrada de fluxo** e, em seguida, **Hub do Edge**.

1. No painel **Nova entrada**, insira **temperatura** como o alias de entrada. 

1. Clique em **Salvar**.

1. Em **Topologia do Trabalho**, abra **Saídas**.

   ![Saída do Azure Stream Analytics](./media/tutorial-deploy-stream-analytics/asa_output.png)

1. Selecione **Adicionar** e, em seguida, **Hub do Edge**.

1. No painel **Nova saída**, insira **alerta** como o alias de saída. 

1. Clique em **Salvar**.

1. Em **Topologia de Trabalho**, selecione **Consulta**e substitua o texto padrão pela consulta abaixo que cria um alerta quando a temperatura média da máquina em uma janela de 30 segundos atinge 70 graus:

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

1. Clique em **Salvar**.

1. Em **Configurar**, selecione **Configurações do IoT Edge**.

1. Escolha a **conta de armazenamento** no menu suspenso.

1. No campo **Contêiner**, selecione **Criar novo** e forneça um nome para o contêiner de armazenamento. 

1. Clique em **Salvar**. 


## <a name="deploy-the-job"></a>Implantar o trabalho

Agora você está pronto para implantar o trabalho do Azure Stream Analytics em seu dispositivo do IoT Edge.

1. No portal do Azure, em seu hub IoT, vá para **IoT Edge** e abra a página de detalhes do dispositivo IoT Edge.

1. Selecione **Definir módulos**.  

   Se você implantou o módulo tempSensor neste dispositivo, talvez ele seja automaticamente preenchido. Se não existir, adicione o módulo fazendo o seguinte:

   1. Clique em **Adicionar** e selecione **Módulo do IoT Edge**.
   1. Como nome, digite **tempSensor**.
   1. Como URI da imagem, insira **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0**. 
   1. Deixe as outras configurações inalteradas.
   1. Clique em **Salvar**.

1. Adicione o trabalho de Edge do Azure Stream Analytics com as seguintes etapas:

   1. Clique em **Adicionar** e selecione **Módulo do Azure Stream Analytics**.
   1. Selecione sua assinatura e o trabalho de Edge do Azure Stream Analytics que você criou. 
   1. Clique em **Salvar**.

1. Selecione **Avançar**.

1. Substitua o valor padrão em **Rotas** pelo código a seguir. Atualize _{moduleName}_ com o nome do módulo do Azure Stream Analytics. O módulo deve ter o mesmo nome que o trabalho usado como base. 

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

1. Selecione **Avançar**.

1. Na etapa **Revisar Implantação**, selecione **Enviar**.

1. Volte para a página de detalhes do dispositivo e selecione **Atualizar**.  

    Você deverá ver o novo módulo do Stream Analytics executado juntamente com o módulo agente do IoT Edge e o hub do IoT Edge.

    ![Saída do módulo][7]

## <a name="view-data"></a>Exibir dados

Agora você pode acessar o dispositivo do IoT Edge para verificar a interação entre o módulo do Azure Stream Analytics e o módulo tempSensor.

1. Verifique se todos os módulos estão em execução no Docker:

   ```cmd/sh
   iotedge list  
   ```
<!--
   ![Docker output][8]
-->
1. Exibir todos os logs do sistema e dados de métricas. Use o nome do módulo do Stream Analytics:

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

Você poderá observar a temperatura da máquina aumentando gradualmente até atingir 70 graus por 30 segundos. Em seguida, o módulo do Stream Analytics dispara uma redefinição e a temperatura da máquina cai para 21. 

   ![Log do Docker][9]

## <a name="clean-up-resources"></a>Limpar recursos 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

Se você pretende continuar para o próximo artigo recomendado, pode manter os recursos e as configurações já criados e reutilizá-los.

Caso contrário, você pode excluir as configurações locais e os recursos do Azure criados neste artigo para evitar encargos. 

> [!IMPORTANT]
> A exclusão de recursos do Azure e do grupo de recursos é irreversível. Depois de excluídos, o grupo de recursos e todos os recursos contidos nele serão excluídos permanentemente. Não exclua acidentalmente o grupo de recursos ou os recursos incorretos. Caso tenha criado o Hub IoT dentro de um grupo de recursos existente que contém recursos que você deseja manter, exclua apenas o próprio recurso do Hub IoT em vez de excluir o grupo de recursos.
>

Para excluir apenas o Hub IoT, execute o comando abaixo usando seu nome de hub e do grupo de recursos:

```azurecli-interactive
az iot hub delete --name {hub_name} --resource-group IoTEdgeResources
```


Para excluir o grupo de recursos inteiro por nome:

1. Entre no [portal do Azure](https://portal.azure.com) e clique em **Grupos de recursos**.

1. Na caixa de texto **Filtrar por nome...**, digite o nome do grupo de recursos que contém seu Hub IoT. 

1. À direita do seu grupo de recursos, na lista de resultados, clique em **...**, depois em **Excluir grupo de recursos**.

<!--
   ![Delete](./media/iot-edge-quickstarts-clean-up-resources/iot-edge-delete-resource-group.png)
-->
1. Você receberá uma solicitação para confirmar a exclusão do grupo de recursos. Digite o nome do grupo de recursos novamente para confirmar e clique em **Excluir**. Após alguns instantes, o grupo de recursos, e todos os recursos contidos nele, serão excluídos.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou um trabalho do Azure Stream Analytics para analisar dados de seu dispositivo IoT Edge. Em seguida, carregou o módulo do Azure Stream Analytics em seu dispositivo IoT Edge para processar e reagir ao aumento de temperatura localmente, além de enviar o fluxo de dados agregados para a nuvem. Continue com os outros tutoriais para saber como o Azure IoT Edge pode criar outras soluções para seus negócios.

> [!div class="nextstepaction"] 
> [Implantar um modelo do Azure Machine Learning como um módulo][lnk-ml-tutorial]

<!-- Images. -->
[1]: ./media/tutorial-deploy-stream-analytics/storage.png
[4]: ./media/tutorial-deploy-stream-analytics/add_device.png
[5]: ./media/tutorial-deploy-stream-analytics/asa_job.png
[6]: ./media/tutorial-deploy-stream-analytics/set_module.png
[7]: ./media/tutorial-deploy-stream-analytics/module_output2.png
[8]: ./media/tutorial-deploy-stream-analytics/docker_output.png
[9]: ./media/tutorial-deploy-stream-analytics/docker_log.png
[10]: ./media/tutorial-deploy-stream-analytics/storage_settings.png
[11]: ./media/tutorial-deploy-stream-analytics/temp_module.png


<!-- Links -->
[lnk-what-is-iot-edge]: what-is-iot-edge.md
[lnk-module-dev]: module-development.md
[iot-hub-get-started-create-hub]: ../../includes/iot-hub-get-started-create-hub.md
[azure-iot]: https://docs.microsoft.com/azure/iot-hub/
[azure-storage]: https://docs.microsoft.com/azure/storage/
[azure-stream]: https://docs.microsoft.com/azure/stream-analytics/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-quickstart-win]: quickstart.md
[lnk-quickstart-lin]: quickstart-linux.md
[lnk-module-tutorial]: tutorial-csharp-module.md
[lnk-ml-tutorial]: tutorial-deploy-machine-learning.md

