---
title: CLI do Azure Service Fabric - sfctl sa-cluster | Microsoft Docs
description: Descreve os comandos do cluster independente sfctl da CLI do Service Fabric.
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
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: d7f33bf0657ca2a6888387b7651706f9de537bb4
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494349"
---
# <a name="sfctl-sa-cluster"></a>sfctl sa-cluster
Gerencie clusters autônomos do Service Fabric.

## <a name="commands"></a>Comandos

|Comando|DESCRIÇÃO|
| --- | --- |
| config | Obtenha a configuração de cluster autônomo do Service Fabric. |
| config-upgrade | Comece a atualizar a configuração de um cluster autônomo do Service Fabric. |
| upgrade-status | Obtenha o status de atualização de configuração do cluster de um cluster autônomo do Service Fabric. |

## <a name="sfctl-sa-cluster-config"></a>sfctl sa-cluster config
Obtenha a configuração de cluster autônomo do Service Fabric.

A configuração do cluster contém propriedades do cluster que incluem diferentes tipos de nó no cluster, as configurações de segurança, falhas e topologias de domínio de atualização etc.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --configuration-api-version [Obrigatório] | A versão da API da configuração do json do cluster autônomo. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

## <a name="sfctl-sa-cluster-config-upgrade"></a>sfctl sa-cluster config-upgrade
Comece a atualizar a configuração de um cluster autônomo do Service Fabric.

Valide os parâmetros de upgrade da configuração fornecidos e comece a atualizar a configuração do cluster do se os parâmetros forem válidos.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --cluster-config            [Obrigatório] | A configuração do cluster que será aplicada ao cluster. |
| --application-health-policies | O JSON codificava o dicionário de pares de nome de tipo de aplicativo e percentual máximo não íntegro antes de gerar o erro. |
| --delta-unhealthy-nodes | O percentual máximo permitido de degradação de integridade delta permitida durante o upgrade. Os valores permitidos são inteiros, de zero a 100. |
| --nova tentativa de verificação da integridade | O período entre as tentativas de uma verificação de integridade, se o aplicativo ou o cluster não estiver íntegro.  Padrão\: PT0H0M0S. |
| --estabilidade de verificação de integridade | O período durante o qual o aplicativo ou o cluster deve permanecer íntegro.  Padrão\: PT0H0M0S. |
| --espera de verificação de integridade | O período de espera após a conclusão de um domínio de atualização, antes de iniciar o processo de verificações de integridade.  Padrão\: PT0H0M0S. |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |
| --unhealthy-applications | O percentual máximo permitido de aplicativos não íntegros durante o upgrade. Os valores permitidos são inteiros, de zero a 100. |
| --unhealthy-nodes | O percentual máximo permitido de nós não íntegros durante o upgrade. Os valores permitidos são inteiros, de zero a 100. |
| --upgrade-domain-delta-unhealthy-nodes | O percentual máximo permitido de degradação de integridade delta do domínio de atualização durante o upgrade. Os valores permitidos são inteiros, de zero a 100. |
| -tempo limite ao domínio de upgrade | O tempo limite para o domínio de atualização.  Padrão\: PT0H0M0S. |
| -tempo limite de upgrade | O tempo de limite do upgrade.  Padrão\: PT0H0M0S. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |

### <a name="examples"></a>Exemplos

Inicie uma atualização de configuração do cluster sfctl sa-cluster config-upgrade --cluster-config <YOUR CLUSTER CONFIG> --application-health-policies "{"fabric:/System":{"ConsiderWarningAsError":true}}"

## <a name="sfctl-sa-cluster-upgrade-status"></a>sfctl sa-cluster upgrade-status
Obtenha o status de atualização de configuração do cluster de um cluster autônomo do Service Fabric.

Obtenha detalhes do status de atualização de configuração do cluster de um cluster autônomo do Service Fabric.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --timeout -t | Tempo limite do servidor em segundos.  Padrão\: 60. |

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração. |
| --help -h | Mostrar esta mensagem de ajuda e sair. |
| --output -o | O formato da saída.  Valores permitidos\: json, jsonc, tabela, tsv.  Padrão\: json. |
| --query | Cadeia de caracteres de consulta JMESPath. Veja http\://jmespath.org/ para saber mais e obter exemplos. |
| --verbose | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos. |


## <a name="next-steps"></a>Próximas etapas
- [Configurar](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).