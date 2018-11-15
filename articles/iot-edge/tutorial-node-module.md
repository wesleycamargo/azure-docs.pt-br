---
title: Tutorial de Node.js do Azure IoT Edge | Microsoft Docs
description: Este tutorial mostra como criar um módulo do IoT Edge com código em Node.js e implantá-lo em um dispositivo de borda
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 09/21/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 40fa0357245ad77fbdb08c5dbb4839d69322954f
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51566785"
---
# <a name="tutorial-develop-and-deploy-a-nodejs-iot-edge-module-to-your-simulated-device"></a>Tutorial: Desenvolver e implantar um módulo do IoT Edge em Node.js em seu dispositivo simulado

Use os módulos do IoT Edge para implantar um código que implementa a lógica de negócios diretamente em seus dispositivos IoT Edge. Este tutorial o orienta através da criação e implantação de um módulo IoT Edge que filtra os dados do sensor. Você usará o dispositivo do IoT Edge simulado que criou nos inícios rápidos. Neste tutorial, você aprenderá como:    

> [!div class="checklist"]
> * Use o Visual Studio Code para criar um módulo Node.js do IoT Edge
> * Usar o Visual Studio Code e o Docker para criar uma imagem do Docker e publicá-la no registro 
> * Implantar o módulo no dispositivo IoT Edge
> * Exibir os dados gerados


O módulo IoT Edge que criado neste tutorial filtra os dados de temperatura gerados pelo seu dispositivo. Ele somente envia mensagens upstream se a temperatura estiver acima de um limite especificado. Este tipo de análise na borda é útil para reduzir a quantidade de dados comunicados e armazenados na nuvem. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo do Azure IoT Edge:

* Você pode usar seu computador de desenvolvimento ou uma máquina virtual como um dispositivo do Edge seguindo as etapas no início rápido para os [dispositivos Linux](quickstart-linux.md) ou [Windows](quickstart.md).

Recursos de nuvem:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na camada padrão ou gratuito no Azure. 

Recursos de desenvolvimento:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Extensão do Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) para Visual Studio Code. 
* [CE do Docker](https://docs.docker.com/engine/installation/). 
* [Node.js e npm](https://nodejs.org). O pacote do npm é distribuído com Node.js, o que significa que quando você baixa Node.js, o npm é automaticamente instalado em seu computador.

## <a name="create-a-container-registry"></a>Criar um registro de contêiner
Neste tutorial, você utiliza a extensão do Azure IoT Edge do Visual Studio Code para compilar um módulo e criar uma **imagem de contêiner** dos arquivos. Em seguida, você efetua push dessa imagem para um **registro** que armazena e gerencia suas imagens. Finalmente, você implanta a imagem do seu registro para executar no dispositivo IoT Edge.  

Você pode usar qualquer registro compatível com o Docker neste tutorial. Dois serviços de registro do Docker populares disponíveis na nuvem são o [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) e o [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Este tutorial utiliza o Registro de Contêiner do Azure. 

1. No [Portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Contêineres** > **Registro de Contêiner do Azure** .
2. Nomeie seu registro, escolha uma assinatura, selecione um grupo de recursos e defina a SKU para **Básica**. 
3. Selecione **Criar**.
4. Depois que o registro de contêiner for criado, navegue até ele e selecione **Chaves de acesso**. 
5. Alterne **Usuário administrador** para **Ativar**.
6. Copie os valores para **Servidor de logon**, **Nome de usuário** e **Senha**. Você usará esses valores mais tarde no tutorial. 

## <a name="create-an-iot-edge-module-project"></a>Criar um projeto de módulo do IoT Edge
As etapas a seguir mostram como criar um módulo Node.js do IoT Edge usando o Visual Studio Code e a extensão do Azure IoT Edge.

### <a name="create-a-new-solution"></a>Criar uma nova solução

Use **npm** para criar um modelo de solução em Node.js para usar de base. 

1. No Visual Studio Code, selecione **Exibir** > **Terminal Integrado** para abrir o terminal integrado do Visual Studio Code.

2. No terminal integrado, digite o seguinte comando para instalar o **yeoman** e o gerador para o módulo Node.js do Azure IoT Edge: 

    ```cmd/sh
    npm install -g yo generator-azure-iot-edge-module
    ```

3. Selecione **Exibir** > **Paleta de comandos** para abrir a paleta de comandos do VS Code. 

3. Na paleta de comandos, digite e execute o comando **Azure: Entrar** e siga as instruções para entrar em sua conta do Azure. Se já tiver entrado, pode ignorar esta etapa.

4. Na paleta de comandos, digite e execute o comando **Azure IoT Edge: nova solução do IoT Edge**. Na paleta de comandos, forneça as seguintes informações para criar sua solução: 

   1. Selecione a pasta na qual deseja criar a solução. 
   2. Forneça um nome para a solução ou aceite o padrão **EdgeSolution**.
   3. Escolha **Módulo Node.js** como o modelo de módulo. 
   4. Nomeie seu módulo **NodeModule**. 
   5. Especifique o Registro de Contêiner do Azure que você criou na seção anterior como o repositório de imagens do primeiro módulo. Substitua **localhost:5000** pelo valor de servidor de logon que você copiou. A cadeia de caracteres final se parece com **\<nome do registro\>.azurecr.io/nodemodule**.

   ![Fornecer o repositório de imagem do Docker](./media/tutorial-node-module/repository.png)

A janela do VS Code carregará seu workspace da solução IoT Edge. O workspace da solução contém cinco componentes de nível superior. Você não editará a pasta **\.vscode** ou o arquivo **\.gitignore** neste tutorial. A pasta **módulos** contém o código Node.js para o seu módulo, bem como Dockerfiles para a compilação de seu módulo como uma imagem de contêiner. O arquivo **\.env** armazena suas credenciais de registro de contêiner. O arquivo **deployment.template.json** contém as informações que o tempo de execução do IoT Edge usa para implantar módulos em um dispositivo. 

Se você não especifica um registro de contêiner durante a criação de sua solução, mas aceita o valor de localhost:5000, você não terá um arquivo \.env. 

   ![Workspace da solução de Node.js](./media/tutorial-node-module/workspace.png)

### <a name="add-your-registry-credentials"></a>Adicionar suas credenciais de registro

O arquivo do ambiente armazena as credenciais para o repositório de contêiner e as compartilha com o tempo de execução do IoT Edge. O tempo de execução precisa dessas credenciais para efetuar pull de imagens privadas para o dispositivo IoT Edge. 

1. No explorador do VS Code, abra o arquivo **.env**. 
2. Atualize os campos com os valores de **nome de usuário** e **senha** que você copiou do registro de contêiner do Azure. 
3. Salve o arquivo. 

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com código personalizado

Cada modelo é fornecido com código de exemplo incluído, que usa dados de sensor simulados do módulo **tempSensor** e os encaminha para o Hub IoT. Nesta seção, adicione código para fazer o NodeModule analisar as mensagens antes de enviá-las. 

1. No explorador do VS Code, abra **modules** > **NodeModule** > **app.js**.

5. Adicione uma variável de limite de temperatura abaixo dos módulos de nó obrigatórios. O limite de temperatura define o valor que a temperatura medida deve exceder para que os dados sejam enviados para o Hub IoT.

    ```javascript
    var temperatureThreshold = 25;
    ```

6. Substitua toda a função `PipeMessage` pela função `FilterMessage`.
    
    ```javascript
    // This function filters out messages that report temperatures below the temperature threshold.
    // It also adds the MessageType property to the message with the value set to Alert.
    function filterMessage(client, inputName, msg) {
        client.complete(msg, printResultFor('Receiving message'));
        if (inputName === 'input1') {
            var message = msg.getBytes().toString('utf8');
            var messageBody = JSON.parse(message);
            if (messageBody && messageBody.machine && messageBody.machine.temperature && messageBody.machine.temperature > temperatureThreshold) {
                console.log(`Machine temperature ${messageBody.machine.temperature} exceeds threshold ${temperatureThreshold}`);
                var outputMsg = new Message(message);
                outputMsg.properties.add('MessageType', 'Alert');
                client.sendOutputEvent('output1', outputMsg, printResultFor('Sending received message'));
            }
        }
    }

    ```

7. Substitua o nome da função `pipeMessage` por `filterMessage` na função `client.on()`.

    ```javascript
    client.on('inputMessage', function (inputName, msg) {
        filterMessage(client, inputName, msg);
        });
    ```

8. Copie o snippet de código a seguir para o retorno de chamada da função `client.open()`, após `client.on()` dentro da instrução `else`. Essa função é invocada quando as propriedades desejadas são atualizadas.

    ```javascript
    client.getTwin(function (err, twin) {
        if (err) {
            console.error('Error getting twin: ' + err.message);
        } else {
            twin.on('properties.desired', function(delta) {
                if (delta.TemperatureThreshold) {
                    temperatureThreshold = delta.TemperatureThreshold;
                }
            });
        }
    });
    ```

9. Salve o arquivo.

## <a name="build-your-iot-edge-solution"></a>Criar solução IoT Edge

Na seção anterior, você criou uma solução IoT Edge e adicionou um código a NodeModule que filtrará mensagens em que a temperatura relatada da máquina estiver abaixo do limite aceitável. Agora você precisa compilar a solução como uma imagem de contêiner e enviá-la por push para seu registro de contêiner. 

1. Entre no Docker digitando o seguinte comando no terminal integrado do Visual Studio Code para que você possa enviar sua imagem de módulo para o ACR: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Use o nome de usuário, a senha e o servidor de logon que você copiou de seu Registro de Contêiner do Azure na primeira seção. Ou recupere-as novamente da seção **Chaves de acesso** do registro no portal do Azure.

2. No gerenciador do VS Code, abra o arquivo **deployment.template.json** no workspace da solução IoT Edge. 

   Este arquivo manda o `$edgeAgent` implantar dois módulos: **tempSensor**, que simula a dados do dispositivo, e **NodeModule**. O valor `NodeModule.image` é definido como uma versão Linux amd64 da imagem. Para saber mais sobre manifestos de implantação, consulte [Entender como os módulos do IoT Edge podem ser utilizados, configurados e reutilizados](module-composition.md).

   Esse arquivo também contém suas credenciais de registro. No arquivo de modelo, o nome de usuário e a senha são preenchidos com espaços reservados. Quando você gera o manifesto de implantação, os campos são atualizados com os valores adicionados ao **.env**. 

4. Adicione o módulo gêmeo NodeModule no manifesto de implantação. Insira o seguinte conteúdo JSON na parte inferior da seção `moduleContent`, após o módulo gêmeo do `$edgeHub`: 
    ```json
        "NodeModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```
5. Salve o arquivo.
6. No explorador do VS Code, clique com o botão direito do mouse no arquivo **deployment.template.json** e selecione **Compilar e enviar por push solução IoT Edge**. 

Quando você solicitar ao Visual Studio Code para compilar sua solução, primeiro ele usará as informações no modelo de implantação e gerará um arquivo `deployment.json` em uma nova pasta **config**. Em seguida, ele executará dois comandos no terminal integrado: `docker build` e `docker push`. Esses dois comandos compilam seu código, conteinerizam o código Node.js e enviam por push para o registro de contêiner que você especificou ao inicializar a solução. 

Você pode obter o endereço de imagem de contêiner completo com marca no comando `docker build` executado no terminal integrado do VS Code. O endereço da imagem é criado de informações do arquivo `module.json` com o formato **\<repositório\>:\<versão\>-\<plataforma\>**. Para este tutorial,ele deve ser parecido com **registryname.azurecr.io/nodemodule:0.0.1-amd64**.

## <a name="deploy-and-run-the-solution"></a>Implantar e executar a solução

O artigo de início rápido que você usou para configurar seu dispositivo do IoT Edge, você implantou um módulo usando o portal do Azure. Também é possível implantar módulos usando a extensão Kit de ferramentas do Azure IoT para Visual Studio Code. Você já tem um manifesto de implantação preparado para o seu cenário, o arquivo **deployment.json**. Agora, tudo o que você precisa fazer é selecionar um dispositivo para receber a implantação.

1. Na paleta de comandos do VS Code, execute **Hub IoT do Azure: selecionar Hub IoT**. 

2. Escolha a assinatura e o hub IoT que contêm o dispositivo do IoT Edge que você deseja configurar. 

3. No explorador do VS Code, expanda a seção **Dispositivos do Hub IoT do Azure**. 

4. Clique com o botão direito no nome do seu dispositivo IoT Edge e selecione **Criar Implantação para Dispositivo Único**. 

   ![Criar implantação para dispositivo único](./media/tutorial-node-module/create-deployment.png)

5. Selecione o arquivo **deployment.json** na pasta **config** e clique em **Selecionar Manifesto de Implantação do Edge**. Não use o arquivo deployment.template.json. 

6. Clique no botão Atualizar. Você deve ver o novo **NodeModule** sendo executado junto com o módulo **TempSensor** em **$edgeAgent** e **$edgeHub**. 


## <a name="view-generated-data"></a>Exibir os dados gerados

Depois que você aplica o manifesto de implantação no seu dispositivo IoT Edge, o tempo de execução do IoT Edge no dispositivo coleta as novas informações de implantação e inicia a execução nele. Todos os módulos em execução no dispositivo que não estão incluídos no manifesto de implantação são interrompidos. Todos os módulos ausentes do dispositivo são iniciados. 

Você pode ver o status do seu dispositivo IoT Edge usando a seção **Dispositivos de Hub IoT do Azure** do explorer do Visual Studio Code. Expanda os detalhes do seu dispositivo para ver uma lista de módulos implantados e em execução. 

No próprio dispositivo IoT Edge você pode ver o status dos seus módulos de implantação usando o comando `iotedge list`. Você deverá ver quatro módulos: os dois módulos de tempo de execução do IoT Edge, tempSensor e o módulo personalizado que você criou neste tutorial. Pode levar alguns minutos para que todos os módulos iniciem, portanto, se você não vir todos inicialmente, execute novamente o comando. 

Para exibir as mensagens que estão sendo geradas por qualquer módulo, use o comando `iotedge logs <module name>`. 

Você pode exibir as mensagens que chegam ao Hub IoT usando o Visual Studio Code. 

1. Para monitorar dados que chegam ao hub IoT, clique em **...** e selecione **Iniciar Monitoramento de Mensagens D2C**.
2. Para monitorar a mensagem D2C para um dispositivo específico, clique com o botão direito do mouse no dispositivo na lista e selecione **Iniciar Monitoramento de Mensagens D2C**.
3. Para interromper o monitoramento de dados, execute o comando **Hub IoT do Azure: Parar o monitoramento de mensagens D2C** na paleta de comandos. 
4. Para exibir ou atualizar módulos gêmeos, clique com o botão direito do mouse no módulo na lista e selecione **Editar módulo gêmeo**. Para atualizar o módulo gêmeo, salve o arquivo JSON gêmeo, clique com o botão direito do mouse na área do editor e selecione **Atualizar Módulo Gêmeo**.
5. Para exibir logs do Docker, você pode instalar o [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) para VS Code e localizar os módulos de execução localmente no explorador do Docker. No menu de contexto, clique em **Mostrar Logs** para exibi-los no terminal integrado. 

## <a name="clean-up-resources"></a>Limpar recursos 

Se você pretende continuar no próximo artigo recomendado, pode manter os recursos e as configurações já criados e reutilizá-los. Você também pode continuar usando o mesmo dispositivo IoT Edge como um dispositivo de teste. 

Caso contrário, você pode excluir as configurações locais e os recursos do Azure criados neste artigo para evitar encargos. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um módulo do IoT Edge que contém código para filtrar os dados brutos gerados pelo seu dispositivo IoT Edge. Continue com um dos tutoriais a seguir para saber mais sobre outras formas pelas quais o Azure IoT Edge pode ajudá-lo a transformar dados em informações de negócios na borda.

> [!div class="nextstepaction"]
> [Implantar a função do Azure como um módulo](tutorial-deploy-function.md)
> [Implantar o Azure Stream Analytics como um módulo](tutorial-deploy-stream-analytics.md)

