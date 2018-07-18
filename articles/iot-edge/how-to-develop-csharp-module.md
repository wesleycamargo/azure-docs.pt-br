---
title: Depurar módulos C# para Azure IoT Edge | Microsoft Docs
description: Usar Visual Studio Code para desenvolver, compilar e depurar um módulo C# para Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/27/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: c237b1eb9874357afa922f809109cf8f2181561e
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048179"
---
# <a name="use-visual-studio-code-to-develop-and-debug-c-modules-for-azure-iot-edge"></a>Usar Visual Studio Code para desenvolver e depurar módulos C# para Azure IoT Edge

É possível enviar a lógica de negócios para operar na borda, transformando-a em módulos para Azure IoT Edge. Este artigo fornece instruções detalhadas para usar o VS Code (Visual Studio Code) como a principal ferramenta de desenvolvimento para desenvolver módulos C#.

## <a name="prerequisites"></a>pré-requisitos
Este artigo assume que você está usando um computador ou uma máquina virtual que executa Windows ou Linux como seu computador de desenvolvimento. O dispositivo do IoT Edge pode ser outro dispositivo físico ou você pode simular dispositivo do IoT Edge no computador de desenvolvimento.

> [!NOTE]
> Este tutorial de depuração descreve como anexar um processo em um contêiner de módulo e depurá-lo com o VS Code. Só é possível depurar C# do Functions em contêineres linux-amd64. Se você não estiver familiarizado com os recursos de depuração do Visual Studio Code, leia sobre [Depuração](https://code.visualstudio.com/Docs/editor/debugging). 

Como este artigo usa o Visual Studio Code como a principal ferramenta de desenvolvimento, instale o VS Code e adicione as extensões necessárias:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Extensão do Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Extensão C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Extensão do Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Para criar um módulo, é necessário o .NET, que compila a pasta de projeto, Docker para compilar a imagem de módulo e um registro de contêiner para manter a imagem de módulo:
* [SDK do .NET Core 2.1](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/) no computador de desenvolvimento. 
* [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) ou [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   > [!TIP]
   > Você pode usar um registro local do Docker para fins de protótipo e teste, em vez de um registro em nuvem. 

Para testar o módulo em um dispositivo, é necessário um hub IoT ativo com pelo menos um dispositivo do IoT Edge. Se você quiser usar o computador como um dispositivo do IoT Edge, poderá fazer isso seguindo as etapas nos tutoriais do [Windows](quickstart.md) ou [Linux](quickstart-linux.md) 

## <a name="create-a-new-solution-template"></a>Crie um novo modelo de solução

As etapas a seguir mostram como criar um módulo do IoT Edge baseado no .NET Core 2.0 usando o Visual Studio Code e a extensão do Azure IoT Edge. Você começa criando uma solução e, em seguida, gerando o primeiro módulo nessa solução. Cada solução pode conter vários módulos. 

1. No Visual Studio Code, selecione **Exibir** > **Terminal Integrado**.
3. Selecione **Exibir** > **Paleta de Comandos**. 
4. Na paleta de comandos, digite e execute o comando **Azure IoT Edge: Nova solução do IoT Edge**.

   ![Executar a nova solução do IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

5. Navegue até a pasta em que você deseja criar a nova solução e clique em **Selecionar pasta**. 
6. Forneça um nome para a solução. 
7. Escolha **Módulo C#** como o modelo para o primeiro módulo na solução.
8. Forneça um nome para o módulo. Escolha um nome exclusivo no registro de contêiner. 
9. Forneça o repositório de imagem para o módulo. O VS Code preenche automaticamente o nome do módulo, portanto, basta substituir **localhost: 5000** pelas suas próprias informações de registro. Se você usar um registro local do Docker para teste, então o localhost está correto. Se usar o Registro de Contêiner do Azure, utilize o servidor de início de sessão nas configurações do registro. O servidor de início de seção é semelhante ao **\<nome do registro\>.azurecr.io**.

O VS Code obtém as informações fornecidas, cria uma solução do IoT Edge e carrega-as em uma nova janela.

   ![Exibir solução do IoT Edge](./media/how-to-develop-csharp-module/view-solution.png)

Dentro da solução há três itens: 
* Uma pasta **.vscode** que contém configurações de depuração.
* Uma pasta **módulos** que contém subpastas para cada módulo. No momento você só tem um, mas é possível adicionar mais na paleta de comandos com o comando **Azure IoT Edge: Adicionar módulo do IoT Edge**. 
* Um arquivo **.env** lista as variáveis de ambiente. Se estiver definido o ACR como seu registro, então haverá um nome de usuário e senha ACR. 
* Um arquivo **deployment.template.json** lista o novo módulo junto com um módulo **tempSensor** de exemplo que simula dados que podem ser usados para teste. Para obter mais informações sobre como os manifestos de implantação funcionam, consulte [Entender como os módulos do IoT Edge podem ser utilizados, configurados e reutilizados](module-composition.md).

## <a name="build-and-deploy-your-module-for-debugging"></a>Compilar e implantar o módulo para depuração

Em cada pasta de módulo há vários arquivos do Docker para diferentes tipos de contêiner. É possível usar qualquer um desses arquivos que terminem com a extensão **.debug** para compilar o módulo para teste. Atualmente, os módulos C# dão suporte a depuração em contêineres linux-amd64.

1. No VS Code, navegue até o arquivo `deployment.template.json`. Atualize a URL da imagem de função, adicionando **.debug** no final.

   ![Adicionar .debug ao nome da imagem](./media/how-to-develop-csharp-module/image-debug.png)

2. Na paleta de comandos do VS Code, digite e execute o comando **Edge: Compilar solução do IoT Edge**.
3. Selecione o arquivo `deployment.template.json` para a solução na paleta de comandos. 
4. No gerenciador de Dispositivos do Hub IoT, clique com o botão direito do mouse em uma ID do dispositivo do IoT Edge e, em seguida, selecione **Criar implantação para dispositivo do IoT Edge**. 
5. Abra a pasta **config** da solução e, em seguida, selecione o arquivo`deployment.json`. Clique em **Selecionar manifesto de implantação do Edge**. 

Em seguida, você pode ver se a implantação foi criada com êxito com uma ID de implantação no terminal integrado do VS Code.

Você pode verificar o status de contêiner no gerenciador de Docker do VS Code ou executando o comando `docker ps` no terminal.

## <a name="start-debugging-c-module-in-vs-code"></a>Começar a depurar o módulo C# no VS Code
O VS Code mantém as informações de configuração de depuração em um arquivo `launch.json` localizado em uma pasta `.vscode` no espaço de trabalho. Esse arquivo `launch.json` foi gerado quando uma nova solução IoT Edge foi criada. Ele será atualizado sempre que você adicionar um novo módulo que oferece suporte à depuração. 

1. Navegue até a exibição de depuração do VS Code e selecione o arquivo de configuração de depuração para o módulo. O nome da opção de depuração deve ser como **ModuleName Remote Debug (.NET Core)** ![ Selecione a configuração de depuração ](./media/how-to-develop-csharp-module/debug-config.png)

2. Navegue até `program.cs`. Adicione um ponto de interrupção neste arquivo.

3. Clique no botão **Iniciar Depuração** ou pressione **F5** e selecione o processo ao qual anexar.

4. Na exibição Depuração do VS Code, você pode ver as variáveis no painel esquerdo. 

> [!NOTE]
> O exemplo anterior mostra como depurar módulo do IoT Edge do .NET Core em contêineres. Ele é baseado na versão de depuração do `Dockerfile.debug`, que inclui o VSDBG (o depurador da linha de comando do .NET Core) na imagem de contêiner durante a criação. Após concluir a depuração dos módulos C#, recomendamos que você use ou personalize diretamente o `Dockerfile` sem o VSDBG para os módulos do IoT Edge prontos para produção.

## <a name="next-steps"></a>Próximas etapas

Após compilar o módulo, saiba como [Implantar módulos do Azure IoT Edge do Visual Studio Code](how-to-deploy-modules-vscode.md)

