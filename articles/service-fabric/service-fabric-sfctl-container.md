---
title: Contêiner do Azure Service Fabric CLI - sfctl | Microsoft Docs
description: Descreve os comandos de contêiner sfctl CLI de malha do serviço.
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
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: cd3725ac547a1ed1fd9207dc48ba3b6227e85ef1
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763918"
---
# <a name="sfctl-container"></a>sfctl container
Execute comandos relacionados ao contêiner em um nó de cluster.

## <a name="commands"></a>Comandos

|Comando|DESCRIÇÃO|
| --- | --- |
| invocar api | Chame a API REST do contêiner. |
| logs | Recuperar os logs de contêiner. |

## <a name="sfctl-container-invoke-api"></a>contêiner de sfctl invoke-api
Chame a API REST do contêiner.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --id do aplicativo [requerido] | Identidade do aplicativo. |
| -código de pacote-id-instância-[requerido] | ID de instância do pacote de código, que pode ser recuperada por 'serviço código--lista de pacotes'. |
| -código de-nome do pacote [requerido] | nome do pacote de código. |
| – contêiner-api-uri-path [requerido] | Caminho de URI de API REST do contêiner, use '{ID}' no lugar de nome/id do contêiner. |
| --node-name                [Obrigatório] | O nome do nó. |
| --service-manifest-name    [Obrigatório] | Nome do manifesto de serviço. |
| – corpo da api de contêiner | Corpo da solicitação HTTP para API REST do contêiner. |
| – contêiner-api-content-type | Tipo de conteúdo para a API REST, o padrão é 'application/json' de contêiner. |
| --container-api-http-verb | Verbo HTTP para o contêiner de API REST, o padrão é GET. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-container-logs"></a>logs de contêiner sfctl
Recuperar os logs de contêiner.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --id do aplicativo [requerido] | Identidade do aplicativo. |
| -código de pacote-id-instância-[requerido] | ID de instância do pacote de código, que pode ser recuperada por 'serviço código--lista de pacotes'. |
| -código de-nome do pacote [requerido] | nome do pacote de código. |
| --node-name                [Obrigatório] | O nome do nó. |
| --service-manifest-name    [Obrigatório] | Nome do manifesto de serviço. |
| -final | Retorne apenas esse número de linhas do log do final dos logs. Especifica como um inteiro ou todos para todas as linhas do log de saída. Padrões para 'todos'. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="next-steps"></a>Próximas etapas
- [Configurar](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).