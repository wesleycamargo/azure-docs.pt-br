---
title: Depurar módulos C# para Azure IoT Edge | Microsoft Docs
description: Usar Visual Studio Code para desenvolver, compilar e depurar um módulo C# para Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 09/27/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 88659d31b64b4a98043606a71602f7c29316a31e
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47423282"
---
# <a name="use-visual-studio-code-to-develop-and-debug-c-modules-for-azure-iot-edge"></a>Usar Visual Studio Code para desenvolver e depurar módulos C# para Azure IoT Edge

Você pode transformar sua lógica de negócios em módulos do Azure IoT Edge. Este artigo mostra como usar o VS Code (Visual Studio Code) como ferramenta principal para desenvolver e depurar módulos C#.

## <a name="prerequisites"></a>Pré-requisitos

É possível usar um computador ou uma máquina virtual que execute Windows, macOS ou Linux como o computador de desenvolvimento. Um dispositivo do IoT Edge pode ser outro dispositivo físico.

Existem duas maneiras de depurar seu módulo C# no VS Code. Uma maneira é anexar um processo em um contêiner de módulo; a outra é a inicialização do código do módulo no modo de depuração. Se você não estiver familiarizado com os recursos de depuração do Visual Studio Code, leia sobre [Depuração](https://code.visualstudio.com/Docs/editor/debugging).

Instale primeiramente o Visual Studio Code e, em seguida, adicione as extensões necessárias a seguir:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Extensão do Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Extensão C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Extensão Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Para criar um módulo, você precisa do .NET para construir a pasta do projeto, o Docker para construir a imagem do módulo e um registro de contêiner para manter a imagem do módulo:
* [SDK do .NET Core 2.1](https://www.microsoft.com/net/download).
* [Docker Community Edition](https://docs.docker.com/install/) no computador de desenvolvimento. 
* [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) ou [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   * Você pode usar um registro do Docker local, em vez de um registro em nuvem, para fins de protótipo e teste. 

Para configurar o ambiente de desenvolvimento local para depurar, executar e testar a solução IoT Edge, você precisará da [Ferramenta de desenvolvimento do Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/). Instale o [Python (2.7/3.6) e o Pip](https://www.python.org/). Em seguida, instale **iotedgehubdev**, executando o comando abaixo no terminal.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

Para testar o módulo em um dispositivo, é necessário ter um hub IoT ativo com pelo menos uma ID de dispositivo do IoT Edge criada. Se você estiver executando o daemon do IoT Edge no computador de desenvolvimento, talvez seja necessário interromper o EdgeHub e o EdgeAgent antes de passar para a próxima etapa. 

## <a name="create-a-new-solution-with-c-module"></a>Crie uma nova solução com o módulo C#

Siga estas etapas para criar um módulo do IoT Edge baseado no .NET Core 2.1 usando o Visual Studio Code e a extensão do Azure IoT Edge. Primeiro, crie uma solução e, em seguida, gere o primeiro módulo nessa solução. Cada solução pode conter mais de um módulo. 

1. No Visual Studio Code, selecione **Exibir** > **Terminal Integrado**.
2. Selecione **Exibir** > **Paleta de Comandos**. 
3. Na paleta de comandos, insira e execute o comando **Azure IoT Edge: New IoT Edge Solution**.

   ![Executar a nova solução do IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

4. Navegue até a pasta na qual deseja criar a nova solução. Escolha **Selecionar pasta**. 
5. Digite um nome para a solução. 
6. Selecione **Módulo C#** como o modelo para o primeiro módulo da solução.
7. Digite um nome para o módulo. Escolha um nome exclusivo no registro de contêiner. 
8. Forneça o nome do repositório de imagens do módulo. O VS Code popula automaticamente o nome do módulo com **localhost:5000**. Substitua-o pelas informações de seu registro. Se você usa um registro local do Docker para testes, **localhost** é uma opção adequada. Se usar o Registro de Contêiner do Azure, utilize o servidor de início de sessão nas configurações do registro. O servidor de início de seção é semelhante ao **\<nome do registro\>.azurecr.io**. Substitua somente a parte de localhost da cadeia de caracteres, não exclua o nome do módulo.

   ![Fornecer o repositório de imagem do Docker](./media/how-to-develop-csharp-module/repository.png)

O VS Code usa as informações fornecidas, cria uma solução do IoT Edge e, em seguida, a carrega em uma nova janela.

   ![Exibir solução do IoT Edge](./media/how-to-develop-csharp-module/view-solution.png)

Há quatro itens na solução: 

* Uma pasta **.vscode** que contém configurações de depuração.
* Uma pasta **módulos** com subpastas para cada módulo. Neste ponto, você tem apenas um. No entanto, é possível adicionar mais na paleta de comandos usando o comando **Azure IoT Edge: Add IoT Edge Module**. 
* Um arquivo **.env** lista as variáveis de ambiente. Se o Registro de Contêiner do Azure for seu registro, você terá um nome de usuário e uma senha do Registro de Contêiner do Azure nele. 

   >[!NOTE]
   >O arquivo de ambiente será criado somente se você fornecer um repositório de imagens para o módulo. Se você aceitou os padrões do localhost para testar e depurar localmente, não será necessário declarar variáveis de ambiente. 

* Um arquivo **deployment.template.json** lista o novo módulo em conjunto com um módulo **tempSensor** de exemplo que simula dados que podem ser usados para teste. Para obter mais informações sobre o funcionamento dos manifestos de implantação, consulte [Saiba como usar manifestos de implantação para implantar módulos e estabelecer rotas](module-composition.md). 

## <a name="develop-your-module"></a>Desenvolver seu módulo

O código padrão do módulo C# que vem com a solução está localizado em **módulos** > ** [nome do seu módulo] ** > **Program.cs**. O módulo e o arquivo deployment.template.json são configurados de forma que você possa compilar a solução, enviá-la por push ao registro de contêiner e implantá-la em um dispositivo para iniciar os testes sem lidar com nenhum código. O módulo é criado para apenas receber entradas de uma fonte (nesse caso, o módulo tempSensor que simula dados) e redirecioná-las ao Hub IoT. 

Quando estiver pronto para personalizar o modelo C# com seu próprio código, use os [SDKs do Hub IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md) para compilar módulos que tratem das principais necessidades das soluções de IoT, como confiabilidade, segurança e gerenciamento do dispositivo. 

O suporte ao C# no VS Code é otimizado para desenvolvimento do .NET Core de plataforma cruzada. Saiba mais sobre [como trabalhar com C# no VS Code](https://code.visualstudio.com/docs/languages/csharp).

## <a name="launch-and-debug-module-code-without-container"></a>Iniciar e depurar código do módulo sem contêiner

O módulo C# do IoT Edge é um aplicativo .Net Core. E isso depende do SDK do dispositivo C# IoT do Azure. Como o módulo C# do IoT exige as configurações de ambiente para iniciar e executar, no código do módulo padrão, você inicializa um **ModuleClient** com configurações de ambiente e nome de entrada. Também precisa enviar ou encaminhar mensagens aos canais de entrada. O módulo C# padrão contém apenas um canal de entrada e o nome é **input1**.

### <a name="setup-iot-edge-simulator-for-single-module-app"></a>Configurar simulador do IoT Edge para aplicativo de módulo único

1. Para configurar e iniciar o simulador, na paleta de comandos do VS Code, digite e selecione **Azure IoT Edge: iniciar simulador de hub do IoT Edge para módulo único**. Também é necessário especificar os nomes de entrada para aplicativo de módulo único, digite **input1** e pressione Enter. O comando disparará a CLI **iotedgehubdev** e iniciará o simulador IoT Edge e um contêiner de módulo de utilitário de teste. Será possível ver as saídas abaixo no terminal integrado, se o simulador foi iniciado no modo de módulo único com êxito. Você também poderá ver um comando `curl` para ajudar a enviar mensagens. Você o usará mais tarde.

   ![Configurar simulador do IoT Edge para aplicativo de módulo único](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Você pode mover para o Docker Explorer e ver o status de execução do módulo.

   ![Status do módulo do simulador](media/how-to-develop-csharp-module/simulator-status.png)

   O contêiner **edgeHubDev** é o núcleo do simulador de IoT Edge local. Ele pode ser executado no computador de desenvolvimento sem o daemon de segurança do IoT Edge e fornecer configurações de ambiente ao aplicativo de módulo nativo ou contêineres de módulo. O contêiner de **entrada** expôs restAPIs para ajudar a conectar as mensagens ao canal de entrada de destino no módulo.

2. Na paleta de comandos do VS Code, digite e selecione **Azure IoT Edge: Definir credenciais do módulo para configurações do usuário** para definir as configurações do ambiente do módulo em `azure-iot-edge.EdgeHubConnectionString` e `azure-iot-edge.EdgeModuleCACertificateFile` nas configurações do usuário. É possível encontrar essas configurações de ambiente referenciadas em **.vscode** > **launch.json** e nas [configurações de usuário do VS Code](https://code.visualstudio.com/docs/getstarted/settings).

### <a name="build-module-app-and-debug-in-launch-mode"></a>Compilar aplicativo de módulo e depurar no modo de inicialização

1. No terminal integrado, altere o diretório para a pasta **CSharpModule**, execute o seguinte comando para criar o aplicativo .Net Core.

    ```cmd
    dotnet build
    ```

2. Navegue até `program.cs`. Adicione um ponto de interrupção neste arquivo.

3. Navegue até a exibição de depuração do VS Code: Exibição > Depuração. Selecione a configuração de depuração **Depuração local do ModuleName (.NET Core)** no menu suspenso. 

  ![Entrar no modo de depuração no VS Code](media/how-to-develop-csharp-module/debug-view.png)

4. Clique em **Iniciar depuração** ou pressione **F5**. Você iniciará a sessão de depuração.

   > [!NOTE]
   > Se o .Net Core `TargetFramework` não for consistente com o caminho do programa em `launch.json`. Você precisará atualizar manualmente o caminho do programa em `launch.json` para respeitar o `TargetFramework` no seu arquivo. csproj. Assim, esse VS Code é capaz de inicializar com êxito o programa.

5. No terminal integrado do VS Code, execute o seguinte comando para enviar uma mensagem **Olá, Mundo** para o módulo. Este é o comando mostrado nas etapas anteriores ao configurar o simulador do IoT Edge com êxito.

    ```cmd
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Se você estiver usando o Windows, certificando-se de que o shell do terminal integrado do VS Code é **Git Bash** ou **WSL Bash**. Não é possível executar o comando `curl` no PowerShell ou no Prompt de Comando. 
   
   > [!TIP]
   > Também é possível usar [PostMan](https://www.getpostman.com/) ou outras ferramentas de API para enviar mensagens por meio de `curl`.

6. Na exibição de Depuração do VS Code, você verá as variáveis no painel esquerdo. 

    ![Observar variáveis](media/how-to-develop-csharp-module/single-module-variables.png)

7. Para parar a sessão de depuração, clique no botão Parar ou pressione **Shift + F5**. Na paleta de comandos do VS Code, digite e selecione **Azure IoT Edge: Parar o simulador do IoT Edge** para interromper e limpar o simulador.

## <a name="build-module-container-for-debugging-and-debug-in-attach-mode"></a>Compilar contêiner de módulo para depuração e depuração em modo de anexação

A solução padrão contém dois módulos, um é um módulo de sensor de temperatura simulado e o outro é o módulo de pipe do C#. O sensor de temperatura simulado continua enviando mensagens ao módulo de pipe do C# e, em seguida, as mensagens são redirecionadas para o Hub IoT. Na pasta do módulo que você criou, há vários arquivos do Docker para diferentes tipos de contêineres. Use qualquer um dos arquivos que terminam com a extensão **.debug** para compilar o módulo para teste. Atualmente, os módulos C# dão suporte para depuração apenas nos contêineres amd64 do Linux em modo de anexação.

### <a name="setup-iot-edge-simulator-for-iot-edge-solution"></a>Configurar o simulador do IoT Edge para solução do IoT Edge

No computador de desenvolvimento, é possível iniciar o simulador do IoT Edge, em vez de instalar o daemon de segurança do IoT Edge para executar a solução do IoT Edge. 

1. No Device Explorer, no lado esquerdo, clique com o botão direito do mouse na ID do dispositivo do IoT Edge, selecione **Configurar simulador do IoT Edge** para iniciar o simulador com a cadeia de conexão do dispositivo.

2. Você pode ver que o simulador do IoT Edge foi configurado com êxito no terminal integrado.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Compilar e executar contêiner para depuração e depuração em modo de anexação

1. No VS Code, navegue até o arquivo `deployment.template.json`. Atualize a URL de imagem do módulo C#, incluindo **.debug** no final.

   ![Adicione **.debug** ao nome da imagem](./media/how-to-develop-csharp-module/image-debug.png)

2. Navegue até `program.cs`. Adicione um ponto de interrupção neste arquivo.

3. No explorador de arquivos do VS Code, selecione o arquivo `deployment.template.json` para a solução, no menu de contexto, clique em **Compilar e executar a solução do IoT Edge no simulador**. É possível observar todos os registros de contêiner de módulo na mesma janela. Você também pode navegar para o Docker Explorer para observar o status do contêiner.

   ![Observar variáveis](media/how-to-develop-csharp-module/view-log.png)

4. Navegue até a exibição de depuração do VS Code. Selecione o arquivo de configuração de depuração do módulo. O nome da opção de depuração deve ser semelhante a **ModuleName Remote Debug (.NET Core)**

   ![Selecionar configuração](media/how-to-develop-csharp-module/debug-config.png)

5. Selecione **Iniciar Depuração** ou selecione **F5**. Selecione o processo ao qual ele deverá ser anexado.

6. Na exibição de Depuração do VS Code, você verá as variáveis no painel esquerdo.

7. Para parar a sessão de depuração, clique no botão Parar ou pressione **Shift + F5**. E, na paleta de comandos do VS Code, digite e selecione **Azure IoT Edge: Parar simulador do IoT Edge**.

    > [!NOTE]
    > Este exemplo mostra como depurar módulo do IoT Edge do .NET Core em contêineres. Ele é baseado na versão de depuração do `Dockerfile.debug`, que inclui o VSDBG (depurador de linha de comando do .NET Core do Visual Studio) na imagem de contêiner durante a criação. Após depurar seus módulos C#, recomendamos que você use ou personalize diretamente o `Dockerfile` sem o VSDBG para os módulos do IoT Edge prontos para produção.


## <a name="next-steps"></a>Próximas etapas

Após compilar o módulo, saiba como [Implantar módulos do Azure IoT Edge do Visual Studio Code](how-to-deploy-modules-vscode.md).

Para desenvolver módulos para seus dispositivos do IoT Edge, consulte [Entender e usar os SDKs de Hub IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md).
