---
title: CLI do Azure Service Fabric - sfctl rpm | Microsoft Docs
description: Descreve os comandos do sfctl rpm da CLI do Service Fabric.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 02/22/2018
ms.author: ryanwi
ms.openlocfilehash: 34e7693ea40df2bf12fd6e9be2ef627f30748bcd
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="sfctl-rpm"></a>sfctl rpm
Consultar e enviar comandos para o serviço de gerenciador de reparo.

## <a name="commands"></a>Comandos
|Get-Help|DESCRIÇÃO|
| --- | --- |
|    Aprovar-força| Força a aprovação de determinada tarefa de reparo.|
|    excluir       | Exclui uma tarefa de reparo concluída.|
|    list         | Obtém uma lista de tarefas de reparo, correspondendo os filtros fornecidos.|

## <a name="sfctl-rpm-delete"></a>excluir sfctl rpm
Exclui uma tarefa de reparo concluída.

Essa API dá suporte à plataforma do Service Fabric; não é destinada a ser usada diretamente a partir do seu código. 

### <a name="arguments"></a>Argumentos
|Argumento|DESCRIÇÃO|
| --- | --- |
|    --id da tarefa [obrigatório]| A ID da tarefa de reparo concluída a ser excluída.|
|    --versão           | O número da versão atual da tarefa de reparo. Se diferente de zero, a solicitação só terá êxito se esse valor corresponder à versão atual da tarefa de reparo. Se for zero, nenhuma verificação de versão será executada.|

### <a name="global-arguments"></a>Argumentos globais
|Argumento|DESCRIÇÃO|
| --- | --- |
|    --debug             | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
|    --help -h           | Mostrar esta mensagem de ajuda e sair.|
|    --output -o         | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão: json.
|    --query             | Cadeia de caracteres de consulta JMESPath. Consulte http://jmespath.org/ para saber mais e obter exemplos.|
|    --verbose           | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|


## <a name="sfctl-rpm-list"></a>lista sfctl rpm
Obtém uma lista de tarefas de reparo, correspondendo os filtros fornecidos.

Essa API dá suporte à plataforma do Service Fabric; não é destinada a ser usada diretamente a partir do seu código. 

### <a name="arguments"></a>Argumentos
|Argumento|DESCRIÇÃO|
| --- | --- |
|    --filtro do executor| O nome do executor de reparos cujas tarefas declaradas devem ser incluídas na lista.|
|    -filtro de estado   | Um bitwise OR dos valores a seguir, especificando qual estado de tarefa deve ser incluído na lista de resultados. - 1 - Criado - 2 - Declarado - 4 - Preparando - 8 - Aprovado - 16 - Executando - 32 - Restaurando - 64 - Concluído.|
|    -filtro de id de tarefa | O prefixo de ID da tarefa de reparo a ser correspondido.|

### <a name="global-arguments"></a>Argumentos globais
|Argumento|DESCRIÇÃO|
| --- | --- |
|    --debug          | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
|    --help -h        | Mostrar esta mensagem de ajuda e sair.|
|    --output -o      | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão| json.|
|    --query          | Cadeia de caracteres de consulta JMESPath. Consulte http://jmespath.org/ para saber mais e obter exemplos.|
|    --verbose        | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="next-steps"></a>Próximas etapas
- [Configurar](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).