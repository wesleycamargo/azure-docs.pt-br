---
title: 'Início Rápido: API REST do Azure SignalR Service | Microsoft Docs'
description: Um início rápido para usar a API REST do Azure SignalR Service.
services: signalr
documentationcenter: ''
author: sffamily
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET
ms.workload: tbd
ms.date: 06/13/2018
ms.author: zhshang
ms.openlocfilehash: 93c1198ecfba6db809228ed6dcd99c705f53926c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972752"
---
# <a name="quickstart-broadcast-real-time-messages-from-console-app"></a>Início Rápido: difundir mensagens em tempo real do aplicativo de console

O Azure SignalR Service fornece [API REST](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) para dar suporte ao servidor para cenários de comunicação do cliente, como difusão. Você pode escolher qualquer linguagem de programação que pode fazer a chamada à API REST. Você pode postar mensagens para todos os clientes conectados, um cliente específico por nome ou um grupo de clientes.

Neste início rápido, você aprenderá como enviar mensagens de um aplicativo de linha de comando para aplicativos cliente conectados em C#.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido pode ser executado no macOS, Windows ou Linux.
* [SDK do .NET Core](https://www.microsoft.com/net/download/core)
* Um editor de texto ou editor de código de sua escolha.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no portal do Azure em <https://portal.azure.com/> com sua conta do Azure.


[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Durante a implantação do serviço, vamos alternar para preparar o código. Clone o [aplicativo de exemplo do GitHub](https://github.com/aspnet/AzureSignalR-samples.git), defina a cadeia de conexão do SignalR Service e execute o aplicativo localmente.

1. Abra uma janela de terminal git. Mude para uma pasta em que deseja clonar o projeto de exemplo.

1. Execute o comando a seguir para clonar o repositório de exemplo. Este comando cria uma cópia do aplicativo de exemplo no seu computador.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

## <a name="build-and-run-the-sample"></a>Criar e executar a amostra

Este exemplo é um aplicativo de console mostrando o uso do Azure SignalR Service. Ele fornece dois modos:

- Modo de servidor: use comandos simples para chamar a API REST do Azure SignalR Service.
- Modo cliente: conectar-se ao Azure SignalR Service e receber mensagens de servidor.

Também é possível encontrar como gerar um token de acesso para autenticar com o Azure SignalR Service.

### <a name="build-the-executable-file"></a>Criar o arquivo executável

Como exemplo, usamos macOS osx.10.13-x64. Você pode encontrar [referências](https://docs.microsoft.com/dotnet/core/rid-catalog) sobre como criar em outras plataformas.
```bash
cd AzureSignalR-samples/samples/Serverless/

dotnet publish -c Release -r osx.10.13-x64
```

### <a name="start-a-client"></a>Iniciar um cliente

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="start-a-server"></a>Iniciar um servidor

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless server -c "<ConnectionString>" -h <HubName>
```

## <a name="run-the-sample-without-publishing"></a>Executar o exemplo sem publicação

Você também pode executar o comando a seguir para iniciar um servidor ou cliente

```bash
# Start a server
dotnet run -- server -c "<ConnectionString>" -h <HubName>

# Start a client
dotnet run -- client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="use-user-secrets-to-specify-connection-string"></a>Use segredos do usuário para especificar a Cadeia de Conexão

Você pode executar `dotnet user-secrets set Azure:SignalR:ConnectionString "<ConnectionString>"` no diretório raiz do exemplo. Depois disso, você não precisa mais da opção `-c "<ConnectionString>"`.

## <a name="usage"></a>Uso

Depois que o servidor for iniciado, use o comando para enviar a mensagem

```
send user <User Id>
send users <User List>
send group <Group Name>
send groups <Group List>
broadcast
```

Você pode iniciar vários clientes com nomes de clientes diferentes.


[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]
