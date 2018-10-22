---
title: Trabalhar com vários módulos do Azure IoT Edge no VS Code | Microsoft Docs
description: Usar a extensão de IoT para o Visual Studio Code para desenvolver vários módulos de uma vez para o Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 31fe210b87a052438956d813db0d104e0f2cdb6e
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041238"
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code"></a>Desenvolver uma solução IoT Edge com vários módulos no Visual Studio Code

Você pode usar o Visual Studio Code para desenvolver uma solução do Azure IoT Edge com vários módulos. Este artigo mostra como criar, atualizar e implantar uma solução do IoT Edge que redireciona os dados do sensor em um dispositivo do IoT Edge simulado no VS Code. 

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas deste artigo, são necessários os pré-requisitos a seguir:

- [Visual Studio Code](https://code.visualstudio.com/)
- [Extensão do Azure IoT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)
- [C# para extensão do Visual Studio Code (fornecido por OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
- [Docker](https://docs.docker.com/engine/installation/)
- O [SDK 2.1 do .NET Core](https://www.microsoft.com/net/download)
- Um hub IoT ativo com pelo menos um dispositivo do IoT Edge

Também será necessário [Docker para VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) com integração do Device Explorer do Hub IoT para gerenciar imagens e contêineres.

## <a name="create-your-iot-edge-solution"></a>Criar solução IoT Edge

1. No Visual Studio Code, abra o terminal integrado do selecionando **Exibir** > **Terminal Integrado**. 

1. Na **Paleta de Comandos** do VS Code, insira e execute o comando **Azure IoT Edge: Nova solução do IoT Edge**. Selecione a pasta de workspace e forneça o nome da solução (o padrão é EdgeSolution). Crie um módulo C# (com o nome **PipeModule**) como o primeiro módulo de usuário nesta solução. O modelo padrão de módulo C# é um módulo de pipe, que redireciona mensagens diretamente de upstream para downstream. Você também precisa especificar o repositório de imagens do Docker para o seu primeiro módulo. O repositório de imagem padrão é baseado em um registro local do Docker (**localhost:5000/<first module name>**). Você pode alterá-lo para Registro de Contêiner do Azure ou Hub do Docker. 

2. A janela do VS Code carregará seu workspace da solução IoT Edge. A pasta raiz contém uma pasta de **módulos**, uma pasta **.vscode** e um arquivo de modelo de manifesto de implantação. Configurações de depuração estão localizadas na pasta .vscode. Todos os códigos do módulo do usuário são subpastas da pasta de módulos. O arquivo deployment.template.json é o modelo de manifesto de implementação. Alguns dos parâmetros neste arquivo são analisados a partir do arquivo module.json, que existe em todas as pastas de módulos.

3. Adicione o segundo módulo a este projeto de solução. Há várias formas de adicionar um novo módulo à solução atual. Digite e execute o comando **Azure IoT Edge: Adicionar módulo do IoT Edge**. Selecione o arquivo de modelo de implantação para atualizar. Ou clique com o botão direito na pasta de módulos ou com o botão direito no arquivo deployment.template.json e selecione **Adicionar Módulo do IoT Edge**. Em seguida, haverá uma lista suspensa para selecionar o tipo de módulo. Selecione um módulo **Azure Functions - C#** com o nome **PipeFunction** e seu repositório de imagens do Docker. O modelo padrão de módulo de funções C# é um módulo de pipe, que redireciona mensagens diretamente de upstream para downstream.

4. Abra o arquivo deployment.template.json. Verifique se o arquivo declara três módulos e o tempo de execução. A mensagem é gerada a partir do módulo tempSensor. A mensagem é redirecionada diretamente pelos módulos SampleModule e SampleFunction e, em seguida, enviada para o Hub IoT. 

5. Atualize as rotas para esses módulos com o conteúdo a seguir:

   ```json
        "routes": {
          "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/PipeModule/inputs/input1\")",
          "PipeModuleToPipeFunction": "FROM /messages/modules/PipeModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/PipeFunction/inputs/input1\")",
          "PipeFunctionToIoTHub": "FROM /messages/modules/PipeFunction/outputs/output1 INTO $upstream"
        },
   ```

5. Salve o arquivo.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Criar e implantar sua solução do Azure IoT Edge

1. Na **Paleta de Comandos** do VS Code, insira e execute o comando **Azure IoT Edge: Compilar solução do IoT Edge**. Com base no arquivo module.json em cada pasta de módulo, o comando começa a compilar, colocar em contêiner e efetuar push de cada imagem do Docker do módulo. Em seguida, o comando transmite o valor necessário para o arquivo deployment.template.json e gera o arquivo deployment.json com informações da pasta de configuração. O terminal integrado no VS Code mostra o andamento da compilação. 

2. No **Device Explorer** do Hub IoT, clique com o botão direito do mouse em uma ID de dispositivo do IoT Edge e selecione o comando **Criar implantação para dispositivo do Edge**. Selecione o arquivo deployment.json na pasta de configuração. O terminal integrado no VS Code mostra a implantação criada com êxito com uma ID de implantação.

3. Se você estiver simulando um dispositivo do IoT Edge no computador de desenvolvimento, é possível inspecionar para ver se todos os contêineres de imagem do módulo iniciam em poucos minutos.

## <a name="view-the-generated-data"></a>Exibir os dados gerados

1. Para monitorar os dados que chegam ao Hub IoT, selecione **Exibir** > **Paleta de Comandos**. Em seguida, selecione o comando **IoT: Iniciar monitoramento de mensagem D2C**. 
2. Para interromper o monitoramento de dados, use o comando **IoT: Parar o monitoramento de mensagem D2C** na **Paleta de Comandos**. 

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre outros cenários para desenvolvimento com o Azure IoT Edge no Visual Studio Code:

* Desenvolver módulos no VS Code com [C#](how-to-develop-csharp-module.md) ou [Node.js](how-to-develop-node-module.md).
* Desenvolver Azure Functions no VS Code com [C#](how-to-develop-csharp-function.md).

Para desenvolver módulos para seus dispositivos do IoT Edge, consulte [Entender e usar os SDKs de Hub IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md).