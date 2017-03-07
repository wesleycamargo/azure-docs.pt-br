---
title: "Exemplos de CLI do Azure - Serviço de aplicativo | Microsoft Docs"
description: "Exemplos de CLI do Azure - Serviço de aplicativo"
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.service: app-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 02/21/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: f2c95ec5cd32632bb5b9683f5273721f465aebdf
ms.openlocfilehash: 2d7d2154a2910c0bd7ff24c404c4eb05203b1453
ms.lasthandoff: 03/01/2017


---
# <a name="azure-app-service-cli-samples"></a>Exemplos de CLI do serviço de aplicativo do Azure

A tabela a seguir inclui links para bash scripts criados usando a CLI do Azure.

| | |
|-|-|
|**Como criar o aplicativo**||
| [Como criar um aplicativo Web com a implantação do GitHub](./scripts/app-service-cli-deploy-github.md)| Cria um aplicativo Web do Azure que recebe o código do GitHub. |
| [Como criar um aplicativo Web com a implantação contínua do GitHub](./scripts/app-service-cli-continuous-deployment-github.md)| Cria um aplicativo Web do Azure que implanta continuamente o código do GitHub. |
| [Como criar um aplicativo Web com a implantação contínua do Visual Studio Team Services](./scripts/app-service-cli-continuous-deployment-vsts.md)| Cria um aplicativo Web do Azure que implanta continuamente o código do Visual Studio Team Services. |
| [Como criar um aplicativo Web e implantar o código de um repositório Git local](./scripts/app-service-cli-deploy-local-git.md) | Cria um aplicativo Web do Azure e configura o push de código de um repositório Git local. |
| [Como criar um aplicativo Web e implantar o código em um ambiente de preparo](./scripts/app-service-cli-deploy-staging-environment.md) | Cria um aplicativo Web do Azure com um slot de implantação para alterações de código de preparo. |
| [Como criar um aplicativo Web do ASP.NET Core em um contêiner do Docker Hub](./scripts/app-service-cli-linux-docker-aspnetcore.md)| Cria um aplicativo Web do Azure no Linux e carrega uma imagem do Docker do Docker Hub. |
| [Como criar um aplicativo Web do ASP.NET Core em um contêiner de Docker do registro de contêiner do Azure](./scripts/app-service-cli-linux-acr-aspnetcore.md)| Cria um aplicativo Web do Azure no Linux e carrega uma imagem de Docker do registro de contêiner do Azure. |
|**Como configurar o aplicativo**||
| [Como mapear um domínio personalizado para um aplicativo Web](./scripts/app-service-cli-configure-custom-domain.md)| Cria um aplicativo Web do Azure e mapeia um nome de domínio personalizado para ele. |
|**Como escalar um aplicativo**||
| [Como escalar manualmente um aplicativo Web](./scripts/app-service-cli-scale-manual.md) | Cria um aplicativo Web do Azure e pode ser dimensionado em 2 instâncias. |
| [Como escalar um aplicativo Web em todo o mundo com uma arquitetura de alta disponibilidade](./scripts/app-service-cli-scale-high-availability.md) | Cria dois aplicativos Web do Azure em duas regiões geográficas diferentes e os disponibiliza por meio de um único ponto de extremidade usando o Gerenciador de tráfego do Azure. |
|**Como conectar o aplicativo aos recursos**||
| [Como conectar um aplicativo Web a um Banco de Dados SQL](./scripts/app-service-cli-app-service-sql.md)| Cria um aplicativo Web do Azure e um Banco de Dados SQL e, em seguida, adiciona a cadeia de conexão do banco de dados para as configurações do aplicativo. |
| [Como conectar um aplicativo Web a uma conta de armazenamento](./scripts/app-service-cli-app-service-storage.md)| Cria um aplicativo Web do Azure e uma conta de armazenamento e, em seguida, adiciona a cadeia de conexão de armazenamento às configurações do aplicativo. |
|**Como monitorar o aplicativo**||
| [Como monitorar um aplicativo Web com logs do servidor Web](./scripts/app-service-cli-monitor.md) | Cria um aplicativo Web do Azure, habilita o registro em log para ele e baixa os logs em sua máquina local. |
| | |

