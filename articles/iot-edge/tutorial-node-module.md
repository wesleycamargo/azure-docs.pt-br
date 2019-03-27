---
title: Tutorial para criar módulo personalizado do Node.js – Azure IoT Edge | Microsoft Docs
description: Este tutorial mostra como criar um módulo do IoT Edge com código em Node.js e implantá-lo em um dispositivo de borda
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 10026f0a9ff702ee45926ca097e9123ea3db06d5
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225919"
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
* Se você está executando o IoT Edge no Windows, o IoT Edge versão 1.0.5 não é compatível com módulos Node.js. Para obter mais informações, confira as [notas sobre a versão 1.0.5](https://github.com/Azure/azure-iotedge/releases/tag/1.0.5). Para conhecer as etapas para instalar uma versão específica, confira [Atualizar o tempo de execução e o daemon de segurança do IoT Edge](how-to-update-iot-edge.md).

Recursos de nuvem:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na camada padrão ou gratuito no Azure. 

Recursos de desenvolvimento:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Ferramentas de IoT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) para Visual Studio Code. 
* [CE do Docker](https://docs.docker.com/engine/installation/). 
* [Node.js e npm](https://nodejs.org). O pacote do npm é distribuído com Node.js, o que significa que quando você baixa Node.js, o npm é automaticamente instalado em seu computador.

## <a name="create-a-container-registry"></a>Criar um registro de contêiner

Neste tutorial, você usará as Ferramentas IoT do Azure para Visual Studio Code para criar um módulo e uma **imagem de contêiner** com base nos arquivos. Em seguida, você efetua push dessa imagem para um **registro** que armazena e gerencia suas imagens. Finalmente, você implanta a imagem do seu registro para executar no dispositivo IoT Edge.  

Você pode usar qualquer registro compatível com o Docker para manter as imagens de contêiner. Dois serviços de registro populares do Docker são o [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) e o [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Este tutorial utiliza o Registro de Contêiner do Azure. 

Caso ainda não tenha um registro de contêiner, siga estas etapas para criar um novo no Azure:

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Contêineres** > **Registro de Contêiner**.

2. Forneça os seguintes valores para criar o seu registro de contêiner:

   | Campo | Valor |
   | ----- | ----- |
   | Nome do registro | Forneça um nome exclusivo. |
   | Assinatura | Selecione uma assinatura na lista suspensa. |
   | Grupo de recursos | É recomendável que você use o mesmo grupo de recursos para todos os recursos de teste que foram criados durante os tutoriais e guias de início rápido do IoT Edge. Por exemplo, **IoTEdgeResources**. |
   | Local padrão | Escolha um local perto de você. |
   | Usuário administrador | Definido como **Habilitar**. |
   | SKU | Selecione **Basic**. |

5. Selecione **Criar**.

6. Depois que o registro de contêiner for criado, navegue até ele e escolha **Chaves de acesso**. 

7. Copie os valores para **Servidor de logon**, **Nome de usuário** e **Senha**. É possível usar esses valores posteriormente no tutorial para fornecer acesso ao registro de contêiner. 

## <a name="create-an-iot-edge-module-project"></a>Criar um projeto de módulo do IoT Edge
As etapas a seguir mostram como criar um módulo Node.js do IoT Edge usando o Visual Studio Code e as Ferramentas de IoT do Azure.

### <a name="create-a-new-solution"></a>Criar uma nova solução

Use **npm** para criar um modelo de solução em Node.js para usar de base. 

1. No Visual Studio Code, selecione **Exibir** > **Terminal Integrado** para abrir o terminal integrado do Visual Studio Code.

2. No terminal integrado, digite o seguinte comando para instalar o **yeoman** e o gerador para o módulo Node.js do Azure IoT Edge: 

    ```cmd/sh
    npm install -g yo generator-azure-iot-edge-module
    ```

3. Selecione **Exibir** > **Paleta de comandos** para abrir a paleta de comandos do VS Code. 

3. Na paleta de comandos, digite e execute o comando **Azure: Entrar** e siga as instruções para entrar na conta do Azure. Se já tiver entrado, pode ignorar esta etapa.

4. Na paleta de comandos, digite e execute o comando **Azure IoT Edge: nova solução do IoT Edge**. Siga os prompts na paleta de comandos para criar sua solução.

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha o local no computador de desenvolvimento em que o VS Code criará os arquivos de solução. |
   | Fornecer um nome para a solução | Insira um nome descritivo para a solução ou aceite o padrão **EdgeSolution**. |
   | Selecionar modelo do módulo | Escolha **Módulo Node.js**. |
   | Fornecer um nome de módulo | Nomeie seu módulo **NodeModule**. |
   | Fornecer o repositório de imagem do Docker para o módulo | Um repositório de imagem inclui o nome do registro de contêiner e o nome da imagem de contêiner. Sua imagem de contêiner é preenchida previamente o nome fornecido na última etapa. Substitua **localhost:5000** pelo valor do servidor de logon do seu registro de contêiner do Azure. Você pode recuperar o servidor de logon da página Visão Geral do seu registro de contêiner no portal do Azure. <br><br>O repositório de imagem final se parece com \<nome_do_registro\>.azurecr.io/nodemodule. |
 
   ![Fornecer o repositório de imagem do Docker](./media/tutorial-node-module/repository.png)

A janela do VS Code carregará seu workspace da solução IoT Edge. O workspace da solução contém cinco componentes de nível superior. A pasta **módulos** contém o código Node.js para o seu módulo, bem como Dockerfiles para a compilação de seu módulo como uma imagem de contêiner. O arquivo **\.env** armazena suas credenciais de registro de contêiner. O arquivo **deployment.template.json** contém as informações que o tempo de execução do IoT Edge usa para implantar módulos em um dispositivo e o arquivo **deployment.debug.template.json** contém a versão de depuração dos módulos. Você não editará a pasta **\.vscode** ou o arquivo **\.gitignore** neste tutorial. 

Se você não especifica um registro de contêiner durante a criação de sua solução, mas aceita o valor de localhost:5000, você não terá um arquivo \.env. 

<!--
   ![Node.js solution workspace](./media/tutorial-node-module/workspace.png)
-->

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

9. Salve o arquivo app.js.

10. No gerenciador do VS Code, abra o arquivo **deployment.template.json** no workspace da solução IoT Edge. Esse arquivo informa ao agente do IoT Edge quais módulos implantar, nesse caso, **tempSensor** e **NodeModule**, e informa ao hub do IoT Edge como rotear mensagens entre eles. A extensão do Visual Studio Code preenche automaticamente a maioria das informações necessárias no modelo de implantação. Mesmo assim, verifique se tudo está preciso para sua solução: 

   1. A plataforma padrão do seu IoT Edge é definida como **amd64** na barra de status do VS Code, o que significa que seu **NodeModule** está definido para a versão amd64 do Linux da imagem. Altere a plataforma padrão na barra de status de **amd64** para **arm32v7** ou **windows-amd64** se essa for a arquitetura do seu dispositivo IoT Edge. 

      ![Atualizar a plataforma da imagem do módulo](./media/tutorial-node-module/image-platform.png)

   2. Verifique se o modelo tem o nome do módulo correto, não o nome padrão **SampleModule** alterado quando você criou a solução de IoT Edge.

   3. A seção **registryCredentials** armazena suas credenciais de registro do Docker, de modo que o agente do IoT Edge possa efetuar pull da imagem do módulo. Os pares de nome de usuário e senha reais são armazenados no arquivo .env que é ignorado pelo git. Adicione suas credenciais ao arquivo .env caso ainda não tenha feito isso.  

   4. Caso deseje saber mais sobre manifestos de implantação, confira [Saiba como implantar módulos e estabelecer rotas no IoT Edge](module-composition.md).

11. Adicione o módulo gêmeo NodeModule no manifesto de implantação. Insira o seguinte conteúdo JSON na parte inferior da seção `moduleContent`, após o módulo gêmeo do `$edgeHub`: 

   ```json
     "NodeModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Adicionar gêmeo de módulo ao modelo de implantação](./media/tutorial-node-module/module-twin.png)

12. Salve o arquivo deployment.template.json.


## <a name="build-your-iot-edge-solution"></a>Criar solução IoT Edge

Na seção anterior, você criou uma solução IoT Edge e adicionou um código a NodeModule que filtrará mensagens em que a temperatura relatada da máquina estiver abaixo do limite aceitável. Agora você precisa compilar a solução como uma imagem de contêiner e enviá-la por push para seu registro de contêiner. 

1. Entre no Docker digitando o seguinte comando no terminal integrado do Visual Studio Code para que você possa enviar sua imagem de módulo para o ACR: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Use o nome de usuário, a senha e o servidor de logon que você copiou de seu Registro de Contêiner do Azure na primeira seção. Ou recupere-as novamente da seção **Chaves de acesso** do registro no portal do Azure.

2. No explorador do VS Code, clique com o botão direito do mouse no arquivo **deployment.template.json** e selecione **Compilar e enviar por push solução IoT Edge**. 

Quando você solicitar ao Visual Studio Code para compilar sua solução, primeiro ele usará as informações no modelo de implantação e gerará um arquivo `deployment.json` em uma nova pasta **config**. Em seguida, ele executará dois comandos no terminal integrado: `docker build` e `docker push`. Esses dois comandos compilam o código, acondicionam o código Node.js em contêiner e efetuam push do código para o registro do contêiner que você especificou quando inicializou a solução. 

Você pode obter o endereço de imagem de contêiner completo com marca no comando `docker build` executado no terminal integrado do VS Code. O endereço da imagem é criado de informações do arquivo `module.json` com o formato **\<repositório\>:\<versão\>-\<plataforma\>**. Para este tutorial,ele deve ser parecido com **registryname.azurecr.io/nodemodule:0.0.1-amd64**.

>[!TIP]
>Se você receber um erro ao tentar compilar e efetuar push do seu módulo, faça as seguintes verificações:
>* Você entrou no Docker no Visual Studio Code usando as credenciais do seu Registro de contêiner? Essas credenciais são diferentes daquelas que você usa para entrar no portal do Azure.
>* Seu repositório de contêiner está correto? Abra **modules** > **cmodule** > **module.json** e encontre o campo **repository**. O repositório de imagens deve se parecer com **\<nome_do_registro\>.azurecr.io/nodemodule**. 
>* Você está criando o mesmo tipo de contêiner que seu computador de desenvolvimento está executando? Visual Studio Code assume como padrão contêineres do Linux amd64. Se seu computador de desenvolvimento está executando contêineres do Windows ou Linux arm32v7, atualize a plataforma na barra de status azul na parte inferior da janela do VS Code para corresponder à sua plataforma de contêiner.

## <a name="deploy-and-run-the-solution"></a>Implantar e executar a solução

O artigo de início rápido que você usou para configurar seu dispositivo do IoT Edge, você implantou um módulo usando o portal do Azure. Você também pode implantar módulos usando a extensão do Kit de Ferramentas do Hub IoT do Azure (anteriormente conhecida como extensão do Kit de Ferramentas do Azure IoT) para Visual Studio Code. Você já tem um manifesto de implantação preparado para o seu cenário, o arquivo **deployment.json**. Agora, tudo o que você precisa fazer é selecionar um dispositivo para receber a implantação.

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
3. Para interromper o monitoramento de dados, execute o comando **Hub IoT do Azure: Parar de monitorar mensagem de D2C** na paleta de comandos. 
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

