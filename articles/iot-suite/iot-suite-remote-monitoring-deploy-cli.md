---
title: "Implantar a solução Java de monitoramento remoto – Azure | Microsoft Docs"
description: "Este tutorial mostra como provisionar a solução pré-configurada de monitoramento remoto usando a CLI."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/29/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 94c3db3286623264e9df7873962d10dd5cc662d4
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="deploy-the-remote-monitoring-preconfigured-solution-using-the-cli"></a>Implantar a solução pré-configurada de monitoramento remoto usando a CLI

Este tutorial mostra como provisionar a solução pré-configurada de monitoramento remoto. Implante a solução usando a CLI. Também é possível implantar a solução usando a interface do usuário na Web em azureiotsuite.com. Para saber mais sobre essa opção, consulte [Implantar uma solução pré-configurada de monitoramento remoto](iot-suite-remote-monitoring-deploy.md).

## <a name="prerequisites"></a>pré-requisitos

Para implantar a solução pré-configurada de monitoramento remoto, você precisará de uma assinatura ativa do Azure.

Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).

Para executar a CLI, você precisará do [Node.js](https://nodejs.org/) instalado no seu computador local.

## <a name="install-the-cli"></a>Instalar a CLI

Para instalar a CLI, execute o seguinte comando no seu ambiente de linha de comando:

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Entrar na CLI

Antes de implantar a solução pré-configurada, você deve entrar na sua assinatura do Azure usando a CLI como mostrado a seguir:

```cmd/sh
pcs login
```

Siga as instruções na tela para concluir o processo de entrada.

## <a name="deployment-options"></a>Opções de implantação

Há várias opções para definem o processo de implantação ao implantar a solução pré-configurada:

| Opção | Valores | DESCRIÇÃO |
| ------ | ------ | ----------- |
| SKU    | `basic`, `standard`, `local` | Uma implantação _básica_ destina-se a testes e demonstrações, e implanta todos os microsserviços em uma única máquina virtual. Uma implantação _padrão_ destina-se à produção e implanta os microsserviços em diversas máquinas virtuais. Uma implantação _local_ configura um contêiner do Docker para executar o microservices em seu computador local e usa os serviços do Azure, como armazenamento e o Cosmos DB, na nuvem. |
| Tempo de execução | `dotnet`, `java` | Seleciona a implementação de linguagem dos microsserviços. |

Para saber mais sobre como usar a implantação local, consulte [Executar a solução de monitoramento remota localmente](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Running-the-Remote-Monitoring-Solution-Locally#deploy-azure-services-and-set-environment-variables).

## <a name="deploy-the-preconfigured-solution"></a>Implantar a solução pré-configurada

### <a name="example-deploy-net-version"></a>Exemplo: implantar a versão .NET

O exemplo a seguir mostra como implantar a versão .NET básica da solução pré-configurada de monitoramento remoto:

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>Exemplo: implantar a versão Java

O exemplo a seguir mostra como implantar a versão Java padrão da solução pré-configurada de monitoramento remoto:

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>Opções de comando pcs

Quando você executa o comando `pcs` para implantar uma solução, será solicitado que você informe:

- Um nome para a solução. Esse nome deve ser exclusivo.
- A assinatura do Azure a utilizar.
- Um local.
- Credenciais para as máquinas virtuais que hospedam o microsserviços. Você pode usar essas credenciais para acessar as máquinas virtuais para solução de problemas.

Quando o comando `pcs` for concluído, ele exibirá a URL da sua nova implantação de solução pré-configurada. O comando `pcs` também cria um arquivo `{deployment-name}-output.json` com informações adicionais, como o nome do Hub IoT provisionado para você.

Para saber mais sobre os parâmetros de linha de comando, execute:

```cmd/sh
pcs -h
```

Para obter mais informações sobre a CLI, consulte [Como usar a CLI](https://github.com/Azure/pcs-cli/blob/master/README.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Configurar a solução pré-configurada
> * Implantar a solução pré-configurada
> * Entrar na solução pré-configurada

Agora que você implantou a solução de monitoramento remoto, a próxima etapa será [explorar os recursos do painel da solução](./iot-suite-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->