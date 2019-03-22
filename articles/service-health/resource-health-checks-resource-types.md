---
title: Tipos de recurso com suporte por meio do Azure Resource Health | Microsoft Docs
description: Tipos de recurso com suporte por meio do Azure Resource Health
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.date: 01/29/2019
ms.openlocfilehash: a8bc82a2717bfa6838b2331ef54ed8098422c223
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57770332"
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Tipos de recursos e verificações de integridade no Azure Resource Health
Abaixo, temos uma lista completa de todas as verificações executadas por meio do Resource Health segundo o tipo do recurso.

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers
|Verificações executadas|
|---|
|<ul><li>O servidor está em funcionamento?</li><li>O servidor está funcionando fora da memória?</li><li>O servidor está iniciando?</li><li>O servidor está recuperando?</li></ul>|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|Verificações executadas|
|---|
|<ul><li>O serviço de Gerenciamento de Api está ativo e em funcionamento?</li></ul>|

## <a name="microsoftcacheredisredis"></a>Microsoft.CacheRedis/Redis
|Verificações executadas|
|---|
|<ul><li>Todos os nós do Cache estão em funcionamento?</li><li>É possível alcançar o Cache de dentro do datacenter?</li><li>O Cache atingiu o número máximo de conexões?</li><li> O Cache esgotou a memória disponível? </li><li>O Cache está apresentando um alto número de falhas de página?</li><li>O Cache está sob uma carga pesada?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/profile
|Verificações executadas|
|---|
|<ul> <li>O portal complementar pode ser acessado para operações de configuração da CDN?</li><li>Há problemas contínuos de entrega com os pontos de extremidade da CDN?</li><li>Os usuários podem alterar a configuração de seus recursos da CDN?</li><li>Alterações de configuração estão sendo propagadas com a velocidade esperada?</li><li>Os usuários podem gerenciar a configuração da CDN usando o PowerShell, a API ou o Portal do Azure?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classiccompute/virtualmachines
|Verificações executadas|
|---|
|<ul><li>O servidor host está em funcionamento?</li><li>A inicialização do SO host foi concluída?</li><li>O contêiner da máquina virtual está provisionado e ligado?</li><li>Há conectividade de rede entre o host e a conta de armazenamento?</li><li>A inicialização do SO convidado foi concluída?</li><li>Há manutenção planejada contínua?</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.cognitiveservices/accounts
|Verificações executadas|
|---|
|<ul><li>É possível alcançar a conta de dentro do datacenter?</li><li>O provedor de recursos dos Serviços Cognitivos está disponível?</li><li>O Serviço Cognitivo está disponível na região apropriada?</li><li>Operações de leitura podem ser executadas na conta de armazenamento que contém os metadados do recurso?</li><li>A cota de chamadas à API foi atingida?</li><li>O limite de leitura de chamadas à API foi atingido?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.compute/virtualmachines
|Verificações executadas|
|---|
|<ul><li>O servidor que hospeda a máquina virtual está em funcionamento?</li><li>A inicialização do SO host foi concluída?</li><li>O contêiner da máquina virtual está provisionado e ligado?</li><li>Há conectividade de rede entre o host e a conta de armazenamento?</li><li>A inicialização do SO convidado foi concluída?</li><li>Há manutenção planejada contínua?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/accounts
|Verificações executadas|
|---|
|<ul><li>Os usuários tiveram problemas ao enviar ou listar seus trabalhos do Data Lake Analytics?</li><li>As tarefas do Data Lake Analytics não podem ser concluídas para erros do sistema?</li></ul>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/accounts
|Verificações executadas|
|---|
|<ul><li>Os usuários tiveram problemas para fazer o upload de dados para o Data Lake Store?</li><li>Os usuários tiveram problemas para fazer o upload de dados para o Data Lake Store?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.devices/iothubs

|Verificações executadas|
|---|
|<ul><li>O hub IoT está em execução?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|Verificações executadas|
|---|
|<ul><li>Alguma solicitação do banco de dados ou da coleção deixou de ser atendida devido a uma indisponibilidade de serviço do Azure Cosmos DB?</li><li>Alguma solicitação de documentos deixou de ser atendida devido a uma indisponibilidade de serviço do Azure Cosmos DB?</li></ul>|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults
|Verificações executadas|
|---|
|<ul><li>As solicitações para a falha do cofre de chave são decorrentes dos problemas da plataforma Azure KeyVault?</li><li>As solicitações para o cofre de chave estão sendo limitadas devido a muitas solicitações feitas pelo cliente?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft.network/connections
|Verificações executadas|
|---|
|<ul><li>O túnel VPN está conectado?</li><li>Há conflitos de configuração na conexão?</li><li>As chaves pré-compartilhadas estão configuradas corretamente?</li><li>O dispositivo de VPN local é acessível?</li><li>Há incompatibilidades na política de segurança IPsec/IKE?</li><li>A conexão VPN S2S está provisionada corretamente ou em um estado de falha?</li><li>A conexão VNET para VNET está provisionada corretamente ou em um estado de falha?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|Verificações executadas|
|---|
|<ul><li>O Gateway de VPN pode ser acessado pela Internet?</li><li>O Gateway de VPN está em modo de espera?</li><li>O serviço de VPN está em execução no gateway?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/namespace
|Verificações executadas|
|---|
|<ul><li> Operações de tempo de execução como o registro, a instalação ou o envio podem ser executadas no namespace?</li></ul>|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities
|Verificações executadas|
|---|
|<ul><li>O recurso de capacidade está funcionando?</li><li>Todas as cargas de trabalho estão funcionando?</li></ul>|

## <a name="microsoftpowerbiworkspacecollections"></a>Microsoft.PowerBI/workspaceCollections
|Verificações executadas|
|---|
|<ul><li>O SO host está em funcionamento?</li><li>O workspaceCollection pode ser acessado de fora do datacenter?</li><li>O provedor de recursos do PowerBI está disponível?</li><li>O serviço do PowerBI está disponível na região apropriada?</li></ul>|

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchServices
|Verificações executadas|
|---|
|<ul><li>Operações de diagnóstico podem ser executadas no cluster?</li></ul>|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces
|Verificações executadas|
|---|
|<ul><li>Os clientes estão enfrentando erros de Barramento de Serviço gerado pelo usuário?</li><li>Os usuários estão enfrentando um aumento de erros transitórios devido a uma atualização de namespace do Barramento de Serviço?</li></ul>|

## <a name="microsoftsqlserverdatabase"></a>Microsoft.SQL/Server/database
|Verificações executadas|
|---|
|<ul><li> Houve logons no banco de dados?</li></ul>|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts
|Verificações executadas|
|---|
|<ul><li>As solicitações para ler dados da conta de Armazenamento falham devido a problemas da plataforma do Azure Storage?</li><li>As solicitações para gravar dados da conta de Armazenamento falham devido a problemas da plataforma do Azure Storage?</li><li>O cluster de Armazenamento em que a conta de armazenamento reside está indisponível?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
|Verificações executadas|
|---|
|<ul><li>Todos os hosts em que o trabalho está sendo executado estão em funcionamento?</li><li>Não foi possível iniciar o trabalho?</li><li>Há atualizações de tempo de execução em andamento?</li><li>O trabalho está em um estado esperado (por exemplo, em execução ou parado pelo cliente)?</li><li>O trabalho encontrou exceções de memória insuficiente?</li><li>Há atualizações de computação agendadas em andamento?</li><li>O Gerenciador de Execução (plano de controle) está disponível?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft.web/serverFarms
|Verificações executadas|
|---|
|<ul><li>O servidor host está em funcionamento?</li><li>Os Serviços de Informações da Internet estão em execução?</li><li>O balanceador de carga está em execução?</li><li>O Plano do Serviço de Aplicativo pode ser alcançado a partir do datacenter?</li><li>A conta de armazenamento que está hospedando o conteúdo dos sites para o serverFarm está disponível?</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft.web/sites
|Verificações executadas|
|---|
|<ul><li>O servidor host está em funcionamento?</li><li>Os servidor de Informações da Internet está em execução?</li><li>O balanceador de carga está em execução?</li><li>É possível alcançar o aplicativo Web de dentro do datacenter?</li><li>A conta de armazenamento que está hospedando o conteúdo do site está disponível?</li></ul>|

# <a name="next-steps"></a>Próximas etapas
-  Consulte [Introdução ao painel de Integridade do Serviço do Azure](service-health-overview.md) e [Introdução ao Azure Resource Health](resource-health-overview.md) para entender mais sobre eles. 
-  [Perguntas frequentes sobre o Azure Resource Health](resource-health-faq.md)
- Configure alertas para receber notificações de problemas de integridade. Para obter mais informações, consulte [Configurar alertas do eventos do Service Health](../azure-monitor/platform/alerts-activity-log-service-notifications.md). 
