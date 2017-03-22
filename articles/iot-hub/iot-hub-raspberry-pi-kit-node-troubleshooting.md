---
title: Conectar o Raspberry Pi (C) ao IoT do Azure - Solucionar problemas | Microsoft Docs
description: "Página de solução de problemas para experiência Raspberry Pi Node.js"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: problemas de iot, problemas internet das coisas
ms.assetid: 22cf50dc-8206-42a2-a1fc-f75fa85135fa
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 2c5e2955584b165ca0ca95ce424a2050fb3f6ccd
ms.lasthandoff: 01/24/2017


---
# <a name="troubleshooting"></a>Solucionar problemas
## <a name="hardware-issues"></a>Problemas de hardware
### <a name="the-application-runs-well-but-the-led-is-not-blinking"></a>O aplicativo funciona bem, mas o LED não está piscando
Esse problema está sempre relacionado à conectividade de circuito do hardware. Use as etapas a seguir para identificar problemas:

1. Verifique se você escolheu o **GPIO** correto na placa. As duas portas devem ser **GPIO GND (Pino 6)** e **GPIO 04 (Pino 7)**.
2. Verifique se a polaridade do seu LED está correta. A base maior deve indicar o pino de nó **positivo**.
3. Use o **Pino 3.3 V** e o **Pino GND** em seu Raspberry Pi 3. Trate o Pi como corrente contínua. Verifique se o LED funciona bem.

![Especificação do LED](media/iot-hub-raspberry-pi-lessons/troubleshooting/led_spec.png)

### <a name="other-hardware-issues"></a>Outros problemas de hardware
Para obter informações sobre como resolver problemas comuns no Raspberry Pi 3, consulte a [página oficial de solução de problemas](http://elinux.org/R-Pi_Troubleshooting).

## <a name="nodejs-package-issues"></a>Problemas do pacote de Node.js
### <a name="no-response-during-gulp-tasks"></a>Sem resposta durante as tarefas de gulp
Se você encontrar problemas ao executar tarefas de gulp, adicione a opção `--verbose` para depuração. Tente finalizar as tarefas de gulp atuais usando Ctrl + C e, em seguida, execute o seguinte comando na janela do console para ver mensagens de depuração. Você pode ver mensagens de erro detalhadas em sua saída do console.

```bash
gulp --verbose
```

### <a name="device-discovery-issues"></a>Problemas de descoberta de dispositivo
Para obter ajuda na solução de problemas comuns com o comando `devdisco`, consulte o [Leiame](https://github.com/Azure/device-discovery-cli/blob/develop/readme.md).

### <a name="npm-issues"></a>problemas de npm
Tente atualizar o pacote npm usando o comando a seguir:

```bash
npm install -g npm
```

Se o problema persistir, deixe seus comentários no final deste artigo ou crie um problema de GitHub em nosso [repositório de exemplo](https://github.com/Azure-Samples/iot-hub-node-raspberrypi-getting-started).

## <a name="remote-debugging"></a>Depuração remota
### <a name="run-the-sample-application-in-debug-mode"></a>Execute o aplicativo de exemplo no modo de depuração
```bash
gulp run --debug
```

Quando o mecanismo de depuração estiver pronto, você deverá ver ```Debugger listening on port 5858``` na saída do console.

### <a name="configure-visual-studio-code-to-connect-to-the-remote-device"></a>Configurar o Visual Studio Code para se conectar ao dispositivo remoto
1. Abra o painel **Depurar** painel no lado esquerdo.
2. Clique no botão verde **Iniciar Depuração** (F5). O Visual Studio Code abre um arquivo launch.json.
3. Atualize o arquivo launch.json com o seguinte conteúdo. Substitua `[device hostname or IP address]` pelo nome do host ou endereço IP do dispositivo real.

> [!NOTE]
> Para saber mais sobre a depuração do Visual Studio, consulte [Depuração no Visual Studio Code](https://code.visualstudio.com/Docs/editor/debugging#_launchjson-attributes).


```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Attach",
            "type": "node",
            "request": "attach",
            "port": 5858,
            "address": "[device hostname or IP address]",
            "restart": false,
            "sourceMaps": false,
            "outDir": null,
            "localRoot": "${workspaceRoot}",
            "remoteRoot": null
        }
    ]
}
```

![Configuração de depuração remota](media/iot-hub-raspberry-pi-lessons/troubleshooting/remote_debugging_configuration.png)

### <a name="attach-to-the-remote-application"></a>Anexar ao aplicativo remoto
Clique no botão verde **Iniciar Depuração** (F5) para iniciar a depuração.

Leia [JavaScript no Código do VS](https://code.visualstudio.com/docs/languages/javascript#_debugging) para saber mais sobre o depurador.

![Depuração remota interativa](media/iot-hub-raspberry-pi-lessons/troubleshooting/remote_debugging_interactive.png)

## <a name="azure-cli-issues"></a>Problemas da CLI do Azure
A interface de linha de comando do Azure (CLI do Azure) é uma compilação de visualização. Para procurar soluções, você pode consultar o [Guia de Instalação de Visualização](https://github.com/Azure/azure-cli/blob/master/doc/preview_install_guide.md).

Se você encontrar erros com a ferramenta, emita um [problema](https://github.com/Azure/azure-cli/issues) na seção **problemas** do repositório do GitHub.

Para obter ajuda para solucionar problemas comuns, consulte o [Leiame](https://github.com/Azure/azure-cli/blob/master/README.rst).

## <a name="python-installation-issues"></a>Problemas de instalação do Python
### <a name="legacy-installation-issues-macos"></a>Problemas de instalação herdada (macOS)
Ao instalar o pip, um erro de permissão será lançado quando houver pacotes herdados instalados com permissões **su**. Essa situação ocorre porque a instalação anterior do Python usando brew (macOS) não está completamente desinstalada. Alguns pacotes do pip de uma instalação anterior foram criados pela raiz, o que causa o erro de permissão. A solução é remover os pacotes instalados pela raiz. Use as etapas a seguir para concluir esta tarefa:

1. Acesse: /usr/local/lib/python2.7/site-packages
2. Listar pacotes criados por raiz: `ls -l | grep root`
3. Desinstalar pacotes da etapa 2: `sudo rm -rf {package name}`
4. Reinstale o Python.

## <a name="azure-iot-hub-issues"></a>Problemas do Hub IoT do Azure
Se você tiver provisionado com êxito o Hub IoT do Azure com a CLI do Azure e precisar de uma ferramenta para gerenciar os dispositivos conectados ao seu Hub IoT, tente as seguintes ferramentas.

### <a name="device-explorer"></a>Gerenciador de dispositivos
O [Gerenciador de dispositivos](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) é executado no computador local do Windows e se conecta ao seu hub IoT no Azure. Ele se comunica com os seguintes [pontos de extremidade de Hub IoT](iot-hub-devguide.md):


* *Gerenciamento de identidade do dispositivo* para provisionar e gerenciar dispositivos registrados com seu hub IoT.
* *Receber do dispositivo para nuvem* para que você possa monitorar as mensagens enviadas de seu dispositivo ao seu Hub IoT.
* *Enviar do dispositivo para nuvem* para que você possa enviar mensagens para seus dispositivos de seu Hub IoT.

Configure a cadeia de conexão do Hub IoT dentro desta ferramenta para usar todos os seus recursos.

### <a name="iothub-explorer"></a>iothub-explorer
[iothub-explorer](https://github.com/Azure/iothub-explorer) é uma ferramenta de CLI de várias plataformas de exemplo para gerenciar dispositivos. Use a ferramenta para gerenciar os dispositivos no Registro de identidade, monitorar mensagens de dispositivo para a nuvem e enviar mensagens de nuvem para o dispositivo.

Para instalar a versão mais recente (pré-lançamento) da ferramenta Gerenciador do iothub, execute o comando a seguir em seu ambiente de linha de comando:

```bash
npm install -g iothub-explorer@latest
```

Você pode usar o comando a seguir para obter ajuda adicional sobre todos os comandos do Gerenciador do iothub e seus parâmetros:

```bash
iothub-explorer help
```

### <a name="azure-portal"></a>Portal do Azure
Uma experiência completa de CLI ajuda você a criar e gerenciar todos os recursos do Azure. Você também poderá usar o [portal do Azure](../azure-portal-overview.md) para ajudar a provisionar, gerenciar e depurar seus recursos do Azure.

## <a name="azure-storage-issues"></a>Problemas de Armazenamento do Azure
[O Gerenciador de Armazenamento do Microsoft Azure (Preview)](http://storageexplorer.com) é um aplicativo autônomo da Microsoft que pode ser usado para trabalhar com dados do Armazenamento do Azure no Windows, macOS e Linux. Com essa ferramenta você pode se conectar à sua tabela e ver os dados contidos nela. Você pode usar essa ferramenta para solucionar seus problemas de Armazenamento do Azure.


