---
title: Depurar módulos C# para Azure IoT Edge | Microsoft Docs
description: Usar Visual Studio Code para desenvolver, compilar e depurar um módulo C# para Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/27/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 92e476d38e6e56edca19afe78bed2705feadd0bb
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39040901"
---
# <a name="use-visual-studio-code-to-develop-and-debug-c-modules-for-azure-iot-edge"></a>Usar Visual Studio Code para desenvolver e depurar módulos C# para Azure IoT Edge

Você pode transformar sua lógica de negócios em módulos do Azure IoT Edge. Este artigo mostra como usar o VS Code (Visual Studio Code) como ferramenta principal para desenvolver e depurar módulos C#.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo presume que você esteja usando um computador ou uma máquina virtual que executa Windows ou Linux como seu computador de desenvolvimento. Seu dispositivo do IoT Edge pode ser outro dispositivo físico. Você também pode simular seu dispositivo do IoT Edge no computador de desenvolvimento.

> [!NOTE]
> Este artigo sobre depuração demonstra como anexar um processo em um contêiner de módulo e depurá-lo com o VS Code. Só é possível depurar funções de C# em contêineres amd64 Linux. Se você não estiver familiarizado com os recursos de depuração do Visual Studio Code, leia sobre [Depuração](https://code.visualstudio.com/Docs/editor/debugging). 

Este artigo usa o Visual Studio Code como ferramenta de desenvolvimento principal; instale o VS Code. Em seguida, adicione as extensões necessárias:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Extensão do Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Extensão C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Extensão Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Para criar um módulo, você precisa do .NET para construir a pasta do projeto, o Docker para construir a imagem do módulo e um registro de contêiner para manter a imagem do módulo:
* [SDK do .NET Core 2.1](https://www.microsoft.com/net/download).
* [Docker Community Edition](https://docs.docker.com/install/) no computador de desenvolvimento. 
* [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) ou [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   > [!TIP]
   > Você pode usar um registro do Docker local, em vez de um registro em nuvem, para fins de protótipo e teste. 

Para testar o módulo em um dispositivo, é necessário um hub IoT ativo com pelo menos um dispositivo do IoT Edge. Para usar seu computador como um dispositivo do IoT Edge, siga as etapas no guia de início rápido para [Windows](quickstart.md) ou [Linux](quickstart-linux.md). 

## <a name="create-a-new-solution-template"></a>Crie um novo modelo de solução

Siga estas etapas para criar um módulo do IoT Edge baseado no .NET Core 2.0 usando o Visual Studio Code e a extensão do Azure IoT Edge. Primeiro, crie uma solução e, em seguida, gere o primeiro módulo nessa solução. Cada solução pode conter mais de um módulo. 

1. No Visual Studio Code, selecione **Exibir** > **Terminal Integrado**.
3. Selecione **Exibir** > **Paleta de Comandos**. 
4. Na paleta de comandos, insira e execute o comando **Azure IoT Edge: New IoT Edge Solution**.

   ![Executar a nova solução do IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

5. Navegue até a pasta na qual deseja criar a nova solução. Escolha **Selecionar pasta**. 
6. Digite um nome para a solução. 
7. Selecione **Módulo C#** como o modelo para o primeiro módulo da solução.
8. Digite um nome para o módulo. Escolha um nome exclusivo no registro de contêiner. 
9. Forneça o nome do repositório de imagens do módulo. O VS Code popula automaticamente o nome do módulo com **localhost:5000**. Substitua-o pelas informações de seu registro. Se você usa um registro local do Docker para testes, **localhost** é uma opção adequada. Se usar o Registro de Contêiner do Azure, utilize o servidor de início de sessão nas configurações do registro. O servidor de início de seção é semelhante ao **\<nome do registro\>.azurecr.io**.

O VS Code usa as informações fornecidas, cria uma solução do IoT Edge e, em seguida, a carrega em uma nova janela.

   ![Exibir solução do IoT Edge](./media/how-to-develop-csharp-module/view-solution.png)

Há quatro itens na solução: 
* Uma pasta **.vscode** que contém configurações de depuração.
* Uma pasta **módulos** com subpastas para cada módulo. Neste ponto, você tem apenas um. No entanto, é possível adicionar mais na paleta de comandos usando o comando **Azure IoT Edge: Add IoT Edge Module**. 
* Um arquivo **.env** lista as variáveis de ambiente. Se o Registro de Contêiner do Azure for seu registro, você terá um nome de usuário e uma senha do Registro de Contêiner do Azure nele. 

   >[!NOTE]
   >O arquivo de ambiente será criado somente se você fornecer um repositório de imagens para o módulo. Se você aceitou os padrões do localhost para testar e depurar localmente, não será necessário declarar variáveis de ambiente. 

* Um arquivo **deployment.template.json** lista o novo módulo em conjunto com um módulo **tempSensor** de exemplo que simula dados que podem ser usados para teste. Para obter mais informações sobre o funcionamento dos manifestos de implantação, consulte [Saiba como usar manifestos de implantação para implantar módulos e estabelecer rotas](module-composition.md). 

## <a name="devlop-your-module"></a>Desenvolver seu módulo

O código padrão do Azure Functions que vem com a solução está localizado em **módulos** > **\<nome do seu módulo\>** > **Program.cs**. O módulo e o arquivo deployment.template.json são configurados de forma que você possa compilar a solução, enviá-la por push ao registro de contêiner e implantá-la em um dispositivo para iniciar os testes sem lidar com nenhum código. O módulo é criado para apenas receber entradas de uma fonte (nesse caso, o módulo tempSensor que simula dados) e redirecioná-las ao Hub IoT. 

Quando estiver pronto para personalizar o modelo C# com seu próprio código, use os [SDKs do Hub IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md) para compilar módulos que tratem das principais necessidades das soluções de IoT, como confiabilidade, segurança e gerenciamento do dispositivo. 

## <a name="build-and-deploy-your-module-for-debugging"></a>Compilar e implantar o módulo para depuração

Em cada pasta de módulo, há vários arquivos do Docker para diferentes tipos de contêiner. Use qualquer um dos arquivos que terminam com a extensão **.debug** para compilar o módulo para teste. Atualmente, os módulos C# dão suporte à depuração apenas em contêineres amd64 Linux.

1. No VS Code, navegue até o arquivo `deployment.template.json`. Atualize a URL da imagem de função, adicionando **.debug** no final.

   ![Adicione **.debug** ao nome da imagem](./media/how-to-develop-csharp-module/image-debug.png)

2. Na paleta de comandos do VS Code, insira e execute o comando **Edge: Build IoT Edge solution**.
3. Selecione o arquivo `deployment.template.json` para a solução na paleta de comandos. 
4. No Device Explorer do Hub IoT do Azure, clique com botão direito do mouse em uma ID de dispositivo do IoT Edge. Em seguida, selecione **Criar implantação para dispositivo do IoT Edge**. 
5. Abra a pasta **config** de sua solução. Em seguida, selecione o arquivo `deployment.json`. Escolha **Selecionar Manifesto de Implantação do Edge**. 

Você verá a implantação criada com êxito, com uma ID de implantação em um terminal integrado com o VS Code.

Verifique o status do contêiner no gerenciador de Docker do VS Code ou executando o comando `docker ps` no terminal.

## <a name="start-debugging-c-module-in-vs-code"></a>Começar a depurar o módulo C# no VS Code
O VS Code mantém as informações de configuração de depuração em um arquivo `launch.json` localizado em uma pasta `.vscode` no espaço de trabalho. Esse arquivo `launch.json` foi gerado quando uma nova solução IoT Edge foi criada. Ele será atualizado sempre que você adicionar um novo módulo que oferece suporte à depuração. 

1. Navegue até a exibição de depuração do VS Code. Selecione o arquivo de configuração de depuração do módulo. O nome da opção de depuração deve ser semelhante a **ModuleName Remote Debug (.NET Core)**

   ![Selecione a configuração de depuração](./media/how-to-develop-csharp-module/debug-config.png).

2. Navegue até `program.cs`. Adicione um ponto de interrupção neste arquivo.

3. Selecione **Iniciar Depuração** ou selecione **F5**. Selecione o processo ao qual ele deverá ser anexado.

4. Na exibição de Depuração do VS Code, você verá as variáveis no painel esquerdo. 

> [!NOTE]
> Este exemplo mostra como depurar módulo do IoT Edge do .NET Core em contêineres. Ele é baseado na versão de depuração do `Dockerfile.debug`, que inclui o depurador de linha de comando do .NET Core VSDBG na imagem de contêiner durante a criação. Após depurar seus módulos C#, recomendamos que você use ou personalize diretamente o `Dockerfile` sem o VSDBG para os módulos do IoT Edge prontos para produção.

## <a name="next-steps"></a>Próximas etapas

Após compilar o módulo, saiba como [Implantar módulos do Azure IoT Edge do Visual Studio Code](how-to-deploy-modules-vscode.md).

Para desenvolver módulos para seus dispositivos do IoT Edge, consulte [Entender e usar os SDKs de Hub IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md).
