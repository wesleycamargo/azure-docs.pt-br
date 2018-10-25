---
title: Desenvolver e depurar módulos C# para o Azure IoT Edge no Visual Studio 2017 | Microsoft Docs
description: Use o Visual Studio 2017 para desenvolver e depurar módulos C# para o Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 09/24/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 768ff899ca2c71cb32fe29bdd5d58654d8f7d431
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394741"
---
# <a name="use-visual-studio-2017-to-develop-and-debug-c-modules-for-azure-iot-edge-preview"></a>Use o Visual Studio 2017 para desenvolver e depurar módulos C# para o Azure IoT Edge (versão prévia)

Você pode transformar sua lógica de negócios em módulos do Azure IoT Edge. Este artigo mostra como usar o Visual Studio 2017 como a principal ferramenta para desenvolver e depurar módulos C#.

As ferramentas do Azure IoT Edge para Visual Studio fornecem os seguintes benefícios:

- Criar, editar, compilar, executar e depurar soluções do Azure IoT Edge e módulos no seu computador de desenvolvimento local.
- Implantar a solução do Azure IoT Edge no dispositivo do Azure IoT Edge por meio do Hub IoT do Azure.
- Codificar seus módulos do IoT do Azure no C# tendo todos os benefícios de desenvolvimento do Visual Studio.
- Gerenciar dispositivos do Azure IoT Edge e módulos com interface do usuário. 

Este tópico mostra como usar as Ferramentas do Azure IoT Edge para Visual Studio 2017 a fim de desenvolver seus módulos do IoT Edge em C#. Você também aprenderá como implantar seu projeto no dispositivo Azure IoT Edge.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo presume que você esteja usando um computador ou uma máquina virtual que executa Windows como seu computador de desenvolvimento. Seu dispositivo do IoT Edge pode ser outro dispositivo físico.

Como este artigo usa o Visual Studio 2017 como ferramenta de desenvolvimento principal, instale o Visual Studio. E certifique-se de incluir a **carga de trabalho de desenvolvimento do Azure** na sua instalação do Visual Studio 2017. Você pode [modificar o Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2017) e adicionar a carga de trabalho de desenvolvimento do Azure.

Depois que o Visual Studio 2017 estiver pronto, você também precisará:

- Baixar e instalar a [extensão do Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.azureiotedgetools) no marketplace do Visual Studio para criar o projeto do IoT Edge no Visual Studio 2017.
- [Docker Community Edition](https://docs.docker.com/install/) no computador de desenvolvimento para compilar e executar suas imagens de módulo. Você precisa definir corretamente o Docker CE em execução no modo de contêiner do Linux ou no modo de contêiner do Windows.
- Para configurar o ambiente de desenvolvimento local para depurar, executar e testar a solução IoT Edge, você precisará da [Ferramenta de desenvolvimento do Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/). Instale o [Python (2.7/3.6) e o Pip](https://www.python.org/). Em seguida, instale **iotedgehubdev**, executando o comando abaixo no terminal. Certifique-se de que sua versão da ferramenta de desenvolvimento do Azure IoT EdgeHub é posterior à versão 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) ou [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   - Você pode usar um registro do Docker local, em vez de um registro em nuvem, para fins de protótipo e teste. 

- Para testar o módulo, é necessário ter um hub IoT ativo com pelo menos uma ID de dispositivo do IoT Edge criada. Se você estiver executando o daemon do IoT Edge no computador de desenvolvimento, talvez seja necessário interromper o EdgeHub e o EdgeAgent antes de iniciar o desenvolvimento no Visual Studio. 

### <a name="check-your-tools-version"></a>Verificar a versão de ferramentas

1. No menu **Ferramentas**, clique em **Extensões e Atualizações**. Expanda **Instalado > Ferramentas** e você poderá encontrar **Azure IoT Edge** e **Cloud Explorer**.

2. Observe a versão instalada. Você pode comparar esta versão com a versão mais recente no Visual Studio Marketplace ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.azureiotedgetools))

3. Se a sua versão for mais antiga, atualize suas ferramentas no Visual Studio conforme mostrado na seção a seguir.

### <a name="update-your-tools"></a>Atualize suas ferramentas

1. Na caixa de diálogo **Extensões e Atualizações**, expanda **Atualizações > Visual Studio Marketplace**, escolha **Azure IoT Edge** ou **Cloud Explorer** e selecione **Atualizar**.

2. Depois de fazer o download da atualização das ferramentas, feche o Visual Studio para disparar a atualização das ferramentas usando o instalador VSIX.

3. No instalador, escolha **OK** para iniciar e depois Modificar para atualizar as ferramentas.

4. Depois que a atualização for concluída, escolha Fechar e reinicie o Visual Studio.

### <a name="create-an-azure-iot-edge-project"></a>Criar um projeto do Azure IoT Edge

O modelo de projeto do Azure IoT Edge no Visual Studio cria um projeto que pode ser implantado em dispositivos do Azure IoT Edge no Hub IoT do Azure. Primeiro, crie uma solução do Azure IoT Edge e, em seguida, gere o primeiro módulo C# nessa solução. Cada solução do IoT Edge pode conter mais de um módulo. 

1. No Visual Studio, selecione **Novo** > **Projeto** no menu **Arquivo**.

2. No diálogo **Novo projeto**, selecione **Instalado**, expanda **Visual C# > Nuvem**, selecione **Azure IoT Edge**, digite um **Nome** para seu projeto, especifique o local e clique em **OK**. O nome do projeto padrão é **AzureIoTEdgeApp1**. 

   ![Novo Projeto](./media/how-to-visual-studio-develop-csharp-module/create-new.jpg)

3. Na janela **Configuração do módulo do IoT Edge**, selecione **Módulo C#** e digite e especifique o nome do módulo e o repositório de imagens do módulo.  O VS popula automaticamente o nome do módulo com **localhost:5000**. Substitua-o pelas informações de seu registro. Se você usar um registro local do Docker para teste, então o localhost está correto. Se usar o Registro de Contêiner do Azure, utilize o servidor de início de sessão nas configurações do registro. O servidor de logon é semelhante a **<registry name>.azurecr.io**. Substitua somente a parte de localhost da cadeia de caracteres, não exclua o nome do módulo. O nome do módulo padrão é **IoTEdgeModule1**

4. Clique em **OK** para criar o projeto do Azure IoT Edge com um módulo C#.

Agora você tem um projeto **AzureIoTEdgeApp1** e um projeto **IoTEdgeModule1** em nossa solução. O projeto **AzureIoTEdgeApp1** tem um arquivo **deployment.template.json**. Esse arquivo define os módulos que você deseja compilar e implantar para sua solução IoT Edge e define as rotas entre os módulos. A solução padrão tem um módulo **tempSensor** e um módulo **IoTEdgeModule1**. O módulo **tempSensor** gera dados simulados para o módulo **IoTEdgeModule1**, enquanto o código padrão no módulo **IoTEdgeModule1** é um módulo de mensagem de pipe, que redireciona diretamente mensagens recebidas para o Hub IoT.

O projeto **IoTEdgeModule1** é um aplicativo de console do .Net Core 2.1. Ele contém os **Dockerfiles** que você precisa para seu dispositivo IoT Edge em execução com o contêiner do Windows ou do Linux. O arquivo **module.json** descreve os metadados de um módulo. O **program.cs** é o código do módulo real, que usa o SDK do dispositivo IoT do Azure como uma dependência.

## <a name="develop-your-module"></a>Desenvolver seu módulo

O código padrão do módulo C# que vem com a solução está localizado em **IoTEdgeModule1** > **Program.cs**. O módulo e o arquivo deployment.template.json são configurados de forma que você possa compilar a solução, enviá-la por push ao registro de contêiner e implantá-la em um dispositivo para iniciar os testes sem lidar com nenhum código. O módulo é criado para apenas receber entradas de uma fonte (nesse caso, o módulo tempSensor que simula dados) e redirecioná-las ao Hub IoT. 

Quando estiver pronto para personalizar o modelo C# com seu próprio código, use os [SDKs do Hub IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md) para compilar módulos que tratem das principais necessidades das soluções de IoT, como confiabilidade, segurança e gerenciamento do dispositivo. 

## <a name="initialize-iotegehubdev-with-iot-edge-device-connection-string"></a>Inicialize **iotegehubdev** com a cadeia de conexão do dispositivo IoT Edge

1. Você precisa obter a cadeia de conexão de qualquer dispositivo IoT Edge. Você pode copiar o valor de "Primary Connection String" do Cloud Explorer no Visual Studio 2017, como a seguir. Por favor, não copie a sequência de conexão do dispositivo não-Edge, o ícone do dispositivo IoT Edge é diferente daquele do dispositivo não-Edge.

   ![Copiar Cadeia de Conexão de Dispositivo de Borda](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

2. Você precisa clicar com o botão direito do mouse no projeto **AzureIoTEdgeApp1** para abrir o menu de contexto e, em seguida, clicar em **Configurar a Edge Device Connection String**, a janela de configuração do Azure IoT Edge será exibida.

   ![Abra a janela de cadeia de caracteres de Conexão de borda definida](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

3. Na janela de configuração, insira a cadeia de conexão que você obteve no primeiro passo e clique no botão **OK**.

>[!NOTE]
>Este é um trabalho único, você só precisa executar esta etapa uma vez em uma máquina, todas as soluções subsequentes do Azure IoT Edge obterão de graça. É claro que você pode executar novamente essa etapa se a cadeia de conexão for inválida ou se precisar mudar para outra cadeia de conexão.

## <a name="build-and-debug-single-c-module"></a>Compilar e depurar um módulo C# único

Normalmente, queremos testar/depurar cada módulo antes de colocá-lo em execução dentro de uma solução com vários módulos.

1. Selecione **IoTEdgeModule1** como o projeto de inicialização no menu de contexto.

   ![Definir o projeto de inicialização](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

2. Pressione **F5** ou clique no botão abaixo para executar o módulo. Pode levar entre 10 e 20 segundos na primeira vez.

   ![Executar módulo](./media/how-to-visual-studio-develop-csharp-module/run-module.png)


3. Você deverá ver um aplicativo de console .Net Core iniciado se o módulo tiver sido inicializado com êxito.

   ![Módulo em execução](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

4. Agora você pode definir um ponto de interrupção **PipeMessage** na **Program.cs** e depois enviar a mensagem executando o comando a seguir em seu **Git Bash** ou **WSL Bash** (não execute no CMD ou no PowerShell) (você também pode encontrar este comando na janela de Saída do VS):

    ```cmd
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Depurar módulo único](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    O ponto de interrupção deve ser disparado. Você pode assistir as variáveis na janela Locais do Visual Studio.

   > [!TIP]
   > Também é possível usar [PostMan](https://www.getpostman.com/) ou outras ferramentas de API para enviar mensagens por meio de `curl`.

5. Pressione **Ctrl + F5** ou clique no botão Parar para parar a depuração.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>Compilar e depurar a solução do IoT Edge com vários módulos

Após concluir o desenvolvimento de módulo único, queremos executar e depurar a solução inteira com vários módulos.

1. Adicione o segundo módulo C# à solução. Clique com o botão direito do mouse em **AzureIoTEdgeApp1** e selecione **Adicionar** -> **Novo módulo do IoT Edge**. O nome padrão do segundo módulo é **IoTEdgeModule2** e ainda é um módulo de pipe.

2. Navegue até **deployment.template.json**. Você verá que **IoTEdgeModule2** foi adicionado na seção **Módulos**. Substitua a seção **Rotas** pelo seguinte. Se você tiver personalizado seus nomes de módulo, certifique-se de atualizar os nomes no seguinte após a substituição.

    ```json
        "routes": {
          "IoTEdgeModule1ToIoTHub": "FROM /messages/modules/IoTEdgeModule1/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule1": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule1/inputs/input1\")",
          "IoTEdgeModule2ToIoTHub": "FROM /messages/modules/IoTEdgeModule2/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule2": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule2/inputs/input1\")"
        },
    ```

3. Defina o projeto **AzureIoTEdgeApp1** como um projeto de inicialização no menu de contexto.

4. Defina os pontos de interrupção e pressione F5, em seguida, execute e depure vários módulos simultaneamente. Você deve ver as janelas do aplicativo do console .Net Core, cada janela indica seu módulo C#. 

   ![Depurar módulos múltiplos](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

5. Pressione **Ctrl + F5** ou clique no botão Parar para parar a depuração.

## <a name="build-and-push-images"></a>Compilar e efetuar push de imagens

1. Certifique-se de que **AzureIoTEdgeApp1** é seu projeto de inicialização. Selecione a configuração **Depurar** ou **Versão** para suas imagens de módulo serem compiladas.

    > [!NOTE]
    > Ao escolher **Depurar**, o VS usará `Dockerfile.debug` para compilar imagens do Docker. Isso inclui o VSDBG, depurador de linha de comando do .NET Core, na imagem de contêiner durante a criação. É recomendável que você use a configuração **Versão** que usa o `Dockerfile` sem o VSDBG para os módulos do IoT Edge prontos para produção.

2. Se você estiver usando o registro privado, como o Registro de Contêiner do Azure, execute o log do Docker com o comando a seguir no seu terminal. Se estiver usando o registro local, você poderá [Executar um registro local](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server> 
    ```

3. Se estiver usando o registro privado, como o Registro de Contêiner do Azure, você precisará colocar o nome de usuário e a senha do registro em `deployment.template.json` nas configurações de tempo de execução com o seguinte conteúdo. Lembre-se de substituir o espaço reservado pelo seu nome de usuário administrador e senha atuais.

    ```json
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "registry1": {
                "username": "<username>",
                "password": "<password>",
                "address": "<registry name>.azurecr.io"
              }
            }
          }
    ```

4. Clique com o botão direito do mouse em **AzureIoTEdgeApp1** e escolha o item de menu de contexto **Compilar e efetuar push da solução de borda**. Ele compilará e efetuará push da imagem do docker para cada módulo.

   ![Compilar e efetuar push de imagens](./media/how-to-visual-studio-develop-csharp-module/build-and-push.png)


## <a name="deploy-the-solution"></a>Implantar a solução

O artigo de início rápido que você usou para configurar seu dispositivo do IoT Edge, você implantou um módulo usando o portal do Azure. Você também pode implantar módulos usando o Cloud Explorer para o Visual Studio. Você já tem um manifesto de implantação preparado para o seu cenário, o arquivo `deployment.json`. Agora, tudo o que você precisa fazer é selecionar um dispositivo para receber a implantação.

1. Abra **Cloud Explorer** clicando em **Exibir** > **Cloud Explorer**. Verifique se que você fez logon com sua conta no Visual Studio 2017.

2. No **Cloud Explorer**, expanda sua assinatura, localize o Hub IoT do Azure e o dispositivo Azure IoT Edge que você deseja implantar.

3. Clique com o botão direito do mouse no dispositivo do IoT Edge para criar implantação para ele. Você precisa escolher o arquivo de manifesto de implantação sob o `$AzureIoTEdgeAppSolutionDir\config\deployment.(amd64|amd64.debug|windows-amd64).json`.

>>[!NOTE]
>>Você não deverá marcar `$AzureIoTEdgeAppSolutionDir\config\deployment_for_local_debug.json`

4. Clique no botão Atualizar. Você deve ver os novos módulos sendo executados junto com o módulo **TempSensor** em **$edgeAgent** e **$edgeHub**.

## <a name="view-generated-data"></a>Exibir os dados gerados

1. Para monitorar a mensagem D2C para um dispositivo específico, clique no dispositivo na lista e selecione **Iniciar monitoramento de mensagens D2C** na janela **Ação**.

2. Para interromper o monitoramento de dados, clique no dispositivo na lista e selecione **Parar monitoramento de mensagens de D2C** na janela **Ação**.

## <a name="next-steps"></a>Próximas etapas

Para desenvolver módulos para seus dispositivos do IoT Edge, consulte [Entender e usar os SDKs de Hub IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md).
