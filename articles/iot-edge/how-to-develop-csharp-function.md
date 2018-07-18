---
title: Depurar módulos do Functions no Azure IoT Edge | Microsoft Docs
description: Usar o Visual Studio Code para depurar C# do Azure Functions com o Azure IoT Edge
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/26/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5ee5d368bde0f176442a01ba266791f8a9c2cbc3
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37052820"
---
# <a name="use-visual-studio-code-to-develop-and-debug-azure-functions-for-azure-iot-edge"></a>Use o código do Visual Studio para desenvolver e depurar as funções do Azure para o Azure IoT Edge

Este artigo fornece instruções detalhadas de como usar o [Visual Studio (VS) Code](https://code.visualstudio.com/) para depurar o Azure Functions no IoT Edge.

## <a name="prerequisites"></a>pré-requisitos
Este artigo assume que você está usando um computador ou uma máquina virtual que executa Windows ou Linux como seu computador de desenvolvimento. O dispositivo do IoT Edge pode ser outro dispositivo físico ou você pode simular o dispositivo do IoT Edge em seu computador de desenvolvimento.

> [!NOTE]
> Este tutorial de depuração descreve como anexar um processo em um contêiner de módulo e depurá-lo com o VS Code. Você só pode depurar módulos C # em contêineres linux-amd64. Se você não estiver familiarizado com os recursos de depuração do código do Visual Studio, leia sobre [depuração](https://code.visualstudio.com/Docs/editor/debugging). 

Este artigo usa o código do Visual Studio como a ferramenta de desenvolvimento principal. Instale o VS Code, depois adicione as extensões necessárias: 

* [Visual Studio Code](https://code.visualstudio.com/) 
* [Extensão de borda do IoT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Extensão C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Extensão Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Para criar um módulo, você precisa do .NET para construir a pasta do projeto, o Docker para construir a imagem do módulo e um registro de contêiner para manter a imagem do módulo:
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/) no seu computador de desenvolvimento. 
* [ Registro do Contêiner do Azure ](https://docs.microsoft.com/azure/container-registry/) ou [ Hub do Docker ](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   > [!TIP]
   > Você pode usar um registro local do Docker para fins de protótipo e teste, em vez de um registro em nuvem. 

Para testar o módulo em um dispositivo, é necessário um hub IoT ativo pelo menos um dispositivo de borda de IoT. Se você quiser usar seu computador como um dispositivo IoT Edge, poderá fazê-lo seguindo as etapas nos tutoriais do [ Windows ](quickstart.md) ou [ Linux ](quickstart-linux.md). 

## <a name="create-a-new-solution-template"></a>Crie um novo modelo de solução

As etapas a seguir mostram como criar uma solução IoT Edge que contenha um módulo de função C #. Cada solução pode conter vários módulos.

1. No Visual Studio Code, selecione  **exibição** >  **Integrated Terminal**.
3. Selecione **Visualizar**  >  **Paleta de Comandos**.
4. Na paleta de comandos, digite e execute o comando **Edge IoT do Azure: nova solução do IoT Edge**. 

   ![Execute a nova solução de borda IoT](./media/how-to-develop-csharp-module/new-solution.png)

5. Navegue até a pasta em que você deseja criar a nova solução e clique em **Selecionar pasta**. 
6. Forneça um nome para sua solução. 
7. Escolha **Funções do Azure - C#** como o modelo do primeiro módulo da solução.
8. Forneça um nome para seu módulo. Escolha um nome único no seu registro de contêiner. 
9. Forneça o repositório de imagens para o módulo. O VS Code autopopula o nome do módulo, portanto, basta substituir **localhost: 5000** pelas suas próprias informações de registro. Se você usar um registro local do Docker para teste, o localhost estará bem. Se você usar o Azure Container Registry, use o servidor de login nas configurações do seu registro. O servidor de login se parece com **\< nome do registro \> .azurecr.io**.

O VS Code obtém as informações fornecidas, cria uma solução IoT Edge com um projeto Function e, em seguida, carrega-a em uma nova janela.

Dentro da solução você tem três itens: 

* Uma pasta **.vscode** que contém configurações de depuração.
* Uma pasta **módulos** que contém subpastas para cada módulo. Agora você só tem um, mas você pode adicionar mais através da paleta de comando com o comando **Azure IoT Edge: Adicionar IoT Edge Module**.
* Um arquivo **.env** lista suas variáveis de ambiente. Se você é ACR como seu registro, agora você tem um nome de usuário e senha ACR nele. 
* Um arquivo **deployment.template.json** lista seu novo módulo junto com um módulo **tempSensor** de amostra, que simula dados que podem ser usados para teste. Para obter mais informações sobre como os manifestos de implantação funcionam, consulte [. Entenda como os módulos do IoT Edge podem ser usados, configurados e reutilizados ](module-composition.md).

## <a name="build-your-iot-edge-function-module-for-debugging-purpose"></a>Criar o módulo Função do IoT Edge para fins de depuração
1. Para iniciar a depuração, você precisa usar o **Dockerfile.amd64.debug** para recriar a imagem do Docker e implantar a solução Edge novamente. No VS Code explorer, navegue até o arquivo `deployment.template.json`. Atualize a URL da imagem de função adicionando um `.debug` no final.

    ![Criar imagem de Depuração](./media/how-to-debug-csharp-function/build-debug-image.png)

2. Recompile a solução. Na paleta de comandos do VS Code, digite e execute o comando **Azure IoT Edge: solução Build IoT Edge**.
3. No gerenciador de dispositivos do Hub IoT do Azure, clique com o botão direito do mouse em uma ID de dispositivo IoT Edge e selecione **Criar implantação para dispositivo Edge**. Selecione o arquivo `deployment.json` na pasta `config`. Em seguida, você pode ver se a implantação foi criada com êxito com uma ID de implantação no terminal integrado do VS Code.

Você pode verificar o status de contêiner no gerenciador de Docker do VS Code ou executando o comando `docker images` no terminal.

## <a name="start-debugging-c-function-in-vs-code"></a>Começar a depurar a função C# no VS Code
1. O VS Code mantém as informações de configuração de depuração em um arquivo `launch.json` localizado em uma pasta `.vscode` no espaço de trabalho. Esse arquivo `launch.json` foi gerado quando uma nova solução IoT Edge foi criada. Ele será atualizado sempre que você adicionar um novo módulo que oferece suporte à depuração. Navegue até a exibição de depuração e selecione o arquivo de configuração de depuração correspondente. O nome da opção de depuração deve ser como **ModuleName Remote Debug (.NET Core)** ![ Selecione a configuração de depuração ](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. Navegue até `run.csx`. Adicione um ponto de interrupção na função.
3. Clique no botão **Iniciar Depuração** ou pressione **F5** e selecione o processo ao qual anexar.
4. Na exibição Depuração do VS Code, você pode ver as variáveis no painel esquerdo. 


> [!NOTE]
> O exemplo acima mostra como depurar a função .Net Core IoT Edge em contêineres. Ele é baseado na versão de depuração do `Dockerfile.amd64.debug`, que inclui o VSDBG (o depurador da linha de comando do .NET Core) na imagem de contêiner durante a criação. Recomendamos que você use ou personalize diretamente o `Dockerfile` sem o VSDBG para a função do IoT Edge pronta para produção após concluir a depuração da sua função do C#.

## <a name="next-steps"></a>Próximas etapas

Uma vez que seu módulo compilado, saiba como [módulos de implantar o Azure IoT borda de código do Visual Studio](how-to-deploy-modules-vscode.md)
