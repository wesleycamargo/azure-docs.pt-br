---
title: "Usar o Visual Studio Code para depurar um módulo C# com o Azure IoT Edge | Microsoft Docs"
description: "Depurar um módulo C# com o Azure IoT Edge no Visual Studio Code."
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/06/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 46d9ca0bf6c9ddf95c147fc2eb62d275c973845e
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="use-visual-studio-code-to-debug-a-c-module-with-azure-iot-edge"></a>Usar o Visual Studio Code para depurar um módulo C# com o Azure IoT Edge
Este artigo fornece instruções detalhadas de como usar o [Visual Studio Code](https://code.visualstudio.com/) como a ferramenta de desenvolvimento principal para depurar seus módulos do Azure IoT Edge.

## <a name="prerequisites"></a>pré-requisitos
Este tutorial assume que você está usando um computador ou uma máquina virtual que executa Windows ou Linux como seu computador de desenvolvimento. O dispositivo do IoT Edge pode ser outro dispositivo físico ou você pode simular dispositivo do IoT Edge no computador de desenvolvimento.

Antes de iniciar essas diretrizes, conclua o tutorial a seguir:
- [Use o Visual Studio Code para desenvolver o módulo C# com o Azure IoT Edge](how-to-vscode-develop-csharp-module.md)

Depois de concluir o tutorial anterior, os seguintes itens deverão estar estão prontos:
- Um registro do Docker local em execução no computador de desenvolvimento. Isso é para fins de teste e de protótipos.
- O arquivo `Program.cs` com o código mais recente do módulo de filtro.
- Um arquivo `deployment.json` atualizado para os módulos de sensor e de filtro.
- Um tempo de execução do IoT Edge que é executado no computador de desenvolvimento.

## <a name="build-your-iot-edge-module-for-debugging"></a>Criar o módulo do IoT Edge para depuração
1. Para iniciar a depuração, use o **dockerfile.debug** para recriar a imagem do Docker e implantar a solução do IoT Edge novamente. No Gerenciador do Visual Studio Code, selecione a pasta de Docker para abri-lo. Em seguida, selecione a pasta **linux-x64**, clique com o botão direito do mouse em **Dockerfile.debug** e selecione **Criar imagem do Docker do módulo do IoT Edge**.

    ![Captura de tela do VS Code Explorer](./media/how-to-debug-csharp-module/build-debug-image.png)

3. Na janela **Selecionar Pasta**, procure por ou insira **./bin/Debug/netcoreapp2.0/publish**. Em seguida, selecione **Selecionar pasta como EXE_DIR**.
4. Na caixa de texto pop-up na parte superior da janela do VS Code, insira o nome da imagem. Por exemplo: `<your container registry address>/filtermodule:latest`. Se você estiver implantando o registro local, deverá ser: `localhost:5000/filtermodule:latest`.
5. Envie a imagem por push para o repositório do Docker. Use o comando **Edge: Push IoT Edge module Docker image** e insira a URL da imagem na caixa de texto pop-up na parte superior da janela do VS Code. Use a mesma URL de imagem usada na etapa anterior.
6. Você pode reutilizar o `deployment.json` para reimplantar. Na paleta de comandos, digite e selecione **Edge: reiniciar Edge** para que o módulo de filtro seja executado com a versão de depuração.

## <a name="start-debugging-in-vs-code"></a>Iniciar a depuração no VS Code
1. Acesse a janela de depuração do VS Code. Pressione **F5** e selecione **IoT Edge(.NET Core)**.

    ![Captura de tela da janela de depuração do VS Code](./media/how-to-debug-csharp-module/f5-debug-option.png)

2. Em `launch.json`, navegue até a seção **Depurar módulo personalizado IoT Edge (.NET Core)**. Em **pipeArgs**, preencha o `<container_name>`. Ele deve ser `filtermodule` neste tutorial.

    ![Captura de tela da janela de depuração do VS Code](./media/how-to-debug-csharp-module/f5-debug-option.png)

3. Navegue até **Program.cs**. Adicione um ponto de interrupção no `method static async Task<MessageResponse> FilterModule(Message message, object userContext)`.
4. Pressione **F5** novamente e selecione o processo ao qual ele deverá ser anexado. Neste tutorial, o nome do processo deve ser `FilterModule.dll`.

    ![Captura de tela da janela de depuração do VS Code](./media/how-to-debug-csharp-module/attach-process.png)

5. Na janela de depuração do VS Code, você pode ver as variáveis no painel esquerdo. 

> [!NOTE]
> O exemplo anterior mostra como depurar módulo do IoT Edge do .NET Core em contêineres. Ele é baseado na versão de depuração do `Dockerfile.debug`, que inclui o VSDBG (o depurador da linha de comando do .NET Core) na imagem de contêiner durante a criação. Após concluir a depuração dos módulos C#, recomendamos que você use ou personalize diretamente o `Dockerfile` sem o VSDBG para os módulos do IoT Edge prontos para produção.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um módulo no IoT Edge e o implantou para depuração. Você começou a depurá-lo no VS Code. Para saber mais sobre outros cenários quando você estiver desenvolvendo o Azure IoT Edge no VS Code, consulte: 

> [!div class="nextstepaction"]
> [Desenvolver e implantar o módulo C# no VS Code](how-to-vscode-develop-csharp-module.md)
