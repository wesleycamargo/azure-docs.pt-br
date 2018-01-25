---
title: CLI do Azure Service Fabric - cluster sfctl | Microsoft Docs
description: Descreve os comandos do cluster sfctl da CLI do Service Fabric.
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
ms.date: 12/22/2017
ms.author: ryanwi
ms.openlocfilehash: 5eeff271fea67cd859dff598cae0010cf3b8e13f
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2018
---
# <a name="sfctl-cluster"></a>cluster sfctl
Selecionar, gerenciar e operar clusters do Service Fabric.

## <a name="commands"></a>Comandos

|Get-Help|DESCRIÇÃO|
| --- | --- |
|    versões de código| Obtém uma lista de versões de códigos de malha que são provisionadas em um cluster do Service Fabric.|
|    versões de configurações | Obtém uma lista de versões de configurações de malha que são provisionadas em um cluster do Service Fabric.|
|    integridade       | Obtém a integridade de um cluster do Service Fabric.|
|    manifest     | Obter o manifesto do cluster do Service Fabric.|
|    cancelamento de operação| Cancela uma operação com falha induzida pelo usuário.|
|    operationgit | Obtém uma lista de operações com falha induzidas pelo usuário e filtradas por entrada fornecida.|
|    provision     | Provisão dos pacotes de código ou a configuração de um cluster do Service Fabric.|
|    sistema de recuperação  | Indica ao cluster do Service Fabric que ele deve tentar recuperar os serviços do sistema que estão atualmente paralisados na perda de quorum.|
|report-health   | Envia um relatório de integridade sobre o cluster do Service Fabric.|
|    selecionar       | Se conecta a um ponto de extremidade do cluster do Service Fabric.|
| unprovision     | Desconfigurar os pacotes de código ou a configuração de um cluster do Service Fabric.|
|    atualizar         | Começar a realizar os upgrades da versão do código ou configuração de um cluster do Service Fabric.|
|    upgrade-resume  | Verifica o upgrade do cluster para passar para o próximo domínio de upgrade.|
|    upgrade-rollback| Reverter o upgrade de um cluster de Service Fabric.|
|    upgrade-status  | Obtém o andamento do upgrade do cluster atual.|
|atualização do upgrade  | Atualize os parâmetros de upgrade de um upgrade de cluster do Service Fabric.|


## <a name="sfctl-cluster-health"></a>Integridade do cluster sfctl
Obtém a integridade de um cluster do Service Fabric.

Obtém a integridade de um cluster do Service Fabric. Use EventsHealthStateFilter para filtrar o conjunto de eventos de integridade relatados no cluster de acordo com o estado de integridade. Da mesma forma, use NodesHealthStateFilter e ApplicationsHealthStateFilter para filtrar o conjunto de nós e aplicativos retornados com base no seu estado de integridade agregada. 

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --filtro do estado de integridade dos aplicativos| Permite filtrar os objetos de estado de integridade do aplicativo retornados no resultado da consulta de integridade do cluster com base em seu estado de integridade. Os valores possíveis para este parâmetro incluem valor inteiro obtido de membros ou operações bit a bit em membros da enumeração HealthStateFilter. São retornados apenas aplicativos que correspondem ao filtro.  Todos os aplicativos são usados para avaliar o estado de integridade agregado. Se não for especificado, retornará todas as entradas. Os valores de estado são uma enumeração baseada em sinalizador; portanto, o valor pode ser uma combinação desses valores obtidos usando o operador 'OR' bit a bit. Por exemplo, se o valor fornecido for 6, então o estado de integridade de aplicativos com um valor OK (2) e Warning (4) de HealthState será retornado. – Default – Valor padrão. Corresponde a qualquer HealthState. O valor é zero. – None – Filtro que não corresponde a nenhum valor de HealthState. Usado para não retornar nenhum resultado em um conjunto determinado de estados. O valor é 1. – Ok – Filtro que corresponde à entrada com o valor Ok de HealthState. O valor é 2. – Warning – Filtro que corresponde à entrada com o valor Warning de HealthState. O valor é 4. – Error – Filtro que corresponde à entrada com o valor Error de HealthState. O valor é 8. – All – Filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535.|
| --events-health-state-filter   | Permite filtrar a coleção de objetos HealthEvent retornada com base no estado de integridade. Os valores possíveis para esse parâmetro incluem o valor inteiro de um dos seguintes estados de integridade. Serão retornados apenas eventos que corresponderem ao filtro. Todos os eventos são usados para avaliar o estado de integridade agregado. Se não for especificado, retornará todas as entradas. Os valores de estado são uma enumeração baseada em sinalizador, então o valor pode ser uma combinação desses valores obtidos usando o operador 'OR' bit a bit. Por exemplo, se o valor fornecido for 6, serão retornados todos os eventos com o valor OK (2) e Warning (4) de HealthState. – Default – Valor padrão. Corresponde a qualquer HealthState. O valor é zero. – None – Filtro que não corresponde a nenhum valor de HealthState. Usado para retornar nenhum resultado em uma coleção determinada de estados. O valor é 1. – Ok – Filtro que corresponde à entrada com o valor Ok de HealthState. O valor é 2. – Warning – Filtro que corresponde à entrada com o valor Warning de HealthState.  O valor é 4. – Error – Filtro que corresponde à entrada com o valor Error de HealthState. O valor é 8. – All – Filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535.|
|--exclude-health-statistics                   | Indica se as estatísticas de integridade devem ser retornadas como parte do resultado da consulta. False por padrão. As estatísticas mostram o número de entidades filhas nos estados de integridade OK, Warning e Error.|
 |   --include-system-application-health-statistics| Indica se as estatísticas de integridade devem incluir as estatísticas de integridade do aplicativo fabric:/System. Falso por padrão. Se IncludeSystemApplicationHealthStatistics for definido como true, as estatísticas de integridade incluirão as entidades que pertencem ao aplicativo fabric:/System. Caso contrário, o resultado da consulta incluirá estatísticas de integridade somente para aplicativos do usuário. As estatísticas de integridade devem ser incluídas no resultado da consulta para que este parâmetro seja aplicado.|
| --nodes-health-state-filter    | Permite filtrar os objetos de estado de integridade de nó retornados no resultado da consulta de integridade do cluster com base em seu estado de integridade. Os valores possíveis para esse parâmetro incluem o valor de inteiro de um dos seguintes estados de integridade. Só serão retornados os nós que corresponderem ao filtro. Todos os nós são usados para avaliar o estado de integridade agregado. Se não for especificado, todas as entradas serão retornadas. Os valores de estado são uma enumeração baseada em sinalizador, então o valor pode ser uma combinação desses valores obtidos usando o operador 'OR' bit a bit. Por exemplo, se o valor fornecido for "6", será retornado o estado de integridade dos nós com um valor OK (2) e Warning (4) de HealthState. –  Default – Valor padrão. Corresponde a qualquer HealthState. O valor é zero. – None – Filtro que não corresponde a nenhum valor de HealthState. Usado para retornar nenhum resultado em uma coleção determinada de estados. O valor é 1. – Ok – Filtro que corresponde à entrada com o valor Ok de HealthState. O valor é 2. – Warning – Filtro que corresponde à entrada com o valor Warning de HealthState.  O valor é 4. – Error – Filtro que corresponde à entrada com o valor Error de HealthState. O valor é 8. – All – Filtro que corresponde à entrada com qualquer valor de HealthState. O valor é 65535.|
| --timeout -t                   | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug                        | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h                      | Mostrar esta mensagem de ajuda e sair.|
| --output -o                    | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.                    Padrão: json.|
| --query                        | Cadeia de caracteres de consulta JMESPath. Consulte http://jmespath.org/ para saber mais e obter exemplos.|
| --verbose                      | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-cluster-manifest"></a>Manifesto do cluster sfctl
Obter o manifesto do cluster do Service Fabric.

Obter o manifesto do cluster do Service Fabric. O manifesto do cluster contém propriedades do cluster que incluem diferentes tipos de nó no cluster, as configurações de segurança, falhas e topologias de domínio de atualização etc. Essas propriedades são especificadas como parte do arquivo Clusterconfig.JSON ao implantar um cluster autônomo. No entanto, a maioria das informações no manifesto do cluster é gerada internamente pela malha do serviço durante a implantação do cluster em outros cenários de implantação (por exemplo, ao usar o [portal do Azure](https://portal.azure.com)). O conteúdo do manifesto do cluster é apenas para fins informativos e os usuários não devem depender do formato do conteúdo do arquivo ou sua interpretação. 

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --timeout -t| Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug  | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h| Mostrar esta mensagem de ajuda e sair.|
| --output -o | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão: json.|
| --query  | Cadeia de caracteres de consulta JMESPath. Consulte http://jmespath.org/ para saber mais e obter exemplos.|
| --verbose| Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-cluster-provision"></a>provisionamento do cluster sfctl
Provisão dos pacotes de código ou a configuração de um cluster do Service Fabric.
Valide e provisione o código ou os pacotes de configuração de um cluster do Service Fabric.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
|--caminho do arquivo do manifesto do cluster| O caminho do arquivo do manifesto do cluster.|
|    --caminho do arquivo do código            | O caminho do pacote de código do cluster.|
|    --timeout -t                | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h  | Mostrar esta mensagem de ajuda e sair.|
| --output -o| O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão: json.|
| --query | Cadeia de caracteres de consulta JMESPath. Consulte http://jmespath.org/ para saber mais e obter exemplos.|
| --verbose  | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-cluster-select"></a>seleção do cluster sfctl
Se conecta a um ponto de extremidade do cluster do Service Fabric.

Se estiver se conectando ao cluster seguro, especifique um certificado (.crt) e o arquivo chave (.key) ou um único arquivo com ambos (.pem). Não especifique ambos. Opcionalmente, se estiver se conectando a um cluster seguro, também especifique um caminho para um arquivo de pacote de autoridade de certificação ou o diretório de certificados de autoridade de certificação confiáveis.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIÇÃO|
| --- | --- |
| --ponto de extremidade [obrigatório]| URL do ponto de extremidade, incluindo o prefixo HTTPS ou HTTPS e porta do cluster.|
| --aad             | Usar o Azure Active Directory para autenticação.|
| --ca              | Caminho para o diretório de certificados de autoridade de certificação para tratar como válido ou o arquivo de pacote de autoridade de certificação.|
| --cert            | Caminho para um arquivo de certificado do cliente.|
| --chave             | Caminho para um arquivo chave de certificado do cliente.|
| --sem verificação       | Desativar a verificação de certificados ao usar HTTPS, observe: essa é uma opção insegura e não deve ser usada para ambientes de produção.|
| --pem             | Caminho para um certificado de cliente, como um arquivo .pem.|

### <a name="global-arguments"></a>Argumentos globais

|Argumento|DESCRIÇÃO|
| --- | --- |
| --debug           | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
| --help -h         | Mostrar esta mensagem de ajuda e sair.|
| --output -o       | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão: json.|
| --query           | Cadeia de caracteres de consulta JMESPath. Consulte http://jmespath.org/ para saber mais e obter exemplos.|
| --verbose         | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="sfctl-cluster-unprovision"></a>desprovisionamento do cluster sfctl
Desprovisione o código ou os pacotes de configuração de um cluster do Service Fabric.

Desconfigurar os pacotes de código ou a configuração de um cluster do Service Fabric.

### <a name="arguments"></a>Argumentos
|Argumento|DESCRIÇÃO|
| --- | --- |
|--versão do código  | A versão do pacote de código do cluster.|
|    --versão de configuração| A versão do manifesto de cluster.|
|    --timeout -t    | Tempo limite do servidor em segundos.  Padrão: 60.|

### <a name="global-arguments"></a>Argumentos globais
|Argumento|DESCRIÇÃO|
| --- | --- |
|--debug         | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
 |   --help -h       | Mostrar esta mensagem de ajuda e sair.|
 |   --output -o     | O formato da saída.  Valores permitidos: json, jsonc, table, tsv.  Padrão: json.|
 |   --query         | Cadeia de caracteres de consulta JMESPath. Consulte http://jmespath.org/ para saber mais e obter exemplos.|
 |   --verbose       | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|


## <a name="sfctl-cluster-upgrade"></a>upgrade do cluster sfctl
Começar a realizar os upgrades da versão do código ou configuração de um cluster do Service Fabric.
Valide os parâmetros de upgrade fornecidos e comece a atualizar o código ou a versão de configuração de um cluster do Service Fabric se os parâmetros forem válidos.

### <a name="arguments"></a>Argumentos
|Argumento|DESCRIÇÃO|
| --- | --- |
|    --mapa de integridade do aplicativo                      | O JSON codificava o dicionário de pares de nome de aplicativo e percentual máximo não íntegro antes de gerar o erro.|
 |   --mapa de integridade do tipo do aplicativo                 | O JSON codificava o dicionário de pares de nome de tipo de aplicativo e percentual máximo não íntegro antes de gerar o erro.|
 |   --versão do código                        | A versão do código do cluster.|
 |   --versão de configuração                      | A versão da configuração do cluster.|
 |   --delta-health-evaluation             | Habilita a avaliação de integridade delta em vez da avaliação de integridade absoluta após a conclusão de cada domínio de atualização.|
 |   --delta-unhealthy-nodes               | O percentual máximo permitido de degradação de integridade de nós permitida durante os upgrades do cluster.  Padrão: 10. O delta é medido entre o estado de nós no início da atualização e o estado de nós no momento da avaliação de integridade. A verificação é realizada após a conclusão de cada atualização de domínio de atualização para garantir que o estado global do cluster esteja dentro dos limites tolerados.|
 |   --ação de falha                      | Os valores possíveis incluem: 'Inválido', 'Reversão', 'Manual'.|
 |   -força-reinicialização                       | Força reinicialização.|
 |   --nova tentativa de verificação da integridade                  | Tempo limite de repetição de verificação de integridade medido em milissegundos.|
 |   --estabilidade de verificação de integridade                 | Verificação da integridade de duração estável, medida em milissegundos.|
  |  --espera de verificação de integridade                   | Duração da espera da verificação da integridade, medida em milissegundos.|
  |  --replica-set-check-timeout           | Tempo limite de verificação do conjunto de réplicas de atualização medido em segundos.|
 |   --rolling-upgrade-mode                | Os valores possíveis incluem: 'Invalid', 'UnmonitoredAuto', 'UnmonitoredManual', 'Monitored'.  Padrão: UnmonitoredAuto.|
  |  --timeout -t                          | Tempo limite do servidor em segundos.  Padrão: 60.|
  |  --unhealthy-applications              | O percentual máximo permitido de aplicativos não íntegros antes de relatar um erro. Por exemplo, para permitir que 10% dos aplicativos sejam problemáticos, esse valor seria 10. A porcentagem representa o máximo tolerado de aplicativos que podem estar não íntegros antes de o cluster ser considerado com erro. Se a porcentagem for respeitada, mas houver pelo menos um aplicativo não íntegro, a integridade será avaliada como ‘Aviso’. Isso é calculado dividindo o número de aplicativos problemáticos pelo número total de instâncias do aplicativo no cluster, exceto aplicativos de tipos de aplicativos que estão incluídos no ApplicationTypeHealthPolicyMap. O cálculo é arredondado para cima para tolerar uma falha em um número pequeno de aplicativos.|
 |   --unhealthy-nodes                     | O percentual máximo permitido de nós não íntegros antes de relatar um erro. Por exemplo, para permitir que 10% dos nós sejam problemáticos, esse valor seria 10. A porcentagem representa o máximo tolerado de nós que podem estar não íntegros antes de o cluster ser considerado com erro. Se a porcentagem for respeitada, mas houver pelo menos um nó não íntegro, a integridade será avaliada como ‘Aviso’. Este percentual é calculado pela divisão do número de nós não íntegros pelo número total de nós no cluster. O cálculo é arredondado para cima para tolerar uma falha em um número pequeno de nós. Em clusters grandes, alguns nós sempre estarão inoperantes ou ausentes para reparos, de modo que esse percentual deve ser configurado para tolerá-los.|
 |   --upgrade-domain-delta-unhealthy-nodes| O percentual máximo permitido de degradação de integridade de nós de domínio de atualização permitida durante os upgrades do cluster. Padrão: 15. O delta é medido entre o estado de nós do domínio do upgrade no início do upgrade e o estado dos nós no momento da avaliação de integridade. A verificação é realizada após a conclusão de cada atualização de domínio de atualização para todos os domínios de atualização concluídos para garantir que o estado dos domínios de atualização esteja dentro dos limites tolerados.|
 |   -tempo limite ao domínio de upgrade              | Tempo limite do domínio de atualização medido em milissegundos.|
 |   -tempo limite de upgrade                     | Tempo limite de atualização medido em milissegundos.|
 |   --aviso-como-erro                    | Os avisos são tratados com a mesma gravidade dos erros.|

### <a name="global-arguments"></a>Argumentos globais
|Argumento|DESCRIÇÃO|
| --- | --- |
|--debug                               | Aumentar o nível de detalhes do log para mostrar todos os logs de depuração.|
|    --help -h                             | Mostrar esta mensagem de ajuda e sair.|
|    --output -o                           | O formato da saída.  Valores permitidos: json, jsonc, table, tsv. Padrão: json.|
|    --query                               | Cadeia de caracteres de consulta JMESPath. Consulte http://jmespath.org/ para saber mais e obter exemplos.|
|    --verbose                             | Aumentar o nível de detalhes do log. Use --debug para logs de depuração completos.|

## <a name="next-steps"></a>Próximas etapas
- [Configurar](service-fabric-cli.md) a CLI do Service Fabric.
- Saiba como usar a CLI do Service Fabric usando os [scripts de exemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).