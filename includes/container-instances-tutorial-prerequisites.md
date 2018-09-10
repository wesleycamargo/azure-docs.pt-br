---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: container-instances
author: mmacy
ms.service: container-instances
ms.topic: include
ms.date: 03/20/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 95a8cf4eca7969631d069a31c9d08ba52c8b02b4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
ms.locfileid: "30193467"
---
Você deve atender aos seguintes requisitos para concluir este tutorial:

**CLI do Azure**: você deve ter a CLI do Azure versão 2.0.29 ou posterior instalada no computador local. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure][azure-cli-install].

**Docker**: este tutorial pressupõe uma compreensão básica dos conceitos fundamentais do Docker como contêineres, imagens de contêiner e comandos básicos do `docker`. Para instruções sobre conceitos básicos do Docker e de contêiner, consulte a [visão geral do Docker][docker-get-started].

**Mecanismo do Docker**: para concluir este tutorial, você precisa do Mecanismo do Docker instalado localmente. O Docker fornece pacotes que configuram o ambiente do Docker no [macOS][docker-mac], [Windows][docker-windows] e [Linux][docker-linux].

> [!IMPORTANT]
> Como o Azure Cloud Shell não inclui o daemon do Docker, você *deve* instalar a CLI do Azure e o Mecanismo do Docker em seu *computador local* para concluir este tutorial. Você não pode usar o Azure Cloud Shell para este tutorial.

<!-- LINKS - External -->
[docker-get-started]: https://docs.docker.com/engine/docker-overview/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli