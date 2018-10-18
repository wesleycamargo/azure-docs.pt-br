---
title: Monitorar a conectividade de dispositivo usando o Azure IoT Central Explorer
description: Monitore as mensagens do dispositivo e observe as alterações do dispositivo gêmeo por meio da CLI do IoT Central Explorer.
author: viv-liu
ms.author: viviali
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 962f394607d20869bf00db624533996b0060eaf2
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2018
ms.locfileid: "45987237"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>Monitorar a conectividade de dispositivo usando o Azure IoT Central Explorer

*Este tópico aplica-se a construtores e administradores.*

Use a CLI do IoT Central Explorer para ver as mensagens que os dispositivos estão enviando para o IoT Central e observe as alterações no dispositivo gêmeo do Hub IoT. É possível usar essa ferramenta de software livre para obter informações mais detalhadas sobre o estado da conectividade de dispositivo e diagnosticar problemas de mensagens do dispositivo que não alcançam a nuvem ou dispositivos que não estão respondendo a alterações do dispositivo gêmeo.

## <a name="visit-the-iotc-explorer-repo-in-githubhttpsakamsiotciotcexplorercligithub"></a>[Visite o repositório iotc-explorer no GitHub](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>Pré-requisitos
+ Node.js versão 8.x ou superior - https://nodejs.org
+ Será necessário obter um administrador do aplicativo e gerar um token de acesso para usar no iotc-explorer

## <a name="installing-iotc-explorer"></a>Instalar iotc-explorer

Para instalar, execute o comando a seguir na linha de comando:

```
npm install -g iotc-explorer
```

> [!NOTE]
> Normalmente, será necessário executar o comando de instalação em `sudo` ambientes Unix-like.

## <a name="running-iotc-explorer"></a>Executar iotc-explorer

Abaixo estão alguns comandos e opções comuns que você pode executar ao usar `iotc-explorer`. Para exibir o conjunto completo de comandos e opções, você pode passar `--help` para `iotc-explorer` ou qualquer um dos subcomandos.

### <a name="login"></a>Logon

Antes de começar, é necessário ter um administrador do aplicativo IoT Central para obter um token de acesso para você usar. O administrador executa as etapas a seguir:
1. Vá para **Administração/Token de Acesso**. 
1. Clique em **Gerar**.
![Captura de tela da página de token de acesso](media/howto-use-iotc-explorer/accesstokenspage.png)

1. Insira um nome de Token, clique em **Avançar** e **copie o valor de Token**.
    > [!NOTE]
    > O valor de token será mostrado apenas uma vez, portanto, copie esse valor antes de fechar a caixa de diálogo. Após fechar a caixa de diálogo, jamais será exibida novamente.

    ![Copiar captura de tela do token de acesso](media/howto-use-iotc-explorer/copyaccesstoken.png)

Então, é possível usar esse token para entrar na CLI, executando:

```sh
iotc-explorer login "<Token value>"
```

Se você preferir não ter o token persistido no histórico do shell, poderá omiti-lo e fornecê-lo quando solicitado:

```
iotc-explorer login
```

### <a name="monitor-device-messages"></a>Monitorar mensagens de dispositivo

É possível inspecionar as mensagens provenientes de um dispositivo específico ou de todos os dispositivos no aplicativo usando o comando `monitor-messages`. Isso iniciará um observador que emitirá continuamente novas mensagens quando chegarem.

Para inspecionar todos os dispositivos no aplicativo, execute o comando a seguir:

```
iotc-explorer monitor-messages
```
Saída: ![monitor-messages command output](media/howto-use-iotc-explorer/monitormessages.PNG)

Para inspecionar um dispositivo específico, basta adicionar a ID do dispositivo ao final do comando:

```
iotc-explorer monitor-messages <your-device-id>
```

Também é possível fazer com que o comando exiba um formato amigável ao computador, adicionando a opção `--raw` ao comando:

```
iotc-explorer monitor-messages --raw
```

### <a name="get-device-twin"></a>Obter dispositivo gêmeo

É possível usar o comando `get-twin` para obter o conteúdo do dispositivo gêmeo de um dispositivo IoT Central. Para fazer isso, execute o comando a seguir:

```
iotc-explorer get-twin <your-device-id>
```

Saída: ![get-twin command output](media/howto-use-iotc-explorer/getdevicetwin.PNG)

Tal como em `monitor-messages`, você pode obter uma saída amigável do computador, passando a opção `--raw`:

```
iotc-explorer get-twin <your-device-id> --raw
```

## <a name="next-steps"></a>Próximas etapas
Agora que você sabe como usar o IoT Central Explorer, a próxima etapa sugerida é explorar [gerenciar dispositivos IoT Central](howto-manage-devices.md).
