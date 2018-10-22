---
title: Depurar módulos de função do Azure no Azure IoT Edge | Microsoft Docs
description: Use o Visual Studio Code para depurar funções do Azure em C# com o Azure IoT Edge
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/26/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4b4f9bd1c7390d64a0db08b55bfb777498a10cb0
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382698"
---
# <a name="use-visual-studio-code-to-develop-and-debug-azure-functions-for-azure-iot-edge"></a>Use o Visual Studio Code para desenvolver e depurar funções do Azure para o Azure IoT Edge

Este artigo mostra como usar o [VS Code (Visual Studio Code)](https://code.visualstudio.com/) para depurar suas funções do Azure IoT Edge.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo presume que você esteja usando um computador ou uma máquina virtual que executa Windows ou Linux como seu computador de desenvolvimento. Seu dispositivo do IoT Edge pode ser outro dispositivo físico. Você também pode simular seu dispositivo do IoT Edge no computador de desenvolvimento.

> [!NOTE]
> Este artigo sobre depuração demonstra como anexar um processo em um contêiner de módulo e depurá-lo com o VS Code. Só é possível depurar funções de C# em contêineres amd64 Linux. Se você não estiver familiarizado com os recursos de depuração do Visual Studio Code, leia sobre [Depuração](https://code.visualstudio.com/Docs/editor/debugging). 

Este artigo usa o código do Visual Studio como a ferramenta de desenvolvimento principal. Instale o VS Code. Em seguida, adicione as extensões necessárias: 

* [Visual Studio Code](https://code.visualstudio.com/) 
* [Extensão do Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Extensão C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Extensão Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Para criar um módulo, você precisa do .NET para construir a pasta do projeto, o Docker para construir a imagem do módulo e um registro de contêiner para manter a imagem do módulo:

* [SDK do .NET Core 2.1](https://www.microsoft.com/net/download)
* [Docker Community Edition](https://docs.docker.com/install/) no computador de desenvolvimento 
* [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) ou [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   * Você pode usar um registro do Docker local, em vez de um registro em nuvem, para fins de protótipo e teste. 

Para testar o módulo em um dispositivo, é necessário um hub IoT ativo com pelo menos um dispositivo do IoT Edge. Para usar seu computador como um dispositivo do IoT Edge, siga as etapas no guia de início rápido para [Windows](quickstart.md) ou [Linux](quickstart-linux.md). 

## <a name="create-a-new-solution-template"></a>Crie um novo modelo de solução

Siga estas etapas para criar uma solução do IoT Edge com um módulo de função em C#. Cada solução pode ter vários módulos.

1. No Visual Studio Code, selecione **Exibir** > **Terminal Integrado**.
3. Selecione **Exibir** > **Paleta de Comandos**.
4. Na paleta de comandos, insira e execute o comando **Azure IoT Edge: New IoT Edge Solution**. 

   ![Execute a nova solução do IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

5. Navegue até a pasta na qual deseja criar a nova solução. Escolha **Selecionar pasta**. 
6. Digite um nome para a solução. 
7. Escolha **Funções do Azure - C#** como o modelo do primeiro módulo da solução.
8. Digite um nome para o módulo. Escolha um nome exclusivo no registro de contêiner. 
9. Forneça o repositório de imagem para o módulo. O VS Code popula automaticamente o nome do módulo com **localhost:5000**. Substitua-o pelas informações de seu registro. Se você usa um registro local do Docker para testes, **localhost** é uma opção adequada. Se usar o Registro de Contêiner do Azure, utilize o servidor de início de sessão nas configurações do registro. O servidor de início de seção é semelhante ao **\<nome do registro\>.azurecr.io**. Substitua somente a parte de localhost da cadeia de caracteres, não exclua o nome do módulo.

   ![Fornecer o repositório de imagem do Docker](./media/how-to-develop-csharp-function/repository.png)

O VS Code obtém as informações fornecidas, cria uma solução do IoT Edge com um projeto do Azure Functions e, em seguida, carrega-a em uma nova janela.

Há quatro itens na solução: 

* Uma pasta **.vscode** que contém configurações de depuração.
* Uma pasta **módulos** com subpastas para cada módulo. Neste ponto, você tem apenas um. No entanto, é possível adicionar mais por meio da paleta de comandos usando o comando **Azure IoT Edge: Add IoT Edge Module**.
* Um arquivo **.env** lista as variáveis de ambiente. Se o Registro de Contêiner do Azure for seu registro, você terá um nome de usuário e uma senha do Registro de Contêiner do Azure nele. 

   >[!NOTE]
   >O arquivo de ambiente será criado somente se você fornecer um repositório de imagens para o módulo. Se você aceitou os padrões do localhost para testar e depurar localmente, não será necessário declarar variáveis de ambiente. 

* Um arquivo **deployment.template.json** lista o novo módulo em conjunto com um módulo **tempSensor** de exemplo que simula dados que podem ser usados para teste. Para obter mais informações sobre o funcionamento dos manifestos de implantação, consulte [Saiba como usar manifestos de implantação para implantar módulos e estabelecer rotas](module-composition.md).

## <a name="develop-your-module"></a>Desenvolver seu módulo

O código do Azure Functions padrão que vem com a solução está localizado em **módulos** > [nome do seu módulo] > **EdgeHubTrigger-Csharp** > **run.csx**. O módulo e o arquivo deployment.template.json são configurados de forma que você possa compilar a solução, enviá-la por push ao registro de contêiner e implantá-la em um dispositivo para iniciar os testes sem lidar com nenhum código. O módulo é criado para apenas receber entradas de uma fonte (nesse caso, o módulo tempSensor que simula dados) e redirecioná-las ao Hub IoT. 

Quando estiver pronto para personalizar o modelo do Azure Functions com seu próprio código, use os [SDKs do Hub IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md) para compilar módulos que tratem das principais necessidades das soluções de IoT, como confiabilidade, segurança e gerenciamento do dispositivo. 

## <a name="build-your-module-for-debugging"></a>Criar o módulo para depuração
1. Para iniciar a depuração, use **Dockerfile.amd64.debug** para recriar a imagem do Docker e implantar a solução do IoT Edge novamente. No gerenciador do VS Code, navegue até o arquivo `deployment.template.json`. Atualize a URL da imagem de função adicionando `.debug` ao final.

    ![Compilar imagem de depuração](./media/how-to-debug-csharp-function/build-debug-image.png)

2. Recompile a solução. Na paleta de comandos do VS Code, insira e execute o comando **Azure IoT Edge: Build IoT Edge solution**.
3. No gerenciador de dispositivos do Hub IoT do Azure, clique com o botão direito do mouse em uma ID de dispositivo do IoT Edge e selecione **Criar implantação para dispositivo Edge**. Selecione o `deployment.json` arquivo o `config` pasta. Você verá a implantação criada com êxito, com uma ID de implantação em um terminal integrado com o VS Code.

Verifique o status do contêiner no gerenciador de Docker do VS Code ou executando o comando `docker ps` no terminal.

## <a name="start-debugging-c-functions-in-vs-code"></a>Começar a depurar funções C# no VS Code
1. O VS Code mantém as informações de configuração de depuração em um arquivo `launch.json` localizado em uma pasta `.vscode` no workspace. Esse arquivo `launch.json` foi gerado quando uma nova solução IoT Edge foi criada. Ele será atualizado sempre que você adicionar um novo módulo que oferece suporte à depuração. Navegue até a exibição de depuração. Selecione o arquivo de configuração de depuração correspondente. O nome da opção de depuração deve ser semelhante a **ModuleName Remote Debug (.NET Core)**.

   ![Selecione a configuração de depuração](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. Navegue até `run.csx`. Adicione um ponto de interrupção na função.
3. Selecione **Iniciar Depuração** ou selecione **F5**. Selecione o processo ao qual ele deverá ser anexado.
4. Na exibição de Depuração do VS Code, você verá as variáveis no painel esquerdo. 


> [!NOTE]
> Este exemplo mostra como depurar funções do IoT Edge do .NET Core em contêineres. Ele é baseado na versão de depuração do `Dockerfile.amd64.debug`, que inclui o depurador de linha de comando do .NET Core VSDBG na imagem de contêiner durante a criação. Recomendamos que você use ou personalize diretamente o `Dockerfile` sem o VSDBG para funções do IoT Edge prontas para produção após depurar suas funções de C#.

## <a name="next-steps"></a>Próximas etapas

Após compilar o módulo, saiba como [Implantar módulos do Azure IoT Edge do Visual Studio Code](how-to-deploy-modules-vscode.md).

Para desenvolver módulos para seus dispositivos do IoT Edge, consulte [Entender e usar os SDKs de Hub IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md).
