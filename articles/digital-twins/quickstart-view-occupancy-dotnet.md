---
title: Encontrar salas disponíveis com os Gêmeos Digitais do Azure (C#) | Microsoft Docs
description: Neste início rápido, você executará dois aplicativos de exemplo .NET Core para enviar telemetria simulada de movimento e de dióxido de carbono para um espaço nos Gêmeos Digitais do Azure. O objetivo é encontrar salas disponíveis com ar fresco nas APIs de Gerenciamento após o processamento computado na nuvem.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/02/2018
ms.author: alinast
ms.openlocfilehash: e0b47b1322a520ad8b09fd2fe2967e628b5e4e03
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49322674"
---
# <a name="quickstart-find-available-rooms-using-azure-digital-twins"></a>Início Rápido: Encontrar salas disponíveis usando os Gêmeos Digitais do Azure

O serviço Gêmeos Digitais do Azure permite que você recrie uma imagem digital de seu ambiente físico. Em seguida, você poderá ser notificado por eventos em seu ambiente e personalizar as respostas para eles. 

Este início rápido usa [dois exemplos do .NET](https://github.com/Azure-Samples/digital-twins-samples-csharp) para digitalizar um prédio imaginário e mostra como encontrar salas disponíveis nele. Com os Gêmeos Digitais, você pode associar vários sensores ao ambiente. Além da disponibilidade de espaço, você também pode descobrir se a qualidade do ar da sala disponível é ideal com a ajuda de um sensor de dióxido de carbono simulado. Um dos aplicativos de exemplo vai gerar dados de sensor aleatórios para ajudá-lo a visualizar o cenário.

## <a name="prerequisites"></a>Pré-requisitos

1. Se você ainda não tiver uma conta do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

1. Os dois consoles de aplicativo executados neste início rápido estão escritos em C#. Você precisará instalar o [SDK do .NET Core versão 2.1.403 ou superior](https://www.microsoft.com/net/download) no computador de desenvolvimento. Se você tiver o SDK do .NET Core instalado, poderá verificar a versão atual do C# no computador de desenvolvimento que está executando o `dotnet --version` em um prompt de comando.

1. Baixe o [projeto de exemplo em C#](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip) e extraia o arquivo digital-twins-samples-csharp-master.zip. 


## <a name="create-a-digital-twins-instance"></a>Criar uma instância dos Gêmeos Digitais

Crie uma nova instância dos Gêmeos Digitais no [portal](https://portal.azure.com) usando as etapas desta seção.

[!INCLUDE [create-digital-twins-portal](../../includes/create-digital-twins-portal.md)]

## <a name="set-permissions-for-your-app"></a>Definir permissões para o aplicativo

Esta seção registra seu aplicativo de exemplo no AAD (Azure Active Directory) para que ele possa acessar a instância dos Gêmeos Digitais. Se você já tiver um registro de aplicativo do AAD, poderá reutilizá-lo como exemplo, desde que ele esteja configurado como mencionado nesta seção. 

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]


## <a name="build-application"></a>Compilar aplicativo

Compile o aplicativo de ocupação usando as seguintes etapas:

1. Abra um prompt de comando e navegue até a pasta na qual os arquivos digital-twins-samples-csharp-master.zip foram extraídos.
1. Execute `cd occupancy-quickstart/src`.
1. Execute `dotnet restore`.
1. Edite *appSettings.json* para atualizar as seguintes variáveis:
    - *ClientId*: insira a *ID do aplicativo* de registro de aplicativo do AAD, anotada na seção anterior.
    - *Locatário*: insira a *ID do diretório* do locatário do AAD, também anotada na seção anterior.
    - *BaseUrl*: a URL da *API de Gerenciamento* da instância dos Gêmeos Digitais que deverá estar no formato `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Substitua os espaços reservados nessa URL pelos valores da instância da seção anterior.

## <a name="provision-graph"></a>Grafo de provisão

Esta etapa provisiona o grafo especial dos Gêmeos Digitais com vários espaços, um dispositivo, dois sensores, uma função personalizada e uma atribuição de função. O grafo espacial será provisionado usando o arquivo [*provisionSample.yaml* ](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml).

1. Execute `dotnet run ProvisionSample`.
    >[!NOTE]
    >Usamos a ferramenta de Logon do Dispositivo da CLI do Azure para autenticar o usuário no Azure AD. O usuário precisa inserir um código especificado para fazer a autenticação usando página de [logon da Microsoft](https://microsoft.com/devicelogin). Depois que o código for inserido, siga as etapas para se autenticar. O usuário deverá fazer a autenticação sempre que a ferramenta for executada.
    
    >[!TIP]
    > Se você estiver recebendo o seguinte erro ao executar esta etapa, verifique se as variáveis foram copiadas corretamente. 
    > `EXIT: Unexpected error: The input is not a valid Base-64 string ...`


1. A etapa de provisionamento pode levar alguns minutos, mais ou menos. Ela também provisionará um Hub IoT na instância dos Gêmeos Digitais e fará loop até que o Hub IoT tenha Status=`Running`.

    ![Exemplo de provisionamento][4]

1. No final da execução, copie a `ConnectionString` do dispositivo para usar no exemplo do simulador de dispositivo. Copie apenas a cadeia de caracteres descrita na imagem abaixo:

    ![Exemplo de provisionamento][1]

## <a name="send-sensor-data"></a>Enviar dados de sensor

Você pode compilar e executar o aplicativo de simulador de sensor usando as etapas abaixo:

1. Na pasta digital-twins-samples-csharp-master, abra um novo prompt de comando e navegue até o projeto baixado.
1. Execute `cd device-connectivity`.
1. Execute `dotnet restore`.
1. Edite *appsettings.json* para atualizar *DeviceConnectionString* com a `ConnectionString` acima.
1. Execute `dotnet run` para começar a enviar dados de sensor. Você os verá sendo enviados ao serviço Gêmeos Digitais como na imagem abaixo:

     ![Conectividade do dispositivo][2]

1. Permita que esse simulador seja executado para que você possa exibir os resultados lado a lado com a ação da próxima etapa. Essa janela mostrará os dados do sensor simulado enviados para os Gêmeos Digitais e a próxima etapa fará consulta em tempo real para encontrar salas disponíveis com ar fresco.

    >[!TIP]
    > Se você estiver recebendo o erro a seguir ao executar esta etapa, verifique se `DeviceConnectionString` foi copiada corretamente.  
    > `EXIT: Unexpected error: The input is not a valid Base-64 string ...`

## <a name="find-available-spaces-with-fresh-air"></a>Encontrar espaços disponíveis com ar fresco

O exemplo de sensor está simulando valores de dados aleatórios para dois sensores: de movimento e de dióxido de carbono. Os espaços disponíveis com ar fresco são definidos no exemplo pela ausência de pessoas e pelo nível de dióxido de carbono abaixo de 1000 ppm. Se a condição não for atendida, o espaço não será considerado disponível ou a qualidade do ar será considerada ruim.

1. Abra o prompt de comando usado para executar a etapa de provisionamento acima.
1. Execute `dotnet run GetAvailableAndFreshSpaces`.
1. Examine esse prompt de comando e o prompt de comando dos dados do sensor lado a lado conforme descrito abaixo. 
1. Um prompt de comando envia os dados simulados de movimento e de dióxido de carbono para os Gêmeos Digitais a cada cinco segundos. O outro comando lê o grafo em tempo real para descobrir os ambientes disponíveis com ar fresco tendo como base dados simulados aleatórios. Ele exibirá uma das seguintes condições quase em tempo real com base nos dados de sensor enviados pela última vez:
    - Salas disponíveis com ar fresco.
    - Sala ocupada ou ar de baixa qualidade.

     ![Obter espaços disponíveis com ar fresco][3]

Para entender o que aconteceu neste início rápido e quais APIs foram chamadas, abra o [Visual Studio Code](https://code.visualstudio.com/Download) com o projeto de espaço de trabalho do código encontrado em digital-twins-samples-csharp (confira o comando abaixo). Os tutoriais se aprofundam no código e ensinam você como modificar dados de configuração e quais APIs são chamadas. Para entender melhor as APIs de Gerenciamento, navegue até sua página do Swagger e localize os Gêmeos Digitais `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net//management/swagger` ou, por questões de conveniência, navegue até o [Swagger dos Gêmeos Digitais](https://docs.westcentralus.azuresmartspaces.net/management/swagger). 

```
<path>\occupancy-quickstart\src>code ..\..\digital-twins-samples.code-workspace
```

## <a name="clean-up-resources"></a>Limpar recursos

Os tutoriais detalham a forma de criar um aplicativo para gerentes de instalações a fim de aumentar a produtividade de ocupação e gerir o prédio com mais eficiência.

Se você planeja continuar nos tutoriais, não limpe os recursos criados neste início rápido. Caso contrário, use as seguintes etapas para excluir todos os recursos criados por este Início Rápido:

1. Exclua a pasta que foi criada quando o repositório de exemplo foi baixado.
1. No menu à esquerda, no [portal do Azure](http://portal.azure.com), clique em **Todos os recursos** e escolha o recurso dos Gêmeos Digitais. Na parte superior do painel **Todos os recursos**, clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Este início rápido forneceu uma visão geral de um cenário simples para encontrar salas com boas condições de trabalho. Para ver uma análise mais detalhada do cenário, prossiga para este tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Implantar os Gêmeos Digitais do Azure e configurar um grafo espacial](tutorial-facilities-setup.md)

<!-- Images -->
[1]: media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample.png
[2]: media/quickstart-view-occupancy-dotnet/digital-twins-device-connectivity.png
[3]: media/quickstart-view-occupancy-dotnet/digital-twins-get-available.png
[4]: media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample1.png
