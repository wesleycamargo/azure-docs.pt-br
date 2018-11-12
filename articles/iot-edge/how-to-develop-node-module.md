---
title: Depurar módulos Node.js para Azure IoT Edge | Microsoft Docs
description: Usar o Visual Studio Code para desenvolver e depurar módulos Node.js para Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 09/21/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: b05492941defc6ac3aa252d6bb29043d55e6b66c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51261767"
---
# <a name="use-visual-studio-code-to-develop-and-debug-nodejs-modules-for-azure-iot-edge"></a>Usar o Visual Studio Code para desenvolver e depurar módulos Node.js para Azure IoT Edge

É possível enviar a lógica de negócios para operar na borda, transformando-a em módulos para Azure IoT Edge. Este artigo fornece instruções detalhadas para usar o VS Code (Visual Studio Code) como a principal ferramenta de desenvolvimento para desenvolver módulos Node.js.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo assume que você usa um computador ou uma máquina virtual executando Windows, macOS ou Linux como o computador de desenvolvimento. Seu dispositivo do IoT Edge pode ser outro dispositivo físico.

> [!NOTE]
> Este artigo de depuração demonstra duas maneiras típicas de depurar o módulo Node.js no VS Code. Uma maneira é anexar um processo em um contêiner de módulo, enquanto a outra é anexar o código do módulo no modo de depuração. Se você não estiver familiarizado com os recursos de depuração do Visual Studio Code, leia sobre [Depuração](https://code.visualstudio.com/Docs/editor/debugging).

Como este artigo usa o Visual Studio Code como a principal ferramenta de desenvolvimento, instale o VS Code e adicione as extensões necessárias:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Extensão do Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Extensão do Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Para criar um módulo, é necessário o Node.js, que inclui npm para compilar a pasta de projeto, Docker para compilar a imagem de módulo e um registro de contêiner para manter a imagem de módulo:
* [Node.js](https://nodejs.org)
* [Docker](https://docs.docker.com/engine/installation/)
* [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) ou [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   * Você pode usar um registro local do Docker para fins de protótipo e teste, em vez de um registro em nuvem. 

Para configurar o ambiente de desenvolvimento local para depurar, executar e testar a solução IoT Edge, você precisará da [Ferramenta de desenvolvimento do Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/). Instale o [Python (2.7/3.6) e o Pip](https://www.python.org/). O PIP está incluso no instalador do Python. Em seguida, instale **iotedgehubdev**, executando o comando abaixo no terminal.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

Para testar o módulo em um dispositivo, é necessário ter um hub IoT ativo com pelo menos uma ID de dispositivo do IoT Edge criada. Se você estiver executando o daemon do IoT Edge no computador de desenvolvimento, talvez seja necessário interromper o EdgeHub e o EdgeAgent antes de passar para a próxima etapa. 

## <a name="create-a-new-solution-template"></a>Crie um novo modelo de solução

As etapas a seguir mostram como criar um módulo do IoT Edge baseado em Node.js usando o Visual Studio Code e a extensão do Azure IoT Edge. Você começa criando uma solução e, em seguida, gerando o primeiro módulo nessa solução. Cada solução pode conter vários módulos. 

1. No Visual Studio Code, selecione **Exibir** > **Terminal Integrado**.
2. No terminal integrado, digite o comando a seguir para instalar (ou atualizar) a última versão do modelo de módulo do Azure IoT Edge para Node.js:

   ```cmd/sh
   npm install -g yo generator-azure-iot-edge-module
   ```

3. No Visual Studio Code, selecione **Exibir** > **Paleta de Comandos**. 
4. Na paleta de comandos, digite e execute o comando **Azure IoT Edge: Nova solução do IoT Edge**.

   ![Executar a nova solução do IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

5. Navegue até a pasta em que você deseja criar a nova solução e clique em **Selecionar pasta**. 
6. Forneça um nome para a solução. 
7. Escolha **Módulo Node.js** como o modelo para o primeiro módulo na solução.
8. Forneça um nome para o módulo. Escolha um nome exclusivo no registro de contêiner. 
9. Forneça o repositório de imagem para o módulo. O VS Code preenche automaticamente o nome do módulo, portanto, basta substituir **localhost: 5000** pelas suas próprias informações de registro. Se você usar um registro local do Docker para teste, então o localhost está correto. Se usar o Registro de Contêiner do Azure, utilize o servidor de início de sessão nas configurações do registro. O servidor de início de seção é semelhante ao **\<nome do registro\>.azurecr.io**. Substitua somente a parte de localhost da cadeia de caracteres, não exclua o nome do módulo.

   ![Fornecer o repositório de imagem do Docker](./media/how-to-develop-node-module/repository.png)

O VS Code obtém as informações fornecidas, cria uma solução do IoT Edge e carrega-as em uma nova janela.

Dentro da solução há três itens: 
* Uma pasta **.vscode** que contém configurações de depuração.
* Uma pasta **módulos** que contém subpastas para cada módulo. No momento você só tem um, mas é possível adicionar mais na paleta de comandos com o comando **Azure IoT Edge: Adicionar módulo do IoT Edge**. 
* Um arquivo **.env** lista as variáveis de ambiente. Se o Registro de Contêiner do Azure for seu registro, você terá um nome de usuário e uma senha do Registro de Contêiner do Azure nele.

   >[!NOTE]
   >O arquivo de ambiente será criado somente se você fornecer um repositório de imagens para o módulo. Se você aceitou os padrões do localhost para testar e depurar localmente, não será necessário declarar variáveis de ambiente. 

* Um arquivo **deployment.template.json** lista o novo módulo junto com um módulo **tempSensor** de exemplo que simula dados que podem ser usados para teste. Para obter mais informações sobre como os manifestos de implantação funcionam, consulte [Entender como os módulos do IoT Edge podem ser utilizados, configurados e reutilizados](module-composition.md).

## <a name="develop-your-module"></a>Desenvolver seu módulo

O código do Node. js padrão que vem com a solução está localizado em **módulos** > [nome do módulo] > **App. js**. O módulo e o arquivo deployment.template.json são configurados de forma que você possa compilar a solução, enviá-la por push ao registro de contêiner e implantá-la em um dispositivo para iniciar os testes sem lidar com nenhum código. O módulo é criado para apenas receber entradas de uma fonte (nesse caso, o módulo tempSensor que simula dados) e redirecioná-las ao Hub IoT. 

Quando estiver pronto para personalizar o modelo Node.js com seu próprio código, use os [SDKs do Hub IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md) para compilar módulos que tratem das principais necessidades das soluções de IoT, como confiabilidade, segurança e gerenciamento do dispositivo. 

O Visual Studio Code tem suporte para Node.js. Saiba mais sobre [como trabalhar com Node.js no VS Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial).

## <a name="launch-and-debug-module-code-without-container"></a>Iniciar e depurar código do módulo sem contêiner

O módulo Node.js do IoT Edge depende do SDK do dispositivo Node.js do Azure IoT. No código do módulo padrão, você inicializa um **ModuleClient** com configurações de ambiente e nome de entrada, o que significa que o módulo Node.js do IoT Edge exige as configurações de ambiente para iniciar e executar, e você também precisa enviar ou rotear mensagens para o canais de entrada. O módulo Node.js padrão contém apenas um canal de entrada e o nome é **input1**.

### <a name="setup-iot-edge-simulator-for-single-module-app"></a>Configurar simulador do IoT Edge para aplicativo de módulo único

1. Para configurar e iniciar o simulador, na paleta de comandos do VS Code, digite e selecione **Azure IoT Edge: iniciar simulador de hub do IoT Edge para módulo único**. Também é necessário especificar os nomes de entrada para aplicativo de módulo único, digite **input1** e pressione Enter. O comando disparará a CLI **iotedgehubdev** e iniciará o simulador IoT Edge e um contêiner de módulo de utilitário de teste. Será possível ver as saídas abaixo no terminal integrado, se o simulador foi iniciado no modo de módulo único com êxito. Você também poderá ver um comando `curl` para ajudar a enviar mensagens. Você o usará mais tarde.

   ![Configurar simulador do IoT Edge para aplicativo de módulo único](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Você pode mover para o Docker Explorer e ver o status de execução do módulo.

   ![Status do módulo do simulador](media/how-to-develop-csharp-module/simulator-status.png)

   O contêiner **edgeHubDev** é o núcleo do simulador de IoT Edge local. Ele pode ser executado no computador de desenvolvimento sem o daemon de segurança do IoT Edge e fornecer configurações de ambiente ao aplicativo de módulo nativo ou contêineres de módulo. O contêiner de **entrada** expôs restAPIs para ajudar a conectar as mensagens ao canal de entrada de destino no módulo.

2. Na paleta de comandos do VS Code, digite e selecione **Azure IoT Edge: Definir credenciais do módulo para configurações do usuário** para definir as configurações do ambiente do módulo em `azure-iot-edge.EdgeHubConnectionString` e `azure-iot-edge.EdgeModuleCACertificateFile` nas configurações do usuário. É possível encontrar essas configurações de ambiente referenciadas em **.vscode** > **launch.json** e nas [configurações de usuário do VS Code](https://code.visualstudio.com/docs/getstarted/settings).

### <a name="debug-nodejs-module-in-launch-mode"></a>Depurar o módulo Node.js no modo de inicialização

1. No terminal integrado, altere o diretório para a pasta **NodeModule** e execute o comando a seguir para instalar pacotes do Node

   ```cmd
   npm install
   ```

2. Navegue até `app.js`. Adicione um ponto de interrupção neste arquivo.

3. Navegue para a visualização de depuração do VS Code. Selecione a configuração de depuração **Depuração local do ModuleName (Node.js)**. 

4. Clique em **Iniciar depuração** ou pressione **F5**. Você iniciará a sessão de depuração.

5. No terminal integrado do VS Code, execute o seguinte comando para enviar uma mensagem **Olá, Mundo** para o módulo. Este é o comando mostrado nas etapas anteriores ao configurar o simulador do IoT Edge com êxito. Pode ser necessário criar ou alternar para outro terminal integrado se o atual estiver bloqueado.

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Se você estiver usando o Windows, certificando-se de que o shell do terminal integrado do VS Code é **Git Bash** ou **WSL Bash**. Não é possível executar o comando `curl` no PowerShell ou no Prompt de Comando. 
   
   > [!TIP]
   > Também é possível usar [PostMan](https://www.getpostman.com/) ou outras ferramentas de API para enviar mensagens por meio de `curl`.

6. Na exibição de Depuração do VS Code, você verá as variáveis no painel esquerdo. 

7. Para parar a sessão de depuração, clique no botão Parar ou pressione **Shift + F5**. Na paleta de comandos do VS Code, digite e selecione **Azure IoT Edge: parar o simulador do IoT Edge** para interromper e limpar o simulador.


## <a name="build-module-container-for-debugging-and-debug-in-attach-mode"></a>Compilar contêiner de módulo para depuração e depuração em modo de anexação

A solução padrão contém dois módulos, um é um módulo de sensor de temperatura simulado e o outro é o módulo de pipe do Node.js. O sensor de temperatura simulado continua enviando mensagens para o módulo de pipe do Node.js; em seguida, as mensagens são redirecionadas para Hub IoT. Na pasta do módulo que você criou, há vários arquivos do Docker para diferentes tipos de contêineres. Use qualquer um dos arquivos que terminam com a extensão **.debug** para compilar o módulo para teste. Atualmente, os módulos Node.js só dão suporte à depuração em contêineres linux-amd64, windows-amd64 e linux-arm32v7.

### <a name="setup-iot-edge-simulator-for-iot-edge-solution"></a>Configurar o simulador do IoT Edge para solução do IoT Edge

No computador de desenvolvimento, é possível iniciar o simulador do IoT Edge, em vez de instalar o daemon de segurança do IoT Edge para executar a solução do IoT Edge. 

1. No Device Explorer, no lado esquerdo, clique com o botão direito do mouse na ID do dispositivo do IoT Edge, selecione **Configurar simulador do IoT Edge** para iniciar o simulador com a cadeia de conexão do dispositivo.

2. Você pode ver que o simulador do IoT Edge foi configurado com êxito no terminal integrado.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Compilar e executar contêiner para depuração e depuração em modo de anexação

1. No VS Code, navegue até o arquivo `deployment.template.json`. Atualize o URL da sua imagem de módulo adicionando **.debug** ao final.

2. Substitua o createOptions do módulo Node.js em **deployment.template.json** com o conteúdo abaixo e salve o arquivo: 
    ```json
    "createOptions": "{\"ExposedPorts\":{\"9229/tcp\":{}},\"HostConfig\":{\"PortBindings\":{\"9229/tcp\":[{\"HostPort\":\"9229\"}]}}}"
    ```

3. Navegue até a exibição de depuração do VS Code. Selecione o arquivo de configuração de depuração do módulo. O nome da opção de depuração deve ser semelhante à **Depuração remota do ModuleName (Node.js)** ou à **Depuração remota do ModuleName (Node.js no contêiner do Windows)**, que depende do tipo de contêiner no computador de desenvolvimento.

4. Selecione **Iniciar Depuração** ou selecione **F5**. Selecione o processo ao qual ele deverá ser anexado.

5. Na exibição de Depuração do VS Code, você verá as variáveis no painel esquerdo.

6. Para parar a sessão de depuração, clique no botão Parar ou pressione **Shift + F5**. E, na paleta de comandos do VS Code, digite e selecione **Azure IoT Edge: Parar simulador do IoT Edge**.

> [!NOTE]
> O exemplo anterior mostra como depurar os módulos do Node.js IoT Edge nos contêineres. Ele adicionou portas expostas no contêiner de módulo createOptions. Após concluir a depuração dos módulos do Node.js, é recomendável remover essas portas expostas de módulos do IoT Edge prontos para produção.

## <a name="next-steps"></a>Próximas etapas

Após compilar o módulo, saiba como [Implantar módulos do Azure IoT Edge do Visual Studio Code](how-to-deploy-modules-vscode.md)

Para desenvolver módulos para seus dispositivos do IoT Edge, consulte [Entender e usar os SDKs de Hub IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md).
