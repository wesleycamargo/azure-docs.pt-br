---
title: "Conectar o Intel Edison (Nó) ao IoT do Azure - Lição 4: solução de problemas| Microsoft Docs"
description: "Página de solução de problemas da experiência Node.js do Intel Edison"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "solução de problemas do arduino"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-node-get-started
ms.assetid: f11c5521-8a36-44c0-baad-f5ec26ab4618
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: d77ee0be35a3f4b38c50cf9322124e4dfbba6ddc
ms.contentlocale: pt-br
ms.lasthandoff: 01/25/2017

---
<a id="troubleshooting" class="xliff"></a>

# Solucionar problemas
<a id="hardware-issues" class="xliff"></a>

## Problemas de hardware
Para obter informações sobre como resolver problemas comuns no Intel Edison, consulte a [página oficial de solução de problemas](https://software.intel.com/en-us/node/637974).

<a id="nodejs-package-issues" class="xliff"></a>

## Problemas do pacote de Node.js
<a id="no-response-during-gulp-tasks" class="xliff"></a>

### Sem resposta durante as tarefas de gulp
Se você encontrar problemas ao executar tarefas de gulp, será possível adicionar a opção `--verbose` para depuração. Tente finalizar as tarefas de gulp atuais usando `Ctrl + C` e, em seguida, execute o seguinte comando na janela do console para ver mensagens de depuração. Você pode ver mensagens de erro detalhadas em sua saída do console. 

```bash
gulp --verbose
```

<a id="npm-issues" class="xliff"></a>

### Problemas de NPM
Tente atualizar o pacote NPM com o comando a seguir:

```bash
npm install -g npm
```

Se o problema persistir, deixe seus comentários no final deste artigo ou crie um problema do GitHub em nosso [repositório de exemplo][sample-repository].

<a id="remote-debugging" class="xliff"></a>

## Depuração remota

<a id="run-the-sample-application-in-debug-mode" class="xliff"></a>

### Execute o aplicativo de exemplo no modo de depuração

```bash
gulp run --debug
```

Quando o mecanismo de depuração estiver pronto, você deverá ver ```Debugger listening on port 5858``` na saída do console.

<a id="configure-vs-code-to-connect-to-the-remote-device" class="xliff"></a>

### Configurar o código do VS para se conectar ao dispositivo remoto

Abra o painel **Depurar** no lado esquerdo.

Clique no botão verde **Iniciar Depuração** (F5). O Código do VS abre um arquivo **launch.json**, que você precisa atualizar.

Atualize o arquivo **launch.json** com o conteúdo a seguir, substitua `[device hostname or IP address]` pelo nome de host ou endereço IP do dispositivo real.  

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

![Configuração de depuração remota](media/iot-hub-intel-edison-lessons/troubleshooting/remote_debugging_configuration.png)

<a id="attach-to-the-remote-application" class="xliff"></a>

### Anexar ao aplicativo remoto

Clique no botão verde **Iniciar Depuração** (F5) e tire proveito da depuração.

Leia [JavaScript no Código do VS](https://code.visualstudio.com/docs/languages/javascript#_debugging) para saber mais sobre o depurador.

![Depuração remota interativa](media/iot-hub-intel-edison-lessons/troubleshooting/remote_debugging_interactive.png)

<a id="azure-cli-issues" class="xliff"></a>

## Problemas da CLI do Azure
A interface de linha de comando do Azure (CLI do Azure) é uma compilação de visualização. Procure pela solução no [Guia de Instalação de Visualização](https://github.com/Azure/azure-cli/blob/master/doc/preview_install_guide.md) para buscar soluções. Tente atualizar a CLI do Azure para a versão mais recente quando comandos não funcionarem conforme o esperado.

Se você encontrar erros com a ferramenta, emita um [problema](https://github.com/Azure/azure-cli/issues) na seção **problemas** do repositório do GitHub.

Para obter ajuda para solucionar problemas comuns, consulte o [Leiame](https://github.com/Azure/azure-cli/blob/master/README.rst).

Se você receber a mensagem “Não foi possível localizar uma versão que atenda ao requisito”, execute o seguinte comando para atualizar o pip para a versão mais recente.

```bash
python -m pip install --upgrade pip
```

<a id="python-installation-issues" class="xliff"></a>

## Problemas de instalação do Python
<a id="legacy-installation-issues-macos" class="xliff"></a>

### Problemas de instalação herdada (macOS)
Ao instalar o **pip**, um erro de permissão será lançado quando houver pacotes mais antigos instalados com permissões **su**. Essa situação ocorre porque a instalação anterior do Python usando brew (macOS) não está completamente desinstalada. Alguns pacotes do **pip** de uma instalação anterior foram criados pela raiz, o que causa o erro de permissão. A solução é remover os pacotes instalados pela raiz. Use as etapas a seguir para concluir esta tarefa:

1. Acesse: /usr/local/lib/python2.7/site-packages
2. Listar pacotes criados por raiz: `ls -l | grep root`
3. Desinstalar pacotes da etapa 2: `sudo rm -rf {package name}`
4. Reinstale o Python.

<a id="azure-iot-hub-issues" class="xliff"></a>

## Problemas do Hub IoT do Azure
Se você tiver provisionado com êxito o Hub IoT do Azure com `azure-cli` e precisar de uma ferramenta para gerenciar os dispositivos conectados ao seu Hub IoT, experimente usar as seguintes ferramentas:

<a id="device-explorer" class="xliff"></a>

### Gerenciador de Dispositivos
O [Gerenciador de Dispositivos](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) é executado no computador local do Windows e se conecta ao seu hub IoT no Azure. Ele se comunica com os seguintes [pontos de extremidade de Hub IoT](iot-hub-devguide.md):

- _Gerenciamento de identidade do dispositivo_ para provisionar e gerenciar dispositivos registrados com seu hub IoT.
- _Receber do dispositivo para nuvem_ para que você possa monitorar as mensagens enviadas de seu dispositivo ao seu Hub IoT.
- _Enviar do dispositivo para nuvem_ para que você possa enviar mensagens para seus dispositivos de seu Hub IoT.

Configure o `IoT hub connection string` dentro desta ferramenta para usar todos os seus recursos.

<a id="iot-hub-explorer" class="xliff"></a>

### Gerenciador do Hub IoT
O [Gerenciador do Hub IoT](https://github.com/Azure/iothub-explorer) é uma ferramenta de CLI de várias plataformas de exemplo para gerenciar clientes de dispositivo. Use a ferramenta para gerenciar os dispositivos no registro de identidade, monitorar mensagens de dispositivo para a nuvem e enviar comandos de nuvem para o dispositivo.

Para instalar a versão mais recente (pré-lançamento) da ferramenta Gerenciador do iothub, execute o comando a seguir em seu ambiente de linha de comando:

```bash
npm install -g iothub-explorer@latest
```

Você pode usar o comando a seguir para obter ajuda adicional sobre todos os comandos do Gerenciador do iothub e seus parâmetros:

```bash
iothub-explorer help
```

<a id="azure-portal" class="xliff"></a>

### Portal do Azure
Uma experiência completa de CLI ajuda você a criar e gerenciar todos os recursos do Azure. Você também poderá usar o [portal do Azure](../azure-portal-overview.md) para ajudar a provisionar, gerenciar e depurar seus recursos do Azure.

<a id="azure-storage-issues" class="xliff"></a>

## Problemas de Armazenamento do Azure
[O Gerenciador de Armazenamento do Microsoft Azure (visualização)](http://storageexplorer.com) é um aplicativo autônomo da Microsoft que pode ser usado para trabalhar com os dados do [Armazenamento do Azure](https://azure.microsoft.com/en-us/services/storage/) no Windows, macOS e Linux. Com essa ferramenta você pode se conectar à sua tabela e ver os dados contidos nela. Você pode usar essa ferramenta para solucionar seus problemas de Armazenamento do Azure.

<a id="next-steps" class="xliff"></a>

## Próximas etapas
Esta página inclui apenas os problemas mais comuns do kit Intel Edison. Você também pode deixar comentários na parte inferior para relatar problemas para solução de problemas no futuro.

Voltar para [Introdução ao Intel Edison (Node.js)](iot-hub-intel-edison-kit-node-get-started.md)

<!-- Images and links -->

[sample-repository]: https://github.com/Azure-Samples/iot-hub-node-edison-getting-started
