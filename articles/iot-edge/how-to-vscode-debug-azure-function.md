---
title: Usar o Visual Studio Code para depurar Azure Functions com o Azure IoT Edge | Microsoft Docs
description: Depurar Azure Functions em C# com o Azure IoT Edge no VS Code
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 3/20/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 8c266a01375bf74fd4df9290255e84bc28e6089c
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="use-visual-studio-code-to-debug-azure-functions-with-azure-iot-edge"></a>Usar o Visual Studio Code para depurar Azure Functions com o Azure IoT Edge

Este artigo fornece instruções detalhadas de como usar o [Visual Studio Code](https://code.visualstudio.com/) como a ferramenta de desenvolvimento principal para depurar o Azure Functions no IoT Edge.

## <a name="prerequisites"></a>pré-requisitos
Este artigo assume que você está usando um computador ou uma máquina virtual que executa Windows ou Linux como seu computador de desenvolvimento. O dispositivo do IoT Edge pode ser outro dispositivo físico ou você pode simular o dispositivo do IoT Edge em seu computador de desenvolvimento.

Antes de seguir as diretrizes neste artigo, conclua as etapas em [Desenvolver uma solução de IoT Edge com vários módulos no Visual Studio Code](tutorial-multiple-modules-in-vscode.md). Depois disso, você deve ter os seguintes itens prontos:
- Um registro do Docker local em execução no computador de desenvolvimento. É recomendável usar um registro do Docker local para fins de protótipo e teste. Você pode atualizar o registro de contêiner no arquivo `module.json` em cada pasta de módulo.
- O espaço de trabalho do projeto da solução IoT Edge com uma subpasta do módulo Azure Function nele.
- O arquivo `run.csx` com o código de função.
- Um tempo de execução do Edge em execução no computador de desenvolvimento.

## <a name="build-your-iot-edge-function-module-for-debugging-purpose"></a>Criar o módulo Função do IoT Edge para fins de depuração
1. Para iniciar a depuração, você precisa usar o **Dockerfile.amd64.debug** para recriar a imagem do Docker e implantar a solução Edge novamente. No VS Code explorer, navegue até o arquivo `deployment.template.json`. Atualize a URL da imagem de função adicionando um `.debug` no final.

    ![Criar imagem de Depuração](./media/how-to-debug-csharp-function/build-debug-image.png)

2. Recompile a solução. Na paleta de comandos do VS Code, digite e execute o comando **Edge: compilar solução IoT Edge**.

3. No gerenciador de dispositivos do Hub IoT do Azure, clique com o botão direito do mouse em uma ID de dispositivo IoT Edge e selecione **Criar implantação para dispositivo Edge**. Selecione `deployment.json` na pasta `config`. Em seguida, você pode ver se a implantação foi criada com êxito com uma ID de implantação no terminal integrado do VS Code.

> [!NOTE]
> Você pode verificar o status de contêiner no gerenciador de Docker do VS Code ou executando o comando `docker images` no terminal.

## <a name="start-debugging-c-function-in-vs-code"></a>Começar a depurar a função C# no VS Code
1. O VS Code mantém as informações de configuração de depuração em um arquivo `launch.json` localizado em uma pasta `.vscode` no espaço de trabalho. Esse arquivo `launch.json` foi gerado ao criar uma nova solução IoT Edge. E será atualizado sempre que você adicionar um novo módulo que oferece suporte à depuração. Navegue até a exibição de depuração e selecione o arquivo de configuração de depuração correspondente.
    ![Selecionar configuração de depuração](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. Navegue até `run.csx`. Adicione um ponto de interrupção na função.

3. Clique no botão Iniciar Depuração ou pressione **F5** e selecione o processo ao qual anexar.

4. Na exibição Depuração do VS Code, você pode ver as variáveis no painel esquerdo. 


> [!NOTE]
> O exemplo acima mostra como depurar a função do IoT Edge do .NET Core em contêineres. Ele é baseado na versão de depuração do `Dockerfile.amd64.debug`, que inclui o VSDBG (o depurador da linha de comando do .NET Core) na imagem de contêiner durante a criação. Recomendamos que você use ou personalize diretamente o `Dockerfile` sem o VSDBG para a função do IoT Edge pronta para produção após concluir a depuração da sua função do C#.

## <a name="next-steps"></a>Próximas etapas


[Usar o Visual Studio Code para depurar um módulo C# com o Azure IoT Edge](how-to-vscode-debug-csharp-module.md)

