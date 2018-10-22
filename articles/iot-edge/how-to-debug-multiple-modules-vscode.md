---
title: Depurar vários módulos para Azure IoT Edge no VS Code | Microsoft Docs
description: Usar o Visual Studio Code para depurar vários módulos com Azure IoT Edge
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 38c66129ac8244d18b0f94c1485c785015e29ab4
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049584"
---
# <a name="use-visual-studio-code-to-debug-multiple-modules-with-azure-iot-edge"></a>Usar o Visual Studio Code para depurar vários módulos com Azure IoT Edge
Este artigo fornece instruções detalhadas de como usar o [Visual Studio (VS) Code](https://code.visualstudio.com/) para depurar o Azure Functions no IoT Edge.

## <a name="prerequisites"></a>pré-requisitos
Conclua o tutorial [Desenvolver uma solução do IoT Edge com vários módulos no Visual Studio Code](tutorial-multiple-modules-in-vscode.md) e verifique se há pelo menos dois módulos em execução no dispositivo do IoT Edge.

## <a name="multi-target-and-remote-debugging-in-vs-code"></a>Vários destinos e depuração remota no VS Code
Com a extensão do VS Code e Azure IoT Edge, é possível anexar o processo do módulo em um contêiner, independentemente do contêiner estar em execução no computador de desenvolvimento ou em um dispositivo do IoT Edge físico remoto. Depurar vários módulos em execução nos contêineres é realmente anexar mais de um processo em contêineres separados. A [depuração de vários destinos](https://code.visualstudio.com/docs/editor/debugging#_multitarget-debugging) do VS Code pode ser utilizada ao depurar vários módulos.

   > [!TIP]
   > Se o contêiner de módulo estiver em execução em um dispositivo do IoT Edge físico remoto, talvez seja necessário configurar o[Computador do Docker](https://docs.docker.com/machine/overview/) para que o mecanismo do Docker no computador de desenvolvimento possa comunicar-se com os hosts do Docker remotos.

### <a name="build-your-iot-edge-modules-for-debugging-purpose"></a>Compilar os módulos do IoT Edge para fins de depuração
1. Para iniciar a depuração de vários módulos, é necessário usar o **Dockerfile.amd64.debug** para recompilar as imagens do docker e implantar a solução do Edge novamente. No VS Code explorer, navegue até o arquivo `deployment.template.json`. Atualize as URLs de imagem, adicionando `.debug` no final. É necessário ter duas imagens do módulo com pelo menos `.debug`. Se estiver trabalhando na solução do tutorial anterior, será necessário ter um módulo de funções C# e um módulo C#. Atualize essas duas URLs de imagem adicionando `.debug` no final e salve esse arquivo. 
2. Recompile a solução. Na paleta de comandos do VS Code, digite e execute o comando **Azure IoT Edge: Compilar solução do IoT Edge** .
3. No gerenciador de dispositivos do Hub IoT do Azure, clique com o botão direito do mouse em uma ID de dispositivo IoT Edge e selecione **Criar implantação para dispositivo Edge**. Selecione o arquivo `deployment.json` na pasta `config`. Em seguida, você pode ver se a implantação foi criada com êxito com uma ID de implantação no terminal integrado do VS Code.

Você pode verificar o status de contêiner no gerenciador de Docker do VS Code ou executando o comando `docker ps` no terminal.

### <a name="start-debugging-c-function-in-vs-code"></a>Começar a depurar a função C# no VS Code
1. O VS Code mantém as informações de configuração de depuração em um arquivo `launch.json` localizado em uma pasta `.vscode` no workspace. Esse arquivo `launch.json` foi gerado quando uma nova solução IoT Edge foi criada. Ele será atualizado sempre que você adicionar um novo módulo que oferece suporte à depuração. Navegue até a exibição de depuração e selecione o arquivo de configuração de depuração correspondente para a depuração remota do módulo de funções C#.
2. Navegue até `run.csx`. Adicione um ponto de interrupção na função.
3. Clique no botão **Iniciar Depuração** ou pressione **F5** e selecione o processo ao qual anexar.
4. Na exibição Depuração do VS Code, você pode ver as variáveis no painel esquerdo. 

### <a name="start-debugging-c-module-at-the-same-time-in-vs-code"></a>Iniciar a depuração do módulo C# ao mesmo tempo no VS Code
1. Na paleta de comandos do VS Code, digite e execute o comando "Workspace: Workspace duplicado em nova janela". Uma nova janela do VS Code inicia com o mesmo workspace.
2. Navegue até a exibição de depuração e selecione o arquivo de configuração de depuração correspondente para a depuração remota do módulo C#.
3. Navegue até `program.cs`. Adicionar um ponto de interrupção no módulo C#.
4. Clique no botão **Iniciar Depuração** ou pressione **F5** e selecione o processo ao qual anexar.
5. Na exibição Depuração do VS Code, você pode ver as variáveis no painel esquerdo. 

### <a name="see-variables-in-multiple-debugging-windows"></a>Consultar variáveis em várias janelas de depuração
1. Agora há pelo menos duas sessões de depuração executando em duas janelas do VS Code. Um dos pontos de interrupção deve ser atingido.
2. Pressione `F10` ou clique no botão de Depuração Parcial na **Barra de ferramentas de Depuração**.
3. O ponto de interrupção em outra janela do VS Code deve ser atingido. 
4. Continuar acima de duas etapas, é possível ver as variáveis de vários módulos em várias janelas de depuração do VS Code.

> [!NOTE]
> O exemplo acima mostra como depurar vários módulos com o Azure IoT Edge. Ele é baseado na versão de depuração do `Dockerfile.amd64.debug`, que inclui o VSDBG (o depurador da linha de comando do .NET Core) na imagem de contêiner durante a criação. Recomendamos que você use ou personalize diretamente o `Dockerfile` sem o VSDBG para a função do IoT Edge pronta para produção após concluir a depuração da sua função do C#.

## <a name="next-steps"></a>Próximas etapas

Após compilar o módulo, saiba como [Implantar módulos do Azure IoT Edge do Visual Studio Code](how-to-deploy-modules-vscode.md) 0
