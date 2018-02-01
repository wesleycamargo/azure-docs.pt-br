---
title: "Usar o Visual Studio Code para depurar um módulo C# com o Azure IoT Edge | Microsoft Docs"
description: "Depurar um módulo C# com o Azure IoT Edge no VS Code"
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/06/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 1ab67cd8aaf59cde3157fcb877ce13f10cb432bb
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2018
---
# <a name="use-visual-studio-code-to-debug-c-module-with-azure-iot-edge"></a>Usar o Visual Studio Code para depurar um módulo C# com o Azure IoT Edge
Este artigo fornece instruções detalhadas de como usar o [Visual Studio Code](https://code.visualstudio.com/) como a ferramenta de desenvolvimento principal para depurar seus módulos do IoT Edge.

## <a name="prerequisites"></a>pré-requisitos
Este tutorial assume que você está usando um computador ou uma máquina virtual que executa Windows ou Linux como seu computador de desenvolvimento. O dispositivo do IoT Edge pode ser outro dispositivo físico ou você pode simular o dispositivo do IoT Edge em seu computador de desenvolvimento.

Conclua os tutoriais a seguir antes de iniciar estas diretrizes.
- [Use o Visual Studio Code para desenvolver o módulo C# com o Azure IoT Edge](how-to-vscode-develop-csharp-module.md)

Depois de concluir o tutorial anterior, os seguintes itens deverão estar estão prontos,
- Um registro do Docker local em execução no computador de desenvolvimento. É recomendável usar um registro do Docker local para fins de protótipo e teste.
- O arquivo `Program.cs` com o código mais recente do módulo de filtro.
- Um arquivo `deployment.json` atualizado para o módulo de sensor e o módulo de filtro.
- Um tempo de execução do Edge em execução no computador de desenvolvimento.

## <a name="build-your-iot-edge-module-for-debugging-purpose"></a>Criar o módulo do IoT Edge para fins de depuração
1. Para iniciar a depuração, você precisa usar o **dockerfile.debug** para recriar a imagem do Docker e implantar a solução do Edge novamente. No gerenciador do VS Code, clique na pasta Docker para abri-la. Em seguida, clique na pasta `linux-x64`, clique com o botão direito do mouse em **Dockerfile.debug** e clique em **Criar imagem do Docker do módulo do IoT Edge**.

    ![Compilar imagem de depuração](./media/how-to-debug-csharp-module/build-debug-image.png)

3. Na janela **Selecionar Pasta** procure por ou insira `./bin/Debug/netcoreapp2.0/publish`. Clique em **Selecionar pasta como EXE_DIR**.
4. Na caixa de texto pop-up na parte superior da janela do VS Code, insira o nome da imagem. Por exemplo: `<your container registry address>/filtermodule:latest`. Se você estiver implantando o registro local, deverá ser `localhost:5000/filtermodule:latest`.
5. Envie a imagem por push para o repositório do Docker. Use o comando **Edge: Push IoT Edge module Docker image** e insira a URL da imagem na caixa de texto pop-up na parte superior da janela do VS Code. Use a mesma URL de imagem usada na etapa acima.
6. Você pode reutilizar o `deployment.json` para reimplantar. Na paleta de comandos, digite e selecione **Edge: reiniciar Edge** para que o módulo de filtro seja executado com a versão de depuração.

## <a name="start-debugging-in-vs-code"></a>Iniciar a depuração no VS Code
1. Acesse a janela de depuração do VS Code. Pressione **F5** e selecione **IoT Edge(.Net Core)**

    ![Pressione F5](./media/how-to-debug-csharp-module/f5-debug-option.png)

2. Na seção `launch.json`, navegue até **Depurar módulo personalizado do IoT Edge (.NET Core)** e preencha o `<container_name>` em `pipeArgs`. Deverá ser `filtermodule` neste tutorial.

    ![Modificar pipeArgs](./media/how-to-debug-csharp-module/f5-debug-option.png)

3. Navegue até a Program.cs. Adicione um ponto de interrupção no `method static async Task<MessageResponse> FilterModule(Message message, object userContext)`.
4. Pressione **F5** novamente. E selecione o processo ao qual ele deverá ser anexado. Neste tutorial, o nome do processo deve ser `FilterModule.dll`

    ![Anexar processo](./media/how-to-debug-csharp-module/attach-process.png)

5. Na janela de depuração do VS Code, você pode ver as variáveis no painel esquerdo. 

> [!NOTE]
> O exemplo acima mostra como depurar módulo do IoT Edge do .Net Core em contêineres. Ele é baseado na versão de depuração do `Dockerfile.debug`, que inclui o VSDBG (o depurador da linha de comando do .NET Core) na imagem de contêiner durante a criação. Recomendamos que você use ou personalize diretamente o `Dockerfile` sem o VSDBG para os módulos do IoT Edge prontos para produção após concluir a depuração dos módulos C#.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um módulo do IoT Edge, implantou-o para fins de depuração e começou a depurá-lo no VS Code. Você pode prosseguir para um dos tutoriais a seguir para saber mais sobre outros cenários de desenvolvimento do Azure IoT Edge no VS Code. 

> [!div class="nextstepaction"]
> [Desenvolver e implantar o módulo C# no VS Code](how-to-vscode-develop-csharp-module.md)
