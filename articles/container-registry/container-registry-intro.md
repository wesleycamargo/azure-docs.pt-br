---
title: Registros de contêiner do Docker privado no Azure – visão geral
description: Introdução ao serviço de Registro de Contêiner do Azure, fornecendo registros Docker privados gerenciados baseados em nuvem.
services: container-registry
author: stevelas
ms.service: container-registry
ms.topic: overview
ms.date: 09/25/2018
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: 0ffad78afd6acdfbcea8df970358c639706f14b8
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55814241"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Introdução aos registros de contêiner do Docker privado no Azure

O Registro de Contêiner do Azure é um serviço gerenciado do [registro Docker](https://docs.docker.com/registry/) com base no Docker Registry 2.0 de fonte aberta. Criar e manter registros de contêiner do Azure para armazenar e gerenciar suas imagens privadas de [contêiner Docker](https://www.docker.com/what-docker).

Use registros de contêiner no Azure com o desenvolvimento de contêiner e os pipelines de implantação existentes. Us o Build do Registro de Contêiner do Azure (Build do ACR) para compilar imagens de contêiner no Azure. Compile sob demanda ou automatizar completamente os builds com a confirmação do código-fonte e os gatilhos de build de atualização de imagem de base.

Para obter informações sobre o Docker e sobre contêineres, consulte a [Visão geral do Docker](https://docs.docker.com/engine/docker-overview/).

## <a name="use-cases"></a>Casos de uso

Obtenha imagens de um registro de contêiner do Azure para vários destinos de implantação:

* **Sistemas de orquestração escalonáveis** que gerenciam aplicativos em contêineres em clusters de hosts, incluindo [Kubernetes](http://kubernetes.io/docs/), [CD/SO](https://docs.mesosphere.com/) e [Docker Swarm](https://docs.docker.com/swarm/).
* **Serviços do Azure** que dão suporte à criação e execução de aplicativos em grande escala, incluindo [Serviço de Kubernetes do Azure (AKS)](../aks/index.yml), [Serviço de Aplicativo](../app-service/index.yml), [Lote](../batch/index.yml), [Service Fabric](/azure/service-fabric/) e outros.

Os desenvolvedores também podem enviar um registro de contêiner como parte de um fluxo de trabalho de desenvolvimento do contêiner. Por exemplo, direcione uma ferramenta de implantação e integração contínua de destino como [Azure DevOps Services](https://docs.microsoft.com/azure/devops/) ou [Jenkins](https://jenkins.io/) a um registro de contêiner.

Configure Tarefas do ACR para recompilar automaticamente as imagens de aplicativo quando suas imagens base forem atualizadas. Use Tarefas do ACR para automatizar os builds de imagem quando sua equipe confirmar o código para um repositório Git.

## <a name="key-concepts"></a>Principais conceitos

* **Registro** - crie um ou mais registros de contêiner em sua assinatura do Azure. Os registros estão disponíveis em três SKUs: [Básico, Standard e Premium](container-registry-skus.md), cada um dá suporte à integração de webhook, autenticação de Registro no Azure Active Directory e funcionalidade de exclusão. Aproveite o armazenamento local e de rede fechada de suas imagens de contêiner criando um registro no mesmo local do Azure de suas implantações. Use o recurso [replicação geográfica](container-registry-geo-replication.md) de registros Premium para cenários avançados de replicação e distribuição de imagens de contêiner. Um nome de registro totalmente qualificado tem a forma `myregistry.azurecr.io`.

  Você [controla o acesso](container-registry-authentication.md) a um registro de contêiner usando uma identidade do Azure, uma [entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md) com suporte do Azure Active Directory ou uma conta do administrador fornecida. Faça logon no Registro usando a Interface de Linha de Comando do Azure ou o comando `docker login` padrão.

* **Repositório** - um registro contém um ou mais repositórios, que são grupos de armazenamento de imagens de contêiner. O Registro de Contêiner do Azure dá suporte a namespaces do repositório de vários níveis. Com namespaces de vários níveis, você pode agrupar coleções de imagens relacionadas a um aplicativo específico ou uma coleção de aplicativos para equipes de desenvolvimento ou operacionais específicas. Por exemplo: 

  * `myregistry.azurecr.io/aspnetcore:1.0.1` representa uma imagem de toda a empresa
  * `myregistry.azurecr.io/warrantydept/dotnet-build` representa uma imagem usada para criar aplicativos .NET, compartilhados no departamento de garantia
  * `myregistry.azurecr.io/warrantydept/customersubmissions/web` representa uma imagem da Web, agrupada no aplicativo de envios do cliente, pertencente ao departamento de garantia

* **Imagem** - armazenada em um repositório, cada imagem é um instantâneo somente leitura de um contêiner compatível com o Docker. Registros de contêiner do Azure podem incluir imagens do Windows e Linux. Você controla os nomes de imagem para todas as implantações de contêiner. Use [comandos do Docker](https://docs.docker.com/engine/reference/commandline/) padrão para envio de imagens em um repositório ou extraia uma imagem de um repositório. Além de imagens de contêiner, o Registro de Contêiner do Azure armazena [relacionadas a formatos de conteúdo](container-registry-image-formats.md) tais como [gráficos do Helm](container-registry-helm-repos.md), usado para implantar aplicativos no Kubernetes.

* **Contêiner** - um contêiner define um aplicativo de software e suas dependências encapsuladas em um sistema de arquivos completo, incluindo código, tempo de execução, ferramentas do sistema e bibliotecas. Execute os contêineres do Docker com base nas imagens do Windows ou Linux que você obtém de um registro de contêiner. Contêineres em execução em um único computador compartilham o kernel do sistema operacional. Contêineres do Docker são totalmente portáteis para todas as principais distribuições Linux, macOS e Windows.

## <a name="azure-container-registry-tasks"></a>Tarefas do Registro de Contêiner do Azure

[Tarefas do Registro de Contêiner do Azure](container-registry-tasks-overview.md) (Tarefas do ACR) é um pacote de recursos dentro do Registro de Contêiner do Azure que fornece builds de contêiner do Docker aprimorados e eficientes no Azure. Use as Tarefas do ACR para estender o loop interno de desenvolvimento para a nuvem descarregando operações `docker build` no Azure. Configurar tarefas de build para automatizar seu pipeline de patch de sistema operacional e estrutura do contêiner e compilar imagens automaticamente quando sua equipe confirma o código para controle do código-fonte.

[Tarefas de várias etapas](container-registry-tasks-overview.md#multi-step-tasks-preview), uma versão prévia do recurso Tarefas do ACR, fornece a definição de tarefas com base em etapa e execução para criar, testar e aplicar patches em imagens de contêiner na nuvem. As etapas da tarefa definem o build de imagem de contêiner individual e operações de push. Elas também podem definir a execução de um ou mais contêineres, com cada etapa usando o contêiner como seu ambiente de execução.

## <a name="next-steps"></a>Próximas etapas

* [Criar um registro de contêiner usando o portal do Azure](container-registry-get-started-portal.md)
* [Criar um registro de contêiner usando a CLI do Azure](container-registry-get-started-azure-cli.md)
* [Automatizar o sistema operacional e a aplicação de patches de estrutura com as Tarefas de ACR](container-registry-tasks-overview.md)
