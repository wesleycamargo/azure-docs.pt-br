---
title: Usar o Visual Studio Code para depurar um módulo C# com o Azure IoT Edge | Microsoft Docs
description: Depurar um módulo C# com o Azure IoT Edge no Visual Studio Code.
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: c2a1acd2c249bdbc92119bc92f055b095f318f00
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2018
---
# <a name="use-visual-studio-code-to-debug-a-c-module-with-azure-iot-edge"></a>Usar o Visual Studio Code para depurar um módulo C# com o Azure IoT Edge
Este artigo fornece instruções detalhadas de como usar o [Visual Studio Code](https://code.visualstudio.com/) como a ferramenta de desenvolvimento principal para depurar seus módulos do Azure IoT Edge.

## <a name="prerequisites"></a>pré-requisitos
Este tutorial assume que você está usando um computador ou uma máquina virtual que executa Windows ou Linux como seu computador de desenvolvimento. O dispositivo do IoT Edge pode ser outro dispositivo físico ou você pode simular dispositivo do IoT Edge no computador de desenvolvimento.

Antes de iniciar essas diretrizes, conclua o tutorial a seguir:
- [Desenvolver uma solução IoT Edge com vários módulos no Visual Studio Code](tutorial-multiple-modules-in-vscode.md)

Depois de concluir o tutorial anterior, os seguintes itens deverão estar estão prontos:
- Um registro do Docker local em execução no computador de desenvolvimento. É recomendável usar um registro do Docker local para fins de protótipo e teste. Você pode atualizar o registro de contêiner no arquivo `module.json` em cada pasta de módulo.
- O espaço de trabalho do projeto da solução IoT Edge com uma subpasta do módulo C# nele.
- O arquivo `Program.cs` com o código mais recente do módulo.
- Um tempo de execução do Edge em execução no computador de desenvolvimento.

## <a name="build-your-iot-edge-c-module-for-debugging"></a>Criar o módulo C# do IoT Edge para depuração
1. Para iniciar a depuração, você precisa usar o **Dockerfile.amd64.debug** para recriar a imagem do Docker e implantar a solução Edge novamente. No VS Code explorer, navegue até o arquivo `deployment.template.json`. Atualize a URL da imagem de função adicionando um `.debug` no final.

2. Recompile a solução. Na paleta de comandos do VS Code, digite e execute o comando **Edge: compilar solução IoT Edge**.

3. No gerenciador de dispositivos do Hub IoT do Azure, clique com o botão direito do mouse em uma ID de dispositivo IoT Edge e selecione **Criar implantação para dispositivo Edge**. Selecione `deployment.json` na pasta `config`. Em seguida, você pode ver se a implantação foi criada com êxito com uma ID de implantação no terminal integrado do VS Code.

> [!NOTE]
> Você pode verificar o status de contêiner no gerenciador de Docker do VS Code ou executando o comando `docker images` no terminal.

## <a name="start-debugging-c-module-in-vs-code"></a>Começar a depurar o módulo C# no VS Code
1. O VS Code mantém as informações de configuração de depuração em um arquivo `launch.json` localizado em uma pasta `.vscode` no espaço de trabalho. Esse arquivo `launch.json` foi gerado ao criar uma nova solução IoT Edge. E será atualizado sempre que você adicionar um novo módulo que oferece suporte à depuração. Navegue até a exibição de depuração e selecione o arquivo de configuração de depuração correspondente.
    ![Selecionar configuração de depuração](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. Navegue até `program.cs`. Adicione um ponto de interrupção neste arquivo.

3. Clique no botão Iniciar Depuração ou pressione **F5** e selecione o processo ao qual anexar.

4. Na exibição Depuração do VS Code, você pode ver as variáveis no painel esquerdo. 

> [!NOTE]
> O exemplo anterior mostra como depurar módulo do IoT Edge do .NET Core em contêineres. Ele é baseado na versão de depuração do `Dockerfile.debug`, que inclui o VSDBG (o depurador da linha de comando do .NET Core) na imagem de contêiner durante a criação. Após concluir a depuração dos módulos C#, recomendamos que você use ou personalize diretamente o `Dockerfile` sem o VSDBG para os módulos do IoT Edge prontos para produção.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um módulo no IoT Edge e o implantou para depuração. Você começou a depurá-lo no VS Code. Para saber mais sobre outros cenários quando você estiver desenvolvendo o Azure IoT Edge no VS Code, consulte: 

> [!div class="nextstepaction"]
> [Desenvolver uma solução IoT Edge com vários módulos no Visual Studio Code](tutorial-multiple-modules-in-vscode.md)

