---
title: Tutorial para criar módulo personalizado do Java – Azure IoT Edge | Microsoft Docs
description: Este tutorial mostra como criar um módulo do IoT Edge com código em Java e implantá-lo em um dispositivo de borda.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/25/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: f099d280615607382bd424063d39bb26cdeea793
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53557845"
---
# <a name="tutorial-develop-a-java-iot-edge-module-and-deploy-to-your-simulated-device"></a>Tutorial: Desenvolver e implantar um módulo do IoT Edge em Java em seu dispositivo simulado

Use os módulos do Azure IoT Edge para implantar um código que implementa a lógica de negócios diretamente em seus dispositivos IoT Edge. Este tutorial o orienta através da criação e implantação de um módulo IoT Edge que filtra os dados do sensor. Você utilizará o dispositivo IoT Edge simulado que foi criado em Implantar Azure IoT Edge em um dispositivo simulado nos inícios rápidos de [Windows](quickstart.md) ou [Linux](quickstart-linux.md). Neste tutorial, você aprenderá como:    

> [!div class="checklist"]
> * Use o Visual Studio Code para criar um módulo do IoT Edge Java com base no pacote de modelo maven do Azure IoT Edge e SDK do dispositivo Java do Azure IoT.
> * Usar o Visual Studio Code e o Docker para criar uma imagem do Docker e publicá-la no registro.
> * Implantar o módulo no dispositivo IoT Edge.
> * Exibir os dados gerados.


O módulo IoT Edge que criado neste tutorial filtra os dados de temperatura gerados pelo seu dispositivo. Ele somente envia mensagens upstream se a temperatura estiver acima de um limite especificado. Esse tipo de análise na borda é útil para reduzir a quantidade de dados que é comunicada e armazenada na nuvem. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo do Azure IoT Edge:

* Você pode configurar um dispositivo IoT Edge, seguindo as etapas nos guias de início rápido para [Linux](quickstart-linux.md) ou [Windows](quickstart.md).
* Para o IoT Edge em dispositivos Windows, a versão 1.0.5 não oferece suporte a módulos Java. Para obter mais informações, confira as [notas sobre a versão 1.0.5](https://github.com/Azure/azure-iotedge/releases/tag/1.0.5). Para conhecer as etapas para instalar uma versão específica, confira [Atualizar o tempo de execução e o daemon de segurança do IoT Edge](how-to-update-iot-edge.md).

Recursos de nuvem:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na camada padrão ou gratuito no Azure. 

Recursos de desenvolvimento:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Pacote de extensão Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) para Visual Studio Code.
* [Extensão do Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) para Visual Studio Code. 
* [Java SE Development Kit 10](https://aka.ms/azure-jdks), e [defina a variável de ambiente `JAVA_HOME`](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) para apontar para a sua instalação do JDK.
* [Maven](https://maven.apache.org/)
* [CE do Docker](https://docs.docker.com/install/)
   * Se você estiver desenvolvendo em um dispositivo Windows, verifique se o Docker está [configurado para usar contêineres do Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers). 


## <a name="create-a-container-registry"></a>Criar um registro de contêiner

Neste tutorial, você usa a extensão do Azure IoT Edge do Visual Studio Code para compilar um módulo e criar uma **imagem de contêiner** dos arquivos. Em seguida, você efetua push dessa imagem para um **registro** que armazena e gerencia suas imagens. Finalmente, você implanta a imagem do seu registro para executar no dispositivo IoT Edge.  

Você pode usar qualquer registro compatível com o Docker para manter as imagens de contêiner. Dois serviços de registro populares do Docker são o [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) e o [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Este tutorial utiliza o Registro de Contêiner do Azure. 

Caso ainda não tenha um registro de contêiner, siga estas etapas para criar um novo no Azure:

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Contêineres** > **Registro de Contêiner**.

2. Forneça os seguintes valores para criar o seu registro de contêiner:

   | Campo | Valor | 
   | ----- | ----- |
   | Nome do registro | Forneça um nome exclusivo. |
   | Assinatura | Selecione uma assinatura na lista suspensa. |
   | Grupo de recursos | Para simplificar o gerenciamento, use o mesmo grupo de recursos para todos os recursos de teste criados durante os tutoriais e guias de início rápido do IoT Edge. Por exemplo, **IoTEdgeResources**. |
   | Local padrão | Escolha um local perto de você. |
   | Usuário administrador | Definido como **Habilitar**. |
   | SKU | Selecione **Basic**. | 

5. Selecione **Criar**.

6. Depois que o registro de contêiner for criado, navegue até ele e escolha **Chaves de acesso**. 

7. Copie os valores para **Servidor de logon**, **Nome de usuário** e **Senha**. É possível usar esses valores posteriormente no tutorial para fornecer acesso ao registro de contêiner. 

## <a name="create-an-iot-edge-module-project"></a>Criar um projeto de módulo do IoT Edge
As seguintes etapas criam um projeto de módulo do IoT Edge que se baseia no pacote de modelo do maven do Azure IoT Edge e no SDK do dispositivo Java do Azure IoT. Você cria o projeto usando o Visual Studio Code e a extensão do Azure IoT Edge.

### <a name="create-a-new-solution"></a>Criar uma nova solução

Crie um modelo de solução em Java que possa ser personalizado com seu próprio código. 

1. No Visual Studio Code, selecione **Exibir** > **Paleta de comandos** para abrir a paleta de comandos do VS Code. 

2. Na paleta de comandos, digite e execute o comando **Azure IoT Edge: nova solução do IoT Edge**. Siga os prompts na paleta de comandos para criar sua solução.

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha o local no computador de desenvolvimento em que o VS Code criará os arquivos de solução. |
   | Fornecer um nome para a solução | Insira um nome descritivo para a solução ou aceite o padrão **EdgeSolution**. |
   | Selecionar modelo do módulo | Escolha o **Módulo Java**. |
   | Fornecer o valor para a ID do grupo | Insira um valor para a ID do grupo ou aceite o padrão **com.edgemodule**. |
   | Fornecer um nome de módulo | Nomeie o módulo como **JavaModule**. |
   | Fornecer o repositório de imagem do Docker para o módulo | Um repositório de imagem inclui o nome do registro de contêiner e o nome da imagem de contêiner. Sua imagem de contêiner foi preenchida automaticamente na última etapa. Substitua **localhost:5000** pelo valor do servidor de logon do seu registro de contêiner do Azure. Você pode recuperar o servidor de logon da página Visão Geral do seu registro de contêiner no portal do Azure. A cadeia de caracteres final se parece com \<nome do Registro\>.azurecr.io/cmodule. |
 
   ![Fornecer o repositório de imagem do Docker](./media/tutorial-java-module/repository.png)
   
Se for a primeira vez para criar o módulo do Java, poderão ser necessários vários minutos para baixar os pacotes do maven. A janela do VS Code então carregará seu workspace da solução IoT Edge. O workspace da solução contém cinco componentes de nível superior. A pasta **módulos** contém o código Java para o seu módulo, bem como arquivos do Docker para a compilação de seu módulo como uma imagem de contêiner. O arquivo **\.env** armazena suas credenciais de registro de contêiner. O arquivo **deployment.template.json** contém as informações que o tempo de execução do IoT Edge usa para implantar módulos em um dispositivo. E o arquivo **deployment.debug.template.json** contém a versão de depuração de módulos. Você não editará a pasta **\.vscode** ou o arquivo **\.gitignore** neste tutorial. 

Se você não especifica um registro de contêiner durante a criação de sua solução, mas aceita o valor de localhost:5000, você não terá um arquivo \.env. 

### <a name="add-your-registry-credentials"></a>Adicionar suas credenciais de registro

O arquivo do ambiente armazena as credenciais para o registro de contêiner e as compartilha com o tempo de execução do IoT Edge. O tempo de execução precisa dessas credenciais para efetuar pull de imagens privadas para o dispositivo IoT Edge. 

1. No explorador do VS Code, abra o arquivo .env. 
2. Atualize os campos com os valores de **nome de usuário** e **senha** que você copiou do registro de contêiner do Azure. 
3. Salve o arquivo. 

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com código personalizado

1. No explorador de VS Code, abra **modules** > **JavaModule** > **src** > **main** > **java** > **com** > **edgemodule** > **App.java**.

5. Adicione o seguinte código na parte superior do arquivo para importar novas classes referenciadas.

    ```java
    import java.io.StringReader;
    import java.util.concurrent.atomic.AtomicLong;
    import java.util.HashMap;
    import java.util.Map;
    
    import javax.json.Json;
    import javax.json.JsonObject;
    import javax.json.JsonReader;
    
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Pair;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Property;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.TwinPropertyCallBack;
    ```

5. Adicione a seguinte definição à classe **Aplicativo**. Essa variável define um limite de temperatura. A temperatura medida do computador não será relatada para o Hub IoT até que ultrapasse esse valor. 

    ```java
    private static final String TEMP_THRESHOLD = "TemperatureThreshold";
    private static AtomicLong tempThreshold = new AtomicLong(25);
    ```

7. Substitua o método execute de **MessageCallbackMqtt** pelo código a seguir. Esse método é chamado sempre que o módulo recebe uma mensagem MQTT do hub do IoT Edge. Ele filtra as mensagens que reportam temperaturas abaixo do limite de temperatura definido através do módulo gêmeo.

    ```java
        private int counter = 0;
       @Override
        public IotHubMessageResult execute(Message msg, Object context) {
            this.counter += 1;
 
            String msgString = new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET);
            System.out.println(
                   String.format("Received message %d: %s",
                            this.counter, msgString));
            if (context instanceof ModuleClient) {
                try (JsonReader jsonReader = Json.createReader(new StringReader(msgString))) {
                    final JsonObject msgObject = jsonReader.readObject();
                    double temperature = msgObject.getJsonObject("machine").getJsonNumber("temperature").doubleValue();
                    long threshold = App.tempThreshold.get();
                    if (temperature >= threshold) {
                        ModuleClient client = (ModuleClient) context;
                        System.out.println(
                            String.format("Temperature above threshold %d. Sending message: %s",
                            threshold, msgString));
                        client.sendEventAsync(msg, eventCallback, msg, App.OUTPUT_NAME);
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            return IotHubMessageResult.COMPLETE;
        }
    ```

8. Adicione as duas seguintes classes internas estáticas à classe **Aplicativo**. Essas classes atualizam a variável de tempThreshold quando a propriedade desejada do gêmeo do módulo muda. Todos os módulos possuem seu próprio módulo gêmeo, o que permite configurar o código em execução dentro de um módulo diretamente da nuvem.

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
 
    protected static class OnProperty implements TwinPropertyCallBack {
        @Override
        public void TwinPropertyCallBack(Property property, Object context) {
            if (!property.getIsReported()) {
                if (property.getKey().equals(App.TEMP_THRESHOLD)) {
                    try {
                        long threshold = Math.round((double) property.getValue());
                        App.tempThreshold.set(threshold);
                    } catch (Exception e) {
                        System.out.println("Faile to set TemperatureThread with exception");
                        e.printStackTrace();
                    }
                }
            }
        }
    }
    ```

9. Adicione as seguintes linhas ao método **main** após **client.open()** para assinar as atualizações duplas de módulo.

    ```java
    client.startTwin(new DeviceTwinStatusCallBack(), null, new OnProperty(), null);
    Map<Property, Pair<TwinPropertyCallBack, Object>> onDesiredPropertyChange = new HashMap<Property, Pair<TwinPropertyCallBack, Object>>() {
        {
            put(new Property(App.TEMP_THRESHOLD, null), new Pair<TwinPropertyCallBack, Object>(new OnProperty(), null));
        }
    };
    client.subscribeToTwinDesiredProperties(onDesiredPropertyChange);
    client.getTwin();
    ```

11. Salve o arquivo.

12. No explorador do VS Code, abra o arquivo deployment.template.json no workspace da solução IoT Edge. Esse arquivo manda o **$edgeAgent** implantar dois módulos: **tempSensor** e **JavaModule**. A plataforma padrão do seu IoT Edge é definida como **amd64** na barra de status do VS Code, o que significa que seu **JavaModule** está definido para a versão amd64 do Linux da imagem. Altere a plataforma padrão na barra de status de **amd64** para **arm32v7** ou **windows-amd64** se essa for a arquitetura do seu dispositivo IoT Edge. 

   Para saber mais sobre manifestos de implantação, consulte [Entender como os módulos do IoT Edge podem ser utilizados, configurados e reutilizados](module-composition.md).

   No arquivo deployment.template.json, a seção **registryCredentials** armazena suas credenciais de registro do Docker. Os pares de nome de usuário e senha reais são armazenados no arquivo .env que é ignorado pelo git.  

13. Adicione o módulo duplo **JavaModule** ao manifesto de implantação. Insira o seguinte conteúdo JSON na parte inferior da seção **moduleContent**, após o módulo gêmeo do **$edgeHub**: 

   ```json
       "JavaModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![Adicionar gêmeo de módulo ao modelo de implantação](./media/tutorial-java-module/module-twin.png)

14. Salve o arquivo.

## <a name="build-your-iot-edge-solution"></a>Criar solução IoT Edge

Na seção anterior, você criou uma solução IoT Edge e adicionou um código a **JavaModule** para filtrar mensagens em que a temperatura relatada do computador estiver abaixo do limite aceitável. Agora, crie a solução como uma imagem de contêiner e enviá-la por push para seu registro de contêiner. 

1. Entre no Docker inserindo o comando a seguir no terminal do Visual Studio Code. Em seguida, envie sua imagem de módulo por push para o registro de contêiner do Azure.
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Use o nome de usuário, a senha e o servidor de logon que você copiou de seu registro de contêiner do Azure na primeira seção. Você também pode recuperar esses valores da seção **Chaves de acesso** do registro no portal do Azure.

2. No explorador do VS Code, clique com o botão direito do mouse no arquivo deployment.template.json e selecione **Compilar e enviar por push solução IoT Edge**. 

Quando você solicitar ao Visual Studio Code para compilar sua solução, primeiro ele usará as informações no modelo de implantação e gerará um arquivo deployment.json em uma nova pasta chamada **config**. Em seguida, ele executará dois comandos no terminal integrado: `docker build` e `docker push`. Esses dois comandos realizam o build do seu código, conteinerizam o aplicativo Java e efetuam o push do código para o Registro de contêiner que você especificou ao inicializar a solução. 

Você pode conferir o endereço de imagem de contêiner completo com marca no terminal integrado do VS Code. O endereço da imagem é criado a partir de informações que estão no arquivo module.json com o formato \<repositório\>:\<versão\>-\<plataforma\>. Para este tutorial, deve ser parecido com registryname.azurecr.io/javamodule:0.0.1-amd64.

## <a name="deploy-and-run-the-solution"></a>Implantar e executar a solução

O artigo de início rápido que você usou para configurar seu dispositivo do IoT Edge, você implantou um módulo usando o portal do Azure. Você também pode implantar módulos usando a extensão do Kit de Ferramentas do Hub IoT do Azure (anteriormente conhecida como extensão do Kit de Ferramentas do Azure IoT) para Visual Studio Code. Você já tem um manifesto de implantação preparado para o seu cenário, o arquivo **deployment.json**. Agora, tudo o que você precisa fazer é selecionar um dispositivo para receber a implantação.

1. Na paleta de comandos do VS Code, execute o comando **Azure: Entrar** e siga as instruções para entrar na conta do Azure. Se já tiver entrado, pode ignorar esta etapa.

2. Na paleta de comandos do VS Code, execute **Hub IoT do Azure: selecionar Hub IoT**. 

3. Escolha a assinatura e o hub IoT que contêm o dispositivo do IoT Edge que você deseja configurar. 

4. No explorador do VS Code, expanda a seção **Dispositivos do Hub IoT do Azure**. 

5. Clique com o botão direito no nome do seu dispositivo IoT Edge e selecione **Criar Implantação para Dispositivo Único**. 

   ![Criar implantação para dispositivo único](./media/tutorial-java-module/create-deployment.png)

6. Selecione o arquivo **deployment.json** na pasta **config** e clique em **Selecionar Manifesto de Implantação do Edge**. Não use o arquivo deployment.template.json. 

7. Clique no botão Atualizar. Você deve ver o novo **JavaModule** sendo executado junto com o módulo **TempSensor** em **$edgeAgent** e **$edgeHub**.  

## <a name="view-generated-data"></a>Exibir os dados gerados

Depois que você aplica o manifesto de implantação no seu dispositivo IoT Edge, o tempo de execução do IoT Edge no dispositivo coleta as novas informações de implantação e inicia a execução nele. Todos os módulos em execução no dispositivo que não estão incluídos no manifesto de implantação são interrompidos. Todos os módulos ausentes do dispositivo são iniciados. 

Você pode ver o status do seu dispositivo IoT Edge usando a seção **Dispositivos de Hub IoT do Azure** do explorer do Visual Studio Code. Expanda os detalhes do seu dispositivo para ver uma lista de módulos implantados e em execução. 

No dispositivo IoT Edge, você pode ver o status dos seus módulos de implantação usando o comando `iotedge list`. Você deverá ver quatro módulos: os dois módulos de tempo de execução do IoT Edge, tempSensor e o módulo personalizado que você criou neste tutorial. Pode levar alguns minutos para que todos os módulos iniciem, portanto, se você não vir todos inicialmente, execute novamente o comando. 

Para exibir as mensagens que estão sendo geradas por qualquer módulo, use o comando `iotedge logs <module name>`. 

Você pode exibir as mensagens que chegam ao Hub IoT usando o Visual Studio Code. 

1. Para monitorar os dados que chegam ao hub IoT, selecione as reticências (**...** ) e selecione **Iniciar Monitoramento de Mensagens D2C**.
2. Para monitorar a mensagem D2C para um dispositivo específico, clique com o botão direito do mouse no dispositivo na lista e selecione **Iniciar Monitoramento de Mensagens D2C**.
3. Para interromper o monitoramento de dados, execute o comando **Hub IoT do Azure: interromper monitoramento de mensagens D2C** na paleta de comandos. 
4. Para exibir ou atualizar o módulos gêmeo, clique com o botão direito do mouse no módulo na lista e selecione **Editar módulo gêmeo**. Para atualizar o módulo gêmeo, salve o arquivo JSON gêmeo, clique com o botão direito do mouse na área do editor e selecione **Atualizar Módulo Gêmeo**.
5. Para exibir os logs do Docker, instale o [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) para VS Code. Você pode encontrar os módulos em execução localmente no Docker Explorer. No menu de contexto, selecione **Mostrar Logs** para exibi-los no terminal integrado.
 
## <a name="clean-up-resources"></a>Limpar recursos 

Se você pretende continuar no próximo artigo recomendado, pode manter os recursos e as configurações já criados e reutilizá-los. Você também pode continuar usando o mesmo dispositivo IoT Edge como um dispositivo de teste. 

Caso contrário, você pode excluir as configurações locais e os recursos do Azure criados neste artigo para evitar encargos. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um módulo do IoT Edge com código para filtrar os dados brutos gerados pelo seu dispositivo IoT Edge. Você pode seguir para os próximos tutoriais a fim de descobrir outras formas pelas quais o Azure IoT Edge pode ajudá-lo a transformar dados em informações de negócios na borda.

> [!div class="nextstepaction"]
> [Armazenar dados na borda com os bancos de dados do SQL Server](tutorial-store-data-sql-server.md)

