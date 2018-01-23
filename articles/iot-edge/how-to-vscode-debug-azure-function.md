---
title: Usar o Visual Studio Code para depurar Azure Functions com o Azure IoT Edge | Microsoft Docs
description: Depurar Azure Functions em C# com o Azure IoT Edge no VS Code
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/20/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 4344a450d218a7424cd055cf086c1e4865c9af10
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2017
---
# <a name="use-visual-studio-code-to-debug-azure-functions-with-azure-iot-edge"></a>Usar o Visual Studio Code para depurar Azure Functions com o Azure IoT Edge

Este artigo fornece instruções detalhadas de como usar o [Visual Studio Code](https://code.visualstudio.com/) como a ferramenta de desenvolvimento principal para depurar o Azure Functions no IoT Edge.

## <a name="prerequisites"></a>pré-requisitos
Este tutorial assume que você esteja usando um computador ou uma máquina virtual que executa Windows ou Linux como seu computador de desenvolvimento. O dispositivo do IoT Edge pode ser outro dispositivo físico ou você pode simular o dispositivo do IoT Edge no computador de desenvolvimento.

Conclua os tutoriais a seguir antes de iniciar estas diretrizes.
- [Usar o Visual Studio Code para desenvolver e implantar Azure Functions no Azure IoT Edge](how-to-vscode-develop-azure-function.md)

Depois de concluir o tutorial anterior, os seguintes itens deverão estar estão prontos,
- Um registro do Docker local em execução no computador de desenvolvimento. É recomendável usar um registro do Docker local para fins de protótipo e teste.
- O arquivo `run.csx` com o código mais recente da função de filtro.
- Um arquivo `deployment.json` atualizado para o módulo de sensor e o módulo da função de filtro.
- Um tempo de execução do Edge em execução no computador de desenvolvimento.

## <a name="build-your-iot-edge-module-for-debugging-purpose"></a>Criar o módulo do IoT Edge para fins de depuração
1. Para iniciar a depuração, você precisa usar o **dockerfile.debug** para recriar a imagem do Docker e implantar a solução do Edge novamente. No gerenciador do VS Code, clique na pasta Docker para abri-la. Em seguida, clique na pasta `linux-x64`, clique com o botão direito do mouse em **Dockerfile.debug** e clique em **Criar imagem do Docker do módulo do IoT Edge**.
3. Na janela **Selecionar Pasta**, navegue até o projeto **FilterFunction** e clique em **Selecionar Pasta como EXE_DIR**.
4. Na caixa de texto pop-up na parte superior da janela do VS Code, insira o nome da imagem. Por exemplo: `<your container registry address>/filterfunction:latest`. Se você estiver implantando no registro local, deverá ser `localhost:5000/filterfunction:latest`.
5. Envie a imagem por push para o repositório do Docker. Use o comando **Edge: Push IoT Edge module Docker image** e insira a URL da imagem na caixa de texto pop-up na parte superior da janela do VS Code. Use a mesma URL de imagem usada na etapa acima.
6. Você pode reutilizar o `deployment.json` para reimplantar. Na paleta de comandos, digite e selecione **Edge: reiniciar Edge** para que a função de filtro seja executada com a versão de depuração.

## <a name="start-debugging-in-vs-code"></a>Iniciar a depuração no VS Code
1. Acesse a janela de depuração do VS Code. Pressione **F5** e selecione **IoT Edge(.Net Core)**
2. Em `launch.json`, navegue até **Depurar função do IoT Edge (.NET Core)** e preencha o `<container_name>` em `pipeArgs`. Deverá ser `filterfunction` neste tutorial.
3. Navegue até run.csx. Adicione um ponto de interrupção na função.
4. Pressione **F5** novamente. E selecione o processo ao qual ele deverá ser anexado.
5. Na janela de depuração do VS Code, você pode ver as variáveis no painel esquerdo. 

> [!NOTE]
> O exemplo acima mostra como depurar a função do IoT Edge do .NET Core em contêineres. Ele é baseado na versão de depuração do `Dockerfile.debug`, que inclui o VSDBG (o depurador da linha de comando do .NET Core) na imagem de contêiner durante a criação. Recomendamos que você use ou personalize diretamente o `Dockerfile` sem o VSDBG para a função do IoT Edge pronta para produção após concluir a depuração da sua função do C#.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou uma Função do Azure, implantou-a no IoT Edge para fins de depuração e começou a depurá-la no VS Code. Você pode prosseguir para um dos tutoriais a seguir para saber mais sobre outros cenários de desenvolvimento do Azure IoT Edge no VS Code. 

> [!div class="nextstepaction"]
> [Desenvolver e implantar o módulo C# no VS Code](how-to-vscode-develop-csharp-module.md)

