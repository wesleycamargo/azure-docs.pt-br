---
title: Implantar a solução de monitoramento remoto localmente – Azure | Microsoft Docs
description: Este guia de instruções mostra como implantar o acelerador de solução de monitoramento remoto no computador local para teste e desenvolvimento.
author: asdonald
manager: timlt
ms.author: asdonald
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 09/26/2018
ms.topic: conceptual
ms.openlocfilehash: 7007b1406dbcfab3af4700418ac2ce07b9e521c0
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47407426"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally"></a>Implantar o acelerador da solução de monitoramento remoto localmente

Este artigo mostra como implantar o acelerador de solução de Monitoramento Remoto no computador local para teste e desenvolvimento. A abordagem descrita no artigo implanta os microsserviços em um contêiner do Docker local e usa os serviços de Hub IoT, do Cosmos DB e do Azure Time Series Insights na nuvem. Para saber como executar o acelerador de solução de Monitoramento Remoto em um IDE no computador local, confira [Starting Microservices on local environment](https://github.com/Azure/remote-monitoring-services-java/blob/master/docs/LOCAL_DEPLOYMENT.md) (Iniciando microsserviços no ambiente local) no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

Para implantar os serviços do Azure usados pelo acelerador de solução de Monitoramento Remoto, você precisará de uma assinatura ativa do Azure.

Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).

Para concluir a implantação local, você precisa ter as seguintes ferramentas instaladas no computador de desenvolvimento local:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Docker Compose](https://docs.docker.com/compose/install/)
* [Node.js v8](https://nodejs.org/) – este software é um pré-requisito para a CLI do PCS que os scripts usam para criar recursos do Azure. Não use o Node.js v10.

> [!NOTE]
> Essas ferramentas estão disponíveis em muitas plataformas, inclusive Windows, Linux e iOS.

### <a name="download-the-source-code"></a>Fazer o download do código-fonte

O repositório de código-fonte de Monitoramento Remoto no GitHub inclui os arquivos de configuração do Docker necessários para baixar, configurar e executar as imagens do Docker que contêm os microsserviços. Para clonar e criar uma versão local do repositório, use o ambiente da linha de comando para navegar até uma pasta adequada no computador local e, em seguida, execute um dos seguintes comandos:

Para baixar a versão mais recente das implementações do microsserviço de Java, execute:

```cmd/sh
git clone https://github.com/Azure/remote-monitoring-services-java.git
```

Para baixar a versão mais recente das implementações do microsserviço .NET, execute:

```cmd\sh
git clone https://github.com/Azure/remote-monitoring-services-dotnet.git
```

> [!NOTE]
> Esses comandos baixam o código-fonte para todos os microsserviços, bem como os scripts usados para executar os microsserviços localmente. Embora o código-fonte não seja necessário para executar os microsserviços no Docker, ele é útil se você planeja modificar o acelerador de solução e testar suas alterações localmente no futuro.

## <a name="deploy-the-azure-services"></a>Implantar os serviços do Azure

Embora este artigo mostre como executar os microsserviços localmente, eles dependem de serviços do Azure em execução na nuvem. É possível implantar esses serviços do Azure [manualmente por meio do portal do Azure](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Manual-steps-to-create-azure-resources-for-local-setup) ou usar o script fornecido. Os exemplos de script a seguir pressupõem que você estará usando o repositório .NET em um computador Windows. Se estiver trabalhando em outro ambiente, ajuste os caminhos, extensões de arquivo e separadores de caminho adequadamente. Para usar os scripts fornecidos para:

### <a name="create-new-azure-resources"></a>Criar novos recursos do Azure

Se você ainda não criou os recursos do Azure necessários, siga estas etapas:

1. No ambiente da linha de comando, navegue até a pasta **remote-monitoring-services-dotnet\scripts\local\launch** em sua cópia clonada do repositório.

2. Execute o script **start.cmd** e siga os prompts. O script solicitará que você entre em sua conta do Azure e reinicie o script. Em seguida, o script solicitará as seguintes informações:
    * Um nome de solução.
    * A assinatura do Azure a utilizar.
    * O local do datacenter do Azure para usar.

    O script cria um grupo de recursos no Azure com o nome da solução. Esse grupo de recursos contém os recursos do Azure usados pelo acelerador de solução.

3. Quando for concluído, o script exibirá uma lista de variáveis de ambiente. Siga as instruções na saída do comando para salvar essas variáveis no arquivo **remote-monitoring-services-dotnet\\scripts\\local\\.env**.

### <a name="use-existing-azure-resources"></a>Usar recursos existentes do Azure

Se você já criou os recursos necessários do Azure, edite as definições de variável de ambiente no arquivo **remote-monitoring-services-dotnet\\scripts\\local\\.env** com o valores necessários. O arquivo **.env** contém informações detalhadas sobre onde encontrar os valores necessários.

## <a name="run-the-microservices-in-docker"></a>Executar os microsserviços no Docker

Os microsserviços em execução nos contêineres de Docker locais precisam acessar os serviços em execução no Azure. Você pode testar a conectividade de Internet de seu ambiente do Docker usando o seguinte comando, que inicia um contêiner pequeno e tenta executar ping de um endereço na Internet:

```cmd/sh
docker run --rm -ti library/alpine ping google.com
```

Para executar o acelerador de solução, navegue até a pasta **remote-monitoring-services-dotnet\\scripts\\local** em seu ambiente de linha de comando e execute o seguinte comando:

```cmd\sh
docker-compose up
```

Na primeira vez que você executar esse comando, o Docker baixa as imagens de microsserviço do hub do Docker para criar os contêineres localmente. Em execuções subsequentes, o Docker executa os contêineres imediatamente.

Você pode usar um shell separado para exibir os logs do contêiner. Primeiro, encontre a ID de contêiner usando o comando `docker ps -a`. Em seguida, use `docker logs {container-id} --tail 1000` para exibir as últimas entradas de log de 1000 para o contêiner especificado.

Para acessar o painel da solução de Monitoramento Remoto, navegue até [http://localhost:8080](http://localhost:8080) em seu navegador.

## <a name="clean-up"></a>Limpar

Para evitar encargos desnecessários, quando terminar o teste, remova os serviços de nuvem de sua assinatura do Azure. A maneira mais fácil de remover os serviços é navegar até o [portal do Azure](https://ms.portal.azure.com) e excluir o grupo de recursos criado quando você executou o script **start.cmd**.

Use o comando `docker-compose down --rmi all` para remover as imagens do Docker e liberar espaço no seu computador local. Você também pode excluir a cópia local do repositório de Monitoramento Remoto criada quando você clonou o código-fonte do GitHub.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Definir uma variável de ambiente local
> * Configurar o acelerador de solução
> * Implantar o acelerador de solução
> * Entrar no acelerador de solução

Agora que você implantou a solução de Monitoramento Remoto, a próxima etapa será [explorar os recursos do painel da solução](quickstart-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->
