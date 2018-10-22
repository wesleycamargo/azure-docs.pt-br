---
title: Depurar módulos do Python para o Azure IoT Edge | Microsoft Docs
description: Use o código do Visual Studio para desenvolver, criar e depurar um módulo Python para o Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 09/13/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 4393d3231ca4f0a813d4586161383bd6d61f6a49
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/13/2018
ms.locfileid: "45552195"
---
# <a name="use-visual-studio-code-to-develop-and-debug-python-modules-for-azure-iot-edge"></a>Use o código do Visual Studio para desenvolver e depurar módulos Python para o Azure IoT Edge

Você pode transformar sua lógica de negócios em módulos do Azure IoT Edge. Este artigo mostra como usar o código do Visual Studio (código VS) como a principal ferramenta para desenvolver e depurar módulos Python.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo presume que você esteja usando um computador ou uma máquina virtual que executa Windows ou Linux como seu computador de desenvolvimento. E você simula seu dispositivo IoT Edge em sua máquina de desenvolvimento com o daemon de segurança IoT Edge.

> [!NOTE]
> Este artigo sobre depuração demonstra como anexar um processo em um contêiner de módulo e depurá-lo com o VS Code. Você só pode depurar módulos Python em contêineres amd64 do Linux. Se você não estiver familiarizado com os recursos de depuração do Visual Studio Code, leia sobre [Depuração](https://code.visualstudio.com/Docs/editor/debugging). 

Este artigo usa o Visual Studio Code como ferramenta de desenvolvimento principal; instale o VS Code. Em seguida, adicione as extensões necessárias:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Extensão do Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Extensão do Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Extensão do Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) para Visual Studio Code. 
* [Python](https://www.python.org/downloads/).
* [PIP](https://pip.pypa.io/en/stable/installing/#installation) para instalar os pacotes do Python (normalmente incluídos com a instalação do Python).
* Instale **Cookiecutter** com o comando a seguir
   
    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```

Para criar um módulo, você precisa do Docker para construir a imagem do módulo e um registro do contêiner para manter a imagem do módulo:
* [Docker Community Edition](https://docs.docker.com/install/) no computador de desenvolvimento. 
* [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) ou [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   * Você pode usar um registro do Docker local, em vez de um registro em nuvem, para fins de protótipo e teste. 

Para testar o módulo em um dispositivo, é necessário um hub IoT ativo com pelo menos um dispositivo do IoT Edge. Para usar seu computador como um dispositivo IoT Edge, siga as etapas no guia de início rápido para [Linux](quickstart-linux.md). 

## <a name="create-a-new-solution-template"></a>Crie um novo modelo de solução

Siga estas etapas para criar um módulo do IoT Edge com base no SDK de Python do IoT do Azure usando o Visual Studio Code e a extensão do Azure IoT Edge. Primeiro, crie uma solução e, em seguida, gere o primeiro módulo nessa solução. Cada solução pode conter mais de um módulo. 

1. No Visual Studio Code, selecione **Exibir** > **Terminal Integrado**.

2. Selecione **Exibir** > **Paleta de Comandos**. 

3. Na paleta de comandos, insira e execute o comando **Azure IoT Edge: New IoT Edge Solution**.

   ![Executar a nova solução do IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

4. Navegue até a pasta na qual deseja criar a nova solução. Escolha **Selecionar pasta**. 

5. Digite um nome para a solução. 

6. Selecione **módulo de Python** como o modelo para o primeiro módulo na solução.

7. Digite um nome para o módulo. Escolha um nome exclusivo no registro de contêiner. 

8. Forneça o nome do repositório de imagens do módulo. O VS Code popula automaticamente o nome do módulo com **localhost:5000**. Substitua-o pelas informações de seu registro. Se você usa um registro local do Docker para testes, **localhost** é uma opção adequada. Se usar o Registro de Contêiner do Azure, utilize o servidor de início de sessão nas configurações do registro. O servidor de início de seção é semelhante ao **\<nome do registro\>.azurecr.io**. Substitua somente a parte de localhost da cadeia de caracteres, não exclua o nome do módulo. 

   ![Fornecer o repositório de imagem do Docker](./media/how-to-develop-c-module/repository.png)

O VS Code usa as informações fornecidas, cria uma solução do IoT Edge e, em seguida, a carrega em uma nova janela.

Há quatro itens na solução: 
* Uma pasta **.vscode** que contém configurações de depuração.
* Uma pasta **módulos** com subpastas para cada módulo. Neste ponto, você tem apenas um. No entanto, é possível adicionar mais na paleta de comandos usando o comando **Azure IoT Edge: Add IoT Edge Module**. 
* Um arquivo **.env** lista as variáveis de ambiente. Se o Registro de Contêiner do Azure for seu registro, você terá um nome de usuário e uma senha do Registro de Contêiner do Azure nele. 

   > [!NOTE]
   > O arquivo de ambiente será criado somente se você fornecer um repositório de imagens para o módulo. Se você aceitou os padrões do localhost para testar e depurar localmente, não será necessário declarar variáveis de ambiente. 

* Um arquivo **deployment.template.json** lista o novo módulo em conjunto com um módulo **tempSensor** de exemplo que simula dados que podem ser usados para teste. Para obter mais informações sobre o funcionamento dos manifestos de implantação, consulte [Saiba como usar manifestos de implantação para implantar módulos e estabelecer rotas](module-composition.md). 

## <a name="develop-your-module"></a>Desenvolver seu módulo

O código do módulo de Python padrão que vem com a solução está localizado em **módulos** > [nome do módulo] > **main.py**. O módulo e o arquivo deployment.template.json são configurados de forma que você possa compilar a solução, enviá-la por push ao registro de contêiner e implantá-la em um dispositivo para iniciar os testes sem lidar com nenhum código. O módulo é criado para apenas receber entradas de uma fonte (nesse caso, o módulo tempSensor que simula dados) e redirecioná-las ao Hub IoT. 

Quando você estiver pronto para personalizar o modelo de Python com seu próprio código, use o [SDKs do Hub IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md) crie módulos que abordam a chave precisa para soluções de IoT, como, confiabilidade, segurança e gerenciamento de dispositivo. 

## <a name="build-and-deploy-your-module-for-debugging"></a>Compilar e implantar o módulo para depuração

Em cada pasta de módulo, há vários arquivos do Docker para diferentes tipos de contêiner. Use qualquer um dos arquivos que terminam com a extensão **.debug** para compilar o módulo para teste. Atualmente, os módulos de Python dá suporte à depuração apenas em contêineres do Linux amd64. 

1. No VS Code, navegue até o arquivo `deployment.template.json`. Atualize o URL da sua imagem de módulo adicionando **.debug** ao final.

2. Substitua o createOptions do módulo Python em **deployment.template.json** com o conteúdo abaixo e salve este arquivo: 
    
    ```json
    "createOptions": "{\"ExposedPorts\":{\"5678/tcp\":{}},\"HostConfig\":{\"PortBindings\":{\"5678/tcp\":[{\"HostPort\":\"5678\"}]}}}"
    ```

3. Navegue até `main.py`, adicione os seguintes códigos após a seção de importação
    
    ```python
    import ptvsd
    ptvsd.enable_attach(('0.0.0.0',  5678))
    ```

4. Adicione a seguinte linha única de código para o retorno de chamada que você deseja depurar

    ```python
    ptvsd.break_into_debugger()
    ```

   Por exemplo, se você deseja depurar `receive_message_callback` método. Você pode Inseto, como a única linha de código abaixo.

    ```python
    def receive_message_callback(message, hubManager):
        ptvsd.break_into_debugger()
        global RECEIVE_CALLBACKS
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        print ( "    Data: <<<%s>>> & Size=%d" % (message_buffer[:size].decode('utf-8'), size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

2. Na paleta de comandos do VS Code, digite e execute o comando do **Azure IoT Edge: Push IoT Edge e Build de solução**.
3. Selecione o arquivo `deployment.template.json` para a solução na paleta de comandos. 
4. No Device Explorer do Hub IoT do Azure, clique com botão direito do mouse em uma ID de dispositivo do IoT Edge. Em seguida, selecione **Criar implantação para dispositivo único**. 
5. Abra a pasta **config** de sua solução. Em seguida, selecione o arquivo `deployment.json`. Escolha **Selecionar Manifesto de Implantação do Edge**. 

Você verá a implantação criada com êxito, com uma ID de implantação em um terminal integrado com o VS Code.

Verifique o status do contêiner no gerenciador de Docker do VS Code ou executando o comando `docker ps` no terminal.

## <a name="start-debugging-python-module-in-vs-code"></a>Iniciar a depuração do módulo de Python no Visual Studio Code
O VS Code mantém as informações de configuração de depuração em um arquivo `launch.json` localizado em uma pasta `.vscode` no workspace. Esse arquivo `launch.json` foi gerado quando uma nova solução IoT Edge foi criada. Ele será atualizado sempre que você adicionar um novo módulo que oferece suporte à depuração. 

1. Navegue até a exibição de depuração do VS Code. Selecione o arquivo de configuração de depuração do módulo. O nome da opção de depuração deve ser semelhante ao **ModuleName depuração remota (Python)**

2. Navegue até `main.c`. Adicionar um ponto de interrupção no método de retorno de chamada que você os colocou `ptvsd.break_into_debugger()` nele.

3. Selecione **Iniciar Depuração** ou selecione **F5**. Selecione o processo ao qual ele deverá ser anexado.

4. Na exibição de Depuração do VS Code, você verá as variáveis no painel esquerdo. 

O exemplo anterior mostra como depurar os módulos do Python IoT Edge nos contêineres. Ele adicionou portas expostas no contêiner de módulo createOptions. Depois de concluir a depuração de seus módulos Python, recomendamos que você remova essas portas expostas para módulos IoT Edge prontos para produção.

## <a name="next-steps"></a>Próximas etapas

Após compilar o módulo, saiba como [Implantar módulos do Azure IoT Edge do Visual Studio Code](how-to-deploy-modules-vscode.md).

Para desenvolver módulos para seus dispositivos do IoT Edge, consulte [Entender e usar os SDKs de Hub IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md).
