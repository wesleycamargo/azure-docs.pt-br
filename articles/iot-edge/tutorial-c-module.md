---
title: Tutorial de C do Azure IoT Edge | Microsoft Docs
description: Este tutorial mostra como criar um módulo do IoT Edge com código em C e implantá-lo em um dispositivo de borda
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 09/21/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: e5c6b523a098bef4bb40ccd924750cc8aefd0e87
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51567359"
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
* [Extensão do Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) para Visual Studio Code.
* [CE do Docker](https://docs.docker.com/install/). 

>[!Note]
>Os módulos do C para o Azure IoT Edge não dão suporte a contêineres do Windows. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-a-container-registry"></a>Criar um registro de contêiner
Neste tutorial, você utiliza a extensão do Azure IoT Edge do Visual Studio Code para compilar um módulo e criar uma **imagem de contêiner** dos arquivos. Em seguida, você efetua push dessa imagem para um **registro** que armazena e gerencia suas imagens. Finalmente, você implanta a imagem do seu registro para executar no dispositivo IoT Edge.  

Você pode usar qualquer registro compatível com o Docker neste tutorial. Dois serviços de registro do Docker populares disponíveis na nuvem são o [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) e o [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Este tutorial utiliza o Registro de Contêiner do Azure. 

O comando da CLI do Azure a seguir cria um registro em um grupo de recursos chamado **IoTEdgeResources**. Substitua **{acr_name}** por um nome exclusivo para o registro. 

   ```azurecli-interactive
   az acr create --resource-group IoTEdgeResources --name {acr_name} --sku Basic --admin-enabled true
   ```

Recupere as credenciais para o registro. 

   ```azurecli-interactive
   az acr credential show --name {acr_name}
   ```

Copie os valores de **Nome de usuário** e uma das senhas. Você usará esses valores no tutorial posteriormente ao publicar a imagem do Docker no registro e ao adicionar as credenciais de registro para o tempo de execução do Edge. 

## <a name="create-an-iot-edge-module-project"></a>Criar um projeto de módulo do IoT Edge
As etapas a seguir mostram como criar um projeto de módulo do IoT Edge baseado no .NET Core 2.0 usando o Visual Studio Code e a extensão do Azure IoT Edge.

### <a name="create-a-new-solution"></a>Criar uma nova solução

Crie um modelo de solução de C que possa ser personalizado com seu próprio código. 

1. Selecione **Exibir** > **Paleta de comandos** para abrir a paleta de comandos do VS Code. 

2. Na paleta de comandos, digite e execute o comando **Azure: Entrar** e siga as instruções para entrar em sua conta do Azure. Se já tiver entrado, pode ignorar esta etapa.

3. Na paleta de comandos, digite e execute o comando **Azure IoT Edge: nova solução do IoT Edge**. Na paleta de comandos, forneça as seguintes informações para criar sua solução: 

   1. Selecione a pasta na qual deseja criar a solução. 
   2. Forneça um nome para a solução ou aceite o padrão **EdgeSolution**.
   3. Escolha **Módulo C** como o modelo de módulo. 
   4. Nomeie o módulo **CModule**. 
   5. Especifique o Registro de Contêiner do Azure que você criou na seção anterior como o repositório de imagens do primeiro módulo. Substitua **localhost:5000/carros** por **\<nome do registro\>.azurecr.io**. Substitua somente a parte de localhost da cadeia de caracteres, não exclua o nome do módulo. 

   ![Fornecer o repositório de imagem do Docker](./media/tutorial-c-module/repository.png)

A janela do VS Code carregará seu workspace da solução IoT Edge. O workspace da solução contém cinco componentes de nível superior. Você não editará a pasta **\.vscode** ou o arquivo **\.gitignore** neste tutorial. A pasta **módulos** contém o código C para o seu módulo, bem como Dockerfiles para a compilação de seu módulo como uma imagem de contêiner. O arquivo **\.env** armazena suas credenciais de registro de contêiner. O arquivo **deployment.template.json** contém as informações que o tempo de execução do IoT Edge usa para implantar módulos em um dispositivo. 

Se você não especifica um registro de contêiner durante a criação de sua solução, mas aceita o valor de localhost:5000, você não terá um arquivo \.env. 

   ![Workspace da solução C](./media/tutorial-c-module/workspace.png)

### <a name="add-your-registry-credentials"></a>Adicionar suas credenciais de registro

O arquivo do ambiente armazena as credenciais para o registro de contêiner e as compartilha com o tempo de execução do IoT Edge. O tempo de execução precisa dessas credenciais para efetuar pull de imagens privadas para o dispositivo IoT Edge. 

1. No explorador do VS Code, abra o arquivo .env. 
2. Atualize os campos com os valores de **nome de usuário** e **senha** que você copiou do registro de contêiner do Azure. 
3. Salve o arquivo. 

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com código personalizado

Adicione código ao módulo C que permita ler dados do sensor, verifique se a temperatura da máquina relatada excedeu um limite de segurança e passe essas informações para o Hub IoT. 

5. Os dados do sensor nesse cenário são fornecidos no formato JSON. Para filtrar as mensagens no formato JSON, importe uma biblioteca JSON para C. Este tutorial utiliza Parson.

   1. Baixe o [repositório Github Parson](https://github.com/kgabis/parson). Copie os arquivos **parson.c** e **parson.h** para a pasta **CModule**.

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

11. Salve o arquivo **main.c**.

## <a name="build-your-iot-edge-solution"></a>Criar solução IoT Edge

Na seção anterior, você criou uma solução IoT Edge e adicionou um código a CModule que filtrará mensagens em que a temperatura relatada da máquina estiver dentro dos limites aceitáveis. Agora você precisa compilar a solução como uma imagem de contêiner e enviá-la por push para seu registro de contêiner. 

1. Abra o terminal integrado do VS Code selecionando **Exibir** > **Terminal integrado**. 

1. Entre no Docker inserindo o comando a seguir no terminal integrado do Visual Studio Code. Você precisa entrar com suas credenciais de Registro de Contêiner do Azure para que você possa enviar sua imagem de módulo para o registro. 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Use o nome de usuário, a senha e o servidor de logon que você copiou de seu Registro de Contêiner do Azure na primeira seção. Ou recupere-as novamente da seção **Chaves de acesso** do registro no portal do Azure.

2. No gerenciador do VS Code, abra o arquivo **deployment.template.json** no workspace da solução IoT Edge. Esse arquivo manda o `$edgeAgent` implantar dois módulos: **tempSensor** e **CModule**. O valor `CModule.image` é definido como uma versão Linux amd64 da imagem. Para saber mais sobre manifestos de implantação, consulte [Entender como os módulos do IoT Edge podem ser utilizados, configurados e reutilizados](module-composition.md).

4. Adicione o módulo gêmeo CModule ao manifesto de implantação. Insira o seguinte conteúdo JSON na parte inferior da seção `moduleContent`, após o módulo gêmeo do `$edgeHub`: 

    ```json
        "CModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

   ![Adicionar CModule gêmeo ao modelo de implantação](./media/tutorial-c-module/module-twin.png)

4. Salve o arquivo **deployment.template.json**.
5. No explorador do VS Code, clique com o botão direito do mouse no arquivo **deployment.template.json** e selecione **Compilar e enviar por push solução IoT Edge**. 

Quando você solicitar ao Visual Studio Code para compilar sua solução, primeiro ele gerará um arquivo `deployment.json` em uma nova pasta **config**. As informações para o arquivo deployment.json são coletadas do arquivo de modelo que você atualizou, do arquivo .env que você usou para armazenar suas credenciais de registro de contêiner e do arquivo module.json na pasta CModule. 

Em seguida, o Visual Studio Code executará dois comandos no terminal integrado: `docker build` e `docker push`. Esses dois comandos compilam o código, conteinerizam `CModule.dll` e enviam por push para o registro de contêiner que você especificou ao inicializar a solução. 

Você pode conferir o endereço de imagem de contêiner completo com marca no terminal integrado do VS Code. O endereço da imagem é criado de informações do arquivo `module.json` com o formato **\<repositório\>:\<versão\>-\<plataforma\>**. Para este tutorial, ele deve ser parecido com **myregistry.azurecr.io/cmodule:0.0.1-amd64**.

## <a name="deploy-and-run-the-solution"></a>Implantar e executar a solução

O artigo de início rápido que você usou para configurar seu dispositivo do IoT Edge, você implantou um módulo usando o portal do Azure. Também é possível implantar módulos usando a extensão Kit de ferramentas do Azure IoT para Visual Studio Code. Você já tem um manifesto de implantação preparado para o seu cenário, o arquivo **deployment.json**. Agora, tudo o que você precisa fazer é selecionar um dispositivo para receber a implantação.

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
3. Para interromper o monitoramento de dados, execute o comando **Hub IoT do Azure: Parar o monitoramento de mensagens D2C** na paleta de comandos. 
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

