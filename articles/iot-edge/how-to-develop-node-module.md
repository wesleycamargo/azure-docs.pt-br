---
title: Depurar módulos Node.js para Azure IoT Edge | Microsoft Docs
description: Usar o Visual Studio Code para desenvolver e depurar módulos Node.js para Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/26/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 78e952b5b1eedc1757cfe636eb13e411044dce54
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2018
ms.locfileid: "42140384"
---
# <a name="develop-and-debug-nodejs-modules-with-azure-iot-edge-for-visual-studio-code"></a>Desenvolver e depurar módulos Node.js com Azure IoT Edge para Visual Studio Code

É possível enviar a lógica de negócios para operar na borda, transformando-a em módulos para Azure IoT Edge. Este artigo fornece instruções detalhadas para usar o VS Code (Visual Studio Code) como a principal ferramenta de desenvolvimento para desenvolver módulos Node.js.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo assume que você está usando um computador ou uma máquina virtual que executa Windows ou Linux como seu computador de desenvolvimento. O dispositivo do IoT Edge pode ser outro dispositivo físico ou você pode simular dispositivo do IoT Edge no computador de desenvolvimento.

> [!NOTE]
> Este tutorial de depuração descreve como anexar um processo em um contêiner de módulo e depurá-lo com o VS Code. É possível depurar módulos Node.js nos contêineres linux-amd64, windows e arm32. Se você não estiver familiarizado com os recursos de depuração do Visual Studio Code, leia sobre [Depuração](https://code.visualstudio.com/Docs/editor/debugging). 

Como este artigo usa o Visual Studio Code como a principal ferramenta de desenvolvimento, instale o VS Code e adicione as extensões necessárias:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Extensão do Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Extensão do Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Para criar um módulo, é necessário o Node.js, que inclui npm para compilar a pasta de projeto, Docker para compilar a imagem de módulo e um registro de contêiner para manter a imagem de módulo:
* [Node.js](https://nodejs.org)
* [Docker](https://docs.docker.com/engine/installation/)
* [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) ou [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   >[!TIP]
   >Você pode usar um registro local do Docker para fins de protótipo e teste, em vez de um registro em nuvem. 

Para testar o módulo em um dispositivo, é necessário um hub IoT ativo com pelo menos um dispositivo do IoT Edge. Se você quiser usar o computador como um dispositivo do IoT Edge, poderá fazer isso seguindo as etapas nos tutoriais do [Windows](quickstart.md) ou [Linux](quickstart-linux.md). 

## <a name="create-a-new-solution-template"></a>Crie um novo modelo de solução

As etapas a seguir mostram como criar um módulo do IoT Edge baseado em Node.js usando o Visual Studio Code e a extensão do Azure IoT Edge. Você começa criando uma solução e, em seguida, gerando o primeiro módulo nessa solução. Cada solução pode conter vários módulos. 

1. No Visual Studio Code, selecione **Exibir** > **Terminal Integrado**.
2. No terminal integrado, digite o comando a seguir para instalar (ou atualizar) a última versão do modelo de módulo do Azure IoT Edge para Node.js:

   ```cmd/sh
   npm install -g yo generator-azure-iot-edge-module
   ```
3. No Visual Studio Code, selecione **Exibir** > **Paleta de Comandos**. 
4. Na paleta de comandos, digite e execute o comando **Azure IoT Edge: Nova solução do IoT Edge**.

   ![Executar a nova solução do IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

5. Navegue até a pasta em que você deseja criar a nova solução e clique em **Selecionar pasta**. 
6. Forneça um nome para a solução. 
7. Escolha **Módulo Node.js** como o modelo para o primeiro módulo na solução.
8. Forneça um nome para o módulo. Escolha um nome exclusivo no registro de contêiner. 
9. Forneça o repositório de imagem para o módulo. O VS Code preenche automaticamente o nome do módulo, portanto, basta substituir **localhost: 5000** pelas suas próprias informações de registro. Se você usar um registro local do Docker para teste, então o localhost está correto. Se usar o Registro de Contêiner do Azure, utilize o servidor de início de sessão nas configurações do registro. O servidor de início de seção é semelhante ao **\<nome do registro\>.azurecr.io**.

O VS Code obtém as informações fornecidas, cria uma solução do IoT Edge e carrega-as em uma nova janela.

Dentro da solução há três itens: 
* Uma pasta **.vscode** que contém configurações de depuração.
* Uma pasta **módulos** que contém subpastas para cada módulo. No momento você só tem um, mas é possível adicionar mais na paleta de comandos com o comando **Azure IoT Edge: Adicionar módulo do IoT Edge**. 
* Um arquivo **.env** lista as variáveis de ambiente. Se o Registro de Contêiner do Azure for seu registro, você terá um nome de usuário e uma senha do Registro de Contêiner do Azure nele.

   >[!NOTE]
   >O arquivo de ambiente será criado somente se você fornecer um repositório de imagens para o módulo. Se você aceitou os padrões do localhost para testar e depurar localmente, não será necessário declarar variáveis de ambiente. 

* Um arquivo **deployment.template.json** lista o novo módulo junto com um módulo **tempSensor** de exemplo que simula dados que podem ser usados para teste. Para obter mais informações sobre como os manifestos de implantação funcionam, consulte [Entender como os módulos do IoT Edge podem ser utilizados, configurados e reutilizados](module-composition.md).

## <a name="develop-your-module"></a>Desenvolver seu módulo

O código Node.js padrão que vem com a solução está localizado nos **módulos** > **\<nome do seu módulo\>** > **app.js**. O módulo e o arquivo deployment.template.json são configurados de forma que você possa compilar a solução, enviá-la por push ao registro de contêiner e implantá-la em um dispositivo para iniciar os testes sem lidar com nenhum código. O módulo é criado para apenas receber entradas de uma fonte (nesse caso, o módulo tempSensor que simula dados) e redirecioná-las ao Hub IoT. 

Quando estiver pronto para personalizar o modelo Node.js com seu próprio código, use os [SDKs do Hub IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md) para compilar módulos que tratem das principais necessidades das soluções de IoT, como confiabilidade, segurança e gerenciamento do dispositivo. 

## <a name="build-and-deploy-your-module-for-debugging"></a>Compilar e implantar o módulo para depuração

Em cada pasta de módulo há vários arquivos do Docker para diferentes tipos de contêiner. É possível usar qualquer um desses arquivos que terminem com a extensão **.debug** para compilar o módulo para teste. Atualmente, os módulos Node.js só dão suporte à depuração em contêineres linux-amd64, windows-amd64 e linux-arm32v7.

1. No VS Code, navegue até o arquivo `deployment.template.json`. Atualize o URL da sua imagem de módulo adicionando **.debug** ao final.
2. Substitua o createOptions do módulo Node.js em **deployment.template.json** com o conteúdo abaixo e salve o arquivo: 
    ```json
    "createOptions": "{\"ExposedPorts\":{\"9229/tcp\":{}},\"HostConfig\":{\"PortBindings\":{\"9229/tcp\":[{\"HostPort\":\"9229\"}]}}}"
    ```

2. Na paleta de comandos do VS Code, digite e execute o comando **Azure IoT Edge: Compilar solução IoT Edge**.
3. Selecione o arquivo `deployment.template.json` para a solução na paleta de comandos. 
4. No gerenciador de Dispositivos do Hub IoT, clique com o botão direito do mouse em uma ID do dispositivo do IoT Edge e, em seguida, selecione **Criar implantação para dispositivo do IoT Edge**. 
5. Abra a pasta **config** da solução e, em seguida, selecione o arquivo`deployment.json`. Clique em **Selecionar Manifesto de Implantação do Edge**. 

Em seguida, você pode ver se a implantação foi criada com êxito com uma ID de implantação no terminal integrado do VS Code.

Você pode verificar o status de contêiner no gerenciador de Docker do VS Code ou executando o comando `docker ps` no terminal.

## <a name="start-debugging-nodejs-module-in-vs-code"></a>Iniciar a depuração do módulo Node.js no VS Code

O VS Code mantém as informações de configuração de depuração em um arquivo `launch.json` localizado em uma pasta `.vscode` no espaço de trabalho. Esse arquivo `launch.json` foi gerado quando uma nova solução IoT Edge foi criada. Ele será atualizado sempre que você adicionar um novo módulo que oferece suporte à depuração. 

1. Navegue até a exibição de depuração do VS Code e selecione o arquivo de configuração de depuração para o módulo.

2. Navegue até `app.js`. Adicione um ponto de interrupção neste arquivo.

3. Clique no botão **Iniciar Depuração** ou pressione **F5** e selecione o processo ao qual anexar.

4. Na exibição Depuração do VS Code, você pode ver as variáveis no painel esquerdo. 

O exemplo anterior mostra como depurar os módulos do Node.js IoT Edge nos contêineres. Ele adicionou portas expostas no contêiner de módulo createOptions. Após concluir a depuração dos módulos do Node.js, é recomendável remover essas portas expostas de módulos do IoT Edge prontos para produção.

## <a name="next-steps"></a>Próximas etapas

Após compilar o módulo, saiba como [Implantar módulos do Azure IoT Edge do Visual Studio Code](how-to-deploy-modules-vscode.md)

Para desenvolver módulos para seus dispositivos do IoT Edge, consulte [Entender e usar os SDKs de Hub IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md).
