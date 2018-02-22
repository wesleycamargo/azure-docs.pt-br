---
title: "Simulação de dispositivo na solução de monitoramento remoto – Azure | Microsoft Docs"
description: "Este tutorial mostra como usar o simulador de dispositivo com a solução de monitoramento remoto pré-configurada."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/15/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 9f51c35be09af6f3a8dde7061dcf57a9c4cc9fdb
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="create-a-new-simulated-device"></a>Criar um novo dispositivo simulado

Este tutorial mostra como personalizar o microsserviço do simulador de dispositivo na solução pré-configurada de monitoramento remoto. Para mostrar os recursos do simulador de dispositivo, este tutorial usa dois cenários no aplicativo de IoT da Contoso.

No primeiro cenário, a Contoso deseja testar um novo dispositivo de lâmpada inteligente. Para executar os testes, você pode criar um novo dispositivo simulado com as seguintes características:

*Propriedades*

| NOME                     | Valores                      |
| ------------------------ | --------------------------- |
| Cor                    | White, Red, Blue            |
| Brilho               | 0 a 100                    |
| Vida útil restante estimada | Contagem regressiva de 10.000 horas |

*Telemetria*

A tabela a seguir mostra os dados que a lâmpada relata para a nuvem como um fluxo de dados:

| NOME   | Valores      |
| ------ | ----------- |
| Status | "on", "off" |
| Temperatura | Graus F |
| online | verdadeiro, falso |

> [!NOTE]
> O valor de telemetria **online** é obrigatório para simulada todos os tipos.

*Métodos*

A tabela a seguir mostra as ações com suporte no novo dispositivo:

| NOME        |
| ----------- |
| Ligar   |
| Desligar  |

*Estado inicial*

A tabela a seguir mostra o status inicial do dispositivo:

| NOME                     | Valores |
| ------------------------ | -------|
| Cor inicial            | Branco  |
| Brilho inicial       | 75     |
| Vida útil restante inicial   | 10.000 |
| Status de telemetria inicial | "on"   |
| Temperatura da telemetria inicial | 200   |

No segundo cenário, você adiciona um novo tipo de telemetria no dispositivo **Resfriador** existente da Contoso.

Este tutorial mostra como usar o simulador de dispositivo com a solução de monitoramento remoto pré-configurada:

Neste tutorial, você aprenderá como:

>[!div class="checklist"]
> * Criar um novo tipo de dispositivo
> * Simular o comportamento do dispositivo personalizado
> * Adicionar um novo tipo de dispositivo no painel
> * Enviar telemetria personalizada de um tipo de dispositivo existente

O vídeo a seguir mostra um passo a passo para conectar dispositivos simulados e reais à solução de monitoramento remoto:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Part-38-Customizing-Azure-IoT-Suite-solution-and-connect-a-real-device/Player]

## <a name="prerequisites"></a>pré-requisitos

Para seguir este tutorial, você precisa do seguinte:

* Uma instância implantada da solução de monitoramento remoto em sua assinatura do Azure. Se você ainda não implantou a solução de monitoramento remoto, conclua o tutorial [Deploy the remote monitoring preconfigured solution](iot-suite-remote-monitoring-deploy.md) (Implantar a solução de monitoramento remoto pré-configurada).

* Visual Studio 2017. Se você não tem o Visual Studio 2017 instalado, baixe a edição gratuita [Visual Studio Community](https://www.visualstudio.com/free-developer-offers/).

* Extensão [Cloud Explorer para Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVS15Preview) do Visual Studio.

* Uma conta no [Hub do Docker](https://hub.docker.com/). Você pode se inscrever gratuitamente para começar.

* O [Git](https://git-scm.com/downloads) instalado no computador da área de trabalho.

## <a name="prepare-your-development-environment"></a>Preparar seu ambiente de desenvolvimento

Conclua as seguintes tarefas para preparar o ambiente de desenvolvimento para a adição de um novo dispositivo simulado à solução de monitoramento remoto:

### <a name="configure-ssh-access-to-the-solution-virtual-machine-in-azure"></a>Configurar o acesso SSH à máquina virtual da solução no Azure

Quando você criou sua solução de monitoramento remoto em [www.azureiotsuite.com](https://www.azureiotsuite.com), você escolheu um nome da solução. O nome da solução torna-se o nome do grupo de recursos do Azure que contém os vários recursos implantados usados pela solução. Os comandos a seguir usam um grupo de recursos chamado **Contoso-01**. Você deve substituir **Contoso-01** pelo nome de seu grupo de recursos.

Os comandos a seguir usam o comando `az` da [CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest). Instale a CLI 2.0 do Azure no computador de desenvolvimento ou use o [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) no [portal do Azure](http://portal.azure.com). A CLI 2.0 do Azure é pré-instalada no Cloud Shell.

1. Para verificar o nome do grupo de recursos que contém os recursos de monitoramentos remoto, execute o seguinte comando:

    ```sh
    az group list | grep "name"
    ```

    Esse comando lista todos os grupos de recursos em sua assinatura. A lista deve conter um grupo de recursos com o mesmo nome da solução de monitoramento remoto.

1. Para tornar o grupo de recurso o grupo padrão para os próximos comandos, execute o seguinte comando usando o nome do grupo de recursos no lugar de **Contoso-01**:

    ```sh
    az configure --defaults group=Contoso-01
    ```

1. Para listar os recursos em seu grupo de recursos, execute o seguinte comando:

    ```sh
    az resource list -o table
    ```

    Anote os nomes da máquina virtual e do grupo de segurança de rede. Use esses valores em etapas posteriores.

1. Para habilitar o acesso SSH à máquina virtual, execute o seguinte comando usando o nome do grupo de segurança de rede da etapa anterior:

    ```sh
    az network nsg rule create --name SSH --nsg-name YOUR-NETWORK-SECURITY-GROUP --priority 101 --destination-port-ranges 22 --access Allow --protocol TCP
    ```

    Para exibir a lista de regras de entrada para a rede, execute o seguinte comando:

    ```sh
    az network nsg rule list --nsg-name YOUR-NETWORK-SECURITY-GROUP -o table
    ```

1. Para alterar a senha da máquina virtual para uma senha conhecida, execute o comando a seguir. Use o nome da máquina virtual que você anotou anteriormente e uma senha de sua escolha:

    ```sh
    az vm user update --name YOUR-VM-NAME --username azureuser --password YOUR-PASSWORD
    ```
1. Para encontrar o endereço IP da máquina virtual, use o seguinte comando e anote o endereço IP público:

    ```sh
    az vm list-ip-addresses --name YOUR-VM-NAME
    ```

1. Agora você pode usar o SSH para se conectar à máquina virtual. O comando `ssh` é pré-instalado no Cloud Shell. Use o endereço IP público da etapa anterior e, quando solicitado, a senha configurada para a máquina virtual:

    ```sh
    ssh azureuser@public-ip-address
    ```

    Agora você tem acesso ao shell na máquina virtual que executa os contêineres do Docker na solução de monitoramento remoto. Para exibir os contêineres em execução, use o seguinte comando:

    ```sh
    docker ps
    ```

### <a name="find-the-service-connection-strings"></a>Encontrar cadeias de conexão do serviço

No tutorial, você trabalha com a solução do Visual Studio que se conecta aos serviços Cosmos DB e Hub IoT da solução. As seguintes etapas mostram uma maneira de encontrar os valores da cadeia de conexão necessários:

1. Para encontrar a cadeia de conexão do Cosmos DB, execute o seguinte comando na sessão SSH conectada à máquina virtual:

    ```sh
    sudo grep STORAGEADAPTER_DOCUMENTDB /app/env-vars
    ```

    Anote a cadeia de conexão. Você usará esse valor posteriormente no tutorial.

1. Para encontrar a cadeia de conexão do Hub IoT, execute o seguinte comando na sessão SSH conectada à máquina virtual:

    ```sh
    sudo grep IOTHUB_CONNSTRING /app/env-vars
    ```

    Anote a cadeia de conexão. Você usará esse valor posteriormente no tutorial.

> [!NOTE]
> Também encontre essas cadeias de conexão no portal do Azure ou usando o comando `az`.

### <a name="stop-the-device-simulation-service-in-the-virtual-machine"></a>Interromper o serviço de simulação de dispositivo na máquina virtual

Quando você modificar o serviço de simulação de dispositivo, execute-o localmente para testar as alterações. Antes de executar o serviço de simulação de dispositivo localmente, você precisa interromper a instância em execução na máquina virtual da seguinte maneira:

1. Para encontrar a **ID DE CONTÊINER** do serviço de **device-simulation**, execute o seguinte comando na sessão SSH conectada à máquina virtual:

    ```sh
    docker ps
    ```

    Anote a ID do contêiner do serviço de **device-simulation**.

1. Para interromper o contêiner de **device-simulation**, execute o seguinte comando:

    ```sh
    docker stop container-id-from-previous-step
    ```

### <a name="clone-the-github-repositories"></a>Clonar os repositórios GitHub

Neste tutorial, você trabalha com os projetos **device-simulation** e **storage-adapter** do Visual Studio. Clone os repositórios de código-fonte do GitHub. Execute esta etapa no computador de desenvolvimento local em que você tem o Visual Studio instalado:

1. Abra um prompt de comando e procure a pasta em que deseja salvar a cópia dos repositórios GitHub **device-simulation** e **storage-adapter**.

1. Para clonar a versão do .NET do repositório **device-simulation**, execute o seguinte comando:

    ```cmd
    git clone https://github.com/Azure/device-simulation-dotnet.git
    ```

    O serviço de simulação de dispositivo na solução de monitoramento remoto permite que você faça alterações nos tipos de dispositivo simulado internos e crie novos tipos de dispositivo simulado. Use tipos de dispositivo personalizado para testar o comportamento da solução de monitoramento remoto antes de conectar os dispositivos físicos.

1. Para clonar a versão do .NET do repositório **storage-adapter**, execute o seguinte comando:

    ```cmd
    git clone https://github.com/Azure/storage-adapter.git
    ```

    O serviço de simulação de dispositivo usa o serviço de adaptador de armazenamento para se conectar ao serviço Cosmos DB no Azure. A solução de monitoramento remoto armazena os dados de configuração do dispositivo simulado em um banco de dados Cosmos DB.

### <a name="run-the-storage-adapter-service-locally"></a>Executar o serviço de adaptador de armazenamento localmente

O serviço de simulação do dispositivo usa o serviço de adaptador de armazenamento para se conectar ao banco de dados Cosmos DB da solução. Se você executar o serviço de simulação de dispositivo localmente, também precisará executar o serviço de adaptador de armazenamento localmente. As seguintes etapas mostram como executar o serviço de adaptador de armazenamento no Visual Studio:

1. No Visual Studio, abra o arquivo de solução **pcs-storage-adapter.sln** no clone local do repositório **storage-adapter**.

1. No Gerenciador de Soluções, clique com botão direito do mouse no projeto **WebService**, escolha **Propriedades** e, em seguida, **Depurar**.

1. Na seção **Variáveis de ambiente**, edite o valor da variável **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** para que ela seja a cadeia de conexão do Cosmos DB que você anotou anteriormente. Em seguida, salve as alterações.

1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **WebService**, escolha **Depurar** e, em seguida, **Iniciar nova instância**.

1. O serviço começa a ser executado localmente e abre `http://localhost:9022/v1/status` no navegador padrão. Verifique se o valor de **Status** é “OK: ativo e em funcionamento”.

1. Deixe o serviço de adaptador de armazenamento em execução localmente até que você conclua o tutorial.

Agora você tem tudo implementado e está pronto para começar a adicionar um novo tipo de dispositivo simulado à sua solução de monitoramento remoto.

## <a name="create-a-simulated-device-type"></a>Criar um tipo de dispositivo simulado

A maneira mais fácil de criar um novo tipo de dispositivo no serviço de simulação de dispositivo é copiar e modificar um tipo existente. As etapas a seguir mostram como copiar o dispositivo **Resfriador** interno para criar um novo dispositivo **Lâmpada**:

1. No Visual Studio, abra o arquivo de solução **device-simulation.sln** no clone local do repositório **device-simulation**.

1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **SimulationAgent**, escolha **Propriedades** e, em seguida, **Depurar**.

1. Na seção **Variáveis de ambiente**, edite o valor da variável **PCS\_IOTHUB\_CONNSTRING** para que ela seja a cadeia de conexão do Hub IoT que você anotou anteriormente. Em seguida, salve as alterações.

1. No Gerenciador de Soluções, clique com o botão direito do mouse na solução **device-simulation** e escolha **Definir Projetos de Inicialização**. Escolha **Único projeto de inicialização** e selecione **SimulationAgent**. Em seguida, clique em **OK**.

1. Cada tipo de dispositivo tem um arquivo de modelo JSON e scripts associados na pasta **Services/data/devicemodels**. No Gerenciador de Soluções, copie os arquivos do **Resfriador** para criar os arquivos de **Lâmpada**, conforme mostrado na seguinte tabela:

    | Fonte                      | Destino                   |
    | --------------------------- | ----------------------------- |
    | chiller-01.json             | lightbulb-01.json             |
    | scripts/chiller-01-state.js | scripts/lightbulb-01-state.js |
    | scripts/reboot-method.js    | scripts/SwitchOn-method.js    |

### <a name="define-the-characteristics-of-the-new-device-type"></a>Definir as características do novo tipo de dispositivo

O arquivo **lightbulb-01.json** define as características do tipo, como a telemetria que ele gera e os métodos compatíveis. As seguintes etapas atualizam o arquivo **lightbulb-01.json** para definir o dispositivo de **Lâmpada**:

1. No arquivo **lightbulb-01.json**, atualize os metadados do dispositivo, conforme mostrado no seguinte trecho:

    ```json
    "SchemaVersion": "1.0.0",
    "Id": "lightbulb-01",
    "Version": "0.0.1",
    "Name": "Lightbulb",
    "Description": "Smart lightbulb device.",
    "Protocol": "MQTT",
    ```

1. No arquivo **lightbulb-01.json**, atualize as definições de simulação, conforme mostrado no seguinte trecho:

    ```json
    "Simulation": {
      "InitialState": {
        "online": true,
        "temperature": 200.0,
        "temperature_unit": "F",
        "status": "on"
      },
      "Script": {
        "Type": "javascript",
        "Path": "lightbulb-01-state.js",
        "Interval": "00:00:20"
      }
    },
    ```

1. No arquivo **lightbulb-01.json**, atualize as propriedades do tipo de dispositivo, conforme mostrado no seguinte trecho:

    ```json
    "Properties": {
      "Type": "Lightbulb",
      "Color": "White",
      "Brightness": 75,
      "EstimatedRemainingLife": 10000
    },
    ```

1. No arquivo **lightbulb-01.json**, atualize as definições de telemetria do tipo de dispositivo, conforme mostrado no seguinte trecho:

    ```json
    "Telemetry": [
      {
        "Interval": "00:00:20",
        "MessageTemplate": "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"status\":\"${status}\"}",
        "MessageSchema": {
          "Name": "lightbulb-status;v1",
          "Format": "JSON",
          "Fields": {
            "temperature": "double",
            "temperature_unit": "text",
            "status": "text"
          }
        }
      }
    ],
    ```

1. No arquivo **lightbulb-01.json**, atualize os métodos do tipo de dispositivo, conforme mostrado no seguinte trecho:

    ```json
    "CloudToDeviceMethods": {
      "SwitchOn": {
        "Type": "javascript",
        "Path": "SwitchOn-method.js"
      },
      "SwitchOff": {
        "Type": "javascript",
        "Path": "SwitchOff-method.js"
      }
    }
    ```

1. Salve o arquivo **lightbulb-01.json**.

### <a name="simulate-custom-device-behavior"></a>Simular o comportamento do dispositivo personalizado

O arquivo **scripts/lightbulb-01-state.js** define o comportamento de simulação do tipo **Lâmpada**. As seguintes etapas atualizam o arquivo **scripts/lightbulb-01-state.js** para definir o comportamento do dispositivo de **Lâmpada**:

1. Edite a definição de estado no arquivo **scripts/lightbulb-01-state.js**, conforme mostrado no seguinte trecho:

    ```js
    // Default state
    var state = {
      online: true,
      temperature: 200.0,
      temperature_unit: "F",
      status: "on"
    };
    ```

1. Adicione uma função **flip** após a função **vary** com a seguinte definição:

    ```js
    /**
    * Simple formula that sometimes flips the status of the lightbulb
    */
    function flip(value) {
      if (Math.random() < 0.2) {
        return (value == "on") ? "off" : "on"
      }
      return value;
    }
    ```

1. Edite a função **main** para implementar o comportamento, conforme é mostrado no trecho a seguir:

    ```js
    function main(context, previousState) {

      // Restore the global state before generating the new telemetry, so that
      // the telemetry can apply changes using the previous function state.
      restoreState(previousState);

      state.temperature = vary(200, 5, 150, 250);

      // Make this flip every so often
      state.status = flip(state.status);

      return state;
    }
    ```

1. Salve o arquivo **scripts/lightbulb-01-state.js**.

O arquivo **scripts/SwitchOn-method.js** implementa o método **Switch On** em um dispositivo de **Lâmpada**. As seguintes etapas atualizam o arquivo **scripts/SwitchOn-method.js**:

1. Edite a definição de estado no arquivo **scripts/SwitchOn-method.js**, conforme mostrado no seguinte trecho:

    ```js
    var state = {
       status: "on"
    };
    ```

1. Para ligar a lâmpada, edite a função **main** da seguinte maneira:

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch On method.");
        state.status = "on";
        updateState(state);
    }
    ```

1. Salve o arquivo **scripts/SwitchOn-method.js**.

1. Faça uma cópia do arquivo **scripts/SwitchOn-method.js** chamado **scripts/SwitchOff-method.js**.

1. Para desligar a lâmpada, edite a função **main** no arquivo **scripts/SwitchOff-method.js** da seguinte maneira:

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch Off method.");
        state.status = "off";
        updateState(state);
    }
    ```

1. Salve o arquivo **scripts/SwitchOff-method.js**.

1. No Gerenciador de Soluções, selecione cada um dos quatro novos arquivos por vez. Na janela **Propriedades** de cada arquivo, verifique se a opção **Copiar para diretório de saída** está definida como **Copiar se mais recente**.

### <a name="configure-the-device-simulation-service"></a>Configurar o serviço de simulação de dispositivo

Para limitar o número de dispositivos simulados que se conectam à solução durante o teste, configure o serviço para executar um único resfriador e um único dispositivo de lâmpada. Os dados de configuração são armazenados na instância do Cosmos DB no grupo de recursos da solução. Para editar os dados de configuração, use a exibição **Cloud Explorer** no Visual Studio:

1. Para abrir a exibição **Cloud Explorer** no Visual Studio, escolha **Exibição** e **Cloud Explorer**.

1. Para encontrar o documento de configuração de simulação, em **Pesquisar recursos**, insira **simulations.1**.

1. Clique duas vezes no documento **simulations.1** para abri-lo para edição.

1. No valor de **Data**, localize a matriz de **DeviceModels** que se parece com o seguinte trecho:

    ```json
    [{\"Id\":\"chiller-01\",\"Count\":1},{\"Id\":\"chiller-02\",\"Count\":1},{\"Id\":\"elevator-01\",\"Count\":1},{\"Id\":\"elevator-02\",\"Count\":1},{\"Id\":\"engine-01\",\"Count\":1},{\"Id\":\"engine-02\",\"Count\":1},{\"Id\":\"prototype-01\",\"Count\":1},{\"Id\":\"prototype-02\",\"Count\":1},{\"Id\":\"truck-01\",\"Count\":1},{\"Id\":\"truck-02\",\"Count\":1}]
    ```

1. Para definir um único resfriador e um único dispositivo simulado de lâmpada, substitua a matriz **DeviceModels** pelo seguinte código:

    ```json
    [{\"Id\":\"chiller-01\",\"Count\":1},{\"Id\":\"lightbulb-01\",\"Count\":1}]
    ```

    Salve a alteração no documento **simulations.1**.

> [!NOTE]
> Também use o Data Explorer do Cosmos DB no portal do Azure para editar o documento **simulations.1**.

### <a name="test-the-lightbulb-device-type-locally"></a>Testar o tipo de dispositivo de Lâmpada localmente

Agora você está pronto para testar o novo tipo de lâmpada simulada executando o projeto de simulação de dispositivo localmente.

1. No Gerenciador de Soluções, clique com o botão direito do mouse em **SimulationAgent**, escolha **Depurar** e, em seguida, **Iniciar nova instância**.

1. Para verificar se os dois dispositivos simulados estão conectados ao Hub IoT, abra o portal do Azure no navegador.

1. Navegue para o hub IoT no grupo de recursos que contém a solução de monitoramento remoto.

1. Na seção **Monitoramento**, escolha **Métricas**. Em seguida, verifique se o número de **Dispositivos conectados** é dois:

    ![Número de dispositivos conectados](media/iot-suite-remote-monitoring-test/connecteddevices.png)

1. No navegador, navegue para o **Painel** de sua solução de monitoramento remoto. No painel de telemetria no **Painel**, selecione **temperatura**. A temperatura para os dois dispositivos simulados é exibida no gráfico:

    ![Telemetria de temperatura](media/iot-suite-remote-monitoring-test/telemetry.png)

Agora você tem a simulação de dispositivo de lâmpada sendo executada localmente. A próxima etapa é implantar o código do simulador atualizado na máquina virtual que executa os microsserviços de monitoramento remoto no Azure.

Antes de continuar, pare a depuração da simulação de dispositivo e dos projetos de adaptador de armazenamento no Visual Studio.

### <a name="deploy-the-updated-simulator-to-the-cloud"></a>Implantar o simulador atualizado na nuvem

Os microsserviços na solução de monitoramento remoto são executados em contêineres do Docker. Os contêineres são hospedados na máquina virtual da solução no Azure. Nesta seção, você:

* Crie uma nova imagem do Docker de simulação de dispositivo.
* Carregue a imagem no repositório do hub do Docker.
* Importe a imagem para a máquina virtual da solução.

As etapas a seguir pressupõem que você tenha um repositório chamado **lightbulb** em sua conta do Hub do Docker.

1. No Visual Studio, no projeto **device-simulation**, abra o arquivo **solution\scripts\docker\build.cmd**.

1. Edite a linha que define a variável de ambiente **DOCKER_IMAGE** com o nome do repositório do Hub do Docker:

    ```cmd
    SET DOCKER_IMAGE=your-docker-hub-acccount/lightbulb
    ```

    Salve a alteração.

1. No Visual Studio, no projeto **device-simulation**, abra o arquivo **solution\scripts\docker\publish.cmd**.

1. Edite a linha que define a variável de ambiente **DOCKER_IMAGE** com o nome do repositório do Hub do Docker:

    ```cmd
    SET DOCKER_IMAGE=your-docker-hub-acccount/lightbulb
    ```

    Salve a alteração.

1. Abra um prompt de comando como administrador. Em seguida, procure a pasta **scripts\docker** no clone do repositório GitHub **device-simulation**.

1. Para criar a imagem do Docker, execute o seguinte comando:

    ```cmd
    build.cmd
    ```

1. Para entrar em sua conta do Hub do Docker, execute o seguinte comando:

    ```cmd
    docker login
    ```

1. Para carregar a nova imagem em sua conta do Hub do Docker, execute o seguinte comando:

    ```cmd
    publish.cmd
    ```

1. Para verificar o upload, navegue para [https://hub.docker.com/](https://hub.docker.com/). Localize o repositório **lightbulb** e escolha **Detalhes**. Em seguida, escolha **Marcações**:

    ![Hub do Docker](media/iot-suite-remote-monitoring-test/dockerhub.png)

    Os scripts adicionaram a marcação **testing** à imagem.

1. Use o SSH para se conectar à máquina virtual da solução no Azure. Em seguida, procure a pasta **App** e edite o arquivo **docker-compose.yaml**:

    ```sh
    cd /app
    sudo nano docker-compose.yaml
    ```

1. Edite a entrada do serviço de simulação de dispositivo para usar a imagem do Docker:

    ```yaml
    devicesimulation:
      image: {your docker ID}/lightbulb:testing
    ```

    Salve suas alterações.

1. Para reiniciar todos os serviços com as novas configurações, execute o seguinte comando:

    ```sh
    sudo ./start.sh
    ```

1. Para verificar o arquivo de log do novo contêiner de simulação de dispositivo, execute o seguinte comando para encontrar a ID de contêiner:

    ```sh
    docker ps
    ```

    Em seguida, execute o seguinte comando usando a ID de contêiner:

    ```sh
    docker logs {container ID}
    ```

Agora você concluiu as etapas para implantar uma versão atualizada do serviço de simulação de dispositivo em sua solução de monitoramento remoto.

No navegador, navegue para o **Painel** de sua solução de monitoramento remoto. No painel de telemetria no **Painel**, selecione **temperatura**. A temperatura para os dois dispositivos simulados é exibida no gráfico:

![Telemetria de temperatura](media/iot-suite-remote-monitoring-test/telemetry.png)

Na página **Dispositivos**, você pode provisionar instâncias de seu novo tipo:

![Exibir a lista de simulações disponíveis](media/iot-suite-remote-monitoring-test/devicesmodellist.png)

Você pode exibir a telemetria do dispositivo simulado:

![Exibir telemetria da lâmpada](media/iot-suite-remote-monitoring-test/devicestelemetry.png)

Você pode chamar os métodos **SwitchOn** e **SwitchOff** em seu dispositivo:

![Chamar métodos da lâmpada](media/iot-suite-remote-monitoring-test/devicesmethods.png)

## <a name="add-a-new-telemetry-type"></a>Adicionar um novo tipo de telemetria

Esta seção descreve como modificar um tipo de dispositivo simulado existente para dar suporte a um novo tipo de telemetria.

### <a name="locate-the-chiller-device-type-files"></a>Localizar os arquivos do tipo de dispositivo Resfriador

As etapas a seguir mostram como localizar os arquivos que definem o dispositivo **Resfriador** interno:

1. Se você ainda não tiver feito isso, use o seguinte comando para clonar o repositório **device-simulation** do GitHub para o computador local:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git
    ```

1. Cada tipo de dispositivo tem um arquivo de modelo JSON e scripts associados na pasta `data/devicemodels`. Os arquivos que definem o tipo de dispositivo do **Resfriador** simulado são:

    * **data/devicemodels/chiller-01.json**
    * **data/devicemodels/scripts/chiller-01-state.js**

### <a name="specify-the-new-telemetry-type"></a>Especificar o novo tipo de telemetria

As etapas a seguir mostram como adicionar um novo tipo **Temperatura Interna** no tipo de dispositivo **Resfriador**:

1. Abra o arquivo **chiller-01.json**.

1. Atualize o valor de **SchemaVersion** da seguinte maneira:

    ```json
    "SchemaVersion": "1.1.0",
    ```

1. Na seção **InitialState**, adicione as duas definições a seguir:

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. Na matriz **Telemetria**, adicione a seguinte definição:

    ```json
    {
      "Interval": "00:00:05",
      "MessageTemplate": "{\"internal_temperature\":${internal_temperature},\"internal_temperature_unit\":\"${internal_temperature_unit}\"}",
      "MessageSchema": {
        "Name": "chiller-internal-temperature;v1",
        "Format": "JSON",
        "Fields": {
          "temperature": "double",
          "temperature_unit": "text"
        }
      }
    },
    ```

1. Salve o arquivo **chiller-01.json**.

1. Abra o arquivo **scripts/chiller-01-state.js**.

1. Adicione os seguintes campos à variável **state**:

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Adicione a seguinte linha à função **main**:

    ```js
    state.internal_temperature = vary(65, 2, 15, 125);
    ```

1. Salve o arquivo **scripts/chiller-01-state.js**.

### <a name="test-the-chiller-device-type"></a>Testar o tipo de dispositivo Resfriador

Para testar o tipo de dispositivo **Resfriador** atualizado, primeiro execute uma cópia local do serviço **device-simulation** para testar se o tipo de dispositivo se comporta como esperado. Depois de testar e depurar o tipo de dispositivo atualizado localmente, você poderá recriar o contêiner e reimplantar o serviço **device-simulation** no Azure.

Quando você executa o serviço **device-simulation** localmente, ele envia a telemetria para a solução de monitoramento remoto. Na página **Dispositivos**, você pode provisionar instâncias de seu tipo atualizado.

Para testar e depurar as alterações localmente, consulte a seção anterior [Testar o tipo de dispositivo Lâmpada localmente](#test-the-lightbulb-device-type-locally).

Para implantar o serviço de simulação de dispositivo atualizado na máquina virtual da solução do Azure, consulte a seção anterior [Implantar o simulador atualizado na nuvem](#deploy-the-updated-simulator-to-the-cloud).

Na página **Dispositivos**, você pode provisionar instâncias de seu tipo atualizado:

![Adicionar Resfriador atualizado](media/iot-suite-remote-monitoring-test/devicesupdatedchiller.png)

Você pode exibir a nova telemetria **Temperatura interna** do dispositivo simulado.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, nós mostramos como:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Criar um novo tipo de dispositivo
> * Simular o comportamento do dispositivo personalizado
> * Adicionar um novo tipo de dispositivo no painel
> * Enviar telemetria personalizada de um tipo de dispositivo existente

Agora, você aprendeu a personalizar o serviço de simulação de dispositivo. A próxima etapa sugerida é saber como [conectar um dispositivo físico à solução de monitoramento remoto](iot-suite-connecting-devices-node.md).

Para obter informações para o desenvolvedor sobre a solução de monitoramento remoto, consulte:

* [Guia de Referência do Desenvolvedor](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guia de Solução de Problemas do Desenvolvedor](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->