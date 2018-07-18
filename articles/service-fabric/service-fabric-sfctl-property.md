---
title: Propriedade do Azure Service Fabric CLI - sfctl | Microsoft Docs
description: Descreve os comandos de propriedade sfctl CLI de malha do serviço.
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
ms.openlocfilehash: acade3d828c785af9468baa30086d3b79542f9b7
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763916"
---
# <a name="sfctl-property"></a>sfctl property
Propriedades de armazenamento e a consulta em nomes do Service Fabric.

## <a name="commands"></a>Comandos

|Comando|DESCRIÇÃO|
| --- | --- |
| excluir | Exclui a propriedade especificada do Service Fabric. |
| get | Obtém a propriedade especificada do Service Fabric. |
| list | Obtém informações sobre todas as propriedades de malha do serviço em um determinado nome. |
| put | Cria ou atualiza uma propriedade de malha do serviço. |

## <a name="sfctl-property-delete"></a>exclusão de propriedade sfctl
Exclui a propriedade especificada do Service Fabric.

Exclui a propriedade do Service Fabric especificada em um determinado nome. Uma propriedade deve ser criada antes que possa ser excluído.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| – id de nome [requerido] | Nome da malha do serviço, sem o ' malha\:' esquema de URI. |
| -nome de propriedade [requerido] | Especifica o nome da propriedade a ser obtida. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-property-get"></a>get de propriedade sfctl
Obtém a propriedade especificada do Service Fabric.

Obtém a propriedade do Service Fabric especificada em um determinado nome. Isso sempre retornará valor e metadados.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| – id de nome [requerido] | Nome da malha do serviço, sem o ' malha\:' esquema de URI. |
| -nome de propriedade [requerido] | Especifica o nome da propriedade a ser obtida. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-property-list"></a>lista de propriedades sfctl
Obtém informações sobre todas as propriedades de malha do serviço em um determinado nome.

Um nome de malha do serviço pode ter um ou mais propriedades nomeadas que armazenam informações personalizadas. Esta operação obtém as informações sobre essas propriedades em uma lista de página. As informações incluem o nome, valor e metadados sobre cada uma das propriedades.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| – id de nome [requerido] | Nome da malha do serviço, sem o ' malha\:' esquema de URI. |
| --continuation-token | O parâmetro do token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio será incluso na resposta da API quando os resultados do sistema não couberem em uma única resposta. Quando esse valor for passado para a próxima chamada de API, a API retornará o próximo conjunto de resultados. Se não houver mais resultados, o token de continuação não conterá um valor. O valor desse parâmetro não deve ser codificado em URL. |
| – incluir valores | Permite especificar se deseja incluir os valores das propriedades retornadas. Verdadeiro se os valores devem ser retornados com os metadados; Falso para retornar somente metadados de propriedade. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-property-put"></a>sfctl propriedade put
Cria ou atualiza uma propriedade de malha do serviço.

Cria ou atualiza a propriedade de malha do serviço especificada em um determinado nome.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| – id de nome [requerido] | Nome da malha do serviço, sem o ' malha\:' esquema de URI. |
| -nome de propriedade [requerido] | O nome da propriedade do Service Fabric. |
| – valor [requerido] | Descreve um valor de propriedade do Service Fabric. Isso é uma cadeia de caracteres JSON. <br><br> A cadeia de caracteres json tem dois campos, o 'tipo' dos dados e o 'valor' dos dados. O valor de 'Kind' deve ser o primeiro item deve aparecer na cadeia de caracteres JSON e pode ser valores 'Binário', 'Int64', 'Double', 'String' ou 'Guid'. O valor deve ser serializar-capaz de tipos de dados. Valores 'Tipo' e 'Data' devem ser fornecidos como cadeias de caracteres. |
| --custom-id-type | Um tipo personalizado da propriedade ID. Usando essa propriedade, o usuário é capaz de marcar o tipo do valor da propriedade. |
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