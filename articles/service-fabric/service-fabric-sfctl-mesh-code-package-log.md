---
title: CLI do Azure Service Fabric – sfctl mesh code-package-log | Microsoft Docs
description: Descreve os comandos do sfctl mesh code-package-log da CLI do Service Fabric.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 81ddcc8c5685a839afabc1e82ecf4246cb813c21
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53283332"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
Obter os logs para o contêiner do pacote de código especificado para a réplica de um determinado serviço.

## <a name="commands"></a>Comandos

|Comando|DESCRIÇÃO|
| --- | --- |
| get | Obtém os logs do contêiner. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl mesh code-package-log get
Obtém os logs do contêiner.

Obtém os logs para o contêiner do pacote de código especificado para a réplica do serviço.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --app-name --application-name [Obrigatório] | O nome do aplicativo. |
| --code-package-name           [Obrigatório] | O nome do pacote de código do serviço. |
| --replica-name                [Obrigatório] | Nome da réplica do Service Fabric. |
| --service-name                [Obrigatório] | O nome do serviço. |
| -final | Número de linhas a serem mostradas do final dos logs. O padrão é 100. 'todos' para mostrar os logs de conclusão. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, table, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |


## <a name="next-steps"></a>Próximas etapas
- [Configurar](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).