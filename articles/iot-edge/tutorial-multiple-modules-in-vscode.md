---
title: Gerenciar vários módulos do Azure IoT Edge no VS Code | Microsoft Docs
description: Use o Visual Studio Code para desenvolver soluções do Azure IoT Edge que usam vários módulos.
author: shizn
manager: ''
ms.author: xshi
ms.date: 03/18/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 4a624994f93e7a3cbb04db2a0ec0b2b823a12ee7
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763027"
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code-preview"></a>Desenvolver uma solução do IoT Edge com vários módulos no Visual Studio Code (versão prévia)

Você pode usar o Visual Studio Code para desenvolver uma solução do Azure IoT Edge com vários módulos. Este artigo mostra como criar, atualizar e implantar uma solução do IoT Edge que redireciona os dados do sensor em um dispositivo do IoT Edge simulado no VS Code. 

## <a name="prerequisites"></a>pré-requisitos

Para concluir as etapas deste artigo, são necessários os pré-requisitos a seguir:

- [Visual Studio Code](https://code.visualstudio.com/)
- [Extensão do Azure IoT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)
- [C# para extensão do Visual Studio Code (fornecido por OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
- [Docker](https://docs.docker.com/engine/installation/)
- [SDK 2.0 do .NET Core](https://www.microsoft.com/net/core#windowscmd)
- Modelo AzureIoTEdgeModule (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- Um hub IoT ativo com pelo menos um dispositivo do IoT Edge

Também será necessário [Docker para VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) com integração do Device Explorer do Hub IoT para gerenciar imagens e contêineres.

## <a name="prepare-your-first-iot-edge-solution"></a>Preparar sua primeira solução do Azure IoT Edge

1. Na **Paleta de Comandos** do VS Code, insira e execute o comando **Edge: Nova solução do IoT Edge**. Selecione a pasta de espaço de trabalho e forneça o nome da solução (o padrão é EdgeSolution). Crie um módulo C# (com o nome **SampleModule**) como o primeiro módulo de usuário nesta solução. Você também precisa especificar o repositório de imagens do Docker para o seu primeiro módulo. O repositório de imagem padrão é baseado em um registro local do Docker (**localhost:5000/<first module name>**). Você pode alterá-lo para Registro de Contêiner do Azure ou Hub do Docker.

   > [!NOTE]
   > Se você estiver usando um registro local do Docker, verifique se o registro está em execução. Insira o comando a seguir na janela do console:
   > 
   > `docker run -d -p 5000:5000 --restart=always --name registry registry:2`

2. A janela do VS Code carregará seu espaço de trabalho da solução IoT Edge. A pasta raiz contém uma pasta de **módulos**, uma pasta **.vscode** e um arquivo de modelo de manifesto de implantação. Configurações de depuração estão localizadas na pasta .vscode. Todos os códigos do módulo do usuário são subpastas da pasta de módulos. O arquivo deployment.template.json é o modelo de manifesto de implementação. Alguns dos parâmetros neste arquivo são analisados a partir do arquivo module.json, que existe em todas as pastas de módulos.

3. Adicione o segundo módulo a este projeto de solução. Digite e execute o comando **Edge: Adicionar módulo do IoT Edge**. Selecione o arquivo de modelo de implantação para atualizar. Selecione um módulo **Azure Function - C#** com o nome **SampleFunction** e seu repositório de imagens do Docker.

4. Abra o arquivo deployment.template.json. Verifique se o arquivo declara três módulos e o tempo de execução. A mensagem é gerada a partir do módulo tempSensor. A mensagem é redirecionada diretamente pelos módulos SampleModule e SampleFunction e, em seguida, enviada para o Hub IoT. 

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

1. Na **Paleta de Comandos** do VS Code, insira e execute o comando **Edge: Compilar solução do IoT Edge**. Com base no arquivo module.json em cada pasta de módulo, o comando começa a compilar, colocar em contêiner e efetuar push de cada imagem do Docker do módulo. Em seguida, o comando transmite o valor necessário para o arquivo deployment.template.json e gera o arquivo deployment.json com informações da pasta de configuração. O terminal integrado no VS Code mostra o andamento da compilação.

2. No **Device Explorer** do Hub IoT, clique com o botão direito do mouse em uma ID de dispositivo do IoT Edge e selecione o comando **Criar implantação para dispositivo do Edge**. Selecione o arquivo deployment.json na pasta de configuração. O terminal integrado no VS Code mostra a implantação criada com êxito com uma ID de implantação.

3. Se você estiver simulando um dispositivo do IoT Edge no computador de desenvolvimento, observe que todos os contêineres de imagem do módulo iniciam em poucos minutos.

## <a name="view-the-generated-data"></a>Exibir os dados gerados

1. Para monitorar os dados que chegam ao Hub IoT, selecione **Exibir** > **Paleta de Comandos**. Em seguida, selecione o comando **IoT: Iniciar monitoramento de mensagem D2C**. 
2. Para interromper o monitoramento de dados, use o comando **IoT: Parar o monitoramento de mensagem D2C** na **Paleta de Comandos**. 

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre outros cenários para desenvolvimento com o Azure IoT Edge no Visual Studio Code:

* [Depurar um módulo C# no VS Code](how-to-vscode-debug-csharp-module.md)
* [Depurar uma função C# no VS Code](how-to-vscode-debug-azure-function.md)
