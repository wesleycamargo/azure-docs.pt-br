---
title: Exemplo de script de CLI do Service Fabric – Listar aplicativos em um cluster
description: Exemplo de script de CLI do Service Fabric – Liste os aplicativos provisionados em um cluster do Service Fabric.
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: timlt
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 04/13/2018
ms.author: aljo
ms.custom: ''
ms.openlocfilehash: 60cb5aaf93521a3e2f7c922949d112ad96a00180
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805630"
---
# <a name="list-applications-running-in-a-service-fabric-cluster"></a>Listar aplicativos executando em um cluster do Service Fabric

Esse script de exemplo se conecta a um cluster do Service Fabric e, em seguida, lista todos os aplicativos provisionados.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-sh[main](../../../cli_scripts/service-fabric/list-application/list-application.sh "List provisioned applications from a cluster")]

## <a name="next-steps"></a>Próximas etapas

Para saber mais, confira a [Documentação da CLI do Service Fabric](../service-fabric-cli.md).

Mais exemplos da CLI do Service Fabric para o Azure Service Fabric podem ser encontrados em [Exemplos da CLI do Service Fabric](../samples-cli.md).
