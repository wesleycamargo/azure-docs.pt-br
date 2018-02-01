---
title: "Personalizar a solução de monitoramento remoto – Azure | Microsoft Docs"
description: "Este artigo fornece informações de como acessar o código-fonte da solução de monitoramento remoto pré-configurada."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/17/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: f5d38091b59110859d4376a5cd16a19f24dad65b
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2018
---
# <a name="customize-the-remote-monitoring-preconfigured-solution"></a>Personalizar a solução de monitoramento remoto pré-configurada

Este artigo fornece informações de como acessar o código-fonte e personalizar a solução de monitoramento remoto pré-configurada. O artigo descreve:

* Os repositórios do GitHub que contêm o código-fonte e os recursos para o microsserviços que compõem a solução pré-configurada.
* Cenários comuns de personalização, como adicionar um novo tipo de dispositivo.

O vídeo a seguir apresenta uma visão geral das opções para customizar a solução de monitoramento remoto pré-configurada:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/How-to-customize-the-Remote-Monitoring-Preconfigured-Solution-for-Azure-IoT/Player]

## <a name="project-overview"></a>Visão geral do projeto

### <a name="implementations"></a>Implementações

A solução de monitoramento remoto tem implementações .NET e Java. Ambas as implementações fornecem funcionalidades semelhantes e contam com os mesmos serviços do Azure subjacentes. Você pode encontrar os repositórios do GitHub de nível superior aqui:

* [Solução .NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Solução Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

### <a name="microservices"></a>Microsserviços

Se você estiver interessado em um recurso específico da solução, acesse os repositórios do GitHub de cada microsserviço individual. Cada microsserviço implementa uma parte diferente da funcionalidade da solução. Para saber mais sobre a arquitetura geral, consulte [Arquitetura da solução de monitoramento remoto pré-configurada](iot-suite-remote-monitoring-sample-walkthrough.md).

Esta tabela resume a disponibilidade atual de cada microsserviço para cada idioma:

<!-- please add links for each of the repos in the table, you can find them here https://github.com/Azure/azure-iot-pcs-team/wiki/Repositories-->

| Microsserviço      | DESCRIÇÃO | Java | .NET |
| ----------------- | ----------- | ---- | ---- |
| Interface do usuário da Web            | Aplicativo Web para a solução de monitoramento remoto. Implementa a interface do usuário usando a estrutura React.js. | [N/A (React.js)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) | [N/A (React.js)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) |
| Gerenciador do Hub IoT   | Gerencia a comunicação com o Hub IoT.        | [Disponível](https://github.com/Azure/iothub-manager-java) | [Disponível](https://github.com/Azure/iothub-manager-dotnet)   |
| Autenticação    |  Gerencia a integração do Azure Active Directory.  | Ainda não está disponível | [Disponível](https://github.com/Azure/pcs-auth-dotnet)   |
| Simulação de dispositivo | Gerencia um pool de dispositivos simulados. | Ainda não está disponível | [Disponível](https://github.com/Azure/device-simulation-dotnet)   |
| Telemetria         | Disponibiliza a telemetria de dispositivo para a interface do usuário. | [Disponível](https://github.com/Azure/device-telemetry-java) | [Disponível](https://github.com/Azure/device-telemetry-dotnet)   |
| Agente de telemetria   | Analisa o fluxo de telemetria, armazena as mensagens do Hub IoT do Azure e gera alertas de acordo com as regras definidas.  | [Disponível](https://github.com/Azure/telemetry-agent-java) | [Disponível](https://github.com/Azure/telemetry-agent-dotnet)   |
| Configuração da interface do usuário         | Gerencia dados de configuração da interface do usuário. | [Disponível](https://github.com/azure/pcs-ui-config-java) | [Disponível](https://github.com/azure/pcs-ui-config-dotnet)   |
| Adaptador de armazenamento   |  Gerencia as interações com o serviço de armazenamento.   | [Disponível](https://github.com/azure/pcs-storage-adapter-java) | [Disponível](https://github.com/azure/pcs-storage-adapter-dotnet)   |
| Proxy reverso     | Expõe recursos privados de forma gerenciada por meio de um ponto de extremidade exclusivo. | Ainda não está disponível | [Disponível](https://github.com/Azure/reverse-proxy-dotnet)   |

No momento, a solução Java usa autenticação do .NET, simulação e microsserviços de proxy reverso. Esses microsserviços serão substituídos pelas versões do Java assim que elas estiverem disponíveis.

## <a name="presentation-and-visualization"></a>Apresentação e visualização

As seções a seguir descrevem as opções para personalizar a camada de apresentação e as visualizações na solução de monitoramento remoto:

### <a name="change-the-logo-in-the-ui"></a>Altere o logotipo na interface do usuário

A implantação padrão usa o nome e o logotipo da empresa Contoso na interface do usuário. Para alterar esses elementos de interface do usuário e exibir o nome e o logotipo da sua empresa:

1. Use o seguinte comando para clonar o repositório da interface do usuário da Web:

    ```cmd/sh
    git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
    ```

1. Para alterar o nome da empresa, abra o arquivo `src/common/lang.js` em um editor de texto.

1. Localize a seguinte linha no arquivo:

    ```js
    CONTOSO: 'Contoso',
    ```

1. Substitua `Contoso` pelo nome da sua empresa. Por exemplo: 

    ```js
    CONTOSO: 'YourCo',
    ```

1. Salve o arquivo.

1. Para atualizar o logotipo, adicione um novo arquivo SVG na pasta `assets/icons`. O logotipo existente é o arquivo `assets/icons/Contoso.svg`.

1. Abra o arquivo `src/components/layout/leftNav/leftNav.js` em um editor de texto.

1. Localize a seguinte linha no arquivo:

    ```js
    import ContosoIcon from '../../../assets/icons/Contoso.svg';
    ```

1. Substitua `Contoso.svg` pelo nome do seu arquivo de logotipo. Por exemplo: 

    ```js
    import ContosoIcon from '../../../assets/icons/YourCo.svg';
    ```

1. Localize a seguinte linha no arquivo:

    ```js
    alt="ContosoIcon"
    ```

1. Substitua `ContosoIcon` pelo seu texto `alt`. Por exemplo: 

    ```js
    alt="YourCoIcon"
    ```

1. Salve o arquivo.

1. Para testar as alterações, você pode executar o `webui` atualizado em seu computador local. Para saber como compilar e executar a solução `webui` localmente, consulte [Build, run and test loca](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/README.md#build-run-and-test-locally) (Construir, executar e testar localmente) no arquivo leiame do repositório do GitHub `webui`.

1. Para implantar as alterações, consulte o [Developer Reference Guide](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide) (Guia de referência do desenvolvedor).

<!--

### Add a new KPI to the Dashboard page

The following steps describe how to add a new KPI to display on the **Dashboard** page. The new KPI shows information about the number of alarms with specific status values as a pie chart:

1. Step 1

1. Step 2
-->

### <a name="customize-the-map"></a>Personalizar o mapa

Consulte a página [Personalizar mapa](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) no GitHub para obter detalhes sobre os componentes de mapa na solução.

<!--
### Customize the telemetry chart

See the [Customize telemetry chart](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of the telemetry chart components in the solution.

### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

### Duplicate an existing control

To duplicate an existing UI element such as a chart or alert, see the [Duplicate a control](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub.

-->

### <a name="other-customization-options"></a>Outras opções de personalização

Para modificar ainda mais a camada de apresentação e visualizações na solução de monitoramento remoto, você pode editar o código. Os repositórios do GitHub relevantes são:

* [UIConfig (.NET)](https://github.com/Azure/pcs-ui-config-dotnet/)
* [UIConfig (Java)](https://github.com/Azure/pcs-ui-config-java/)
* [Azure PCS Remote Monitoring WebUI](https://github.com/Azure/pcs-remote-monitoring-webui) (WebUI de monitoramento remoto de computadores do Azure)

## <a name="device-connectivity-and-streaming"></a>Conectividade e streaming do dispositivo

As seções a seguir descrevem as opções para personalizar a camada de conectividade e streaming do dispositivo na solução de monitoramento remoto. Os [modelos de dispositivos](https://github.com/Azure/device-simulation-dotnet/wiki/Device-Models) descrevem os tipos de dispositivo e a telemetria na solução. Você pode usar modelos de dispositivos para dispositivos físicos e simulados.

Para obter um exemplo de uma implementação de dispositivo físico, consulte [Conectar seu dispositivo à solução pré-configurada de monitoramento remoto (Node.js)](iot-suite-connecting-devices-node.md).

Se você estiver usando um _dispositivo físico_, forneça o aplicativo cliente com um modelo de dispositivo que contenha os metadados e a especificação de telemetria do dispositivo.

As seções a seguir discutem o uso de modelos de dispositivos com dispositivos simulados:

### <a name="add-a-telemetry-type"></a>Adicionar um tipo de telemetria

Os tipos de dispositivo na solução de demonstração da Contoso especificam a telemetria que cada tipo de dispositivo envia. Para especificar os tipos de telemetria adicionais, um dispositivo pode enviar definições de telemetria como metadados para a solução. Se você usar esse formato, o painel consumirá dinamicamente a telemetria e os métodos disponíveis do dispositivo e não será necessário modificar a interface do usuário. Como alternativa, você pode modificar a definição do tipo de dispositivo na solução.

Para aprender como adicionar a telemetria personalizada no microsserviço _simulador de dispositivo_, consulte [Testar sua solução com dispositivos simulados](iot-suite-remote-monitoring-test.md).

### <a name="add-a-device-type"></a>Adicionar um tipo de dispositivo

A solução de demonstração da Contoso define alguns tipos de dispositivo de exemplo. A solução permite que você defina tipos de dispositivo personalizados para atender aos seus requisitos de aplicativo específicos. Por exemplo, sua empresa pode usar um gateway industrial como o dispositivo principal conectado à solução.

Para criar uma representação precisa do seu dispositivo, você precisa modificar o aplicativo que é executado no dispositivo para atender aos requisitos de dispositivo.

Para aprender a adicionar um novo tipo de dispositivo no microsserviço _simulador de dispositivo_, consulte [Testar sua solução com dispositivos simulados](iot-suite-remote-monitoring-test.md).

### <a name="define-custom-methods-for-simulated-devices"></a>Definir métodos personalizados para dispositivos simulados

Para saber como definir métodos personalizados para dispositivos simulados na solução de monitoramento remoto, consulte [Device Models](https://github.com/Azure/device-simulation-dotnet/wiki/%5BAPI-Specifications%5D-Device-Models) (Modelos de Dispositivos) no repositório do GitHub.

<!--
#### Using the simulator service

TODO: add steps for the simulator microservice here
-->

#### <a name="using-a-physical-device"></a>Usando um dispositivo físico

Para implementar métodos e trabalhos em seus dispositivos físicos, consulte os seguintes artigos do Hub IoT:

* [Entender e invocar métodos diretos do Hub IoT](../iot-hub/iot-hub-devguide-direct-methods.md).
* [Agendar trabalhos em vários dispositivos](../iot-hub/iot-hub-devguide-jobs.md).

### <a name="other-customization-options"></a>Outras opções de personalização

Para modificar ainda mais a conectividade do dispositivo e a camada de streaming na solução de monitoramento remoto, você pode editar o código. Os repositórios do GitHub relevantes são:

* [Device Telemetry (.NET)](https://github.com/Azure/device-telemetry-dotnet) [Telemetria do dispositivo (.NET)]
* [Device Telemetry (Java)](https://github.com/Azure/device-telemetry-java) [Telemetria do dispositivo (Java)]
* [Telemetry Agent (.NET)](https://github.com/Azure/telemetry-agent-dotnet) [Agente de telemetria (.NET)]
* [Telemetry Agent (Java)](https://github.com/Azure/telemetry-agent-java) [Agente de telemetria (Java)]

## <a name="data-processing-and-analytics"></a>Processamento de dados e análise

<!--
The following sections describe options to customize the data processing and analytics layer in the remote monitoring solution:

### Rules and actions

See the [Customize rules and actions](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to customize the rules and actions in solution.


### Other customization options
-->

Para modificar o processamento de dados e a camada de análise na solução de monitoramento remoto, você pode editar o código. Os repositórios do GitHub relevantes são:

* [Telemetry Agent (.NET)](https://github.com/Azure/telemetry-agent-dotnet) [Agente de telemetria (.NET)]
* [Telemetry Agent (Java)](https://github.com/Azure/telemetry-agent-java) [Agente de telemetria (Java)]

## <a name="infrastructure"></a>Infraestrutura

<!--
The following sections describe options for customizing the infrastructure services in the remote monitoring solution:

### Change storage

The default storage service for the remote monitoring solution is Cosmos DB. See the [Customize storage service](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to change the storage service the solution uses.

### Change log storage

The default storage service for logs is Cosmos DB. See the [Customize log storage service](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to change the storage service the solution uses for logging.

### Other customization options
-->

Para modificar a infraestrutura na solução de monitoramento remoto, você pode editar o código. Os repositórios do GitHub relevantes são:

* [IoTHub Manager (.NET)](https://github.com/Azure/iothub-manager-dotnet) [Gerenciador do Hub IoT (.NET)]
* [IoTHub Manager (Java)](https://github.com/Azure/iothub-manager-java) [Gerenciador do Hub IoT (Java)]
* [Storage Adapter (.NET)](https://github.com/Azure/pcs-storage-adapter-dotnet) [Adaptador de armazenamento (.NET)]
* [Storage Adapter (Java)](https://github.com/Azure/pcs-storage-adapter-java) [Adaptador de armazenamento (Java)]

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre os recursos disponíveis para ajudá-lo a personalizar a solução pré-configurada.

Para obter mais informações conceituais sobre a solução de monitoramento remoto pré-configurada, consulte [Arquitetura de monitoramento remoto](iot-suite-remote-monitoring-sample-walkthrough.md)

Para obter mais informações sobre como personalizar a solução de monitoramento remoto, consulte:

* [Guia de Referência do Desenvolvedor](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guia de Solução de Problemas do Desenvolvedor](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->