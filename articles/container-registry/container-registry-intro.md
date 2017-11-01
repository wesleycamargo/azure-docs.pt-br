---
title: "Registros de contêiner do Docker privado no Azure"
description: "Introdução ao serviço de Registro de Contêiner do Azure, fornecendo registros Docker privados gerenciados baseados em nuvem."
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: mmacy
tags: 
keywords: 
ms.assetid: ee2b652b-fb7c-455b-8275-b8d4d08ffeb3
ms.service: container-registry
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: stevelas
ms.openlocfilehash: d54caa45078221cdbe091649cb3fe3b65eaa47e8
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2017
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Introdução aos registros de contêiner do Docker privado no Azure

O Registro de Contêiner do Azure é um serviço gerenciado do [registro Docker](https://docs.docker.com/registry/) com base no Docker Registry 2.0 de fonte aberta. Criar e manter registros de contêiner do Azure para armazenar e gerenciar suas imagens privadas de [contêiner Docker](https://www.docker.com/what-docker). Use registros de contêiner no Azure com o desenvolvimento de contêiner e os pipelines de implantação existentes e tire proveito da experiência da comunidade do Docker.

Para obter informações sobre o Docker e sobre contêineres, confira [Guia de usuário do Docker](https://docs.docker.com/engine/userguide/).

## <a name="use-cases"></a>Casos de uso
Obtenha imagens de um registro de contêiner do Azure para vários destinos de implantação:

* **Sistemas de orquestração escalonáveis** que gerenciam aplicativos em contêineres em clusters de hosts, incluindo [CD/SO](https://docs.mesosphere.com/), [Docker Swarm](https://docs.docker.com/swarm/) e [Kubernetes](http://kubernetes.io/docs/).
* **Serviços do Azure** que dão suporte à criação e execução de aplicativos em grande escala, incluindo [Serviço de Contêiner](../container-service/index.yml), [Serviço de Aplicativo](/app-service/index.md), [Lote](../batch/index.md), [Service Fabric](/azure/service-fabric/) e outros.

Os desenvolvedores também podem enviar um registro de contêiner como parte de um fluxo de trabalho de desenvolvimento do contêiner. Por exemplo, direcione uma ferramenta de implantação e integração contínua de destino como [Visual Studio Team Services](https://www.visualstudio.com/docs/overview) ou [Jenkins](https://jenkins.io/) a um Registro de contêiner.

## <a name="key-concepts"></a>Principais conceitos
* **Registro** - crie um ou mais registros de contêiner em sua assinatura do Azure. Os registros estão disponíveis em três SKUs: [Basic, Standard e Premium](container-registry-skus.md), cada um oferece suporte à integração de webhook, autenticação de repositório com o Active Directory do Azure e a funcionalidade de exclusão. Aproveite o armazenamento local e de rede fechada de suas imagens de contêiner criando um registro no mesmo local do Azure de suas implantações. Use o recurso [replicação geográfica](container-registry-geo-replication.md) de registros Premium para cenários avançados de replicação e distribuição de imagens de contêiner. Um nome de registro totalmente qualificado tem a forma `myregistry.azurecr.io`.

  Você [controla o acesso](container-registry-authentication.md) a um registro de contêiner usando uma [entidade de serviço](../active-directory/active-directory-application-objects.md) com suporte do Azure Active Directory ou uma conta de administrador fornecida. Execute o comando `docker login` padrão para se autenticar em um registro.

* **Repositório** - um registro contém um ou mais repositórios, que são grupos de imagens de contêiner. O Registro de Contêiner do Azure dá suporte a namespaces do repositório de vários níveis. Com namespaces de vários níveis, você pode agrupar coleções de imagens relacionadas a um aplicativo específico ou uma coleção de aplicativos para equipes de desenvolvimento ou operacionais específicas. Por exemplo:

  * `myregistry.azurecr.io/aspnetcore:1.0.1` representa uma imagem de toda a empresa
  * `myregistry.azurecr.io/warrantydept/dotnet-build` representa uma imagem usada para criar aplicativos .NET, compartilhados no departamento de garantia
  * `myregistry.azurecr.io/warrantydept/customersubmissions/web` representa uma imagem da Web, agrupada no aplicativo de envios do cliente, pertencente ao departamento de garantia

* **Imagem** - armazenada em um repositório, cada imagem é um instantâneo somente leitura de um contêiner do Docker. Registros de contêiner do Azure podem incluir imagens do Windows e Linux. Você controla os nomes de imagem para todas as implantações de contêiner. Use [comandos do Docker](https://docs.docker.com/engine/reference/commandline/) padrão para envio de imagens em um repositório ou extraia uma imagem de um repositório.

* **Contêiner** - um contêiner define um aplicativo de software e suas dependências encapsuladas em um sistema de arquivos completo, incluindo código, tempo de execução, ferramentas do sistema e bibliotecas. Execute os contêineres do Docker com base nas imagens do Windows ou Linux que você obtém de um registro de contêiner. Contêineres em execução em um único computador compartilham o kernel do sistema operacional. Contêineres do Docker são totalmente portáteis para todas as principais distribuições Linux, macOS e Windows.

## <a name="next-steps"></a>Próximas etapas
* [Criar um registro de contêiner usando o portal do Azure](container-registry-get-started-portal.md)
* [Criar um registro de contêiner usando a CLI do Azure](container-registry-get-started-azure-cli.md)
* [Enviar por push sua primeira imagem usando a CLI do Docker](container-registry-get-started-docker-cli.md)
