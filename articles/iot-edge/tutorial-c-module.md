---
title: Tutorial para criar módulo personalizado do C – Azure IoT Edge | Microsoft Docs
description: Este tutorial mostra como criar um módulo do IoT Edge com código em C e implantá-lo em um dispositivo de borda
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 659d960881f143655e98c6f1d38696f44def3ae8
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54055091"
---
# <a name="tutorial-develop-a-c-iot-edge-module-and-deploy-to-your-simulated-device"></a>Tutorial: Desenvolver e implantar um módulo do IoT Edge em C em seu dispositivo simulado

Use os módulos do IoT Edge para implantar um código que implementa a lógica de negócios diretamente em seus dispositivos IoT Edge. Este tutorial o orienta através da criação e implantação de um módulo IoT Edge que filtra os dados do sensor. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Usar o Visual Studio Code para criar um módulo do IoT Edge em C
> * Usar o Visual Studio Code e o Docker para criar uma imagem do Docker e publicá-la em um registro de contêiner
> * Implantar o módulo no dispositivo IoT Edge
> * Exibir os dados gerados


O módulo IoT Edge que criado neste tutorial filtra os dados de temperatura gerados pelo seu dispositivo. Ele somente envia mensagens upstream se a temperatura estiver acima de um limite especificado. Este tipo de análise na borda é útil para reduzir a quantidade de dados comunicados e armazenados na nuvem.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo do Azure IoT Edge:

* Você pode usar seu computador de desenvolvimento ou uma máquina virtual como um dispositivo do Edge seguindo as etapas no início rápido para os [dispositivos Linux](quickstart-linux.md) ou [Windows](quickstart.md).
* Os módulos do C para o Azure IoT Edge não dão suporte a contêineres do Windows. Se seu dispositivo IoT Edge é um computador Windows, configure-o para [usar contêineres do Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)

Recursos de nuvem:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na camada padrão ou gratuito no Azure.

Recursos de desenvolvimento:

* [Visual Studio Code](https://code.visualstudio.com/).
* [Extensão do C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) para Visual Studio Code.
* [Ferramentas de IoT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) para Visual Studio Code.
* [CE do Docker](https://docs.docker.com/install/).

>[!Note]
>Os módulos do C para o Azure IoT Edge não dão suporte a contêineres do Windows.

## <a name="create-a-container-registry"></a>Criar um registro de contêiner

Neste tutorial, você usará as Ferramentas IoT do Azure para Visual Studio Code para criar um módulo e uma **imagem de contêiner** com base nos arquivos. Em seguida, você efetua push dessa imagem para um **registro** que armazena e gerencia suas imagens. Finalmente, você implanta a imagem do seu registro para executar no dispositivo IoT Edge.

Você pode usar qualquer registro compatível com o Docker para manter as imagens de contêiner. Dois serviços de registro populares do Docker são o [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) e o [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Este tutorial utiliza o Registro de Contêiner do Azure.

Caso ainda não tenha um registro de contêiner, siga estas etapas para criar um novo no Azure:

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Contêineres** > **Registro de Contêiner**.

2. Forneça os seguintes valores para criar o seu registro de contêiner:

   | Campo | Valor |
   | ----- | ----- |
   | Nome do registro | Forneça um nome exclusivo. |
   | Assinatura | Selecione uma assinatura na lista suspensa. |
   | Grupo de recursos | É recomendável que você use o mesmo grupo de recursos para todos os recursos de teste que foram criados durante os tutoriais e guias de início rápido do IoT Edge. Por exemplo, **IoTEdgeResources**. |
   | Local padrão | Escolha um local perto de você. |
   | Usuário administrador | Definido como **Habilitar**. |
   | SKU | Selecione **Basic**. |

5. Selecione **Criar**.

6. Depois que o registro de contêiner for criado, navegue até ele e escolha **Chaves de acesso**.

7. Copie os valores para **Servidor de logon**, **Nome de usuário** e **Senha**. É possível usar esses valores posteriormente no tutorial para fornecer acesso ao registro de contêiner.

## <a name="create-an-iot-edge-module-project"></a>Criar um projeto de módulo do IoT Edge
As etapas a seguir mostram como criar um projeto de módulo do IoT Edge baseado no .NET Core 2.0 usando o Visual Studio Code e as Ferramentas de IoT do Azure.

### <a name="create-a-new-solution"></a>Criar uma nova solução

Crie um modelo de solução de C que possa ser personalizado com seu próprio código.

1. Selecione **Exibir** > **Paleta de comandos** para abrir a paleta de comandos do VS Code.

2. Na paleta de comandos, digite e execute o comando **Azure: Entrar** e siga as instruções para entrar na conta do Azure. Se já tiver entrado, pode ignorar esta etapa.

3. Na paleta de comandos, digite e execute o comando **Azure IoT Edge: nova solução do IoT Edge**. Siga os prompts na paleta de comandos para criar sua solução.

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha o local no computador de desenvolvimento em que o VS Code criará os arquivos de solução. |
   | Fornecer um nome para a solução | Insira um nome descritivo para a solução ou aceite o padrão **EdgeSolution**. |
   | Selecionar modelo do módulo | Escolha **Módulo C**. |
   | Fornecer um nome de módulo | Nomeie o módulo **CModule**. |
   | Fornecer o repositório de imagem do Docker para o módulo | Um repositório de imagem inclui o nome do registro de contêiner e o nome da imagem de contêiner. Sua imagem de contêiner foi preenchida automaticamente na última etapa. Substitua **localhost:5000** pelo valor do servidor de logon do seu registro de contêiner do Azure. Você pode recuperar o servidor de logon da página Visão Geral do seu registro de contêiner no portal do Azure. A cadeia de caracteres final se parece com \<nome do registro\>.azurecr.io/cmodule. |
 
   ![Fornecer o repositório de imagem do Docker](./media/tutorial-c-module/repository.png)

A janela do VS Code carregará seu workspace da solução IoT Edge. O workspace da solução contém cinco componentes de nível superior. A pasta **módulos** contém o código C para o seu módulo, bem como Dockerfiles para a compilação de seu módulo como uma imagem de contêiner. O arquivo **\.env** armazena suas credenciais de registro de contêiner. O arquivo **deployment.template.json** contém as informações que o tempo de execução do IoT Edge usa para implantar módulos em um dispositivo. E o arquivo **deployment.debug.template.json** contém a versão de depuração de módulos. Você não editará a pasta **\.vscode** ou o arquivo **\.gitignore** neste tutorial.

Se você não especifica um registro de contêiner durante a criação de sua solução, mas aceita o valor de localhost:5000, você não terá um arquivo \.env.

<!--
   ![C solution workspace](./media/tutorial-c-module/workspace.png)
-->

### <a name="add-your-registry-credentials"></a>Adicionar suas credenciais de registro

O arquivo do ambiente armazena as credenciais para o registro de contêiner e as compartilha com o tempo de execução do IoT Edge. O tempo de execução precisa dessas credenciais para efetuar pull de imagens privadas para o dispositivo IoT Edge.

1. No explorador do VS Code, abra o arquivo .env.
2. Atualize os campos com os valores de **nome de usuário** e **senha** que você copiou do registro de contêiner do Azure.
3. Salve o arquivo.

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com código personalizado

Adicione código ao módulo C que permita ler dados do sensor, verifique se a temperatura da máquina relatada excedeu um limite de segurança e passe essas informações para o Hub IoT.

5. Os dados do sensor nesse cenário são fornecidos no formato JSON. Para filtrar as mensagens no formato JSON, importe uma biblioteca JSON para C. Este tutorial utiliza Parson.

   1. Baixe o [repositório do GitHub Parson](https://github.com/kgabis/parson). Copie os arquivos **parson.c** e **parson.h** para a pasta **CModule**.

   2. Abra **modules** > **CModule** > **CMakeLists.txt**. Na parte superior do arquivo, importe os arquivos Parson como uma biblioteca chamada **my_parson**.

      ```
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. Adicione **my_parson** à lista de bibliotecas na função **target_link_libraries** de CMakeLists.txt.

   4. Salve o arquivo **CMakeLists.txt**.

   5. Abra **modules** > **CModule** > **Main.c**. No final da lista onde são incluídas as instruções, adicione uma nova para incluir `parson.h` para suporte a JSON:

      ```c
      #include "parson.h"
      ```

6. No arquivo **main.c**, adicione uma variável global chamada `temperatureThreshold` após a seção de inclusão. Esta variável define o valor que a temperatura medida deve exceder para que os dados sejam enviados para o Hub IoT.

    ```c
    static double temperatureThreshold = 25;
    ```

7. Substitua toda a função `CreateMessageInstance` pelo seguinte código. Essa função alocará um contexto para o retorno de chamada.

    ```c
    static MESSAGE_INSTANCE* CreateMessageInstance(IOTHUB_MESSAGE_HANDLE message)
    {
        MESSAGE_INSTANCE* messageInstance = (MESSAGE_INSTANCE*)malloc(sizeof(MESSAGE_INSTANCE));
        if (NULL == messageInstance)
        {
            printf("Failed allocating 'MESSAGE_INSTANCE' for pipelined message\r\n");
        }
        else
        {
            memset(messageInstance, 0, sizeof(*messageInstance));

            if ((messageInstance->messageHandle = IoTHubMessage_Clone(message)) == NULL)
            {
                free(messageInstance);
                messageInstance = NULL;
            }
            else
            {
                messageInstance->messageTrackingId = messagesReceivedByInput1Queue;
                MAP_HANDLE propMap = IoTHubMessage_Properties(messageInstance->messageHandle);
                if (Map_AddOrUpdate(propMap, "MessageType", "Alert") != MAP_OK)
                {
                    printf("ERROR: Map_AddOrUpdate Failed!\r\n");
                }
            }
        }

        return messageInstance;
    }
    ```

8. Substitua toda a função `InputQueue1Callback` pelo seguinte código. Essa função implementa o filtro de mensagens real.

    ```c
    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) != IOTHUB_MESSAGE_OK)
        {
            messageBody = "<null>";
        }

        printf("Received Message [%zu]\r\n Data: [%s]\r\n",
                messagesReceivedByInput1Queue, messageBody);

        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;
        if (json_object_dotget_value(root_object, "machine.temperature") != NULL && (temperature = json_object_dotget_number(root_object, "machine.temperature")) > temperatureThreshold)
        {
            printf("Machine temperature %f exceeds threshold %f\r\n", temperature, temperatureThreshold);
            // This message should be sent to next stop in the pipeline, namely "output1".  What happens at "outpu1" is determined
            // by the configuration of the Edge routing table setup.
            MESSAGE_INSTANCE *messageInstance = CreateMessageInstance(message);
            if (NULL == messageInstance)
            {
                result = IOTHUBMESSAGE_ABANDONED;
            }
            else
            {
                printf("Sending message (%zu) to the next stage in pipeline\n", messagesReceivedByInput1Queue);

                clientResult = IoTHubModuleClient_LL_SendEventToOutputAsync(iotHubModuleClientHandle, messageInstance->messageHandle, "output1", SendConfirmationCallback, (void *)messageInstance);
                if (clientResult != IOTHUB_CLIENT_OK)
                {
                    IoTHubMessage_Destroy(messageInstance->messageHandle);
                    free(messageInstance);
                    printf("IoTHubModuleClient_LL_SendEventToOutputAsync failed on sending msg#=%zu, err=%d\n", messagesReceivedByInput1Queue, clientResult);
                    result = IOTHUBMESSAGE_ABANDONED;
                }
                else
                {
                    result = IOTHUBMESSAGE_ACCEPTED;
                }
            }
        }
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

9. Adicione uma função `moduleTwinCallback`. Este método recebe atualizações sobre as propriedades desejadas do módulo gêmeo e atualiza a variável **temperatureThreshold** para corresponder. Todos os módulos possuem seu próprio módulo gêmeo, o que permite configurar o código em execução dentro de um módulo diretamente da nuvem.

    ```c
    static void moduleTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
    {
        printf("\r\nTwin callback called with (state=%s, size=%zu):\r\n%s\r\n",
            ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
        JSON_Value *root_value = json_parse_string(payLoad);
        JSON_Object *root_object = json_value_get_object(root_value);
        if (json_object_dotget_value(root_object, "desired.TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_dotget_number(root_object, "desired.TemperatureThreshold");
        }
        if (json_object_get_value(root_object, "TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_get_number(root_object, "TemperatureThreshold");
        }
    }
    ```

10. Substitua a função `SetupCallbacksForModule` pelo seguinte código.

    ```c
    static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
    {
        int ret;

        if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
        {
            printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
            ret = __FAILURE__;
        }
        else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
        {
            printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
            ret = __FAILURE__;
        }
        else
        {
            ret = 0;
        }

        return ret;
    }
    ```

11. Salve o arquivo main.c.

12. No gerenciador do VS Code, abra o arquivo **deployment.template.json** no workspace da solução IoT Edge. Esse arquivo informa ao agente do IoT Edge quais módulos implantar, nesse caso, **tempSensor** e **CModule**, e informa ao hub do IoT Edge como rotear mensagens entre eles. A extensão do Visual Studio Code preenche automaticamente a maioria das informações necessárias no modelo de implantação. Mesmo assim, verifique se tudo está preciso para sua solução: 

   1. A plataforma padrão do IoT Edge é definida como **amd64** na barra de status do VS Code, o que significa que o **CModule** está definido com a versão amd64 do Linux da imagem. Altere a plataforma padrão na barra de status de **amd64** para **arm32v7** ou **windows-amd64** se essa for a arquitetura do seu dispositivo IoT Edge. 

      ![Atualizar a plataforma da imagem do módulo](./media/tutorial-c-module/image-platform.png)

   2. Verifique se o modelo tem o nome do módulo correto, não o nome padrão **SampleModule** alterado quando você criou a solução de IoT Edge.

   3. A seção **registryCredentials** armazena suas credenciais de registro do Docker, de modo que o agente do IoT Edge possa efetuar pull da imagem do módulo. Os pares de nome de usuário e senha reais são armazenados no arquivo .env que é ignorado pelo git. Adicione suas credenciais ao arquivo .env caso ainda não tenha feito isso.  

   4. Caso deseje saber mais sobre manifestos de implantação, confira [Saiba como implantar módulos e estabelecer rotas no IoT Edge](module-composition.md).

13. Adicione o módulo gêmeo CModule ao manifesto de implantação. Insira o seguinte conteúdo JSON na parte inferior da seção `moduleContent`, após o módulo gêmeo do `$edgeHub`:

    ```json
        "CModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

   ![Adicionar CModule gêmeo ao modelo de implantação](./media/tutorial-c-module/module-twin.png)

14. Salve o arquivo **deployment.template.json**.

## <a name="build-and-push-your-solution"></a>Compilar e enviar por push sua solução

Na seção anterior, você criou uma solução IoT Edge e adicionou um código a CModule que filtrará mensagens em que a temperatura relatada da máquina estiver dentro dos limites aceitáveis. Agora você precisa compilar a solução como uma imagem de contêiner e enviá-la por push para seu registro de contêiner.

1. Abra o terminal integrado do VS Code selecionando **Exibir** > **Terminal integrado**.

1. Entre no Docker inserindo o comando a seguir no terminal integrado do Visual Studio Code. Você precisa entrar com suas credenciais de Registro de Contêiner do Azure para que você possa enviar sua imagem de módulo para o registro.
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Use o nome de usuário, a senha e o servidor de logon que você copiou de seu Registro de Contêiner do Azure na primeira seção. Ou recupere-as novamente da seção **Chaves de acesso** do registro no portal do Azure.

2. No explorador do VS Code, clique com o botão direito do mouse no arquivo **deployment.template.json** e selecione **Compilar e enviar por push solução IoT Edge**.

Quando você solicitar ao Visual Studio Code para compilar sua solução, primeiro ele gerará um arquivo `deployment.json` em uma nova pasta **config**. As informações para o arquivo deployment.json são coletadas do arquivo de modelo que você atualizou, do arquivo .env que você usou para armazenar suas credenciais de registro de contêiner e do arquivo module.json na pasta CModule.

Em seguida, o Visual Studio Code executará dois comandos no terminal integrado: `docker build` e `docker push`. Esses dois comandos compilam o código, conteinerizam `CModule.dll` e enviam por push para o registro de contêiner que você especificou ao inicializar a solução.

Você pode conferir o endereço de imagem de contêiner completo com marca no terminal integrado do VS Code. O endereço da imagem é criado de informações do arquivo `module.json` com o formato **\<repositório\>:\<versão\>-\<plataforma\>**. Para este tutorial, ele deve ser parecido com **myregistry.azurecr.io/cmodule:0.0.1-amd64**.

## <a name="deploy-and-run-the-solution"></a>Implantar e executar a solução

O artigo de início rápido que você usou para configurar seu dispositivo do IoT Edge, você implantou um módulo usando o portal do Azure. Você também pode implantar módulos usando a extensão do Kit de Ferramentas do Hub IoT do Azure (anteriormente conhecida como extensão do Kit de Ferramentas do Azure IoT) para Visual Studio Code. Você já tem um manifesto de implantação preparado para o seu cenário, o arquivo **deployment.json**. Agora, tudo o que você precisa fazer é selecionar um dispositivo para receber a implantação.

1. Na paleta de comandos do VS Code, execute **Hub IoT do Azure: selecionar Hub IoT**.

2. Escolha a assinatura e o hub IoT que contêm o dispositivo do IoT Edge que você deseja configurar.

3. No explorador do VS Code, expanda a seção **Dispositivos do Hub IoT do Azure**.

4. Clique com o botão direito no nome do seu dispositivo IoT Edge e selecione **Criar Implantação para Dispositivo Único**.

   ![Criar implantação para dispositivo único](./media/tutorial-c-module/create-deployment.png)

5. Selecione o arquivo **deployment.json** na pasta **config** e clique em **Selecionar Manifesto de Implantação do Edge**. Não use o arquivo deployment.template.json.

6. Clique no botão Atualizar. Você deve ver o novo **CModule** sendo executado junto com o módulo **TempSensor** em **$edgeAgent** e **$edgeHub**.

## <a name="view-generated-data"></a>Exibir os dados gerados

Depois que você aplica o manifesto de implantação no seu dispositivo IoT Edge, o tempo de execução do IoT Edge no dispositivo coleta as novas informações de implantação e inicia a execução nele. Todos os módulos em execução no dispositivo que não estão incluídos no manifesto de implantação são interrompidos. Todos os módulos ausentes do dispositivo são iniciados.

Você pode ver o status do seu dispositivo IoT Edge usando a seção **Dispositivos de Hub IoT do Azure** do explorer do Visual Studio Code. Expanda os detalhes do seu dispositivo para ver uma lista de módulos implantados e em execução.

No próprio dispositivo IoT Edge você pode ver o status dos seus módulos de implantação usando o comando `iotedge list`. Você deverá ver quatro módulos: os dois módulos de tempo de execução do IoT Edge, tempSensor e o módulo personalizado que você criou neste tutorial. Pode levar alguns minutos para que todos os módulos iniciem, portanto, se você não vir todos inicialmente, execute novamente o comando.

Para exibir as mensagens que estão sendo geradas por qualquer módulo, use o comando `iotedge logs <module name>`.

Você pode exibir as mensagens que chegam ao Hub IoT usando o Visual Studio Code.

1. Para monitorar dados que chegam ao hub IoT, clique em **...** e selecione **Iniciar Monitoramento de Mensagens D2C**.
2. Para monitorar a mensagem D2C para um dispositivo específico, clique com o botão direito do mouse no dispositivo na lista e selecione **Iniciar Monitoramento de Mensagens D2C**.
3. Para interromper o monitoramento de dados, execute o comando **Hub IoT do Azure: Parar de monitorar mensagem de D2C** na paleta de comandos.
4. Para exibir ou atualizar módulos gêmeos, clique com o botão direito do mouse no módulo na lista e selecione **Editar módulo gêmeo**. Para atualizar o módulo gêmeo, salve o arquivo JSON gêmeo, clique com o botão direito do mouse na área do editor e selecione **Atualizar Módulo Gêmeo**.
5. Para exibir logs do Docker, você pode instalar o [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) para VS Code e localizar os módulos de execução localmente no explorador do Docker. No menu de contexto, clique em **Mostrar Logs** para exibi-los no terminal integrado.

## <a name="clean-up-resources"></a>Limpar recursos

Se você pretende continuar no próximo artigo recomendado, pode manter os recursos e as configurações já criados e reutilizá-los. Você também pode continuar usando o mesmo dispositivo IoT Edge como um dispositivo de teste.

Caso contrário, você pode excluir as configurações locais e os recursos do Azure criados neste artigo para evitar encargos.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um módulo do IoT Edge que contém código para filtrar os dados brutos gerados pelo seu dispositivo IoT Edge. Quando estiver pronto para criar seus próprios módulos, você poderá saber mais sobre como [Desenvolver um módulo em C com o Azure IoT Edge para Visual Studio Code](how-to-develop-c-module.md). Você pode seguir para os próximos tutoriais a fim de descobrir outras formas pelas quais o Azure IoT Edge pode ajudá-lo a transformar dados em informações de negócios na borda.

> [!div class="nextstepaction"]
> [Armazenar dados na borda com os bancos de dados do SQL Server](tutorial-store-data-sql-server.md)

