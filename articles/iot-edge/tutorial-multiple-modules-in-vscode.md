---
title: Trabalhar com vários módulos de borda IoT Edge no Visual Studio Code | Microsoft Docs
description: Implantar o Azure Machine Learning como um módulo em um dispositivo de borda
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 6c94701507f86f6ecab2875f952215cc3e4cc719
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code---preview"></a>Desenvolver uma solução IoT Edge com vários módulos no Visual Studio Code - visualização
Você pode usar o Visual Studio Code para desenvolver uma solução do Azure IoT Edge com vários módulos Este artigo orienta na criação, atualização e implantação de uma solução IoT Edge que redireciona os dados do sensor do dispositivo IoT Edge simulada no Visual Studio Code. Neste artigo, você aprenderá a:

* Use o Visual Studio Code para criar uma solução de Borda IoT Edge
* Use o código VS para adicionar um novo módulo à sua solução do Azure IoT Edge em funcionamento. 
* Implante a solução do Azure IoT Edge (vários módulos) em seu dispositivo do Azure IoT Edge
* Exibir os dados gerados

## <a name="prerequisites"></a>pré-requisitos
* Conclua os tutoriais abaixo
  * [Implantar o módulo C#](tutorial-csharp-module.md)
  * [Implantar a função C#](tutorial-deploy-function.md)
  * [Implantar o módulo de Python](tutorial-python-module.md)
* [Docker para o código VS](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) com integração do explorador para gerenciar Imagens e Contêineres.


## <a name="prepare-your-first-iot-edge-solution"></a>Preparar sua primeira solução do Azure IoT Edge
1. Na paleta de comandos do VS Code, digite e execute o comando **Edge: Nova solução IoT Edge**. Em seguida, selecione a sua pasta de espaço de trabalho, forneça o nome da solução (o nome padrão é **EdgeSolution**) e crie um módulo C# (**SampleModule**) como o primeiro módulo de usuário nesta solução. Você também precisa especificar o repositório de imagens do Docker para o seu primeiro módulo. O repositório de imagens padrão baseia-se em um registro de Docker local (`localhost:5000/<first module name>`). Você também pode alterá-lo para registro de contêiner do Azure ou Hub do Docker.

> [!NOTE]
> Se você estiver usando um registro do Docker local, verifique se o registro está em execução digitando o comando `docker run -d -p 5000:5000 --restart=always --name registry registry:2` na janela do console.

2. A janela de código VS carregará seu espaço de trabalho da solução do Azure IoT Edge. Há uma `modules` pasta, uma `.vscode` pasta e uma implantação de manifesto do arquivo de modelo na pasta raiz. Você pode ver a depuração de configurações na `.vscode` pasta. Todos os códigos de usuário do módulo serão subpastas na pasta `modules`. O `deployment.template.json` é o modelo de manifesto de implantação. Alguns dos parâmetros nesse arquivo serão analisados a partir de `module.json`, que existe em cada pasta de módulo.

3. Adicione o segundo módulo a esse projeto de solução. Neste momento digite e execute **Borda: módulo adicionar Azure IoT Edge** e selecione o arquivo de modelo de implantação para atualizar. Selecione uma **Função do Azure - C#** com nome o **SampleFunction** e seu repositório de imagens do Docker para adicionar.

4. Agora sua primeira solução do Azure IoT Edge com dois módulos básicos está pronta. O módulo C# padrão atua como um módulo de mensagem de pipe enquanto a geração de C# atua como uma função de mensagens do pipe. No `deployment.template.json`, você verá que esta solução contém três módulos. A mensagem será gerada a partir do `tempSensor` módulo e será conectada diretamente `SampleModule` e `SampleFunction`, enviada o hub IoT. Atualize as rotas para esses módulos com o conteúdo abaixo. 
   ```json
        "routes": {
          "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/SampleModule/inputs/input1\")",
          "PipeModuleToPipeFunction": "FROM /messages/modules/SampleModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/SampleFunction/inputs/input1\")",
          "PipeFunctionToIoTHub": "FROM /messages/modules/SampleFunction/outputs/output1 INTO $upstream"
        },
   ```

5. Salve o arquivo.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Criar e implantar sua solução do Azure IoT Edge
1. Na paleta de comandos do VS Code, digite e execute o comando **Edge: compilar solução IoT Edge**. Com base no `module.json` arquivo em cada pasta de módulo, esse comando verificará e começará a criar, colocará em contêiner e enviará por push cada imagem do docker do módulo. Em seguida, ele analisa o valor necessário para `deployment.template.json`, gerar o `deployment.json` com o valor real na `config` pasta. Você pode ver o progresso da compilação no terminal integrado do VS Code.

2. No gerenciador de dispositivos do Hub IoT do Azure, clique com o botão direito do mouse em uma ID de dispositivo IoT Edge e selecione **Criar implantação para dispositivo Edge**. Selecione `deployment.json` na pasta `config`. Em seguida, você pode ver se a implantação foi criada com êxito com uma ID de implantação no terminal integrado do VS Code.

3. Se você estiver [simulando um dispositivo de Azure IoT Edge](tutorial-simulate-device-linux.md) no computador de desenvolvimento. Você verá que todos os contêineres de imagem do módulo serão iniciados em alguns minutos.

## <a name="view-generated-data"></a>Exibir os dados gerados

1. Para monitorar os dados recebidos do Hub IoT, selecione **Exibir** > **Paleta de Comandos...** e procure **IoT: Iniciar o monitoramento de mensagem D2C**. 
2. Para interromper o monitoramento de dados, use o comando **IoT: Parar o monitoramento de mensagem D2C** na Paleta de Comandos. 

## <a name="next-steps"></a>Próximas etapas

Você pode prosseguir para um dos artigos a seguir para saber mais sobre outros cenários de desenvolvimento do Azure IoT Edge no Visual Studio Code:

* [Depurar um módulo C# no VS Code](how-to-vscode-debug-csharp-module.md)
* [Depurar uma função C# no VS Code](how-to-vscode-debug-azure-function.md)