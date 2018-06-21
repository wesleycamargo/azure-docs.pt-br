---
title: CLI do Azure Service Fabric - composição sfctl | Microsoft Docs
description: Descreve os comandos de composição do sfctl da CLI do Service Fabric.
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
ms.openlocfilehash: cc3d3e35ce3dd457d981dfe9420be765cf9fc45a
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763401"
---
# <a name="sfctl-compose"></a>composição sfctl
Criar, excluir e gerenciar aplicativos do Docker Compose.

## <a name="commands"></a>Comandos

|Comando|DESCRIÇÃO|
| --- | --- |
| create | Cria uma implantação de composição do Service Fabric. |
| list | Obtém a lista de implantação de composição criada no cluster do Service Fabric. |
| remove | Exclui uma implantação de composição existente do Service Fabric do cluster. |
| status | Obtém informações sobre um aplicativo de implantação do Service Fabric. |
| atualizar | Começa a atualização de uma implantação de composição no cluster do Service Fabric. |
| upgrade-status | Obtém detalhes para a atualização mais recente realizada na implantação da composição do Service Fabric. |

## <a name="sfctl-compose-create"></a>criar composição sfctl
Cria uma implantação de composição do Service Fabric.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --nome de implantação [obrigatório] | O nome da implantação. |
| -caminho de arquivo [requerido] | Caminho para o arquivo de destino do Docker Compose. |
| -senha criptografada | Em vez de solicitar uma senha de registro do contêiner, use uma frase secreta já criptografada. |
| --has-pass | Solicitará uma senha para o registro de contêiner. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |
| --user | Nome de usuário para conectar o registro de contêiner. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-compose-list"></a>lista de composição sfctl
Obtém a lista de implantação de composição criada no cluster do Service Fabric.

Obtém o status sobre as implantações de redação que foram criadas ou no processo de criação do cluster do Service Fabric. A resposta inclui o nome, o status e outros detalhes sobre a implantação de composição. Se a lista de implantações não couber em uma página, uma página de resultados é retornada, bem como um token de continuação que pode ser usado para a próxima página.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --continuation-token | O parâmetro do token de continuação é usado para obter o próximo conjunto de resultados. Um token de continuação com um valor não vazio será incluso na resposta da API quando os resultados do sistema não couberem em uma única resposta. Quando esse valor for passado para a próxima chamada de API, a API retornará o próximo conjunto de resultados. Se não houver mais resultados, o token de continuação não conterá um valor. O valor desse parâmetro não deve ser codificado em URL. |
| --max-results | O número máximo de resultados a serem retornados como parte das consultas paginadas. Esse parâmetro define o limite superior no número de resultados retornados. Os resultados retornados podem ser inferiores aos resultados máximos especificados se não couberem na mensagem, de acordo com as restrições de tamanho máximo de mensagem definidas na configuração. Se esse parâmetro for zero, ou não for especificado, a consulta paginada incluirá o máximo de resultados possível na mensagem de retorno. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-compose-remove"></a>remoção de composição sfctl
Exclui uma implantação de composição existente do Service Fabric do cluster.

Exclui uma implantação de composição existente do Service Fabric.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --nome de implantação [obrigatório] | A identidade da implantação. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-compose-status"></a>status de composição sfctl
Obtém informações sobre um aplicativo de implantação do Service Fabric.

Retorna o status da implantação de composição que foi criada ou em processo de criação no cluster do Service Fabric e cujo nome corresponde ao especificado como o parâmetro. A resposta inclui o nome, status e outros detalhes sobre a implantação.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --nome de implantação [obrigatório] | A identidade da implantação. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-compose-upgrade"></a>upgrade de composição sfctl
Começa a atualização de uma implantação de composição no cluster do Service Fabric.

Valida os parâmetros de atualização fornecidos e inicia a atualização da implantação, se os parâmetros são válidos.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --deployment-name [Obrigatório] | O nome da implantação. |
| --caminho do arquivo [obrigatório] | Caminho para o arquivo de destino do Docker Compose. |
| --falha-svc-tipo-integridade-mapa | Dicionário codificado JSON que descreve a política de integridade usada para avaliar a integridade dos serviços. |
| -senha criptografada | Em vez de solicitar uma senha de registro do contêiner, use uma frase secreta já criptografada. |
| --ação de falha | Os valores possíveis incluem\: 'Inválido', 'Reversão', 'Manual'. |
| -força-reinicialização | Força reinicialização. |
| --has-pass | Solicitará uma senha para o registro de contêiner. |
| --nova tentativa de verificação da integridade | Tempo limite de repetição de verificação de integridade medido em milissegundos. |
| --estabilidade de verificação de integridade | Verificação da integridade de duração estável, medida em milissegundos. |
| --espera de verificação de integridade | Duração da espera da verificação da integridade, medida em milissegundos. |
| --verificação do conjunto de réplicas | Tempo limite de verificação do conjunto de réplicas de atualização medido em segundos. |
| --mapa de integridade do tipo de svc | Lista codificada JSON de objetos que descrevem as políticas de integridade usadas para avaliar a integridade de diferentes tipos de serviços. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |
| --aplicativo prejudicial | O percentual máximo permitido de aplicativos não íntegros antes de relatar um erro. <br><br> Por exemplo, para permitir que 10% dos aplicativos sejam problemáticos, esse valor seria 10. A porcentagem representa o máximo tolerado de aplicativos que podem estar não íntegros antes de o cluster ser considerado com erro. Se a porcentagem for respeitada, mas houver pelo menos um aplicativo não íntegro, a integridade será avaliada como ‘Aviso’. Isso é calculado dividindo o número de aplicativos problemáticos no número total de instâncias de aplicativos no cluster. |
| -tempo limite ao domínio de upgrade | Tempo limite do domínio de atualização medido em milissegundos. |
| --tipo de upgrade | Padrão\: sem interrupção. |
| --modo de upgrade | Os valores possíveis incluem\: 'Invalid', 'UnmonitoredAuto', 'UnmonitoredManual', 'Monitored'.  Padrão\: UnmonitoredAuto. |
| -tempo limite de upgrade | Tempo limite de atualização medido em milissegundos. |
| --user | Nome de usuário para conectar o registro de contêiner. |
| --aviso-como-erro | Os avisos são tratados com a mesma gravidade dos erros. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Consulte http\://jmespath.org/ para obter mais informações e exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-compose-upgrade-status"></a>sfctl compor o status de atualização
Obtém detalhes para a atualização mais recente realizada na implantação da composição do Service Fabric.

Retorna as informações sobre o estado da atualização de implantação de redação juntamente com detalhes para auxiliar na depuração problemas de integridade do aplicativo.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --nome de implantação [obrigatório] | A identidade da implantação. |
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