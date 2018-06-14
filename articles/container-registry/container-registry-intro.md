---
title: Registros de contêiner do Docker privado no Azure
description: Introdução ao serviço de Registro de Contêiner do Azure, fornecendo registros Docker privados gerenciados baseados em nuvem.
services: container-registry
author: stevelas
manager: jeconnoc
ms.service: container-registry
ms.topic: overview
ms.date: 05/08/2018
ms.author: stevelas
ms.custom: mvc
ms.openlocfilehash: 883da7d1487922f5cd986a67d7a7bded78ea3759
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/11/2018
ms.locfileid: "34058073"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Introdução aos registros de contêiner do Docker privado no Azure

O Registro de Contêiner do Azure é um serviço gerenciado do [registro Docker](https://docs.docker.com/registry/) com base no Docker Registry 2.0 de fonte aberta. Criar e manter registros de contêiner do Azure para armazenar e gerenciar suas imagens privadas de [contêiner Docker](https://www.docker.com/what-docker).

Use registros de contêiner no Azure com o desenvolvimento de contêiner e os pipelines de implantação existentes. Us o Build do Registro de Contêiner do Azure (Build do ACR) para compilar imagens de contêiner no Azure. Compile sob demanda ou automatizar completamente os builds com a confirmação do código-fonte e os gatilhos de build de atualização de imagem de base.

Para obter informações sobre o Docker e sobre contêineres, consulte a [Visão geral do Docker](https://docs.docker.com/engine/docker-overview/).

## <a name="use-cases"></a>Casos de uso

Obtenha imagens de um registro de contêiner do Azure para vários destinos de implantação:

* **Sistemas de orquestração escalonáveis** que gerenciam aplicativos em contêineres em clusters de hosts, incluindo [CD/SO](https://docs.mesosphere.com/), [Docker Swarm](https://docs.docker.com/swarm/) e [Kubernetes](http://kubernetes.io/docs/).
* **Serviços do Azure** que dão suporte à criação e execução de aplicativos em grande escala, incluindo [Serviço de Kubernetes do Azure (AKS)](../aks/index.yml), [Serviço de Aplicativo](/app-service/index.md), [Lote](../batch/index.yml), [Service Fabric](/azure/service-fabric/) e outros.

Os desenvolvedores também podem enviar um registro de contêiner como parte de um fluxo de trabalho de desenvolvimento do contêiner. Por exemplo, direcione uma ferramenta de implantação e integração contínua de destino como [Visual Studio Team Services](https://www.visualstudio.com/docs/overview) ou [Jenkins](https://jenkins.io/) a um Registro de contêiner.

Configure as tarefas de build do [Build do ACR](#azure-container-registry-build) para recompilar automaticamente as imagens de aplicativo quando suas imagens de base são atualizadas. Use o Build do ACR para automatizar os builds de imagem quando sua equipe confirmar o código para um repositório Git. *O Build do ACR está atualmente em visualização.*

## <a name="key-concepts"></a>Principais conceitos

* **Registro** - crie um ou mais registros de contêiner em sua assinatura do Azure. Os registros estão disponíveis em três SKUs: [Básico, Standard e Premium](container-registry-skus.md), cada um oferece suporte à integração de webhook, autenticação de registro com o Azure Active Directory e a funcionalidade de exclusão. Aproveite o armazenamento local e de rede fechada de suas imagens de contêiner criando um registro no mesmo local do Azure de suas implantações. Use o recurso [replicação geográfica](container-registry-geo-replication.md) de registros Premium para cenários avançados de replicação e distribuição de imagens de contêiner. Um nome de registro totalmente qualificado tem a forma `myregistry.azurecr.io`.

  Você [controla o acesso](container-registry-authentication.md) a um registro de contêiner usando uma [entidade de serviço](../active-directory/active-directory-application-objects.md) com suporte do Azure Active Directory ou uma conta de administrador fornecida. Execute o comando `docker login` padrão para se autenticar em um registro.

* **Repositório** - um registro contém um ou mais repositórios, que são grupos de imagens de contêiner. O Registro de Contêiner do Azure dá suporte a namespaces do repositório de vários níveis. Com namespaces de vários níveis, você pode agrupar coleções de imagens relacionadas a um aplicativo específico ou uma coleção de aplicativos para equipes de desenvolvimento ou operacionais específicas. Por exemplo: 

  * `myregistry.azurecr.io/aspnetcore:1.0.1` representa uma imagem de toda a empresa
  * `myregistry.azurecr.io/warrantydept/dotnet-build` representa uma imagem usada para criar aplicativos .NET, compartilhados no departamento de garantia
  * `myregistry.azurecr.io/warrantydept/customersubmissions/web` representa uma imagem da Web, agrupada no aplicativo de envios do cliente, pertencente ao departamento de garantia

* **Imagem** - armazenada em um repositório, cada imagem é um instantâneo somente leitura de um contêiner do Docker. Registros de contêiner do Azure podem incluir imagens do Windows e Linux. Você controla os nomes de imagem para todas as implantações de contêiner. Use [comandos do Docker](https://docs.docker.com/engine/reference/commandline/) padrão para envio de imagens em um repositório ou extraia uma imagem de um repositório.

* **Contêiner** - um contêiner define um aplicativo de software e suas dependências encapsuladas em um sistema de arquivos completo, incluindo código, tempo de execução, ferramentas do sistema e bibliotecas. Execute os contêineres do Docker com base nas imagens do Windows ou Linux que você obtém de um registro de contêiner. Contêineres em execução em um único computador compartilham o kernel do sistema operacional. Contêineres do Docker são totalmente portáteis para todas as principais distribuições Linux, macOS e Windows.

## <a name="azure-container-registry-build-preview"></a>Build do Registro de Contêiner do Azure (Visualização)

[O Build do Registro de Contêiner do Azure (Build do ACR)](container-registry-build-overview.md) é um pacote de recursos dentro do Registro de Contêiner do Azure que fornece builds de contêiner do Docker aprimorados e eficientes no Azure. Use o Build do ACR para estender o loop interno de desenvolvimento para a nuvem descarregando `docker build` operações no Azure. Configurar tarefas de build para automatizar seu pipeline de patch de sistema operacional e estrutura do contêiner e compilar imagens automaticamente quando sua equipe confirma o código para controle do código-fonte.

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

## <a name="next-steps"></a>Próximas etapas

* [Criar um registro de contêiner usando o portal do Azure](container-registry-get-started-portal.md)
* [Criar um registro de contêiner usando a CLI do Azure](container-registry-get-started-azure-cli.md)
* [Automatizar o sistema operacional e a aplicação de patch de estrutura com o Build do ACR](container-registry-build-overview.md) (visualização)
