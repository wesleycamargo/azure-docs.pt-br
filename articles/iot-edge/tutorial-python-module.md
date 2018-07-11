---
title: Tutorial de Python do Azure IoT Edge | Microsoft Docs
description: Este tutorial mostra como criar um módulo do IoT Edge com código em Python e implantá-lo em um dispositivo de borda
services: iot-edge
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/26/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 42af2b5ec6b591929f37afebe6546d61b8a3a02a
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2018
ms.locfileid: "37082841"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-to-your-simulated-device"></a>Tutorial: Desenvolver e implantar um módulo do IoT Edge em Python em seu dispositivo simulado

Use os módulos do IoT Edge para implantar um código que implementa a lógica de negócios diretamente em seus dispositivos IoT Edge. Este tutorial o orienta através da criação e implantação de um módulo IoT Edge que filtra os dados do sensor. Você utilizará o dispositivo IoT Edge simulado que foi criado em Implantar Azure IoT Edge em um dispositivo simulado nos inícios rápidos de [Windows][lnk-quickstart-win] ou [Linux][lnk-quickstart-lin]. Neste tutorial, você aprenderá como:    

> [!div class="checklist"]
> * Use o Visual Studio Code para criar um módulo do IoT Edge Python
> * Usar o Visual Studio Code e o Docker para criar uma imagem do Docker e publicá-la no registro 
> * Implantar o módulo no dispositivo IoT Edge
> * Exibir os dados gerados


O módulo IoT Edge que criado neste tutorial filtra os dados de temperatura gerados pelo seu dispositivo. Ele somente envia mensagens upstream se a temperatura estiver acima de um limite especificado. Este tipo de análise na borda é útil para reduzir a quantidade de dados comunicados e armazenados na nuvem. 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

* O dispositivo do Azure IoT Edge criado no início rápido para [Linux](quickstart-linux.md).

   >[!Note]
   >Os módulos do Python para o Azure IoT Edge não oferecem suporte para Windows ou dispositivos ARM. 

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Extensão do Azure IoT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Extensão do Python para o Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python). 
* [Docker](https://docs.docker.com/engine/installation/) no mesmo computador com o Visual Studio Code. A CE (Community Edition) é suficiente para este tutorial. 
* [Python](https://www.python.org/downloads/).
* [PIP](https://pip.pypa.io/en/stable/installing/#installation) para instalar os pacotes do Python (normalmente incluídos com a instalação do Python).

## <a name="create-a-container-registry"></a>Criar um registro de contêiner
Neste tutorial, você utiliza a extensão do Azure IoT Edge do Visual Studio Code para compilar um módulo e criar uma **imagem de contêiner** dos arquivos. Em seguida, você efetua push dessa imagem para um **registro** que armazena e gerencia suas imagens. Finalmente, você implanta a imagem do seu registro para executar no dispositivo IoT Edge.  

Você pode usar qualquer registro compatível com o Docker neste tutorial. Dois serviços de registro do Docker populares disponíveis na nuvem são o [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) e o [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Este tutorial utiliza o Registro de Contêiner do Azure. 

1. No [Portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Contêineres** > **Registro de Contêiner do Azure** .
2. Nomeie seu registro, escolha uma assinatura, selecione um grupo de recursos e defina a SKU para **Básica**. 
3. Selecione **Criar**.
4. Depois que o registro de contêiner for criado, navegue até ele e selecione **Chaves de acesso**. 
5. Alterne **Usuário administrador** para **Ativar**.
6. Copie os valores para **Servidor de logon**, **Nome de usuário** e **Senha**. Você usará esses valores mais tarde no tutorial. 

## <a name="create-an-iot-edge-module-project"></a>Criar um projeto de módulo do IoT Edge
As etapas a seguir mostram como criar uma função do IoT Edge Python usando o Visual Studio Code e a extensão do Azure IoT Edge.

### <a name="create-a-new-solution"></a>Criar uma nova solução

Use o pacote do Python **cookiecutter** para criar um modelo de solução em Python que servirá de base. 

1. No Visual Studio Code, selecione **Exibir** > **Terminal Integrado** para abrir o terminal integrado do Visual Studio Code.

2. No terminal integrado, digite o seguinte comando para instalar (ou atualizar) **cookiecutter**, que você pode usar para criar o modelo de solução Edge no VS Code:

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```

3. Selecione **Exibir** > **Paleta de comandos** para abrir a paleta de comandos do VS Code. 

4. Na paleta de comandos, digite e execute o comando **Azure: Entrar** e siga as instruções para entrar em sua conta do Azure. Se já tiver entrado, pode ignorar esta etapa.

5. Na paleta de comandos, digite e execute o comando **Azure IoT Edge: nova solução do IoT Edge**. Na paleta de comandos, forneça as seguintes informações para criar sua solução: 

   1. Selecione a pasta na qual deseja criar a solução. 
   2. Forneça um nome para a solução ou aceite o padrão **EdgeSolution**.
   3. Escolha **Módulo Python** como o modelo de módulo. 
   4. Nomeie seu módulo **PythonModule**. 
   5. Especifique o Registro de Contêiner do Azure que você criou na seção anterior como o repositório de imagens do primeiro módulo. Substitua **localhost:5000** pelo valor de servidor de logon que você copiou. A cadeia de caracteres final se parece com **\<nome do registro\>.azurecr.io/pythonmodule**.
 
A janela do VS Code carregará seu espaço de trabalho da solução IoT Edge. Há uma pasta **modules**, um arquivo de modelo manifesto de implantação e um arquivo **.env**. 

### <a name="add-your-registry-credentials"></a>Adicionar suas credenciais de registro

O arquivo do ambiente armazena as credenciais para o repositório de contêiner e as compartilha com o tempo de execução do IoT Edge. O tempo de execução precisa dessas credenciais para efetuar pull de imagens privadas para o dispositivo IoT Edge. 

1. No explorador do VS Code, abra o arquivo **.env**. 
2. Atualize os campos com os valores de **nome de usuário** e **senha** que você copiou do registro de contêiner do Azure. 
3. Salve o arquivo. 

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com código personalizado

Cada modelo é fornecido com código de exemplo incluído, que usa dados de sensor simulados do módulo **tempSensor** e os encaminha para o Hub IoT. Nesta seção, adicione o código que expande o pythonModule para analisar as mensagens antes de enviá-las. 

1. No explorador do VS Code, abra **modules** > **PythonModule** > **main.py**.

2. Na parte superior de **main.py**, importe a biblioteca `json`.

    ```python
    import json
    ```

3. Adicione as variáveis `TEMPERATURE_THRESHOLD` e `TWIN_CALLBACKS` sob os contadores globais. O limite de temperatura define o valor que a temperatura da máquina medida deve exceder para que os dados sejam enviados para o Hub IoT.

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = 0
    ```

4. Substitua a função `receive_message_callback` pelo seguinte código:

    ```python
    # receive_message_callback is invoked when an incoming message arrives on the specified 
    # input queue (in the case of this sample, "input1").  Because this is a filter module, 
    # we will forward this message onto the "output1" queue.
    def receive_message_callback(message, hubManager):
        global RECEIVE_CALLBACKS
        global TEMPERATURE_THRESHOLD
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        message_text = message_buffer[:size].decode('utf-8')
        print ( "    Data: <<<%s>>> & Size=%d" % (message_text, size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        data = json.loads(message_text)
        if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
            map_properties.add("MessageType", "Alert")
            print("Machine temperature %s exceeds threshold %s" % (data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

5. Adicione uma nova função chamada `module_twin_callback`. Essa função será invocada quando as propriedades desejadas forem atualizadas.

    ```python
    # module_twin_callback is invoked when twin's desired properties are updated.
    def module_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global TEMPERATURE_THRESHOLD
        print ( "\nTwin callback called with:\nupdateStatus = %s\npayload = %s\ncontext = %s" % (update_state, payload, user_context) )
        data = json.loads(payload)
        if "desired" in data and "TemperatureThreshold" in data["desired"]:
            TEMPERATURE_THRESHOLD = data["desired"]["TemperatureThreshold"]
        if "TemperatureThreshold" in data:
            TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
        TWIN_CALLBACKS += 1
        print ( "Total calls confirmed: %d\n" % TWIN_CALLBACKS )
    ```

6. Na classe `HubManager`, adicione uma nova linha ao `__init__` método para inicializar a `module_twin_callback` função que você acabou de adicionar.

    ```python
    # sets the callback when a twin's desired properties are updated.
    self.client.set_module_twin_callback(module_twin_callback, self)
    ```

7. Salve o arquivo.

## <a name="build-your-iot-edge-solution"></a>Criar solução IoT Edge

Na seção anterior, você criou uma solução IoT Edge e adicionou um código a PythonModule que filtrará mensagens em que a temperatura relatada da máquina estiver abaixo do limite aceitável. Agora você precisa compilar a solução como uma imagem de contêiner e enviá-la por push para seu registro de contêiner. 

1. Entre no Docker digitando o seguinte comando no terminal integrado do Visual Studio Code para que você possa enviar sua imagem de módulo para o ACR: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Use o nome de usuário, a senha e o servidor de logon que você copiou de seu Registro de Contêiner do Azure na primeira seção. Ou recupere-as novamente da seção **Chaves de acesso** do registro no portal do Azure.

2. No gerenciador do VS Code, abra o arquivo **deployment.template.json** no espaço de trabalho da solução IoT Edge. 

   Este arquivo manda o `$edgeAgent` implantar dois módulos: **tempSensor**, que simula a dados do dispositivo, e **PythonModule**. O valor `PythonModule.image` é definido como uma versão Linux amd64 da imagem. Para saber mais sobre manifestos de implantação, consulte [Entender como os módulos do IoT Edge podem ser utilizados, configurados e reutilizados](module-composition.md).

   Esse arquivo também contém suas credenciais de registro. No arquivo de modelo, o nome de usuário e a senha são preenchidos com espaços reservados. Quando você gera o manifesto de implantação, os campos são atualizados com os valores adicionados ao **.env**. 

3. Adicione o módulo gêmeo PythonModule no manifesto de implantação. Insira o seguinte conteúdo JSON na parte inferior da seção `moduleContent`, após o módulo gêmeo do `$edgeHub`: 
    ```json
        "PythonModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

4. Salve o arquivo.

5. No gerenciador do VS Code, clique com o botão direito do mouse no arquivo **deployment.template.json** e selecione **Compilar solução IoT Edge**. 

Quando você solicitar ao Visual Studio Code para compilar sua solução, primeiro ele usará as informações no modelo de implantação e gerará um arquivo `deployment.json` em uma nova pasta **config**. Em seguida, ele executará dois comandos no terminal integrado: `docker build` e `docker push`. Esses dois comandos compilam seu código, conteinerizam o código Python e enviam por push para o registro de contêiner que você especificou ao inicializar a solução. 

Você pode obter o endereço de imagem de contêiner completo com marca no comando `docker build` executado no terminal integrado do VS Code. O endereço da imagem é criado de informações do arquivo `module.json` com o formato **\<repositório\>:\<versão\>-\<plataforma\>**. Para este tutorial,ele deve ser parecido com **registryname.azurecr.io/pythonmodule:0.0.1-amd64**.

## <a name="deploy-and-run-the-solution"></a>Implantar e executar a solução

Você pode usar o portal do Azure para implantar o módulo Python em um dispositivo IoT Edge como foi feito no guia de início rápido, mas também pode implantar e monitorar os módulos do Visual Studio Code. As seções a seguir usam a extensão do Azure IoT Edge para VS Code que estava listada nos pré-requisitos. Caso ainda não o tenha feito, instale-a agora. 

1. Abra a paleta de comandos do VS Code selecionando **Exibir** > **Paleta de comandos**.

2. Pesquise e execute o comando **Azure: Entrar**. Siga as instruções para entrar na conta do Azure. 

3. Na paleta de comandos, pesquise e execute o comando **Hub IoT do Azure: Selecionar Hub IoT**. 

4. Selecione a assinatura que contém seu Hub IoT e selecione a que você deseja acessar.

5. No explorador do VS Code, expanda a seção **Dispositivos do Hub IoT do Azure**. 

6. Clique com o botão direito do mouse no nome do seu dispositivo IoT Edge e selecione **Criar Implantação de dispositivo IoT Edge**. 

7. Navegue até a pasta de solução que contém PythonModule. Abra a pasta **config** e selecione o arquivo **deployment.json**. Clique em **Selecionar Manifesto de Implantação do Edge**.

8. Atualize a seção **Dispositivos Hub IoT do Azure**. Você deve ver o novo **PythonModule** sendo executado junto com o módulo **TempSensor** em **$edgeAgent** e **$edgeHub**. 

## <a name="view-generated-data"></a>Exibir os dados gerados

1. Para monitorar dados que chegam ao hub IoT, clique em **...** e selecione **Iniciar Monitoramento de Mensagens D2C**.
2. Para monitorar a mensagem D2C para um dispositivo específico, clique com o botão direito do mouse no dispositivo na lista e selecione **Iniciar Monitoramento de Mensagens D2C**.
3. Para interromper o monitoramento de dados, execute o comando **Hub IoT do Azure: Parar o monitoramento de mensagens D2C** na paleta de comandos. 
4. Para exibir ou atualizar módulos gêmeos, clique com o botão direito do mouse no módulo na lista e selecione **Editar módulo gêmeo**. Para atualizar o módulo gêmeo, salve o arquivo JSON gêmeo, clique com o botão direito do mouse na área do editor e selecione **Atualizar Módulo Gêmeo**.
5. Para exibir logs do Docker, você pode instalar o [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) para VS Code e localizar os módulos de execução localmente no explorador do Docker. No menu de contexto, clique em **Mostrar Logs** para exibi-los no terminal integrado. 

## <a name="clean-up-resources"></a>Limpar recursos 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

Se você pretende continuar para o próximo artigo recomendado, pode manter os recursos e as configurações já criados e reutilizá-los.

Caso contrário, você pode excluir as configurações locais e os recursos do Azure criados neste artigo para evitar encargos. 

> [!IMPORTANT]
> A exclusão de recursos do Azure e do grupo de recursos é irreversível. Depois de excluídos, o grupo de recursos e todos os recursos contidos nele serão excluídos permanentemente. Não exclua acidentalmente o grupo de recursos ou os recursos incorretos. Caso tenha criado o Hub IoT dentro de um grupo de recursos existente que contém recursos que você deseja manter, exclua apenas o próprio recurso do Hub IoT em vez de excluir o grupo de recursos.
>

Para excluir apenas o Hub IoT, execute o comando abaixo usando seu nome de hub e do grupo de recursos:

```azurecli-interactive
az iot hub delete --name MyIoTHub --resource-group TestResources
```


Para excluir o grupo de recursos inteiro por nome:

1. Entre no [portal do Azure](https://portal.azure.com) e clique em **Grupos de recursos**.

2. Na caixa de texto **Filtrar por nome...**, digite o nome do grupo de recursos que contém seu Hub IoT. 

3. À direita do seu grupo de recursos, na lista de resultados, clique em **...**, depois em **Excluir grupo de recursos**.

4. Você receberá uma solicitação para confirmar a exclusão do grupo de recursos. Digite o nome do grupo de recursos novamente para confirmar e clique em **Excluir**. Após alguns instantes, o grupo de recursos, e todos os recursos contidos nele, serão excluídos.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um módulo do IoT Edge que contém código para filtrar os dados brutos gerados pelo seu dispositivo IoT Edge. Continue com um dos tutoriais a seguir para saber mais sobre outras formas pelas quais o Azure IoT Edge pode ajudá-lo a transformar dados em informações de negócios na borda.

> [!div class="nextstepaction"]
> [Implantar a função do Azure como um módulo](tutorial-deploy-function.md)
> [Implantar o Azure Stream Analytics como um módulo](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-quickstart-win]: quickstart.md
[lnk-quickstart-lin]: quickstart-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
