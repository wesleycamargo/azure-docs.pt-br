---
title: Criar módulo personalizado do Python – Azure IoT Edge | Microsoft Docs
description: Este tutorial mostra como criar um módulo do IoT Edge com código em Python e implantá-lo em um dispositivo de borda.
services: iot-edge
author: shizn
manager: philmea
ms.reviewer: kgremban
ms.author: xshi
ms.date: 03/24/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 0affd965bbfc587933a9cdbf5b96c470a6e4dd6a
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2019
ms.locfileid: "58578281"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-to-your-simulated-device"></a>Tutorial: Desenvolver e implantar um módulo do IoT Edge em Python em seu dispositivo simulado

Use os módulos do Azure IoT Edge para implantar um código que implementa a lógica de negócios diretamente em seus dispositivos IoT Edge. Este tutorial o conduz pela criação e implantação de um módulo do IoT Edge que filtra os dados de sensor no dispositivo do IoT Edge que você configurou no início rápido. Neste tutorial, você aprenderá como:    

> [!div class="checklist"]
> * Usar o Visual Studio Code para criar um módulo do IoT Edge Python.
> * Usar o Visual Studio Code e o Docker para criar uma imagem do Docker e publicá-la no registro.
> * Implantar o módulo no dispositivo IoT Edge.
> * Exibir os dados gerados.


O módulo IoT Edge que criado neste tutorial filtra os dados de temperatura gerados pelo seu dispositivo. Ele somente envia mensagens upstream se a temperatura estiver acima de um limite especificado. Esse tipo de análise na borda é útil para reduzir a quantidade de dados que é comunicada e armazenada na nuvem. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo do Azure IoT Edge:

* Você pode usar uma máquina virtual do Azure como um dispositivo do IoT Edge seguindo as etapas no início rápido do [Linux](quickstart-linux.md).
* Os módulos do Python para o IoT Edge não são compatíveis com contêineres Windows.

Recursos de nuvem:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na camada padrão ou gratuito no Azure. 

Recursos de desenvolvimento:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Ferramentas de IoT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) para Visual Studio Code.
* [Extensão do Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) para Visual Studio Code. 
* [Python](https://www.python.org/downloads/).
* [PIP](https://pip.pypa.io/en/stable/installing/#installation) para instalar os pacotes do Python (normalmente incluídos com a instalação do Python).
* [CE do Docker](https://docs.docker.com/install/). 
  * Se você estiver desenvolvendo em um computador Windows, verifique se o Docker está [configurado para usar contêineres do Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers). 

>[!Note]
>Verifique se a sua pasta `bin` está dentro do caminho para sua plataforma. Normalmente, `~/.local/` para UNIX e macOS ou `%APPDATA%\Python` no Windows.

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
As etapas a seguir criam um módulo Python do IoT Edge usando o Visual Studio Code e as Ferramentas de IoT do Azure.

### <a name="create-a-new-solution"></a>Criar uma nova solução

Use o pacote do Python **cookiecutter** para criar um modelo de solução em Python que servirá de base. 

1. No Visual Studio Code, selecione **Exibir** > **Terminal** para abrir o terminal integrado do Visual Studio Code.

2. No terminal, digite o seguinte comando para instalar (ou atualizar) **cookiecutter**, que você pode usar para criar o modelo de solução IoT Edge:

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```
   >[!Note]
   >Verifique se o diretório onde o cookiecutter será instalado está no CAMINHO do seu ambiente para que seja possível invocá-lo em um prompt de comando. O diretório faz parte da saída do script de instalação, por exemplo `C:\Users\{user}\AppData\Roaming\Python\Python{version}\Scripts`.
   >
   >Reinicie o Visual Studio Code para acompanhar as alterações no CAMINHO. 

3. Selecione **Exibir** > **Paleta de comandos** para abrir a paleta de comandos do VS Code. 

4. Na paleta de comandos, insira e execute o comando **Azure: Entrar** e siga as instruções para entrar na conta do Azure. Se já tiver entrado, pode ignorar esta etapa.

5. Na paleta de comandos, digite e execute o comando **Azure IoT Edge: Nova solução do IoT Edge**. Siga os prompts e forneça as seguintes informações para criar sua solução:

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha o local no computador de desenvolvimento em que o VS Code criará os arquivos de solução. |
   | Fornecer um nome para a solução | Insira um nome descritivo para a solução ou aceite o padrão **EdgeSolution**. |
   | Selecionar modelo do módulo | Escolha **Módulo de Python**. |
   | Fornecer um nome de módulo | Nomeie seu módulo **PythonModule**. |
   | Fornecer o repositório de imagem do Docker para o módulo | Um repositório de imagem inclui o nome do registro de contêiner e o nome da imagem de contêiner. Sua imagem de contêiner é preenchida previamente o nome fornecido na última etapa. Substitua **localhost:5000** pelo valor do servidor de logon do seu registro de contêiner do Azure. Você pode recuperar o servidor de logon da página Visão Geral do seu registro de contêiner no portal do Azure. <br><br>O repositório de imagem final se parece com \<nome_do_registro\>.azurecr.io/pythonmodule. |
 
   ![Fornecer o repositório de imagem do Docker](./media/tutorial-python-module/repository.png)

A janela do VS Code carregará seu workspace da solução IoT Edge. O workspace da solução contém cinco componentes de nível superior. A pasta **módulos** contém o código Python para o seu módulo, bem como Dockerfiles para a compilação de seu módulo como uma imagem de contêiner. O arquivo **\.env** armazena suas credenciais de registro de contêiner. O arquivo **deployment.template.json** contém as informações que o tempo de execução do IoT Edge usa para implantar módulos em um dispositivo. E o arquivo **deployment.debug.template.json** contém a versão de depuração de módulos. Você não editará a pasta **\.vscode** ou o arquivo **\.gitignore** neste tutorial.  

Se você não especifica um registro de contêiner durante a criação de sua solução, mas aceita o valor de localhost:5000, você não terá um arquivo \.env. 

<!--
   ![Python solution workspace](./media/tutorial-python-module/workspace.png)
-->

### <a name="add-your-registry-credentials"></a>Adicionar suas credenciais de registro

O arquivo do ambiente armazena as credenciais para o repositório de contêiner e as compartilha com o tempo de execução do IoT Edge. O tempo de execução precisa dessas credenciais para efetuar pull de imagens privadas para o dispositivo IoT Edge. 

1. No explorador do VS Code, abra o arquivo **.env**. 
2. Atualize os campos com os valores de **nome de usuário** e **senha** que você copiou do registro de contêiner do Azure. 
3. Salve o arquivo .env. 

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com código personalizado

Cada modelo inclui código de exemplo que usa dados simulados de sensor do módulo **tempSensor** e os encaminha ao hub IoT. Nesta seção, adicione o código que expande o **PythonModule** para analisar as mensagens antes de enviá-las. 

1. No explorador do VS Code, abra **modules** > **PythonModule** > **main.py**.

2. No início do arquivo **main.py**, importe a biblioteca **json**:

    ```python
    import json
    ```

3. Adicione as variáveis **TEMPERATURE_THRESHOLD** e **TWIN_CALLBACKS** nos contadores globais. O limite de temperatura define o valor que a temperatura da máquina medida deve exceder para que os dados sejam enviados ao hub IoT.

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = 0
    ```

4. Substitua a função **receive_message_callback** pelo seguinte código:

    ```python
    # receive_message_callback is invoked when an incoming message arrives on the specified 
    # input queue (in the case of this sample, "input1").  Because this is a filter module, 
    # we forward this message to the "output1" queue.
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

5. Adicione uma nova função chamada **module_twin_callback**. Essa função é invocada quando as propriedades desejadas são atualizadas.

    ```python
    # module_twin_callback is invoked when the module twin's desired properties are updated.
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

6. Na classe **HubManager**, adicione uma nova linha ao método **__init__** para inicializar a função **module_twin_callback** que você acabou de adicionar:

    ```python
    # Sets the callback when a module twin's desired properties are updated.
    self.client.set_module_twin_callback(module_twin_callback, self)
    ```

7. Salve o arquivo main.py.

8. No gerenciador do VS Code, abra o arquivo **deployment.template.json** no workspace da solução IoT Edge. Esse arquivo informa ao agente do IoT Edge quais módulos implantar, nesse caso, **tempSensor** e **PythonModule**, e informa ao hub do IoT Edge como rotear mensagens entre eles. A extensão do Visual Studio Code preenche automaticamente a maioria das informações necessárias no modelo de implantação. Mesmo assim, verifique se tudo está preciso para sua solução: 

   1. A plataforma padrão do seu IoT Edge é definida como **amd64** na barra de status do VS Code, o que significa que seu **PythonModule** está definido para a versão amd64 do Linux da imagem. Altere a plataforma padrão na barra de status de **amd64** para **arm32v7** se essa for a arquitetura do seu dispositivo IoT Edge. 

      ![Atualizar a plataforma da imagem do módulo](./media/tutorial-python-module/image-platform.png)

   2. Verifique se o modelo tem o nome do módulo correto, não o nome padrão **SampleModule** alterado quando você criou a solução de IoT Edge.

   3. A seção **registryCredentials** armazena suas credenciais de registro do Docker, de modo que o agente do IoT Edge possa efetuar pull da imagem do módulo. Os pares de nome de usuário e senha reais são armazenados no arquivo .env que é ignorado pelo git. Adicione suas credenciais ao arquivo .env caso ainda não tenha feito isso.  

   4. Caso deseje saber mais sobre manifestos de implantação, confira [Saiba como implantar módulos e estabelecer rotas no IoT Edge](module-composition.md).

9. Adicione o módulo gêmeo **PythonModule** no manifesto de implantação. Insira o seguinte conteúdo JSON na parte inferior da seção **moduleContent**, após o módulo gêmeo do **$edgeHub**: 

   ```json
       "PythonModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![Adicionar gêmeo de módulo ao modelo de implantação](./media/tutorial-python-module/module-twin.png)

10. Salve o arquivo deployment.template.json.

## <a name="build-and-push-your-solution"></a>Compilar e enviar por push sua solução

Na seção anterior, você criou uma solução IoT Edge e adicionou um código a **PythonModule** para filtrar mensagens em que a temperatura relatada da máquina estiver abaixo do limite aceitável. Agora você precisa compilar a solução como uma imagem de contêiner e enviá-la por push para seu registro de contêiner. 

1. Entre no Docker inserindo o comando a seguir no terminal integrado do Visual Studio Code. Em seguida, envie sua imagem de módulo por push para o registro de contêiner do Azure: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Use o nome de usuário, a senha e o servidor de logon que você copiou de seu registro de contêiner do Azure na primeira seção. Você também pode recuperar esses valores da seção **Chaves de acesso** do registro no portal do Azure.

   Você pode ver um aviso de segurança recomendando o uso do parâmetro --password-stdin. Embora seu uso esteja fora do escopo deste artigo, é recomendável seguir essa prática recomendada. Para obter mais informações, consulte a referência do comando [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin). 


2. No explorador do VS Code, clique com o botão direito do mouse no arquivo deployment.template.json e selecione **Compilar e enviar por push solução IoT Edge**. 

Quando você solicitar ao Visual Studio Code para compilar sua solução, primeiro ele usará as informações no modelo de implantação e gerará um arquivo deployment.json em uma nova pasta chamada **config**. Em seguida, ele executará dois comandos no terminal integrado: `docker build` e `docker push`. Esses dois comandos compilam seu código, conteinerizam o código em Python e enviam o código por push para o registro de contêiner especificado ao inicializar a solução. 

Você pode obter o endereço de imagem de contêiner completo com marca no comando `docker build` executado no terminal integrado do VS Code. O endereço da imagem é criado de informações do arquivo module.json com o formato \<repositório\>:\<versão\>-\<plataforma\>. Para este tutorial,ele deve ser parecido com registryname.azurecr.io/pythonmodule:0.0.1-amd64.

>[!TIP]
>Se você receber um erro ao tentar compilar e efetuar push do seu módulo, faça as seguintes verificações:
>* Você entrou no Docker no Visual Studio Code usando as credenciais do seu Registro de contêiner? Essas credenciais são diferentes daquelas que você usa para entrar no portal do Azure.
>* Seu repositório de contêiner está correto? Abra **Módulos** > **PythonModule** > **module.json** e encontre o campo **repository**. O repositório de imagens deve se parecer vom **\<nome_do_registro\>.azurecr.io/pythonmodule**. 
>* Você está criando o mesmo tipo de contêiner que seu computador de desenvolvimento está executando? Visual Studio Code assume como padrão contêineres do Linux amd64. Se seu computador de desenvolvimento consistir em contêineres Linux arm32v7, atualize a plataforma na barra de status azul na parte inferior da janela do VS Code para corresponder. Módulos Python não dão suporte a contêineres do Windows. 

## <a name="deploy-and-run-the-solution"></a>Implantar e executar a solução

O artigo de início rápido que você usou para configurar seu dispositivo do IoT Edge, você implantou um módulo usando o portal do Azure. Você também pode implantar módulos usando a extensão do Kit de Ferramentas do Hub IoT do Azure (anteriormente conhecida como extensão do Kit de Ferramentas do Azure IoT) para Visual Studio Code. Você já tem um manifesto de implantação preparado para o seu cenário, o arquivo **deployment.json**. Agora, tudo o que você precisa fazer é selecionar um dispositivo para receber a implantação.

1. Na paleta de comandos do VS Code, execute **Hub IoT do Azure: selecionar Hub IoT**. 

2. Escolha a assinatura e o hub IoT que contêm o dispositivo do IoT Edge que você deseja configurar. 

3. No explorador do VS Code, expanda a seção **Dispositivos do Hub IoT do Azure**. 

4. Clique com o botão direito no nome do seu dispositivo IoT Edge e selecione **Criar Implantação para Dispositivo Único**. 

   ![Criar implantação para dispositivo único](./media/tutorial-python-module/create-deployment.png)

5. Selecione o arquivo **deployment.amd64** ou **deployment.arm32v7** (dependendo da sua arquitetura de destino) na pasta **config** e clique em **Selecionar Manifesto de Implantação do Edge**. Não use o arquivo deployment.template.json. 

6. Clique no botão Atualizar. Você deve ver o novo **PythonModule** sendo executado junto com o módulo **TempSensor** em **$edgeAgent** e **$edgeHub**. 

## <a name="view-generated-data"></a>Exibir os dados gerados

Depois que você aplica o manifesto de implantação no seu dispositivo IoT Edge, o tempo de execução do IoT Edge no dispositivo coleta as novas informações de implantação e inicia a execução nele. Todos os módulos em execução no dispositivo que não estão incluídos no manifesto de implantação são interrompidos. Todos os módulos ausentes do dispositivo são iniciados. 

Você pode ver o status do seu dispositivo IoT Edge usando a seção **Dispositivos de Hub IoT do Azure** do explorer do Visual Studio Code. Expanda os detalhes do seu dispositivo para ver uma lista de módulos implantados e em execução. 

No próprio dispositivo IoT Edge, você pode ver o status dos seus módulos de implantação usando o comando `iotedge list`. Você deverá ver quatro módulos: os dois módulos de tempo de execução do IoT Edge, tempSensor e o módulo personalizado que você criou neste tutorial. Pode levar alguns minutos para que todos os módulos iniciem, portanto, se você não vir todos inicialmente, execute novamente o comando. 

Para exibir as mensagens que estão sendo geradas por qualquer módulo, use o comando `iotedge logs <module name>`. 

Você pode exibir as mensagens que chegam ao Hub IoT usando o Visual Studio Code. 

1. Para monitorar os dados que chegam ao hub IoT, selecione as reticências (**...** ) e selecione **Iniciar Monitoramento de Mensagens D2C**.
2. Para monitorar a mensagem D2C para um dispositivo específico, clique com o botão direito do mouse no dispositivo na lista e selecione **Iniciar Monitoramento de Mensagens D2C**.
3. Para interromper o monitoramento de dados, execute o comando **Hub IoT do Azure: interromper monitoramento de mensagens D2C** na paleta de comandos. 
4. Para exibir ou atualizar o módulos gêmeo, clique com o botão direito do mouse no módulo na lista e selecione **Editar módulo gêmeo**. Para atualizar o módulo gêmeo, salve o arquivo JSON gêmeo, clique com o botão direito do mouse na área do editor e selecione **Atualizar Módulo Gêmeo**.
5. Para exibir os logs do Docker, instale a [Extensão do Docker para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker). Você pode encontrar os módulos em execução localmente no Docker Explorer. No menu de contexto, clique em **Mostrar Logs** para exibi-los no terminal integrado. 

## <a name="clean-up-resources"></a>Limpar recursos 

Se você pretende continuar no próximo artigo recomendado, pode manter os recursos e as configurações já criados e reutilizá-los. Você também pode continuar usando o mesmo dispositivo IoT Edge como um dispositivo de teste. 

Caso contrário, você pode excluir as configurações locais e os recursos do Azure criados neste artigo para evitar encargos. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um módulo do IoT Edge com código para filtrar os dados brutos gerados pelo seu dispositivo IoT Edge. Você pode seguir para os próximos tutoriais a fim de descobrir outras formas pelas quais o Azure IoT Edge pode ajudá-lo a transformar dados em informações de negócios na borda.

> [!div class="nextstepaction"]
> [Implantar o Azure Functions como um módulo](tutorial-deploy-function.md)
> [Implantar o Azure Stream Analytics como um módulo](tutorial-deploy-stream-analytics.md)
