---
title: Implantar os Gêmeos Digitais do Azure | Microsoft Azure
description: Saiba como implantar a instância dos Gêmeos Digitais do Azure e configurar seus recursos espaciais usando as etapas deste tutorial.
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: 9ca8a9de9a286d4b14dae4a822f3e9baf4747c60
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49363326"
---
# <a name="tutorial-deploy-azure-digital-twins-and-configure-a-spatial-graph"></a>Tutorial: Implantar os Gêmeos Digitais do Azure e configurar um grafo espacial

O serviço Gêmeos Digitais do Azure permite reunir pessoas, lugares e dispositivos em um sistema espacial coerente. Esta série de tutoriais demonstra como usar os Gêmeos Digitais do Azure para detectar a ocupação de salas com condições ideais de temperatura e qualidade do ar. Estes tutoriais o orientarão em um aplicativo de console do .NET para criar um cenário de um prédio comercial com vários andares e várias salas em cada andar. As salas contêm dispositivos, com sensores conectados que detectam movimento, temperatura ambiente e qualidade do ar. Você aprenderá a replicar as áreas físicas e entidades no prédio como objetos digitais usando o serviço Gêmeos Digitais. Você simulará eventos de dispositivo usando outro aplicativo de console. Em seguida, você aprenderá a monitorar os eventos provenientes dessas áreas físicas e entidades quase em tempo real. Um administrador do escritório pode usar essas informações para ajudar um funcionário que trabalhe no prédio a reservar salas de reunião com condições ideais. Um gerente de instalações comerciais pode usar a configuração para descobrir tendências de uso de salas e monitorar as condições de trabalho para fins de manutenção.

No primeiro tutorial desta série, você aprenderá a:

> [!div class="checklist"]
> * Implantar Gêmeos Digitais
> * Conceder permissões ao aplicativo
> * Modificar o aplicativo de exemplo dos Gêmeos Digitais
> * Provisionar o prédio


Estes tutoriais usam e modificam os mesmos exemplos que o [início rápido para encontrar salas disponíveis](quickstart-view-occupancy-dotnet.md) usa, para uma cobertura mais detalhada e aprofundada dos conceitos.


## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se você ainda não tem uma conta do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Os exemplos dos Gêmeos Digitais usados nestes tutoriais estão escritos em C#. Instale o [SDK do .NET Core versão 2.1.403 ou superior](https://www.microsoft.com/net/download) no computador de desenvolvimento para executar a amostra. Verifique se a versão correta está instalada em seu computador executando `dotnet --version` em uma janela de comando.

- Use o [Visual Studio Code](https://code.visualstudio.com/) para explorar o código de exemplo. 

<a id="deploy" />

## <a name="deploy-digital-twins"></a>Implantar Gêmeos Digitais

Use as etapas nesta seção para criar uma nova instância do serviço Gêmeos Digitais. Apenas uma instância pode ser criada por assinatura; vá para a próxima seção se você já tiver uma em execução. 

[!INCLUDE [create-digital-twins-portal](../../includes/create-digital-twins-portal.md)]


<a id="permissions" />

## <a name="grant-permissions-to-your-app"></a>Conceder permissões ao aplicativo

Os Gêmeos Digitais usam o [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) para controlar o [acesso de leitura/gravação](../active-directory/develop/v1-permissions-and-consent.md) ao serviço. Todo aplicativo que precise se conectar à instância dos Gêmeos Digitais deve ser registrado no Azure Active Directory. As etapas nesta seção mostram como registrar seu aplicativo de exemplo.

Se você já tiver um *registro de aplicativo*, poderá reutilizá-lo para o exemplo. No entanto, percorra esta seção para ter certeza de que o registro do aplicativo está configurado corretamente.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]


## <a name="configure-digital-twins-sample"></a>Configurar o exemplo dos Gêmeos Digitais

Esta seção percorre um aplicativo dos Gêmeos Digitais que se comunica com as [APIs REST dos Gêmeos Digitais](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index). 

### <a name="download-the-sample"></a>Baixar o exemplo
Se você já tem os exemplos baixados do [início rápido para encontrar salas disponíveis](quickstart-view-occupancy-dotnet.md), pode ignorar estas etapas.

1. Baixe os [exemplos de .NET dos Gêmeos Digitais](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip). 
2. Extraia o conteúdo da pasta ZIP em seu computador. 

### <a name="explore-the-sample"></a>Explorar o exemplo
Na pasta de exemplo extraída, abra o arquivo **_digital-twins-samples-csharp\digital-twins-samples.code-workspace_** no Visual Studio Code. Ele contém dois projetos: 

1. O exemplo de provisionamento **_occupancy-quickstart_** permite configurar e provisionar um [grafo de inteligência espacial](concepts-objectmodel-spatialgraph.md#graph), que é a imagem digitalizada dos espaços físicos, e os recursos contidos nele. Ele usa um [modelo de objeto](concepts-objectmodel-spatialgraph.md#model) que define objetos de um prédio inteligente. Para obter uma lista completa das APIs REST e dos objetos dos Gêmeos Digitais, visite [esta documentação da API REST](https://docs.westcentralus.azuresmartspaces.net/management/swagger) ou a URL da **API de Gerenciamento** que foi criada para [sua instância](#deploy).

   Para explorar o exemplo e ver como ele se comunica com sua instância dos Gêmeos Digitais, você pode começar com a pasta **_src\actions_**. Os arquivos na pasta implementam os comandos que você usará nestes tutoriais:
    - o arquivo *provisionSample.cs* mostra como provisionar o grafo espacial,
    - o arquivo *getSpaces.cs* obtém informações sobre os espaços provisionados,
    - o *getAvailableAndFreshSpaces.cs* obtém os resultados de uma função personalizada chamada de função definida pelo usuário e
    - o *createEndpoints.cs* cria pontos de extremidade para interagir com outros serviços.

1. O exemplo de simulação **_device-connectivity_** simula dados de sensor e o envia para o hub IoT provisionado para a instância do Gêmeo Digital. Você usará essa amostra no [próximo tutorial depois de ter provisionado o grafo espacial](tutorial-facilities-udf.md#simulate). Os identificadores de sensores e dispositivos usados para configurar este exemplo devem ser os mesmos que serão usados para provisionar o grafo.

### <a name="configure-the-provisioning-sample"></a>Configurar o exemplo de provisionamento
1. Abra uma janela de comando e navegue até o exemplo baixado. Execute o comando a seguir:

    ```cmd/sh
    cd occupancy-quickstart/src
    ```

1. Restaure as dependências para o projeto de exemplo executando este comando:

    ```cmd/sh
    dotnet restore
    ```

1. No Visual Studio Code, abra o arquivo  **_appSettings.json_** que pertence ao projeto **occupancy-quickstart** e atualize os seguintes valores:
    1. *ClientId*: insira a **ID do Aplicativo** de seu registro de aplicativo do AAD anotado na seção para [definir permissões de aplicativo](#permissions).
    1. *Locatário*: insira a **ID do diretório** de seu [locatário AAD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant), também anotado na seção para [definir permissões de aplicativo](#permissions).
    1. *BaseUrl*: insira a URL da instância dos Gêmeos Digitais. Para obter essa URL, substitua os espaços reservados na URL pelos valores de sua instância: **https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/**. Você também pode obter essa URL modificando a URL da **API de Gerenciamento** da [seção de implantação](#deploy), substituindo **swagger/** por **api/v1.0/**.

1. Consulte uma lista dos recursos de Gêmeos Digitais que você pode explorar com o exemplo executando o comando a seguir.

    ```cmd/sh
    dotnet run
    ```

<a id="provision-spaces" />

## <a name="understand-provisioning-process"></a>Entender o processo de provisionamento
Esta seção mostra como o exemplo provisiona um grafo espacial de um prédio. 

No Visual Studio Code, navegue até a pasta **_occupancy-quickstart\src\actions_**  e abra o arquivo *provisionSample.cs*. Observe a seguinte função:

```csharp
public static async Task<IEnumerable<ProvisionResults.Space>> ProvisionSample(HttpClient httpClient, ILogger logger)
{
    IEnumerable<SpaceDescription> spaceCreateDescriptions;
    using (var r = new StreamReader("actions/provisionSample.yaml"))
    {
        spaceCreateDescriptions = await GetProvisionSampleTopology(r);
    }

    var results = await CreateSpaces(httpClient, logger, spaceCreateDescriptions, Guid.Empty);

    Console.WriteLine($"Completed Provisioning: {JsonConvert.SerializeObject(results, Formatting.Indented)}");

    return results;
}

```

Essa função usa o *provisionSample.yaml* na mesma pasta. Abra esse arquivo e observe a hierarquia de um prédio comercial: o *Local*, o *Andar*, a *Área*e as *Salas*. Qualquer um desses espaços físicos pode conter *dispositivos* e *sensores*. Cada entrada tem um modelo predefinido `type`, por exemplo, *Andar*, *Sala*. 

O arquivo de exemplo *yaml* mostra um grafo espacial usando o modelo de objeto dos Gêmeos Digitais `Default`. Esse modelo fornece nomes genéricos para a maioria dos tipos (por exemplo, Temperature para SensorDataType, Map para SpaceBlobType) e tipos de espaço (por exemplo, Room com subtipos FocusRoom, ConferenceRoom e assim por diante), o que é suficiente para um prédio. Se você precisou criar um grafo espacial para um local de tipo diferente, como uma fábrica, talvez seja necessário usar um modelo de objeto diferente. Você pode descobrir quais modelos estão disponíveis para uso executando o comando `dotnet run GetOntologies` na linha de comando de exemplo de provisionamento. Para obter mais detalhes sobre grafos espaciais e modelos de objeto, leia [Noções básicas sobre modelo de objeto e grafo de inteligência espacial dos Gêmeos Digitais](concepts-objectmodel-spatialgraph.md). 

### <a name="modify-sample-spatial-graph"></a>Modificar grafo espacial de exemplo
O *provisionSample.yaml* contém os seguintes nós:

- **resources**: o nó `resources` cria um recurso do Hub IoT para se comunicar com os dispositivos em sua configuração. Um hub IoT no nó raiz do grafo pode se comunicar com todos os dispositivos e sensores no grafo.  

- **spaces**: no modelo de objeto dos Gêmeos Digitais, `spaces` representa os locais físicos. Cada espaço tem um `Type`, por exemplo, *Region*, *Venue* ou *Customer*, e um `Name` amigável. Os espaços podem pertencer a outros espaços, criando uma estrutura hierárquica. O *provisionSample.yaml* tem um grafo espacial de um prédio imaginário. Observe o aninhamento lógico dos espaços do tipo `Floor` no `Venue`, `Area` em um andar e nós `Room` em uma área. 

- **devices**: os espaços podem conter `devices`, que são entidades físicas ou virtuais que gerenciam uma quantidade de sensores. Por exemplo, um dispositivo pode ser o telefone do usuário, um pod de sensor do Raspberry Pi, um gateway e outros. No prédio imaginário do exemplo, observe como a sala chamada *Sala de Foco* contém um dispositivo *Raspberry Pi 3 A1*. Cada nó do dispositivo é identificado por um único `hardwareId`, que está codificado no exemplo. Para configurar esse exemplo para uma produção real, substitua-os pelos valores de sua configuração.  

- **sensors**: um dispositivo pode conter vários `sensors`, que podem detectar e registrar alterações físicas, como temperatura, movimento, nível de bateria, etc. Cada nó de sensor é identificado exclusivamente por um `hardwareId`, que, no nosso caso, está no código. Para um aplicativo real, substitua-os pelos identificadores exclusivos dos sensores da instalação. O arquivo *provisionSample.yaml* tem dois sensores para gravar *Movimento* e *CarbonDioxide*. Adicione outro sensor para registrar a *Temperatura*, adicionando as linhas a seguir abaixo das linhas do sensor CarbonDioxide. Observe que eles são fornecidos em *provisionSample.yaml* como linhas comentadas; você pode simplesmente remover os comentários removendo o caractere '#' na frente de cada linha. 

    ```yaml
            - dataType: Temperature
              hardwareId: SAMPLE_SENSOR_TEMPERATURE
    ```
    > [!NOTE]
    > Verifique se o alinhamento das chaves `dataType` e `hardwareId` também se alinha com as instruções acima desse trecho de código. Verifique também se o editor não substitui espaços por tabulações. 

Salve e feche o arquivo *provisionSample.yaml*. No próximo tutorial, você adicionará outras informações a esse arquivo e provisionará o prédio de exemplo dos Gêmeos Digitais do Azure.


## <a name="clean-up-resources"></a>Limpar recursos

Se quiser parar de explorar os Gêmeos Digitais do Azure além desse ponto, você pode excluir os recursos criados neste tutorial:

1. No menu à esquerda no [portal do Azure](http://portal.azure.com), clique em **Todos os recursos**, selecione o grupo de recursos dos Gêmeos Digitais e **exclua-os**.
2. Se precisar, você também poderá excluir o aplicativo de exemplo no computador de trabalho. 


## <a name="next-steps"></a>Próximas etapas

Vá para o próximo tutorial da série para saber como implementar uma lógica personalizada para monitorar as condições do prédio de exemplo. 
> [!div class="nextstepaction"]
> [Tutorial: Provisionar o prédio e monitorar as condições de trabalho](tutorial-facilities-udf.md)

