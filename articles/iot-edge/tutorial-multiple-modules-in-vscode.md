---
title: Gerenciar vários módulos do Azure IoT Edge no VS Code | Microsoft Docs
description: Use o Visual Studio Code para desenvolver soluções de IoT Edge que usam vários módulos.
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 4d9caa7aa95c99fa30e8ec76c5b6362615725622
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34166345"
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code---preview"></a>Desenvolver uma solução IoT Edge com vários módulos no Visual Studio Code - visualização
Você pode usar o Visual Studio Code para desenvolver uma solução do Azure IoT Edge com vários módulos Este artigo orienta na criação, atualização e implantação de uma solução IoT Edge que redireciona os dados do sensor do dispositivo IoT Edge simulada no Visual Studio Code. 

## <a name="prerequisites"></a>pré-requisitos

Para concluir todas as etapas neste artigo, você precisará dos seguintes pré-requisitos:

- [Visual Studio Code](https://code.visualstudio.com/) 
- [Extensão do Azure IoT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
- [C# para extensão do Visual Studio Code (fornecido por OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
- [Docker](https://docs.docker.com/engine/installation/)
- [SDK 2.0 do .NET Core](https://www.microsoft.com/net/core#windowscmd) 
- Modelo AzureIoTEdgeModule (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- Um hub IoT ativo com pelo menos um dispositivo do IoT Edge


* [Docker para o código VS](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) com integração do explorador para gerenciar Imagens e Contêineres.


## <a name="prepare-your-first-iot-edge-solution"></a>Preparar sua primeira solução do Azure IoT Edge
1. Na paleta de comandos do VS Code, digite e execute o comando **Edge: Nova solução IoT Edge**. Em seguida, selecione a sua pasta de espaço de trabalho, forneça o nome da solução (o nome padrão é **EdgeSolution**) e crie um módulo C# (**SampleModule**) como o primeiro módulo de usuário nesta solução. Você também precisa especificar o repositório de imagens do Docker para o seu primeiro módulo. O repositório de imagens padrão baseia-se em um registro de Docker local (`localhost:5000/<first module name>`). Você também pode alterá-lo para registro de contêiner do Azure ou Hub do Docker.

   > [!NOTE]
   > Se você estiver usando um registro do Docker local, verifique se o registro está em execução digitando o comando `docker run -d -p 5000:5000 --restart=always --name registry registry:2` na janela do console.

2. A janela do VS Code carregará seu espaço de trabalho da solução IoT Edge. Uma pasta raiz contém uma pasta `modules`, uma pasta `.vscode` e um arquivo de modelo de manifesto de implantação. Você pode ver a depuração de configurações na `.vscode` pasta. Todos os códigos de usuário do módulo serão subpastas na pasta `modules`. O `deployment.template.json` é o modelo de manifesto de implantação. Alguns dos parâmetros nesse arquivo serão analisados a partir de `module.json`, que existe em cada pasta de módulo.

3. Adicione o segundo módulo a esse projeto de solução. Neste momento digite e execute **Borda: módulo adicionar Azure IoT Edge** e selecione o arquivo de modelo de implantação para atualizar. Selecione uma **Função do Azure - C#** com o nome **SampleFunction** e seu repositório de imagens do Docker.

4. Abra o arquivo `deployment.template.json` e verifique se que ele declara três módulos além do tempo de execução. A mensagem será gerada a partir do `tempSensor` módulo e será conectada diretamente `SampleModule` e `SampleFunction`, enviada o hub IoT. 
5. Atualize as rotas para esses módulos com o conteúdo a seguir:
   ```json
   "routes": {
      "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/SampleModule/inputs/input1\")",
      "PipeModuleToPipeFunction": "FROM /messages/modules/SampleModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/SampleFunction/inputs/input1\")",
      "PipeFunctionToIoTHub": "FROM /messages/modules/SampleFunction/outputs/output1 INTO $upstream"
   },
   ```

6. Salve o arquivo.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Criar e implantar sua solução do Azure IoT Edge
1. Na paleta de comandos do VS Code, digite e execute o comando **Edge: compilar solução IoT Edge**. Com base no arquivo `module.json` em cada pasta de módulo, esse comando começa a criar, colocar em contêiner e enviar por push cada imagem do docker do módulo. Em seguida, ele passa o valor necessário para `deployment.template.json` e gera o arquivo `deployment.json` com informações sobre a pasta `config`. Você pode ver o progresso da compilação no terminal integrado do VS Code.

2. No gerenciador de dispositivos do Hub IoT do Azure, clique com o botão direito do mouse em uma ID de dispositivo IoT Edge e selecione **Criar implantação para dispositivo Edge**. Selecione `deployment.json` na pasta `config`. Em seguida, você pode ver se a implantação foi criada com êxito com uma ID de implantação no terminal integrado do VS Code.

3. Se estiver simulando um dispositivo IoT Edge no computador de desenvolvimento, você verá que todos os contêineres de imagem do módulo serão iniciados em alguns minutos.

## <a name="view-generated-data"></a>Exibir os dados gerados

1. Para monitorar os dados recebidos do Hub IoT, selecione **Exibir** > **Paleta de Comandos...** e procure **IoT: Iniciar o monitoramento de mensagem D2C**. 
2. Para interromper o monitoramento de dados, use o comando **IoT: Parar o monitoramento de mensagem D2C** na Paleta de Comandos. 

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre outros cenários para desenvolver o Azure IoT Edge no Visual Studio Code:

* [Depurar um módulo C# no VS Code](how-to-vscode-debug-csharp-module.md)
* [Depurar uma função C# no VS Code](how-to-vscode-debug-azure-function.md)