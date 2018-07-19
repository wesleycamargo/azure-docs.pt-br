---
title: Exemplo de script de CLI do Service Fabric – Listar aplicativos em um cluster
description: Exemplo de script de CLI do Service Fabric – Liste os aplicativos provisionados em um cluster do Service Fabric.
services: service-fabric
documentationcenter: ''
author: TylerMSFT
manager: timlt
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 04/13/2018
ms.author: twhitney
ms.custom: ''
ms.openlocfilehash: bea88460ecaa093a0719d627e3608fd1d530727b
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39070191"
---
# <a name="list-applications-running-in-a-service-fabric-cluster"></a>Listar aplicativos executando em um cluster do Service Fabric

Esse script de exemplo se conecta a um cluster do Service Fabric e, em seguida, lista todos os aplicativos provisionados.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-sh[main](../../../cli_scripts/service-fabric/list-application/list-application.sh "List provisioned applications from a cluster")]

## <a name="next-steps"></a>Próximas etapas

Para saber mais, confira a [Documentação da CLI do Service Fabric](../service-fabric-cli.md).

Mais exemplos da CLI do Service Fabric para o Azure Service Fabric podem ser encontrados em [Exemplos da CLI do Service Fabric](../samples-cli.md).
