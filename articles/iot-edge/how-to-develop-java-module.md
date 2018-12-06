---
title: Depurar módulos Java para o Azure IoT Edge | Microsoft Docs
description: Usar o Visual Studio Code para desenvolver e depurar módulos Java para o Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: philmea
ms.author: xshi
ms.date: 09/21/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 3e50bf42076132f69fcb655da61a790fe207b949
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52444402"
---
# <a name="use-visual-studio-code-to-develop-and-debug-java-modules-for-azure-iot-edge"></a>Usar o Visual Studio Code para desenvolver e depurar módulos Java para o Azure IoT Edge

É possível enviar a lógica de negócios para operar na borda, transformando-a em módulos para Azure IoT Edge. Este artigo fornece instruções detalhadas para usar o VS Code (Visual Studio Code) como a principal ferramenta de desenvolvimento para desenvolver e depurar módulos Java.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo assume que você usa um computador ou uma máquina virtual executando Windows, macOS ou Linux como o computador de desenvolvimento. Seu dispositivo do IoT Edge pode ser outro dispositivo físico.

> [!NOTE]
> Este artigo de depuração demonstra duas maneiras típicas de depurar o módulo Java no VS Code. Uma maneira é anexar um processo em um contêiner de módulo, enquanto a outra é anexar o código do módulo no modo de depuração. Se você não estiver familiarizado com os recursos de depuração do Visual Studio Code, leia sobre [Depuração](https://code.visualstudio.com/Docs/editor/debugging).

Como este artigo usa o Visual Studio Code como a principal ferramenta de desenvolvimento, instale o VS Code e adicione as extensões necessárias:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Extensão do Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Pacote de extensão Java para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
* [Extensão do Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Para criar um módulo, você precisa do Java e Maven para criar e executar o código do módulo, do Docker para criar a imagem do módulo e um registro de contêiner para armazenar a imagem do módulo:
* [Java SE Development Kit 10](https://aka.ms/azure-jdks), e [defina a variável de ambiente `JAVA_HOME`](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) para apontar para a sua instalação do JDK.
* [Maven](https://maven.apache.org/)
* [Docker](https://docs.docker.com/engine/installation/)
* [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) ou [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   * Você pode usar um registro local do Docker para fins de protótipo e teste, em vez de um registro em nuvem. 

Para configurar o ambiente de desenvolvimento local para depurar, executar e testar a solução IoT Edge, você precisará da [Ferramenta de desenvolvimento do Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/). Instale o [Python (2.7/3.6) e o Pip](https://www.python.org/). Em seguida, instale **iotedgehubdev**, executando o comando abaixo no terminal.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

Para testar o módulo em um dispositivo, é necessário ter um hub IoT ativo com pelo menos uma ID de dispositivo do IoT Edge criada. Se você estiver executando o daemon do IoT Edge no computador de desenvolvimento, talvez seja necessário interromper o EdgeHub e o EdgeAgent antes de passar para a próxima etapa. 


## <a name="create-a-new-solution-template"></a>Crie um novo modelo de solução

As etapas a seguir mostram como criar um módulo do IoT Edge baseado em Java usando o Visual Studio Code e a extensão do Azure IoT Edge. Você começa criando uma solução e, em seguida, gerando o primeiro módulo nessa solução. Cada solução pode conter vários módulos. 

1. No Visual Studio Code, selecione **Exibir** > **Terminal Integrado**.

3. No Visual Studio Code, selecione **Exibir** > **Paleta de Comandos**. 
4. Na paleta de comandos, digite e execute o comando **Azure IoT Edge: Nova solução do IoT Edge**.

   ![Executar a nova solução do IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

5. Navegue até a pasta em que você deseja criar a nova solução e clique em **Selecionar pasta**. 
6. Forneça um nome para a solução. 
7. Escolha **Módulo Java** como o modelo para o primeiro módulo na solução.
8. Forneça um nome para o módulo. Escolha um nome exclusivo no registro de contêiner. 
8. Forneça um valor para groupId ou aceite o padrão **com.edgemodule**.
9. Forneça o repositório de imagem para o módulo. O VS Code preenche automaticamente o nome do módulo, portanto, basta substituir **localhost: 5000** pelas suas próprias informações de registro. Se você usar um registro local do Docker para teste, então o localhost está correto. Se usar o Registro de Contêiner do Azure, utilize o servidor de início de sessão nas configurações do registro. O servidor de início de seção é semelhante ao **\<nome do registro\>.azurecr.io**. Substitua somente a parte de localhost da cadeia de caracteres, não exclua o nome do módulo. A cadeia de caracteres final se parece com \<nome do registro \>.azurecr.io /\<modulename\>.

   ![Fornecer o repositório de imagem do Docker](./media/how-to-develop-node-module/repository.png)

O VS Code obtém as informações fornecidas, cria uma solução do IoT Edge e carrega-as em uma nova janela.

Dentro da solução há três itens: 
* Uma pasta **.vscode** que contém configurações de depuração.
* Uma pasta **módulos** que contém subpastas para cada módulo. No momento você só tem um, mas é possível adicionar mais na paleta de comandos com o comando **Azure IoT Edge: Adicionar módulo do IoT Edge**. 
* Um arquivo **.env** lista as variáveis de ambiente. Se o Registro de Contêiner do Azure for seu registro, você terá um nome de usuário e uma senha do Registro de Contêiner do Azure nele.

   >[!NOTE]
   >O arquivo de ambiente será criado somente se você fornecer um repositório de imagens para o módulo. Se você aceitou os padrões do localhost para testar e depurar localmente, não será necessário declarar variáveis de ambiente. 

* Um arquivo **deployment.template.json** lista o novo módulo junto com um módulo **tempSensor** de exemplo que simula dados que podem ser usados para teste. Para obter mais informações sobre como os manifestos de implantação funcionam, consulte [Entender como os módulos do IoT Edge podem ser utilizados, configurados e reutilizados](module-composition.md).
* Um arquivo **deployment.debug.template.json** contêiner a versão de depuração de suas imagens de módulo com opções de contêiner adequadas.


## <a name="develop-your-module"></a>Desenvolver seu módulo

O código Java padrão que vem com a solução está localizado em **modules > [nome do seu módulo] > src > main > java > com > edgemodulemodules > App.java**. O módulo e o arquivo deployment.template.json são configurados de forma que você possa compilar a solução, enviá-la por push ao registro de contêiner e implantá-la em um dispositivo para iniciar os testes sem lidar com nenhum código. O módulo é criado para apenas receber entradas de uma fonte (nesse caso, o módulo tempSensor que simula dados) e redirecioná-las ao Hub IoT. 

Quando você estiver pronto para personalizar o modelo Java com seu próprio código, use os [SDKs do Hub IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md) para criar módulos que atendam às principais necessidades de soluções de IoT, como segurança, gerenciamento de dispositivos e confiabilidade. 

O Visual Studio Code é compatível com Java. Saiba mais sobre [como trabalhar com Java no VS Code](https://code.visualstudio.com/docs/java/java-tutorial).

## <a name="launch-and-debug-module-code-without-container"></a>Iniciar e depurar código do módulo sem contêiner
O módulo Java do IoT Edge depende do SDK do dispositivo Java do Azure IoT. No código do módulo padrão, inicialize um **ModuleClient** com configurações de ambiente e nome de entrada, o que significa que o módulo Java do IoT Edge exige as configurações de ambiente para ser iniciado e executado, e você também precisa enviar ou rotear as mensagens para os canais de entrada. Seu módulo Java padrão contém apenas um canal de entrada e o nome é **input1**.

### <a name="setup-iot-edge-simulator-for-iot-edge-solution"></a>Configurar o simulador do IoT Edge para solução do IoT Edge

No computador de desenvolvimento, é possível iniciar o simulador do IoT Edge, em vez de instalar o daemon de segurança do IoT Edge para executar a solução do IoT Edge. 

1. No Device Explorer, no lado esquerdo, clique com o botão direito do mouse na ID do dispositivo do IoT Edge, selecione **Configurar simulador do IoT Edge** para iniciar o simulador com a cadeia de conexão do dispositivo.

2. Você pode ver que o simulador do IoT Edge foi configurado com êxito no terminal integrado.

### <a name="setup-iot-edge-simulator-for-single-module-app"></a>Configurar simulador do IoT Edge para aplicativo de módulo único

1. Para configurar e iniciar o simulador, na paleta de comandos do VS Code, digite e selecione **Azure IoT Edge: iniciar simulador de hub do IoT Edge para módulo único**. Também é necessário especificar os nomes de entrada para aplicativo de módulo único, digite **input1** e pressione Enter. O comando disparará a CLI **iotedgehubdev** e iniciará o simulador IoT Edge e um contêiner de módulo de utilitário de teste. Será possível ver as saídas abaixo no terminal integrado, se o simulador foi iniciado no modo de módulo único com êxito. Você também poderá ver um comando `curl` para ajudar a enviar mensagens. Você o usará mais tarde.

   ![Configurar simulador do IoT Edge para aplicativo de módulo único](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Você pode mover para o Docker Explorer e ver o status de execução do módulo.

   ![Status do módulo do simulador](media/how-to-develop-csharp-module/simulator-status.png)

   O contêiner **edgeHubDev** é o núcleo do simulador de IoT Edge local. Ele pode ser executado no computador de desenvolvimento sem o daemon de segurança do IoT Edge e fornecer configurações de ambiente ao aplicativo de módulo nativo ou contêineres de módulo. O contêiner de **entrada** expôs restAPIs para ajudar a conectar as mensagens ao canal de entrada de destino no módulo.

2. Na paleta de comandos do VS Code, digite e selecione **Azure IoT Edge: Definir credenciais do módulo para configurações do usuário** para definir as configurações do ambiente do módulo em `azure-iot-edge.EdgeHubConnectionString` e `azure-iot-edge.EdgeModuleCACertificateFile` nas configurações do usuário. É possível encontrar essas configurações de ambiente referenciadas em **.vscode** > **launch.json** e nas [configurações de usuário do VS Code](https://code.visualstudio.com/docs/getstarted/settings).

### <a name="debug-java-module-in-launch-mode"></a>Depurar o módulo Java no modo de inicialização

2. Navegue até `App.java`. Adicione um ponto de interrupção neste arquivo.

3. Navegue para a visualização de depuração do VS Code. Selecione a configuração de depuração **ModuleName Local Debug (java)**. 

4. Clique em **Iniciar depuração** ou pressione **F5**. Você iniciará a sessão de depuração.

5. No terminal integrado do VS Code, execute o seguinte comando para enviar uma mensagem **Olá, Mundo** para o módulo. Este é o comando mostrado nas etapas anteriores ao configurar o simulador do IoT Edge com êxito. Pode ser necessário criar ou alternar para outro terminal integrado se o atual estiver bloqueado.

    ```cmd
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Se você estiver usando o Windows, certificando-se de que o shell do terminal integrado do VS Code é **Git Bash** ou **WSL Bash**. Não é possível executar o comando `curl` no PowerShell ou no Prompt de Comando. 
   
   > [!TIP]
   > Também é possível usar [PostMan](https://www.getpostman.com/) ou outras ferramentas de API para enviar mensagens por meio de `curl`.

6. Na exibição de Depuração do VS Code, você verá as variáveis no painel esquerdo. 

7. Para parar a sessão de depuração, clique no botão Parar ou pressione **Shift + F5**. Na paleta de comandos do VS Code, digite e selecione **Azure IoT Edge: parar o simulador do IoT Edge** para interromper e limpar o simulador.


## <a name="build-module-container-for-debugging-and-debug-in-attach-mode"></a>Compilar contêiner de módulo para depuração e depuração em modo de anexação

A solução padrão contém dois módulos, um deles é um módulo de sensor de temperatura simulado e o outro é o módulo de pipe Java. O sensor de temperatura simulado continua enviando mensagens para o módulo de pipe Java e as mensagens são redirecionadas para o Hub IoT. Na pasta do módulo que você criou, há vários arquivos do Docker para diferentes tipos de contêineres. Use qualquer um dos arquivos que terminam com a extensão **.debug** para compilar o módulo para teste. Por padrão, **deployment.debug.template.json** contém a versão de depuração da imagem. Atualmente, os módulos Java só são compatíveis com depuração em contêineres linux-amd64 e linux-arm32v7. Você pode alternar sua plataforma padrão do Azure IoT Edge na barra de status do Código VS.

### <a name="setup-iot-edge-simulator-for-iot-edge-solution"></a>Configurar o simulador do IoT Edge para solução do IoT Edge

No computador de desenvolvimento, é possível iniciar o simulador do IoT Edge, em vez de instalar o daemon de segurança do IoT Edge para executar a solução do IoT Edge. 

1. No Device Explorer, no lado esquerdo, clique com o botão direito do mouse na ID do dispositivo do IoT Edge, selecione **Configurar simulador do IoT Edge** para iniciar o simulador com a cadeia de conexão do dispositivo.

2. Você pode ver que o simulador do IoT Edge foi configurado com êxito no terminal integrado.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Compilar e executar contêiner para depuração e depuração em modo de anexação

1. Navegue até `App.java`. Adicione um ponto de interrupção neste arquivo.

2. No explorador de arquivos do VS Code, selecione o arquivo `deployment.debug.template.json` para a solução, no menu de contexto, clique em **Compilar e executar a solução do IoT Edge no simulador**. É possível observar todos os registros de contêiner de módulo na mesma janela. Você também pode navegar para o Docker Explorer para observar o status do contêiner.

5. Navegue até a exibição de depuração do VS Code. Selecione o arquivo de configuração de depuração do módulo. O nome da opção de depuração deve ser semelhante a **ModuleName Remote Debug (Java)**.

6. Selecione **Iniciar Depuração** ou selecione **F5**. Selecione o processo ao qual ele deverá ser anexado.

7. Na exibição de Depuração do VS Code, você verá as variáveis no painel esquerdo.

8. Para parar a sessão de depuração, clique no botão Parar ou pressione **Shift + F5**. E, na paleta de comandos do VS Code, digite e selecione **Azure IoT Edge: Parar simulador do IoT Edge**.

> [!NOTE]
> O exemplo anterior mostra como depurar os módulos Java IoT Edge nos contêineres. Ele adicionou portas expostas no contêiner de módulo createOptions. Após concluir a depuração dos módulos Java, é recomendável remover essas portas expostas de módulos do IoT Edge prontos para produção.

## <a name="next-steps"></a>Próximas etapas

Após compilar o módulo, saiba como [Implantar módulos do Azure IoT Edge do Visual Studio Code](how-to-deploy-modules-vscode.md)

Para desenvolver módulos para seus dispositivos do IoT Edge, consulte [Entender e usar os SDKs de Hub IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md).
