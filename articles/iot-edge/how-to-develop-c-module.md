---
title: Depurar módulos C para o Azure IoT Edge | Microsoft Docs
description: Use o código do Visual Studio para desenvolver, criar e depurar um módulo C para o Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 07/20/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 9fc067c46828079f7369683b5edec682747cd5c7
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391445"
---
# <a name="use-visual-studio-code-to-develop-and-debug-c-modules-for-azure-iot-edge"></a>Use o código do Visual Studio para desenvolver e depurar módulos C para o Azure IoT Edge

Você pode transformar sua lógica de negócios em módulos do Azure IoT Edge. Este artigo mostra como usar o código do Visual Studio (código VS) como a principal ferramenta para desenvolver e depurar módulos C.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo presume que você esteja usando um computador ou uma máquina virtual que executa Windows ou Linux como seu computador de desenvolvimento. E você simula seu dispositivo IoT Edge em sua máquina de desenvolvimento.

> [!NOTE]
> Este artigo sobre depuração demonstra como anexar um processo em um contêiner de módulo e depurá-lo com o VS Code. Você só pode depurar módulos C em contêineres Linux amd64. Se você não estiver familiarizado com os recursos de depuração do Visual Studio Code, leia sobre [Depuração](https://code.visualstudio.com/Docs/editor/debugging). 

Este artigo usa o Visual Studio Code como ferramenta de desenvolvimento principal; instale o VS Code. Em seguida, adicione as extensões necessárias:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Extensão do Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Extensão do C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) para Visual Studio Code.
* [Extensão do Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Para criar um módulo, você precisa do Docker para construir a imagem do módulo e um registro do contêiner para manter a imagem do módulo:
* [Docker Community Edition](https://docs.docker.com/install/) no computador de desenvolvimento. 
* [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) ou [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   > [!TIP]
   > Você pode usar um registro do Docker local, em vez de um registro em nuvem, para fins de protótipo e teste. 

Para testar o módulo em um dispositivo, é necessário um hub IoT ativo com pelo menos um dispositivo do IoT Edge. Para usar seu computador como um dispositivo do IoT Edge, siga as etapas no guia de início rápido para [Windows](quickstart.md) ou [Linux](quickstart-linux.md). 

## <a name="create-a-new-solution-template"></a>Crie um novo modelo de solução

Siga estas etapas para criar um módulo IoT Edge com base no SDC do Azure IoT C usando o Visual Studio Code e a extensão do Azure IoT Edge. Primeiro, crie uma solução e, em seguida, gere o primeiro módulo nessa solução. Cada solução pode conter mais de um módulo. 

1. No Visual Studio Code, selecione **Exibir** > **Terminal Integrado**.
3. Selecione **Exibir** > **Paleta de Comandos**. 
4. Na paleta de comandos, insira e execute o comando **Azure IoT Edge: New IoT Edge Solution**.

   ![Executar a nova solução do IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

5. Navegue até a pasta na qual deseja criar a nova solução. Escolha **Selecionar pasta**. 
6. Digite um nome para a solução. 
7. Selecione **módulo C** como o modelo para o primeiro módulo na solução.
8. Digite um nome para o módulo. Escolha um nome exclusivo no registro de contêiner. 
9. Forneça o nome do repositório de imagens do módulo. O VS Code popula automaticamente o nome do módulo com **localhost:5000**. Substitua-o pelas informações de seu registro. Se você usa um registro local do Docker para testes, **localhost** é uma opção adequada. Se usar o Registro de Contêiner do Azure, utilize o servidor de início de sessão nas configurações do registro. O servidor de início de seção é semelhante ao **\<nome do registro\>.azurecr.io**.

O VS Code usa as informações fornecidas, cria uma solução do IoT Edge e, em seguida, a carrega em uma nova janela.

   ![Exibir solução do IoT Edge](./media/how-to-develop-c-module/view-solution.png)

Há quatro itens na solução: 
* Uma pasta **.vscode** que contém configurações de depuração.
* Uma pasta **módulos** com subpastas para cada módulo. Neste ponto, você tem apenas um. No entanto, é possível adicionar mais na paleta de comandos usando o comando **Azure IoT Edge: Add IoT Edge Module**. 
* Um arquivo **.env** lista as variáveis de ambiente. Se o Registro de Contêiner do Azure for seu registro, você terá um nome de usuário e uma senha do Registro de Contêiner do Azure nele. 

   > [!NOTE]
   > O arquivo de ambiente será criado somente se você fornecer um repositório de imagens para o módulo. Se você aceitou os padrões do localhost para testar e depurar localmente, não será necessário declarar variáveis de ambiente. 

* Um arquivo **deployment.template.json** lista o novo módulo em conjunto com um módulo **tempSensor** de exemplo que simula dados que podem ser usados para teste. Para obter mais informações sobre o funcionamento dos manifestos de implantação, consulte [Saiba como usar manifestos de implantação para implantar módulos e estabelecer rotas](module-composition.md). 

## <a name="develop-your-module"></a>Desenvolver seu módulo

O código do módulo padrão C que vem com a solução está localizado em **módulos** > **\<seu nome de módulo\>** > **Main. c** . O módulo e o arquivo deployment.template.json são configurados de forma que você possa compilar a solução, enviá-la por push ao registro de contêiner e implantá-la em um dispositivo para iniciar os testes sem lidar com nenhum código. O módulo é criado para apenas receber entradas de uma fonte (nesse caso, o módulo tempSensor que simula dados) e redirecioná-las ao Hub IoT. 

Quando você estiver pronto para personalizar o modelo C com seu próprio código, use os [SDKs do Hub do Azure IoT](../iot-hub/iot-hub-devguide-sdks.md) para criar módulos que atendam às principais necessidades de soluções de IoT, como segurança, gerenciamento de dispositivos e confiabilidade. 

## <a name="build-and-deploy-your-module-for-debugging"></a>Compilar e implantar o módulo para depuração

Em cada pasta de módulo, há vários arquivos do Docker para diferentes tipos de contêiner. Use qualquer um dos arquivos que terminam com a extensão **.debug** para compilar o módulo para teste. Atualmente, os módulos C suportam a depuração apenas nos contêineres amd64 do Linux.

1. No VS Code, navegue até o arquivo `deployment.template.json`. Atualize o URL da sua imagem de módulo adicionando **.debug** ao final.

    ![Adicione **.debug** ao nome da imagem](./media/how-to-develop-c-module/image-debug.png)

2. Substitua o createOptions do módulo Node.js em **deployment.template.json** com o conteúdo abaixo e salve o arquivo: 
    
    ```json
    "createOptions": "{\"HostConfig\": {\"Privileged\": true}}"
    ```

2. Na paleta de comandos do VS Code, insira e execute o comando **Edge: Build IoT Edge solution**.
3. Selecione o arquivo `deployment.template.json` para a solução na paleta de comandos. 
4. No Device Explorer do Hub IoT do Azure, clique com botão direito do mouse em uma ID de dispositivo do IoT Edge. Em seguida, selecione **Criar implantação para dispositivo do IoT Edge**. 
5. Abra a pasta **config** de sua solução. Em seguida, selecione o arquivo `deployment.json`. Escolha **Selecionar Manifesto de Implantação do Edge**. 

Você verá a implantação criada com êxito, com uma ID de implantação em um terminal integrado com o VS Code.

Verifique o status do contêiner no gerenciador de Docker do VS Code ou executando o comando `docker ps` no terminal.

## <a name="start-debugging-c-module-in-vs-code"></a>Iniciar a depuração C módulo no VS Code
O VS Code mantém as informações de configuração de depuração em um arquivo `launch.json` localizado em uma pasta `.vscode` no espaço de trabalho. Esse arquivo `launch.json` foi gerado quando uma nova solução IoT Edge foi criada. Ele será atualizado sempre que você adicionar um novo módulo que oferece suporte à depuração. 

1. Navegue até a exibição de depuração do VS Code. Selecione o arquivo de configuração de depuração do módulo. O nome da opção de depuração deve ser semelhante ao **ModuleName Remote Debug (C)**

   ![Selecione a configuração de depuração](./media/how-to-develop-c-module/debug-config.png).

2. Navegue até `main.c`. Adicione um ponto de interrupção neste arquivo.

3. Selecione **Iniciar Depuração** ou selecione **F5**. Selecione o processo ao qual ele deverá ser anexado.

4. Na exibição de Depuração do VS Code, você verá as variáveis no painel esquerdo. 

O exemplo anterior mostra como depurar os módulos C IoT Edge nos contêineres. Ele adicionou portas expostas no contêiner de módulo createOptions. Após concluir a depuração dos módulos do Node.js, é recomendável remover essas portas expostas de módulos do IoT Edge prontos para produção.

## <a name="next-steps"></a>Próximas etapas

Após compilar o módulo, saiba como [Implantar módulos do Azure IoT Edge do Visual Studio Code](how-to-deploy-modules-vscode.md).

Para desenvolver módulos para seus dispositivos do IoT Edge, consulte [Entender e usar os SDKs de Hub IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md).
