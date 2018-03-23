---
title: Implantar a solução de monitoramento remoto localmente – Azure | Microsoft Docs
description: Este tutorial mostra como implantar a solução de monitoramento remoto pré-configurada em seu computador local para teste e desenvolvimento.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 03/07/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 77f40e2f10cbdb9930a22a4248e19bb3356af7af
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="deploy-the-remote-monitoring-preconfigured-solution-locally"></a>Implantar a solução de monitoramento remoto pré-configurada localmente

Este artigo mostra como implantar a solução de monitoramento remoto pré-configurada em seu computador local para teste e desenvolvimento. Essa abordagem implanta os microsserviços em um contêiner do Docker local e usa o Hub IoT, Cosmos DB e serviços de armazenamento do Azure na nuvem. Você usa as soluções pré-configuradas (PCS) CLI para implantar os serviços de nuvem do Azure.

## <a name="prerequisites"></a>pré-requisitos

Para implantar os serviços do Azure usados pela solução de monitoramento remoto pré-configurada, você precisará de uma assinatura ativa do Azure.

Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).

Para concluir a implantação local, você precisa ter as seguintes ferramentas instaladas no computador de desenvolvimento local:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Docker Compose](https://docs.docker.com/compose/install/)
* [Node. js](https://nodejs.org/) - este software é um pré-requisito para PCS CLI.
* PCS CLI

> [!NOTE]
> Essas ferramentas estão disponíveis em muitas plataformas, inclusive Windows, Linux e iOS.

### <a name="install-the-pcs-cli"></a>Instalar a PCS CLI

Para instalar a CLI, execute o seguinte comando no seu ambiente de linha de comando:

```cmd/sh
npm install iot-solutions -g
```

Para obter mais informações sobre a CLI, consulte [Como usar a CLI](https://github.com/Azure/pcs-cli/blob/master/README.md).

## <a name="deploy-the-azure-services"></a>Implantar os serviços do Azure

Embora este artigo mostra como executar os microsserviços localmente, eles dependem de três serviços do Azure em execução na nuvem. Você pode implantar esses serviços do Azure manualmente por meio do portal do Azure, ou usar a PCS CLI. Este artigo mostra como usar a ferramenta `pcs`.

### <a name="sign-in-to-the-cli"></a>Entrar na CLI

Antes de implantar a solução pré-configurada, você deve entrar na sua assinatura do Azure usando a CLI como mostrado a seguir:

```cmd/sh
pcs login
```

Siga as instruções na tela para concluir o processo de entrada.

### <a name="run-a-local-deployment"></a>Executar uma implantação local

Use o comando abaixo para iniciar a implantação local:

```cmd/pcs
pcs -s local
```

O script solicitará as seguintes informações:

* Um nome de solução.
* A assinatura do Azure a utilizar.
* O local do datacenter do Azure para usar.

O script cria uma instância de Hub IoT, uma instância de Cosmos DB e uma conta de armazenamento do Azure em um grupo de recursos em sua assinatura do Azure. O nome do grupo de recursos é o nome da solução que você escolheu quando executou a ferramenta `pcs`.

O script leva vários minutos para ser executado. Quando for concluído, você verá uma mensagem `Copy the following environment variables to /scripts/local/.env file:`. Copie as definições de variável de ambiente seguindo a mensagem, você pode usá-las em uma etapa posterior.

## <a name="download-the-source-code"></a>Fazer o download do código-fonte

O repositório de código-fonte de monitoramento remoto inclui os arquivos de configuração do Docker que você precisa baixar, configurar e executar as imagens do Docker que contêm os microsserviços. Para clonar o repositório, navegue até uma pasta adequada no computador local e execute um dos seguintes comandos:

Para instalar as implementações de Java dos microsserviços, execute:

```cmd/sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-java
```

Para instalar as implementações de .Net dos microsserviços, execute:

```cmd\sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet
```

Esses comandos baixam o código-fonte para todos os microsserviços. Embora não seja necessário o código-fonte para executar os microsserviços no Docker, o código-fonte é útil se você posteriormente planeja modificar a solução pré-configurada e testar suas alterações localmente.

## <a name="run-the-microservices-in-docker"></a>Executar os microsserviços no Docker

Para executar os microsserviços no Docker, primeiro edite o arquivo **scripts\\local\\.env** em sua cópia local do repositório. Substitua todo o conteúdo do arquivo com as definições de variável de ambiente que você anotou quando executou o comando `pcs` anteriormente. Essas variáveis de ambiente habilitam os microsserviços no contêiner do Docker para se conectar aos serviços do Azure criados pela ferramenta `pcs`.

Para executar a solução pré-configurada, navegue até a pasta **scripts\local** no seu ambiente de linha de comando e execute o seguinte comando:

```cmd\sh
docker-compose up
```

Na primeira vez que você executar esse comando, o Docker baixa as imagens de microsserviço do hub do Docker para criar os contêineres localmente. Em execuções subsequentes, o Docker executa os contêineres imediatamente.

Você pode usar um shell separado para exibir os logs do contêiner. Primeiro, encontre a ID de contêiner usando o comando `docker ps -a`. Em seguida, use `docker logs {container-id} --tail 1000` para exibir as últimas entradas de log de 1000 para o contêiner especificado.

Para acessar o painel de solução de monitoramento remoto, navegue até [http://localhost:8080](http://localhost:8080) em seu navegador.

## <a name="tidy-up"></a>Limpar

Para evitar encargos desnecessários, quando você terminar o teste, remova os serviços de nuvem de sua assinatura do Azure. A maneira mais fácil de remover os serviços é usar o portal do Azure para excluir o grupo de recursos criado pela ferramenta `pcs`.

Use o comando `docker-compose down --rmi all` para remover as imagens do Docker e liberar espaço no seu computador local. Você também pode excluir a cópia local do repositório de monitoramento remoto criado quando você clonou o código-fonte do GitHub.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Configurar a solução pré-configurada
> * Implantar a solução pré-configurada
> * Entrar na solução pré-configurada

Agora que você implantou a solução de monitoramento remoto, a próxima etapa será [explorar os recursos do painel da solução](./iot-suite-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->