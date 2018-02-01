---
title: "Perguntas frequentes da solução de fábrica conectada – Azure | Microsoft Docs"
description: "Perguntas frequentes sobre a fábrica conectada do IoT Suite"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: ab72152fc937e3c4552147fce29c95ea0efcadf4
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2018
---
# <a name="frequently-asked-questions-for-iot-suite-connected-factory-preconfigured-solution"></a>Perguntas frequentes sobre a solução pré-configurada de fábrica conectada do IoT Suite

Consulte também as [Perguntas frequentes](iot-suite-faq.md) gerais sobre o IoT Suite.

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solution"></a>Onde encontrar o código-fonte da solução pré-configurada?

O código-fonte é armazenado no seguinte repositório GitHub:

* [Solução pré-configurada de fábrica conectada](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>O que é OPC UA?

O OPC UA (Unified Architecture), lançado em 2008, é um padrão de interoperabilidade orientado a serviços e independente de plataforma. O OPC UA é usado por vários sistemas e dispositivos industriais, como computadores, PLCs e sensores do setor. O OPC UA integra a funcionalidade das especificações do OPC Clássico a uma estrutura extensível com segurança interna. É um padrão controlado pela OPC Foundation. A [OPC Foundation](http://opcfoundation.org/) é uma organização sem fins lucrativos com mais de 440 membros. A meta da organização é usar as especificações do OPC para facilitar a interoperabilidade segura e confiável entre fornecedores e plataformas por meio de:

* Infraestrutura
* Especificações
* Tecnologia
* Processos

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-preconfigured-solution"></a>Por que a Microsoft escolheu o OPC UA para a solução pré-configurada de fábrica conectada?

A Microsoft escolheu OPC UA porque ele é um padrão aberto, comprovado, não proprietário, independente de plataforma e reconhecido no setor. É um requisito para soluções de arquitetura de referência Industrie 4.0 (RAMI4.0) garantindo a interoperabilidade entre um amplo conjunto de processos de fabricação e equipamento. A Microsoft considera a demanda dos clientes para criar soluções do Industrie 4.0. O suporte para OPC UA ajuda a reduzir a barreira para os clientes atingirem suas metas e fornece valor comercial imediato para eles.

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>Como fazer para adicionar um endereço IP público à VM de simulação?

Você tem duas opções para adicionar o endereço IP:

* Usar o script `Simulation/Factory/Add-SimulationPublicIp.ps1` do PowerShell no [repositório](https://github.com/Azure/azure-iot-connected-factory). Passar o nome da implantação como um parâmetro. Para uma implantação local, use `<your username>ConnFactoryLocal`. O script imprime o endereço IP da VM.

* No portal do Azure, localize o grupo de recursos de sua implantação. Com exceção de uma implantação local, o grupo de recursos tem o nome especificado como o nome da solução ou da implantação. Para uma implantação local que usa o script de build, o nome do grupo de recursos é `<your username>ConnFactoryLocal`. Agora, adicione um novo recurso **Endereço IP público** ao grupo de recursos.

> [!NOTE]
> Em qualquer caso, instale os últimos patches seguindo as instruções deste [site do Ubuntu](https://wiki.ubuntu.com/Security/Upgrades). Mantenha a instalação atualizada pelo período em que a VM estiver acessível por meio de um endereço IP público.

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>Como fazer para remover o endereço IP público da VM de simulação?

Você tem duas opções para remover o endereço IP:

* Usar o script Simulation/Factory/Remove-SimulationPublicIp.ps1 do PowerShell do [repositório](https://github.com/Azure/azure-iot-connected-factory). Passar o nome da implantação como um parâmetro. Para uma implantação local, use `<your username>ConnFactoryLocal`. O script imprime o endereço IP da VM.

* No portal do Azure, localize o grupo de recursos de sua implantação. Com exceção de uma implantação local, o grupo de recursos tem o nome especificado como o nome da solução ou da implantação. Para uma implantação local que usa o script de build, o nome do grupo de recursos é `<your username>ConnFactoryLocal`. Agora, remova o recurso **Endereço IP público** do grupo de recursos.

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>Como fazer para entrar na VM de simulação?

Há suporte para a entrada na VM de simulação apenas se você implantou a solução usando o script `build.ps1` do PowerShell no [repositório](https://github.com/Azure/azure-iot-connected-factory).

Se você implantou a solução por meio de www.azureiotsuite.com, não poderá entrar na VM. Você não poderá entrar porque a senha é gerada aleatoriamente e não é possível redefini-la.

1. Adicione um endereço IP público à VM. Consulte [Como fazer para adicionar um endereço IP público à VM de simulação?](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)
1. Crie uma sessão do SSH para a VM usando o endereço IP da VM.
1. O nome de usuário a ser usado é `docker`.
1. A senha a ser usada depende da versão usada para implantação:
    * Para soluções implantadas com o script build.ps1 antes de 1º de junho de 2017, a senha é `Passw0rd`.
    * Para soluções implantadas com o script build.ps1 após 1º de junho de 2017, encontre a senha no arquivo `<name of your deployment>.config.user`. A senha é armazenada na configuração **VmAdminPassword**. A senha é gerada aleatoriamente no momento da implantação, a menos que você a especifique usando o parâmetro `-VmAdminPassword` do script `build.ps1`

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>Como fazer para interromper e iniciar todos os processos do Docker na VM de simulação?

1. Entre na VM de simulação. Consulte [Como fazer para entrar na VM de simulação?](#how-do-i-sign-in-to-the-simulation-vm)
1. Para verificar quais contêineres estão ativos, execute `docker ps`.
1. Para interromper todos os contêineres de simulação, execute `./stopsimulation`.
1. Para iniciar todos os contêineres de simulação:
    * Exporte uma variável do shell com o nome **IOTHUB_CONNECTIONSTRING**. Use o valor da configuração **IotHubOwnerConnectionString** no arquivo `<name of your deployment>.config.user`. Por exemplo: 

        ```
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * Execute `./startsimulation`.

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>Como fazer para atualizar a simulação na VM?

Se você fez alterações na simulação, use o script `build.ps1` do PowerShell no [repositório](https://github.com/Azure/azure-iot-connected-factory) usando o comando `updatedimulation`. Esse script compila todos os componentes da simulação, interrompe a simulação na VM, carrega-os, instala-os e inicia-os.

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>Como fazer para descobrir a cadeia de conexão do hub IoT usada por minha solução?

Se você implantou sua solução com o script `build.ps1` no [repositório](https://github.com/Azure/azure-iot-connected-factory), a cadeia de conexão é o valor de **IotHubOwnerConnectionString** no arquivo `<name of your deployment>.config.user`.

Encontre também a cadeia de conexão usando o portal do Azure. No recurso Hub IoT no grupo de recursos da implantação, localize as configurações da cadeia de conexão.

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>Quais dispositivos Hub IoT são usados pela simulação de Fábrica conectada?

A simulação registra automaticamente os seguintes dispositivos:

* proxy.beijing.corp.contoso
* proxy.capetown.corp.contoso
* proxy.mumbai.corp.contoso
* proxy.munich0.corp.contoso
* proxy.rio.corp.contoso
* proxy.seattle.corp.contoso
* publisher.beijing.corp.contoso
* publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.munich0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

Usando o [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) ou a ferramenta [a extensão IoT do Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension), verifique quais dispositivos estão registrados no hub IoT usado pela solução. Para usar o gerenciador de dispositivos, você precisa da cadeia de conexão do hub IoT na implantação. Para usar a extensão de IoT do Azure CLI 2.0, é necessário o nome do Hub IoT.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>Como obter dados de log dos componentes da simulação?

Todos os componentes da simulação registram informações em arquivos de log. Esses arquivos podem ser encontrados na VM, na pasta `home/docker/Logs`. Para recuperar os logs, use o script `Simulation/Factory/Get-SimulationLogs.ps1` do PowerShell no [repositório](https://github.com/Azure/azure-iot-connected-factory).

Esse script precisa entrar na VM. Talvez seja necessário fornecer credenciais para a entrada. Consulte [Como fazer para entrar na VM de simulação?](#how-do-i-sign-in-to-the-simulation-vm) para encontrar as credenciais.

O script adiciona/remove um endereço IP público da VM, caso ela ainda não tenha um e o remove. O script coloca todos os arquivos de log em um arquivo morto e baixa o arquivo morto na estação de trabalho de desenvolvimento.

Como alternativa, faça logon na VM por meio do SSH e inspecione os arquivos de log em tempo de execução.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>Como verificar se a simulação está enviando dados para a nuvem?

Com o [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) ou a ferramenta [iothub-explorer](https://github.com/azure/iothub-explorer), inspecione os dados enviados para o Hub IoT de determinados dispositivos. Para usar essas ferramentas, você precisa saber a cadeia de conexão do hub IoT na implantação. Consulte [Como fazer para descobrir a cadeia de conexão do hub IoT usada por minha solução?](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)

Inspecione os dados enviados por um dos dispositivos fornecedores:

* publisher.beijing.corp.contoso
* publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.munich0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

Caso você não veja nenhum dado enviado para o Hub IoT, há um problema com a simulação. Como uma primeira etapa de análise, você deve analisar os arquivos de log dos componentes da simulação. Consulte [Como obter dados de log dos componentes da simulação?](#how-can-i-get-log-data-from-the-simulation-components) Em seguida, tente interromper e iniciar a simulação e, se ainda nenhum dado estiver sendo enviado, atualize a simulação por completo. Consulte [Como fazer para atualizar a simulação na VM?](#how-do-i-update-the-simulation-in-the-vm)

### <a name="how-do-i-enable-an-interactive-map-in-my-connected-factory-solution"></a>Como habilitar um mapa interativo na minha solução de Alocador conectado?

Para habilitar um mapa interativo em sua solução de Alocador conectado, deve ter uma API existente do Bing Maps para o plano Enterprise.

Durante a implantação de [www.azureiotsuite.com](http://www.azureiotsuite.com), o processo de implantação verifica se sua assinatura tem uma API do Bing Maps habilitado para o plano Enterprise e implanta automaticamente um mapa interativo no Alocador conectado. Se isso não for o caso, você poderá habilitar um mapa interativo em sua implantação da seguinte maneira:

Quando você implanta usando o `build.ps1` script no repositório GitHub do Alocador conectado e você tem uma API do Bing Maps para o plano Enterprise, defina a variável de ambiente `$env:MapApiQueryKey` na janela de compilação para a chave de consulta do seu plano. O mapa interativo, em seguida, é habilitado automaticamente.

Se você não tiver uma API do Bing Maps para o plano Enterprise, implante a solução de Alocador conectado de [www.azureiotsuite.com](http://www.azureiotsuite.com) ou usando o script `build.ps1`. Em seguida, adicione uma API do Bing Maps para o plano Enterprise à sua assinatura, conforme explicado em [Como criar uma API do Bing Maps para a conta Enterprise?](#how-do-i-create-a-bing-maps-api-for-enterprise-account). Pesquise a chave de consulta dessa conta conforme explicado em [Como obter a API do Bing Maps para Enterprise QueryKey](#how-to-obtain-your-bing-maps-api-for-enterprise-querykey) e salve essa chave. Navegue até o portal do Azure e acesse o recurso de serviço de aplicativo em sua implantação de Alocador conectado. Navegue até **Configurações do aplicativo**, onde você pode encontrar uma seção **Configurações do aplicativo**. Defina o **MapApiQueryKey** para a chave de consulta que você obteve. Salvar as configurações e, em seguida, navegue até **Visão geral** e reinicie o serviço de aplicativo.

### <a name="how-do-i-create-a-bing-maps-api-for-enterprise-account"></a>Como criar uma API do Bing Maps para a conta Enterprise

Você pode obter um plano gratuito do *Bing Maps for Enterprise do Nível 1 das transações internas*. No entanto, você só pode adicionar dois desses planos a uma assinatura do Azure. Se você não tiver uma API do Bing Maps para a conta Enterprise, crie uma no portal do Azure clicando em **+ Criar um recurso**. Em seguida, procure por **API do Bing Maps for Enterprise** e siga os prompts para criá-la.

![Chave do Bing](media/iot-suite-faq-cf/bing.png)

### <a name="how-to-obtain-your-bing-maps-api-for-enterprise-querykey"></a>Como obter a QueryKey do API do Bing Maps for Enterprise

Depois de criar sua API do Bing Maps for Enterprise, adicione um recurso do Bing Maps for Enterprise ao grupo de recursos de sua solução de Alocador conectado no portal do Azure.

1. No portal do Azure, navegue até o grupo de recursos que contém seu plano da API do Bing Maps for Enterprise.

1. Clique em **Todas as Configurações** e em **Gerenciamento de Chaves**.

1. Há duas chaves: **MasterKey** e **QueryKey**. Copie o valor **QueryKey**.

1. Para que o script do `build.ps1` capture a chave, defina a variável de ambiente `$env:MapApiQueryKey` em seu ambiente do PowerShell como a **QueryKey** do seu plano. O script de compilação, em seguida, adicionará automaticamente o valor às configurações do Serviço de Aplicativo.

1. Execute uma implantação local ou da nuvem usando o script do `build.ps1`.

### <a name="how-do-enable-the-interactive-map-while-debugging-locally"></a>Como habilitar o mapa interativo durante a depuração localmente?

Para habilitar o mapa interativo ao depurar localmente, defina o valor da configuração `MapApiQueryKey` nos arquivos `local.user.config` e `<yourdeploymentname>.user.config` na raiz da sua implantação como o valor da **QueryKey** que você copiou anteriormente.

### <a name="how-do-i-use-a-different-image-at-the-home-page-of-my-dashboard"></a>Como usar uma imagem diferente na home page do meu painel?

Para alterar a imagem estática mostrada na home page do painel, substitua a imagem `WebApp\Content\img\world.jpg`. Em seguida, recompile e reimplante o WebApp.

### <a name="how-do-i-use-non-opc-ua-devices-with-connected-factory"></a>Como usar dispositivos não OPC UA com o Alocador conectado?

Para enviar os dados telemétricos dos dispositivos não OPC UA ao Alocador conectado:

1. [Configurar uma nova estação na topologia do Alocador conectado](iot-suite-connected-factory-configure.md) no arquivo `ContosoTopologyDescription.json`.

1. Ingira os dados telemétricos no formato JSON compatível do Alocador conectado:

    ```json
    [
      {
        "ApplicationUri": "<the_value_of_OpcUri_of_your_station",
        "DisplayName": "<name_of_the_datapoint>",
        "NodeId": "value_of_NodeId_of_your_datapoint_in_the_station",
        "Value": {
          "Value": <datapoint_value>,
          "SourceTimestamp": "<timestamp>"
        }
      }
    ]
    ```

1. O formato de `<timestamp>` é: `2017-12-08T19:24:51.886753Z`

1. Reinicie o serviço de aplicativo do Alocador conectado.

### <a name="next-steps"></a>Próximas etapas

Você também pode explorar alguns dos outros recursos das soluções pré-configuradas do IoT Suite:

* [Visão geral da solução pré-configurada de manutenção preditiva](iot-suite-predictive-overview.md)
* [Visão geral da solução pré-configurada de fábrica conectada](iot-suite-connected-factory-overview.md)
* [Segurança da IoT desde o início](securing-iot-ground-up.md)