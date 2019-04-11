---
title: Funções internas para recursos do Azure | Microsoft Docs
description: Descreve as funções internas do RBAC (controle de acesso baseado em função) e os recursos do Azure. Lista as ações, NotActions, DataActions e NotDataActions.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 04/01/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: f505f922685cd192525814df25cca1a1401d2913
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59281209"
---
# <a name="built-in-roles-for-azure-resources"></a>Funções internas para recursos do Azure

O [controle de acesso baseado em função (RBAC)](overview.md) tem várias funções interna que você pode atribuir a usuários, grupos, entidades de serviço e identidades gerenciadas. Atribuições de função são a maneira de controlar o acesso aos recursos do Azure. Se as funções internas não atenderem às necessidades específicas de sua organização, você poderá criar suas próprias [funções personalizadas para recursos do Azure](custom-roles.md).

Este artigo lista as funções internas para recursos do Azure, que estão sempre evoluindo. Para obter as funções mais recentes, use [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) ou [az role definition list](/cli/azure/role/definition#az-role-definition-list). Caso esteja buscando as funções de administrador do Azure Active Directory, confira [Permissões da função de administrador no Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="built-in-role-descriptions"></a>Descrições de função interna

A tabela a seguir fornece uma breve descrição de cada função interna. Clique no nome de função para ver a lista de `Actions`, `NotActions`, `DataActions` e `NotDataActions` para cada função. Para obter informações sobre o que essas ações significam e como elas se aplicam aos planos de gerenciamento e de dados, consulte [Entender as definições de função para recursos do Azure](role-definitions.md).


| Função interna | DESCRIÇÃO |
| --- | --- |
| [Proprietário](#owner) | Permite que você gerencie tudo, incluindo acesso aos recursos. |
| [Colaborador](#contributor) | Permite que você gerencie tudo, exceto o acesso aos recursos. |
| [Leitor](#reader) | Permite que você veja tudo, mas não faça alterações. |
| [AcrDelete](#acrdelete) | Excluir ACR |
| [AcrImageSigner](#acrimagesigner) | signatário de imagem ACR |
| [AcrPull](#acrpull) | acr pull |
| [AcrPush](#acrpush) | acr push |
| [AcrQuarantineReader](#acrquarantinereader) | leitor de dados de quarentena acr |
| [AcrQuarantineWriter](#acrquarantinewriter) | gravador de dados de quarentena acr |
| [Colaborador de serviço de gerenciamento de API](#api-management-service-contributor) | Pode gerenciar o serviço e as APIs |
| [Função do operador de serviço de gerenciamento da API](#api-management-service-operator-role) | Pode gerenciar serviços, mas não as APIs |
| [Função de leitor do Serviço de Gerenciamento de API](#api-management-service-reader-role) | Acesso somente leitura ao serviço e APIs |
| [Colaborador de componente do Application Insights](#application-insights-component-contributor) | Pode gerenciar os componentes do Application Insights |
| [Depurador de Instantâneos do Application Insights](#application-insights-snapshot-debugger) | Concede permissão ao usuário para exibir e baixar os instantâneos de depuração coletados com o Depurador de Instantâneos do Application Insights. Observe que essas permissões não estão incluídas nas funções [Proprietário](#owner) ou [Colaborador](#contributor). |
| [Operador do Trabalho de Automação](#automation-job-operator) | Criar e gerenciar trabalhos usando runbooks de Automação. |
| [Operador de automação](#automation-operator) | Os Operadores de Automação podem iniciar, interromper, suspender e retomar trabalhos |
| [Operador de Runbook de Automação](#automation-runbook-operator) | Ler propriedades do Runbook - para poder criar Trabalhos do runbook. |
| [Colaborador de Avere](#avere-contributor) | Pode criar e gerenciar um cluster de vFXT Avere. |
| [Operador Avere](#avere-operator) | Usado pelo cluster vFXT Avere para gerenciar o cluster |
| [Função de Administrador do Cluster do Serviço de Kubernetes do Azure](#azure-kubernetes-service-cluster-admin-role) | Liste a ação de credencial de administrador de cluster. |
| [Função de Usuário do Cluster do Serviço de Kubernetes do Azure](#azure-kubernetes-service-cluster-user-role) | Liste a ação de credencial de usuário de cluster. |
| [Proprietário de registro do Azure Stack](#azure-stack-registration-owner) | Permite que você gerencie registros do Microsoft Azure Stack. |
| [Colaborador de Backup](#backup-contributor) | Permite que você gerencie o serviço de backup, mas não pode criar cofres e fornecer acesso a outras pessoas |
| [Operador de Backup](#backup-operator) | Permite que você gerencie serviços de backup, exceto a remoção de backup, a criação de cofres e o fornecimento de acesso a outras pessoas |
| [Leitor de Backup](#backup-reader) | Pode exibir serviços de backup, mas não pode fazer alterações |
| [Leitor de cobrança](#billing-reader) | Permite o acesso de leitura aos dados de cobrança |
| [Colaborador do BizTalk](#biztalk-contributor) | Permite gerenciar serviços do BizTalk, mas não acessá-los. |
| [Colaborador de ponto de extremidade de CDN](#cdn-endpoint-contributor) | Pode gerenciar os pontos de extremidade de CDN, mas não pode conceder acesso a outros usuários. |
| [Leitor de ponto de extremidade de CDN](#cdn-endpoint-reader) | Pode exibir os pontos de extremidade de CDN, mas não fazer alterações. |
| [Colaborador de perfil de CDN](#cdn-profile-contributor) | Pode gerenciar os perfis de CDN e os respectivos pontos de extremidade, mas não pode conceder acesso a outros usuários. |
| [Leitor de perfil de CDN](#cdn-profile-reader) | Pode exibir os perfis de CDN e os respectivos pontos de extremidade, mas não fazer alterações. |
| [Colaborador de rede clássica](#classic-network-contributor) | Permite que você gerencie redes clássicas, mas não acessá-las. |
| [Colaborador da conta de armazenamento clássica](#classic-storage-account-contributor) | Permite que você gerencie contas de armazenamento clássico, mas não acessá-las. |
| [Função do Serviço de Operador da Chave da Conta de Armazenamento Clássica](#classic-storage-account-key-operator-service-role) | Os Operadores da Chave da Conta de Armazenamento Clássica têm permissão para listar e regenerar chaves nas Contas de Armazenamento Clássicas |
| [Colaborador de Máquina Virtual Clássica](#classic-virtual-machine-contributor) | Permite gerenciar máquinas virtuais clássicas, mas não o acesso a elas, nem à rede virtual ou conta de armazenamento à qual estão conectadas. |
| [Colaborador dos Serviços Cognitivos](#cognitive-services-contributor) | Permite criar, ler, atualizar, excluir e gerenciar chaves dos Serviços Cognitivos. |
| [Leitor de Dados de Serviços Cognitivos (Versão Prévia)](#cognitive-services-data-reader-preview) | Permite que você leia os dados dos Serviços Cognitivos. |
| [Usuário dos Serviços Cognitivos](#cognitive-services-user) | Permite ler e listar as chaves dos Serviços Cognitivos. |
| [Função de leitor de conta do Cosmos DB](#cosmos-db-account-reader-role) | Pode ler dados de contas do Azure Cosmos DB. Consulte [Colaborador de conta do DocumentDB](#documentdb-account-contributor) para gerenciar contas do Azure Cosmos DB. |
| [CosmosBackupOperator](#cosmosbackupoperator) | Pode enviar solicitação de restauração de um banco de dados Cosmos DB ou de um contêiner em uma conta |
| [Colaborador do Gerenciamento de Custos](#cost-management-contributor) | Pode exibir os custos e gerenciar a configuração de custo (por exemplo, orçamentos, exportações) |
| [Leitor do Gerenciamento de Custos](#cost-management-reader) | Pode exibir dados e configuração de custos (por exemplo, orçamentos, exportações) |
| [Colaborador do Data Box](#data-box-contributor) | Permite que você gerencie tudo sob o serviço Data Box exceto fornecer acesso a outras pessoas. |
| [Leitor do Data Box](#data-box-reader) | Permite que você gerencie o serviço do Azure Data Box, exceto a ordem de criação ou edição de detalhes do pedido e fornecer acesso a outras pessoas. |
| [Colaborador da fábrica de dados](#data-factory-contributor) | Cria e gerencia data factories, assim como os recursos filhos neles. |
| [Desenvolvedor do Data Lake Analytics](#data-lake-analytics-developer) | Permite enviar, monitorar e gerenciar seus próprios trabalhos, mas não criar nem excluir contas do Data Lake Analytics. |
| [Limpador de Dados](#data-purger) | Pode limpar os dados de análise |
| [Usuário do DevTest Labs](#devtest-labs-user) | Permite conectar, iniciar, reiniciar e encerrar as máquinas virtuais no Azure DevTest Labs. |
| [Colaborador de zona DNS](#dns-zone-contributor) | Permite gerenciar zonas DNS e conjuntos de registros no DNS do Azure, mas não permite controlar quem tem acesso a eles. |
| [Colaborador de Conta do DocumentDB](#documentdb-account-contributor) | Pode gerenciar contas do Azure Cosmos DB. O Azure Cosmos DB era anteriormente conhecido como DocumentDB. |
| [Colaborador de EventGrid EventSubscription](#eventgrid-eventsubscription-contributor) | Permite que você gerencie operações de assinatura de evento EventGrid. |
| [Leitor de EventGrid EventSubscription](#eventgrid-eventsubscription-reader) | Permite que você gerencie operações de assinatura de evento EventGrid. |
| [Colaborador dos serviços de domínio do HDInsight](#hdinsight-domain-services-contributor) | Pode ler, criar, modificar e excluir operações relacionadas aos serviços de domínio necessárias para o Enterprise Security Package do HDInsight |
| [Colaborador de conta do sistemas inteligentes](#intelligent-systems-account-contributor) | Permite gerenciar contas do Intelligent Systems, mas não acessá-las. |
| [Colaborador do Key Vault](#key-vault-contributor) | Permite gerenciar cofres de chaves, mas não acessá-los. |
| [Criador de laboratório](#lab-creator) | Permite a você criar, gerenciar e excluir os laboratórios gerenciados nas contas de laboratório do Azure. |
| [Colaborador do Log Analytics](#log-analytics-contributor) | O Colaborador do Log Analytics pode ler todos os dados de monitoramento e editar as configurações de monitoramento. A edição das configurações de monitoramento inclui a adição da extensão da VM às VMs, leitura das chaves da conta de armazenamento para poder configurar a coleção de logs do Armazenamento do Microsoft Azure, criação e configuração de contas de Automação, adição de soluções e configuração do diagnóstico do Azure em todos os recursos do Azure. |
| [Leitor do Log Analytics](#log-analytics-reader) | Um Leitor do Log Analytics pode exibir e pesquisar todos os dados de monitoramento além de exibir as configurações de monitoramento, incluindo a exibição da configuração do diagnóstico do Azure em todos os recursos do Azure. |
| [Colaborador de aplicativo lógico](#logic-app-contributor) | Permite gerenciar o aplicativo lógico, mas não o acesso a ele. |
| [Operador de aplicativo lógico](#logic-app-operator) | Permite que você leia, habilite e desabilite o aplicativo lógico. |
| [Função do Operador de Aplicativos Gerenciado](#managed-application-operator-role) | Permite que você leia e execute as ações nos recursos de aplicativo gerenciado |
| [Leitor de aplicativos gerenciados](#managed-applications-reader) | Permite ler os recursos de um aplicativo gerenciado e solicitar acesso JIT. |
| [Colaborador de Identidade Gerenciada](#managed-identity-contributor) | Criar, ler, atualizar e excluir a identidade atribuída pelo usuário |
| [Operador de Identidade Gerenciada](#managed-identity-operator) | Ler e atribuir identidade atribuída pelo usuário |
| [Colaborador do Grupo de Gerenciamento](#management-group-contributor) | Função de Colaborador do Grupo de Gerenciamento |
| [Leitor do Grupo de Gerenciamento](#management-group-reader) | Função de Leitor do Grupo de Gerenciamento |
| [Colaborador de monitoramento](#monitoring-contributor) | Pode ler todos os dados de monitoramento e editar configurações de monitoramento. Consulte também [Introdução às funções, permissões e segurança com o Azure Monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
| [Publicador de Métricas de Monitoramento](#monitoring-metrics-publisher) | Habilita a publicação de métricas com base nos recursos do Azure |
| [Leitor de monitoramento](#monitoring-reader) | Pode ler todos os dados de monitoramento (métricas, logs, etc). Consulte também [Introdução às funções, permissões e segurança com o Azure Monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
| [Colaborador de rede](#network-contributor) | Permite gerenciar redes, mas não acessá-las. |
| [Colaborador de Conta APM do New Relic](#new-relic-apm-account-contributor) | Permite que você gerencie contas e aplicativos do Gerenciamento de desempenho de aplicativos da New Relic, mas não tem acesso a eles. |
| [Acesso a Dados e Leitor](#reader-and-data-access) | Permite que você exiba tudo, mas não permitirá que exclua ou crie uma conta de armazenamento ou um recurso contido. Ele também permitirá o acesso de leitura/gravação a todos os dados contidos em uma conta de armazenamento por meio de acesso às chaves de conta de armazenamento. |
| [Colaborador do Cache Redis](#redis-cache-contributor) | Permite gerenciar caches Redis, mas não acessá-los. |
| [Colaborador da Política de Recursos (Versão prévia)](#resource-policy-contributor-preview) | (Versão prévia) Os usuários com aterramento da EA, com direitos para criar/modificar a política de recursos, criam um tíquete de suporte e recursos/hierarquia de leitura. |
| [Colaborador de Coleções de Trabalho do Agendador](#scheduler-job-collections-contributor) | Permite gerenciar as coleções de trabalhos do Agendador, mas não acessá-las. |
| [Colaborador do Serviço de Pesquisa](#search-service-contributor) | Permite gerenciar serviços de pesquisa, mas não acessá-las. |
| [Administrador de Segurança](#security-admin) | Somente na Central de Segurança: Pode exibir políticas de segurança, exibir estados de segurança, editar políticas de segurança, exibir alertas e recomendações, ignorar alertas e recomendações |
| [Gerenciador de Segurança (Herdado)](#security-manager-legacy) | Esta é uma função herdada. Em vez disso, use o Administrador de Segurança |
| [Leitor de segurança](#security-reader) | Somente na Central de Segurança: Pode exibir recomendações e alertas, exibir políticas de segurança, exibir estados de segurança, mas não pode fazer alterações |
| [Colaborador do Site Recovery](#site-recovery-contributor) | Permite gerenciar o serviço do Azure Site Recovery, exceto a criação de cofre e atribuição de função |
| [Operador do Site Recovery](#site-recovery-operator) | Permite failover e failback, mas não executa outras operações de gerenciamento do Azure Site Recovery |
| [Leitor do Site Recovery](#site-recovery-reader) | Permite visualizar o status do Azure Site Recovery, mas não executar outras operações de gerenciamento |
| [Colaborador da Conta de Âncoras Espaciais](#spatial-anchors-account-contributor) | Permite que você gerencie âncoras espaciais em sua conta, mas não que as exclua |
| [Proprietário da Conta de Âncoras Espaciais](#spatial-anchors-account-owner) | Permite gerenciar âncoras espaciais em sua conta, inclusive excluí-las |
| [Leitor da Conta de Âncoras Espaciais](#spatial-anchors-account-reader) | Permite localizar e ler propriedades de âncoras espaciais em sua conta |
| [Colaborador do banco de dados SQL](#sql-db-contributor) | Permite gerenciar Bancos de Dados SQL, mas não acessá-los. Além disso, não é possível gerenciar as políticas relacionadas à segurança ou respectivos servidores SQL pai. |
| [Gerenciador de Segurança do SQL](#sql-security-manager) | Permite você gerenciar as políticas relacionadas à segurança de servidores e bancos de dados SQL, mas não acessá-los. |
| [Colaborador do SQL Server](#sql-server-contributor) | Permite gerenciar servidores e Bancos de Dados SQL, mas não acessá-los, nem as políticas relacionadas à segurança. |
| [Colaborador da Conta de Armazenamento](#storage-account-contributor) | Permite gerenciar contas de armazenamento, mas não acessá-las. |
| [Função do Serviço de Operador da Chave da Conta de Armazenamento](#storage-account-key-operator-service-role) | Os operadores da Chave da Conta de Armazenamento podem listar e regenerar chaves em contas de armazenamento |
| [Colaborador de Dados do Storage Blob](#storage-blob-data-contributor) | Permite o acesso de leitura, gravação e exclusão aos dados e aos contêineres do Azure Storage Blob |
| [Proprietário de Dados do Storage Blob](#storage-blob-data-owner) | Permite acesso completo aos dados e aos contêineres de blob do Armazenamento do Microsoft Azure, incluindo a atribuição de controle de acesso POSIX. |
| [Leitor de Dados do Storage Blob](#storage-blob-data-reader) | Permite o acesso de leitura aos dados e aos contêineres do Azure Storage Blob |
| [Colaborador de Dados da Fila de Armazenamento](#storage-queue-data-contributor) | Permite o acesso de leitura, gravação e exclusão às filas e mensagens da fila do Armazenamento do Azure |
| [Processador de Mensagens de Dados da Fila de Armazenamento](#storage-queue-data-message-processor) | Permite inspecionar, receber e excluir acesso a mensagens da fila do Armazenamento do Azure |
| [Remetente da Mensagem de Dados da Fila de Armazenamento](#storage-queue-data-message-sender) | Permite o envio de mensagens da fila do Armazenamento do Azure |
| [Leitor de Dados da Fila de Armazenamento](#storage-queue-data-reader) | Permite o acesso de leitura às filas e mensagens da fila do Armazenamento do Azure |
| [Colaborador de solicitação de suporte](#support-request-contributor) | Permite criar e gerenciar Solicitações de Suporte |
| [Colaborador do Gerenciador de Tráfego](#traffic-manager-contributor) | Permite gerenciar perfis do Gerenciador de Tráfego, mas não permite controlar quem tem acesso a eles. |
| [Administrador de Acesso do Usuário](#user-access-administrator) | Permite que você gerencie o acesso do usuário aos recursos do Azure. |
| [Logon de administrador da Máquina Virtual](#virtual-machine-administrator-login) | Máquinas Virtuais do Microsoft Azure no portal e logon como administrador |
| [Colaborador de Máquina Virtual](#virtual-machine-contributor) | Permite gerenciar máquinas virtuais, mas não o acesso a elas, nem à rede virtual ou conta de armazenamento à qual estão conectadas. |
| [Logon de usuário da Máquina Virtual](#virtual-machine-user-login) | Visualize as Máquinas Virtuais do Microsoft Azure no portal e faça logon como usuário. |
| [Colaborador do Plano de Web](#web-plan-contributor) | Permite gerenciar os planos da Web para sites, mas não o acesso a eles. |
| [Colaborador do Site](#website-contributor) | Permite gerenciar sites (não planos da Web), mas não acessá-los. |


## <a name="owner"></a>Proprietário
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite que você gerencie tudo, incluindo acesso aos recursos. |
> | **ID** | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | **Ações** |  |
> | * | Criar e gerenciar recursos de todos os tipos |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="contributor"></a>Colaborador
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite que você gerencie tudo, exceto o acesso aos recursos. |
> | **ID** | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | **Ações** |  |
> | * | Criar e gerenciar recursos de todos os tipos |
> | **NotActions** |  |
> | Microsoft.Authorization/*/Delete | Excluir funções e atribuições de função |
> | Microsoft.Authorization/*/Write | Criar funções e atribuições de função |
> | Microsoft.Authorization/elevateAccess/Action | Concede ao chamador acesso de administrador de acesso do usuário no escopo do locatário |
> | Microsoft.Blueprint/blueprintAssignments/write | Criar ou atualizar qualquer artefato do blueprint |
> | Microsoft.Blueprint/blueprintAssignments/delete | Excluir quaisquer artefatos do Blueprint |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="reader"></a>Leitor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite que você veja tudo, mas não faça alterações. |
> | **ID** | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | **Ações** |  |
> | */leitura | Ler recursos de todos os tipos, exceto segredos. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="acrdelete"></a>AcrDelete
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Excluir ACR |
> | **ID** | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | **Ações** |  |
> | Microsoft.ContainerRegistry/registries/artifacts/delete | Exclua o artefato em um registro de contêiner. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="acrimagesigner"></a>AcrImageSigner
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | signatário de imagem ACR |
> | **ID** | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | **Ações** |  |
> | Microsoft.ContainerRegistry/registries/sign/write | Efetuar push/pull de metadados de conteúdo confiável para um registro de contêiner. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="acrpull"></a>AcrPull
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | acr pull |
> | **ID** | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | **Ações** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Efetuar pull ou Obter imagens de um registro de contêiner. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="acrpush"></a>AcrPush
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | acr push |
> | **ID** | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | **Ações** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Efetuar pull ou Obter imagens de um registro de contêiner. |
> | Microsoft.ContainerRegistry/registries/push/write | Efetuar push ou Gravar imagens para um registro de contêiner. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="acrquarantinereader"></a>AcrQuarantineReader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | leitor de dados de quarentena acr |
> | **ID** | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | **Ações** |  |
> | Microsoft.ContainerRegistry/registries/quarantineRead/read | Efetuar pull ou Obter imagens em quarentena do registro de contêiner |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="acrquarantinewriter"></a>AcrQuarantineWriter
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | gravador de dados de quarentena acr |
> | **ID** | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | **Ações** |  |
> | Microsoft.ContainerRegistry/registries/quarantineRead/read | Efetuar pull ou Obter imagens em quarentena do registro de contêiner |
> | Microsoft.ContainerRegistry/registries/quarantineWrite/write | Gravar/Modificar o estado de quarentena das imagens em quarentena |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="api-management-service-contributor"></a>Colaborador de serviço de gerenciamento de API
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Pode gerenciar o serviço e as APIs |
> | **ID** | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | **Ações** |  |
> | Microsoft.ApiManagement/service/* | Criar e gerenciar o serviço de gerenciamento da API |
> | Microsoft.Authorization/*/read | Ler autorização |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="api-management-service-operator-role"></a>Função do operador de serviço de gerenciamento da API
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Pode gerenciar serviços, mas não as APIs |
> | **ID** | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | **Ações** |  |
> | Microsoft.ApiManagement/service/*/read | Ler instâncias do serviço de gerenciamento da API |
> | Microsoft.ApiManagement/service/backup/action | Fazer backup do Serviço de Gerenciamento de API para o contêiner especificado em uma conta de armazenamento fornecida pelo usuário |
> | Microsoft.ApiManagement/service/delete | Excluir uma instância do Serviço de Gerenciamento de API |
> | Microsoft.ApiManagement/service/managedeployments/action | Alterar SKU/unidades, adicionar/remover implantações regionais do Serviço de Gerenciamento de API |
> | Microsoft.ApiManagement/service/read | Ler metadados de uma instância do Serviço de Gerenciamento de API |
> | Microsoft.ApiManagement/service/restore/action | Restaurar o Serviço de Gerenciamento de API do contêiner especificado em uma conta de armazenamento fornecida pelo usuário |
> | Microsoft.ApiManagement/service/updatecertificate/action | Carregar certificado SSL para um Serviço de Gerenciamento de API |
> | Microsoft.ApiManagement/service/updatehostname/action | Configurar, atualizar ou remover nomes de domínio personalizado para um Serviço de Gerenciamento de API |
> | Microsoft.ApiManagement/service/write | Criar uma nova instância do Serviço de Gerenciamento de API |
> | Microsoft.Authorization/*/read | Ler autorização |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Obter lista de chaves do usuário |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="api-management-service-reader-role"></a>Função de leitor do Serviço de Gerenciamento de API
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Acesso somente leitura ao serviço e APIs |
> | **ID** | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | **Ações** |  |
> | Microsoft.ApiManagement/service/*/read | Ler instâncias do serviço de gerenciamento da API |
> | Microsoft.ApiManagement/service/read | Ler metadados de uma instância do Serviço de Gerenciamento de API |
> | Microsoft.Authorization/*/read | Ler autorização |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Obter lista de chaves do usuário |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="application-insights-component-contributor"></a>Colaborador de componente do Application Insights
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Pode gerenciar os componentes do Application Insights |
> | **ID** | ae349356-3a1b-4a5e-921d-050484c6347e |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.Insights/components/* | Criar e gerenciar componentes do Insights |
> | Microsoft.Insights/webtests/* | Criar e gerenciar testes da Web |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="application-insights-snapshot-debugger"></a>Depurador de Instantâneos do Application Insights
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Concede permissão ao usuário para exibir e baixar os instantâneos de depuração coletados com o Depurador de Instantâneos do Application Insights. Observe que essas permissões não estão incluídas nas funções [Proprietário](#owner) ou [Colaborador](#contributor). |
> | **ID** | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="automation-job-operator"></a>Operador do Trabalho de Automação
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Criar e gerenciar trabalhos usando runbooks de Automação. |
> | **ID** | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Ler recursos do Hybrid Runbook Worker |
> | Microsoft.Automation/automationAccounts/jobs/read | Obter um trabalho da Automação do Azure |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Reinicia um trabalho da Automação do Azure |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Interrompe um trabalho da Automação do Azure |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Obter um fluxo de trabalho da Automação do Azure |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspende um trabalho da Automação do Azure |
> | Microsoft.Automation/automationAccounts/jobs/write | Criar um trabalho da Automação do Azure |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Obter a saída de um trabalho |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="automation-operator"></a>Operador de automação
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Os Operadores de Automação podem iniciar, interromper, suspender e retomar trabalhos |
> | **ID** | d3881f73-407a-4167-8283-e981cbba0404 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Ler recursos do Hybrid Runbook Worker |
> | Microsoft.Automation/automationAccounts/jobs/read | Obter um trabalho da Automação do Azure |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Reinicia um trabalho da Automação do Azure |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Interrompe um trabalho da Automação do Azure |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Obter um fluxo de trabalho da Automação do Azure |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspende um trabalho da Automação do Azure |
> | Microsoft.Automation/automationAccounts/jobs/write | Criar um trabalho da Automação do Azure |
> | Microsoft.Automation/automationAccounts/jobSchedules/read | Obter uma agenda de trabalho da Automação do Azure |
> | Microsoft.Automation/automationAccounts/jobSchedules/write | Criar uma agenda de trabalho da Automação do Azure |
> | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Obtém o workspace vinculado à conta de automação |
> | Microsoft.Automation/automationAccounts/read | Obter uma conta da Automação do Azure |
> | Microsoft.Automation/automationAccounts/runbooks/read | Obter um runbook da Automação do Azure |
> | Microsoft.Automation/automationAccounts/schedules/read | Obter um ativo de agendamento da Automação do Azure |
> | Microsoft.Automation/automationAccounts/schedules/write | Criar ou atualizar um ativo de agendamento da Automação do Azure |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Obter a saída de um trabalho |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="automation-runbook-operator"></a>Operador de Runbook de Automação
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Ler propriedades do Runbook - para poder criar Trabalhos do runbook. |
> | **ID** | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Automation/automationAccounts/runbooks/read | Obter um runbook da Automação do Azure |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="avere-contributor"></a>Colaborador de Avere
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Pode criar e gerenciar um cluster de vFXT Avere. |
> | **ID** | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Compute/*/read |  |
> | Microsoft.Compute/availabilitySets/* |  |
> | Microsoft.Compute/virtualMachines/* |  |
> | Microsoft.Compute/disks/* |  |
> | Microsoft.Network/*/read |  |
> | Microsoft.Network/networkInterfaces/* |  |
> | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Microsoft.Network/virtualNetworks/subnets/read | Obter uma definição de sub-rede da rede virtual |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Une a uma rede virtual. Não podem gerar alertas. |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Adicionar recursos como conta de armazenamento ou banco de dados SQL a uma sub-rede. Não podem gerar alertas. |
> | Microsoft.Network/networkSecurityGroups/join/action | Une um grupo de segurança de rede. Não podem gerar alertas. |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Storage/*/read |  |
> | Microsoft.Storage/storageAccounts/* |  |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft.Resources/subscriptions/resourceGroups/resources/read | Obter os recursos do grupo de recursos. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Retorna o resultado da exclusão de um blob |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Retorna um blob ou uma lista de blobs |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Retorna o resultado da gravação de um blob |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="avere-operator"></a>Operador Avere
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Usado pelo cluster vFXT Avere para gerenciar o cluster |
> | **ID** | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | **Ações** |  |
> | Microsoft.Compute/virtualMachines/read | Obter as propriedades de uma máquina virtual |
> | Microsoft.Network/networkInterfaces/read | Obter uma definição de adaptador de rede.  |
> | Microsoft.Network/networkInterfaces/write | Criar uma interface de rede ou atualizar uma interface de rede existente.  |
> | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Microsoft.Network/virtualNetworks/subnets/read | Obter uma definição de sub-rede da rede virtual |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Une a uma rede virtual. Não podem gerar alertas. |
> | Microsoft.Network/networkSecurityGroups/join/action | Une um grupo de segurança de rede. Não podem gerar alertas. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Retornar o resultado da exclusão de um contêiner |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Retorna a lista de contêineres |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Retorna o resultado do contêiner de put blob |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Retorna o resultado da exclusão de um blob |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Retorna um blob ou uma lista de blobs |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Retorna o resultado da gravação de um blob |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="azure-kubernetes-service-cluster-admin-role"></a>Função de Administrador do Cluster do Serviço de Kubernetes do Azure
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Liste a ação de credencial de administrador de cluster. |
> | **ID** | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | **Ações** |  |
> | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Listar a credencial clusterAdmin de um cluster gerenciado |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="azure-kubernetes-service-cluster-user-role"></a>Função de Usuário do Cluster do Serviço de Kubernetes do Azure
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Liste a ação de credencial de usuário de cluster. |
> | **ID** | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | **Ações** |  |
> | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Listar a credencial clusterUser de um cluster gerenciado |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="azure-stack-registration-owner"></a>Proprietário de registro do Azure Stack
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite que você gerencie registros do Microsoft Azure Stack. |
> | **ID** | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | **Ações** |  |
> | Microsoft.AzureStack/registrations/products/listDetails/action | Recupera detalhes estendidos de um produto do Marketplace do Azure Stack |
> | Microsoft.AzureStack/registrations/products/read | Obtém as propriedades de um produto do Marketplace do Azure Stack |
> | Microsoft.AzureStack/registrations/read | Obter as propriedades de um registro do Microsoft Azure Stack |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="backup-contributor"></a>Colaborador de Backup
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite que você gerencie o serviço de backup, mas não pode criar cofres e fornecer acesso a outras pessoas |
> | **ID** | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Gerenciar os resultados da operação no gerenciamento de backup |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Criar e gerenciar contêineres de backup em malhas de backup do cofre de Serviços de Recuperação |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Atualizar a lista de contêineres |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Criar e gerenciar trabalhos de backup |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportar trabalhos |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read |  |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | Criar e gerenciar metadados relacionados ao gerenciamento de backup |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Criar e gerenciar os Resultados das operações de gerenciamento de backup |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/* | Criar e gerenciar políticas de backup |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Criar e gerenciar itens para backup |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Criar e gerenciar itens submetidos a backup |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Criar e gerenciar contêineres que armazenam itens de backup |
> | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Retornar resumos de itens protegidos e servidores protegidos para os Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/certificates/* | Criar e gerenciar certificados relacionados a backup em um cofre de Serviços de Recuperação |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Criar e gerenciar informações estendidas relacionadas ao cofre |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obter os alertas para o cofre dos Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | A operação Obter cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre' |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Criar e gerenciar identidades registradas |
> | Microsoft.RecoveryServices/Vaults/usages/* | Criar e gerenciar o uso do cofre dos Serviços de Recuperação |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Storage/storageAccounts/read | Retornar a lista de contas de armazenamento ou obter as propriedades da conta de armazenamento especificada. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Validar operação no Item protegido |
> | Microsoft.RecoveryServices/Vaults/write | A operação Criar cofre cria um recurso do Azure do tipo 'cofre' |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Retornar o status da operação de backup para o cofre dos Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Retornar todos os servidores de gerenciamento de backup registrados com cofre. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Obter todos os contêineres protegidos |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Verificar o status de backup para os Cofres dos Serviços de Recuperação |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Validar recursos |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Resolver o alerta. |
> | Microsoft.RecoveryServices/operations/read | Operação retorna a lista de operações para um provedor de recursos |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Obtém o Status da operação para uma determinada operação |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Listar todas as Intenções de Proteção de backup |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="backup-operator"></a>Operador de Backup
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite que você gerencie serviços de backup, exceto a remoção de backup, a criação de cofres e o fornecimento de acesso a outras pessoas |
> | **ID** | 00c29273-979b-4161-815C-10b084fb9324 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Retornar o status da operação |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Obter o resultado da operação executada no contêiner de proteção. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Executar um backup para um item protegido. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Obter o resultado da operação executada em itens protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Retornar o status da operação executada em itens protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Retornar detalhes do objeto do item protegido |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Provisionar recuperação de item instantânea para item protegido |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Obter pontos de recuperação para itens protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Restaurar pontos de recuperação para itens protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Revogar a recuperação de item instantânea para item protegido |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Criar um item protegido de backup |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Retornar todos os contêineres registrados |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Atualizar a lista de contêineres |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Criar e gerenciar trabalhos de backup |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportar trabalhos |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read |  |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read |  |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Criar e gerenciar os Resultados das operações de gerenciamento de backup |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Obter os resultados da operação de política. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Retornar todas as políticas de proteção |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Criar e gerenciar itens para backup |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Retornar a lista de todos os itens protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Retornar todos os contêineres pertencentes à assinatura |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Retornar resumos de itens protegidos e servidores protegidos para os Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/certificates/write | A operação Atualizar certificado do recurso atualiza o certificado de credencial de cofre/recurso. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obter os alertas para o cofre dos Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | A operação Obter cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre' |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A operação Obter resultados da operação pode ser usada para obter o status da operação e o resultado da operação enviada de forma assíncrona |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A operação Obter contêineres pode ser usada para obter os contêineres registrados para um recurso. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | A operação Registrar o contêiner de serviço pode ser usada para registrar um contêiner com o Serviço de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Retornar os detalhes de uso para um cofre dos Serviços de Recuperação. |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Storage/storageAccounts/read | Retornar a lista de contas de armazenamento ou obter as propriedades da conta de armazenamento especificada. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Validar operação no Item protegido |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Retornar o status da operação de backup para o cofre dos Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Obter o status da operação de política. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Criar um contêiner registrado |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Consultar cargas de trabalho em um contêiner |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Retornar todos os servidores de gerenciamento de backup registrados com cofre. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Criar uma Intenção de Proteção de backup |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Obter uma Intenção de Proteção de backup |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Obter todos os contêineres protegidos |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Obter todos os itens em um contêiner |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Verificar o status de backup para os Cofres dos Serviços de Recuperação |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Validar recursos |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Resolver o alerta. |
> | Microsoft.RecoveryServices/operations/read | Operação retorna a lista de operações para um provedor de recursos |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Obtém o Status da operação para uma determinada operação |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Listar todas as Intenções de Proteção de backup |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="backup-reader"></a>Leitor de Backup
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Pode exibir serviços de backup, mas não pode fazer alterações |
> | **ID** | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp é uma operação interna usada pelo serviço |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Retornar o status da operação |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Obter o resultado da operação executada no contêiner de proteção. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Obter o resultado da operação executada em itens protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Retornar o status da operação executada em itens protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Retornar detalhes do objeto do item protegido |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Obter pontos de recuperação para itens protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Retornar todos os contêineres registrados |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Retornar o resultado da operação do trabalho. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | Retornar todos os objetos de trabalho |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportar trabalhos |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read |  |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read |  |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Retornar o resultado da operação de backup para o cofre dos Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Obter os resultados da operação de política. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Retornar todas as políticas de proteção |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Retornar a lista de todos os itens protegidos. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Retornar todos os contêineres pertencentes à assinatura |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Retornar resumos de itens protegidos e servidores protegidos para os Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obter os alertas para o cofre dos Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/read | A operação Obter cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre' |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A operação Obter resultados da operação pode ser usada para obter o status da operação e o resultado da operação enviada de forma assíncrona |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A operação Obter contêineres pode ser usada para obter os contêineres registrados para um recurso. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Retornar cofre de armazenamento para o cofre dos Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/backupconfig/read | Retornar a configuração para cofre dos Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Retornar o status da operação de backup para o cofre dos Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Obter o status da operação de política. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Retornar todos os servidores de gerenciamento de backup registrados com cofre. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Obter uma Intenção de Proteção de backup |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Obter todos os itens em um contêiner |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Verificar o status de backup para os Cofres dos Serviços de Recuperação |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Resolver o alerta. |
> | Microsoft.RecoveryServices/operations/read | Operação retorna a lista de operações para um provedor de recursos |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Obtém o Status da operação para uma determinada operação |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Listar todas as Intenções de Proteção de backup |
> | Microsoft.RecoveryServices/Vaults/usages/read | Retornar os detalhes de uso para um cofre dos Serviços de Recuperação. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="billing-reader"></a>Leitor de cobrança
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite o acesso de leitura aos dados de cobrança |
> | **ID** | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Billing/*/read | Ler Informações de cobrança |
> | Microsoft.Commerce/*/read |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.Management/managementGroups/read | Listar grupos de gerenciamento para o usuário autenticado. |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="biztalk-contributor"></a>Colaborador do BizTalk
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite gerenciar serviços do BizTalk, mas não acessá-los. |
> | **ID** | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.BizTalkServices/BizTalk/* | Criar e gerenciar serviços BizTalk |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="cdn-endpoint-contributor"></a>Colaborador de ponto de extremidade de CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Pode gerenciar os pontos de extremidade de CDN, mas não pode conceder acesso a outros usuários. |
> | **ID** | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/* |  |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="cdn-endpoint-reader"></a>Leitor de ponto de extremidade de CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Pode exibir os pontos de extremidade de CDN, mas não fazer alterações. |
> | **ID** | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/*/read |  |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="cdn-profile-contributor"></a>Colaborador de perfil de CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Pode gerenciar os perfis de CDN e os respectivos pontos de extremidade, mas não pode conceder acesso a outros usuários. |
> | **ID** | ec156ff8-a8d1-4d15-830C-5b80698ca432 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/* |  |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="cdn-profile-reader"></a>Leitor de perfil de CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Pode exibir os perfis de CDN e os respectivos pontos de extremidade, mas não fazer alterações. |
> | **ID** | 8f96442b-4075-438f-813d-ad51ab4019af |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/*/read |  |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="classic-network-contributor"></a>Colaborador de rede clássica
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite que você gerencie redes clássicas, mas não acessá-las. |
> | **ID** | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler autorização |
> | Microsoft.ClassicNetwork/* | Criar e gerenciar redes clássicas |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="classic-storage-account-contributor"></a>Colaborador da conta de armazenamento clássica
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite que você gerencie contas de armazenamento clássico, mas não acessá-las. |
> | **ID** | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler autorização |
> | Microsoft.ClassicStorage/storageAccounts/* | Criar e gerenciar contas de armazenamento |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="classic-storage-account-key-operator-service-role"></a>Função do Serviço de Operador da Chave da Conta de Armazenamento Clássica
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Os Operadores da Chave da Conta de Armazenamento Clássica têm permissão para listar e regenerar chaves nas Contas de Armazenamento Clássicas |
> | **ID** | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | **Ações** |  |
> | Microsoft.ClassicStorage/storageAccounts/listkeys/action | Listar as chaves de acesso das contas de armazenamento. |
> | Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Regenera as chaves de acesso existentes da conta de armazenamento. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="classic-virtual-machine-contributor"></a>Colaborador de Máquina Virtual Clássica
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite gerenciar máquinas virtuais clássicas, mas não o acesso a elas, nem à rede virtual ou conta de armazenamento à qual estão conectadas. |
> | **ID** | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler autorização |
> | Microsoft.ClassicCompute/domainNames/* | Criar e gerenciar nomes de domínio de computação clássica |
> | Microsoft.ClassicCompute/virtualMachines/* | Criar e gerenciar máquinas virtuais |
> | Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
> | Microsoft.ClassicNetwork/reservedIps/link/action | Vincular um IP reservado |
> | Microsoft.ClassicNetwork/reservedIps/read | Obter os IPs reservados |
> | Microsoft.ClassicNetwork/virtualNetworks/join/action | Ingressar na rede virtual. |
> | Microsoft.ClassicNetwork/virtualNetworks/read | Obter a rede virtual. |
> | Microsoft.ClassicStorage/storageAccounts/disks/read | Retornar o disco da conta de armazenamento. |
> | Microsoft.ClassicStorage/storageAccounts/images/read | Retornar a imagem da conta de armazenamento. (Preterido. Usar 'Microsoft.ClassicStorage/storageAccounts/vmImages') |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Listar as chaves de acesso das contas de armazenamento. |
> | Microsoft.ClassicStorage/storageAccounts/read | Retornar a conta de armazenamento com a conta fornecida. |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="cognitive-services-contributor"></a>Colaborador dos Serviços Cognitivos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite criar, ler, atualizar, excluir e gerenciar chaves dos Serviços Cognitivos. |
> | **ID** | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.CognitiveServices/* |  |
> | Microsoft.Features/features/read | Obter os recursos de uma assinatura. |
> | Microsoft.Features/providers/features/read | Obter o recurso de uma assinatura em determinado provedor de recursos. |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
> | Microsoft.Insights/diagnosticSettings/* | Criar, atualizar ou ler a configuração de diagnóstico do Analysis Server |
> | Microsoft.Insights/logDefinitions/read | Ler definições de log |
> | Microsoft.Insights/metricdefinitions/read | Ler definições de métrica |
> | Microsoft.Insights/metrics/read | Ler métrica |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/deployments/operations/read | Obter ou lista operações de implantação. |
> | Microsoft.Resources/subscriptions/operationresults/read | Obter os resultados da operação da assinatura. |
> | Microsoft.Resources/subscriptions/read | Obter a lista de assinaturas. |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="cognitive-services-data-reader-preview"></a>Leitor de Dados de Serviços Cognitivos (Versão Prévia)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite que você leia os dados dos Serviços Cognitivos. |
> | **ID** | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | **Ações** |  |
> | *Nenhum* |  |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="cognitive-services-user"></a>Usuário dos Serviços Cognitivos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite ler e listar as chaves dos Serviços Cognitivos. |
> | **ID** | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Ações** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | Microsoft.CognitiveServices/accounts/listkeys/action | Listar chaves |
> | Microsoft.Insights/alertRules/read | Ler alerta de métrica clássico |
> | Microsoft.Insights/diagnosticSettings/read | Ler uma configuração de diagnóstico de recurso |
> | Microsoft.Insights/logDefinitions/read | Ler definições de log |
> | Microsoft.Insights/metricdefinitions/read | Ler definições de métrica |
> | Microsoft.Insights/metrics/read | Ler métrica |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/operations/read | Obter ou lista operações de implantação. |
> | Microsoft.Resources/subscriptions/operationresults/read | Obter os resultados da operação da assinatura. |
> | Microsoft.Resources/subscriptions/read | Obter a lista de assinaturas. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.CognitiveServices/* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="cosmos-db-account-reader-role"></a>Função de leitor de conta do Cosmos DB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Pode ler dados de contas do Azure Cosmos DB. Consulte [Colaborador de conta do DocumentDB](#documentdb-account-contributor) para gerenciar contas do Azure Cosmos DB. |
> | **ID** | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Funções de leitor e atribuições de função, pode ler permissões concedidas a cada usuário |
> | Microsoft.DocumentDB/*/read | Ler qualquer coleção |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Ler as chaves somente leitura da conta do banco de dados. |
> | Microsoft.Insights/MetricDefinitions/read | Ler definições de métrica |
> | Microsoft.Insights/Metrics/read | Ler métrica |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="cosmosbackupoperator"></a>CosmosBackupOperator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Pode enviar solicitação de restauração de um banco de dados Cosmos DB ou de um contêiner em uma conta |
> | **ID** | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | **Ações** |  |
> | Microsoft.DocumentDB/databaseAccounts/backup/action | Envie uma solicitação para configurar o backup |
> | Microsoft.DocumentDB/databaseAccounts/restore/action | Enviar uma solicitação de restauração |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="cost-management-contributor"></a>Colaborador do Gerenciamento de Custos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Pode exibir os custos e gerenciar a configuração de custo (por exemplo, orçamentos, exportações) |
> | **ID** | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | **Ações** |  |
> | Microsoft.Consumption/* |  |
> | Microsoft.CostManagement/* |  |
> | Microsoft.Billing/billingPeriods/read | Listar os períodos de cobrança disponíveis |
> | Microsoft.Resources/subscriptions/read | Obter a lista de assinaturas. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="cost-management-reader"></a>Leitor do Gerenciamento de Custos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Pode exibir dados e configuração de custos (por exemplo, orçamentos, exportações) |
> | **ID** | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | **Ações** |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Billing/billingPeriods/read | Listar os períodos de cobrança disponíveis |
> | Microsoft.Resources/subscriptions/read | Obter a lista de assinaturas. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="data-box-contributor"></a>Colaborador do Data Box
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite que você gerencie tudo sob o serviço Data Box exceto fornecer acesso a outras pessoas. |
> | **ID** | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft.Databox/* |  |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="data-box-reader"></a>Leitor do Data Box
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite que você gerencie o serviço do Azure Data Box, exceto a ordem de criação ou edição de detalhes do pedido e fornecer acesso a outras pessoas. |
> | **ID** | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Databox/*/read |  |
> | Microsoft.Databox/jobs/listsecrets/action |  |
> | Microsoft.Databox/jobs/listcredentials/action | Lista as credenciais não criptografadas relacionadas ao pedido. |
> | Microsoft.Databox/locations/availableSkus/action | Este método retorna a lista de SKUs disponíveis. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="data-factory-contributor"></a>Colaborador da fábrica de dados
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Cria e gerencia data factories, assim como os recursos filhos neles. |
> | **ID** | 673868aa-7521-48A0-acc6-0f60742d39f5 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.DataFactory/dataFactories/* | Criar e gerenciar data factories e recursos filho dentro deles. |
> | Microsoft.DataFactory/factories/* | Criar e gerenciar data factories e recursos filho dentro deles. |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="data-lake-analytics-developer"></a>Desenvolvedor do Data Lake Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite enviar, monitorar e gerenciar seus próprios trabalhos, mas não criar nem excluir contas do Data Lake Analytics. |
> | **ID** | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.BigAnalytics/accounts/* |  |
> | Microsoft.DataLakeAnalytics/accounts/* |  |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | Microsoft.DataLakeAnalytics/accounts/Delete | Excluir uma conta DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Conceder permissões para cancelar trabalhos enviados por outros usuários. |
> | Microsoft.DataLakeAnalytics/accounts/Write | Criar ou atualizar uma conta DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Criar ou atualizar uma conta DataLakeStore vinculada de uma conta DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Desvincular uma conta DataLakeStore de uma conta DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Criar ou atualizar uma conta de armazenamento vinculada de uma conta DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | Desvincular uma conta de armazenamento de uma conta DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Criar ou atualizar uma regra de firewall. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | Excluir uma regra de firewall. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Criar ou atualizar uma política de computação. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | Excluir uma política de computação. |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="data-purger"></a>Limpador de Dados
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Pode limpar os dados de análise |
> | **ID** | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | **Ações** |  |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Insights/components/purge/action | Limpe dados do Application Insights |
> | Microsoft.OperationalInsights/workspaces/*/read |  |
> | Microsoft.OperationalInsights/workspaces/purge/action | Excluir dados especificados do workspace |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="devtest-labs-user"></a>Usuário do DevTest Labs
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite conectar, iniciar, reiniciar e encerrar as máquinas virtuais no Azure DevTest Labs. |
> | **ID** | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Compute/availabilitySets/read | Obter as propriedades de um conjunto de disponibilidade |
> | Microsoft.Compute/virtualMachines/*/read | Ler as propriedades de uma máquina virtual (tamanhos de VM, status de tempo de execução, extensões de VM etc.) |
> | Microsoft.Compute/virtualMachines/deallocate/action | Desligar a máquina virtual e liberar os recursos de computação |
> | Microsoft.Compute/virtualMachines/read | Obter as propriedades de uma máquina virtual |
> | Microsoft.Compute/virtualMachines/restart/action | Reinicia a máquina virtual |
> | Microsoft.Compute/virtualMachines/start/action | Inicia a máquina virtual |
> | Microsoft.DevTestLab/*/read | Ler as propriedades de um laboratório |
> | Microsoft.DevTestLab/labs/claimAnyVm/action | Declarar uma máquina virtual aleatória declarável no laboratório. |
> | Microsoft.DevTestLab/labs/createEnvironment/action | Criar máquinas virtuais em um laboratório. |
> | Microsoft.DevTestLab/labs/formulas/delete | Excluir fórmulas. |
> | Microsoft.DevTestLab/labs/formulas/read | Ler fórmulas. |
> | Microsoft.DevTestLab/labs/formulas/write | Adicionar ou modificar fórmulas. |
> | Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Avaliar a política de laboratório. |
> | Microsoft.DevTestLab/labs/virtualMachines/claim/action | Assumir o controle de uma máquina virtual existente |
> | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | Lista as agendas de iniciar/parar aplicáveis, se houver alguma. |
> | Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action | Obtém uma cadeia de caracteres que representa o conteúdo do arquivo RDP para a máquina virtual |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Une um pool de endereços de back-end de Balanceador de carga. Não podem gerar alertas. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Une uma regra de nat de entrada do balanceador de carga. Não podem gerar alertas. |
> | Microsoft.Network/networkInterfaces/*/read | Ler as propriedades de uma interface de rede (por exemplo, todos os balanceadores de carga dos quais o adaptador de rede faz parte) |
> | Microsoft.Network/networkInterfaces/join/action | Adicionar uma máquina Virtual a um adaptador de rede. Não podem gerar alertas. |
> | Microsoft.Network/networkInterfaces/read | Obter uma definição de adaptador de rede.  |
> | Microsoft.Network/networkInterfaces/write | Criar uma interface de rede ou atualizar uma interface de rede existente.  |
> | Microsoft.Network/publicIPAddresses/*/read | Ler as propriedades de um endereço IP público |
> | Microsoft.Network/publicIPAddresses/join/action | Une um endereço ip público. Não podem gerar alertas. |
> | Microsoft.Network/publicIPAddresses/read | Obter uma definição de endereço IP público. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Une a uma rede virtual. Não podem gerar alertas. |
> | Microsoft.Resources/deployments/operations/read | Obter ou lista operações de implantação. |
> | Microsoft.Resources/deployments/read | Obter ou lista implantações. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Storage/storageAccounts/listKeys/action | Retornar as chaves de acesso da conta de armazenamento especificada. |
> | **NotActions** |  |
> | Microsoft.Compute/virtualMachines/vmSizes/read | Listar os tamanhos disponíveis para os quais a máquina virtual possa ser atualizada |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="dns-zone-contributor"></a>Colaborador de zona DNS
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite gerenciar zonas DNS e conjuntos de registros no DNS do Azure, mas não permite controlar quem tem acesso a eles. |
> | **ID** | befefa01-2a29-4197-83a8-272ff33ce314 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.Network/dnsZones/* | Criar e gerenciar zonas e registros DNS |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="documentdb-account-contributor"></a>Colaborador de Conta do DocumentDB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Pode gerenciar contas do Azure Cosmos DB. O Azure Cosmos DB era anteriormente conhecido como DocumentDB. |
> | **ID** | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.DocumentDb/databaseAccounts/* | Criar e gerenciar contas do Azure Cosmos DB |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="eventgrid-eventsubscription-contributor"></a>Colaborador de EventGrid EventSubscription
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite que você gerencie operações de assinatura de evento EventGrid. |
> | **ID** | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.EventGrid/eventSubscriptions/* |  |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | Listar assinaturas de eventos globais por tipo de tópico |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Listar assinaturas de eventos regionais |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Listar assinaturas de eventos regionais por tipo de tópico |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="eventgrid-eventsubscription-reader"></a>Leitor de EventGrid EventSubscription
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite que você gerencie operações de assinatura de evento EventGrid. |
> | **ID** | 2414bbcf-6497-4faf-8c65-045460748405 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.EventGrid/eventSubscriptions/read | Ler um eventSubscription |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | Listar assinaturas de eventos globais por tipo de tópico |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Listar assinaturas de eventos regionais |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Listar assinaturas de eventos regionais por tipo de tópico |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="hdinsight-domain-services-contributor"></a>Colaborador dos serviços de domínio do HDInsight
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Pode ler, criar, modificar e excluir operações relacionadas aos serviços de domínio necessárias para o Enterprise Security Package do HDInsight |
> | **ID** | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | **Ações** |  |
> | Microsoft.AAD/*/read |  |
> | Microsoft.AAD/domainServices/*/read |  |
> | Microsoft.AAD/domainServices/oucontainer/* |  |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="intelligent-systems-account-contributor"></a>Colaborador de conta do sistemas inteligentes
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite gerenciar contas do Intelligent Systems, mas não acessá-las. |
> | **ID** | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.IntelligentSystems/accounts/* | Criar e gerenciar contas do Sistemas Inteligentes |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="key-vault-contributor"></a>Colaborador do Key Vault
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite gerenciar cofres de chaves, mas não acessá-los. |
> | **ID** | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
> | Microsoft.KeyVault/* |  |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | Microsoft.KeyVault/locations/deletedVaults/purge/action | Limpar um cofre de chaves com exclusão reversível |
> | Microsoft.KeyVault/hsmPools/* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="lab-creator"></a>Criador de laboratório
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite a você criar, gerenciar e excluir os laboratórios gerenciados nas contas de laboratório do Azure. |
> | **ID** | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.LabServices/labAccounts/*/read |  |
> | Microsoft.LabServices/labAccounts/createLab/action | Criar um laboratório em uma conta de laboratório. |
> | Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action |  |
> | Microsoft.LabServices/labAccounts/getRegionalAvailability/action | Obter informações sobre disponibilidade regional para cada categoria de tamanho configurado em uma conta de laboratório |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="log-analytics-contributor"></a>Colaborador do Log Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | O Colaborador do Log Analytics pode ler todos os dados de monitoramento e editar as configurações de monitoramento. A edição das configurações de monitoramento inclui a adição da extensão da VM às VMs, leitura das chaves da conta de armazenamento para poder configurar a coleção de logs do Armazenamento do Microsoft Azure, criação e configuração de contas de Automação, adição de soluções e configuração do diagnóstico do Azure em todos os recursos do Azure. |
> | **ID** | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | **Ações** |  |
> | */leitura | Ler recursos de todos os tipos, exceto segredos. |
> | Microsoft.Automation/automationAccounts/* |  |
> | Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Listar as chaves de acesso das contas de armazenamento. |
> | Microsoft.Compute/virtualMachines/extensions/* |  |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
> | Microsoft.Insights/diagnosticSettings/* | Criar, atualizar ou ler a configuração de diagnóstico do Analysis Server |
> | Microsoft.OperationalInsights/* |  |
> | Microsoft.OperationsManagement/* |  |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Retornar as chaves de acesso da conta de armazenamento especificada. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="log-analytics-reader"></a>Leitor do Log Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Um Leitor do Log Analytics pode exibir e pesquisar todos os dados de monitoramento além de exibir as configurações de monitoramento, incluindo a exibição da configuração do diagnóstico do Azure em todos os recursos do Azure. |
> | **ID** | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **Ações** |  |
> | */leitura | Ler recursos de todos os tipos, exceto segredos. |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Pesquisar usando o novo mecanismo. |
> | Microsoft.OperationalInsights/workspaces/search/action | Executar uma consulta de pesquisa |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Recupera as chaves compartilhadas do workspace. Essas chaves são usadas para conectar agentes do Insights Operacionais da Microsoft ao workspace. |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="logic-app-contributor"></a>Colaborador de aplicativo lógico
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite gerenciar o aplicativo lógico, mas não o acesso a ele. |
> | **ID** | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Listar as chaves de acesso das contas de armazenamento. |
> | Microsoft.ClassicStorage/storageAccounts/read | Retornar a conta de armazenamento com a conta fornecida. |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
> | Microsoft.Insights/diagnosticSettings/* | Criar, atualizar ou ler a configuração de diagnóstico do Analysis Server |
> | Microsoft.Insights/logdefinitions/* | Essa permissão é necessária para usuários que precisam de acesso aos Logs de atividade por meio do portal. Liste as categorias de log no Log de Atividades. |
> | Microsoft.Insights/metricDefinitions/* | Ler definições de métricas (lista de tipos de métrica disponíveis para um recurso). |
> | Microsoft.Logic/* | Gerenciar recursos de Aplicativos Lógicos. |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/operationresults/read | Obter os resultados da operação da assinatura. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Storage/storageAccounts/listkeys/action | Retornar as chaves de acesso da conta de armazenamento especificada. |
> | Microsoft.Storage/storageAccounts/read | Retornar a lista de contas de armazenamento ou obter as propriedades da conta de armazenamento especificada. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft.Web/connectionGateways/* | Criar e gerenciar um Gateway de Conexão. |
> | Microsoft.Web/connections/* | Criar e gerenciar uma Conexão. |
> | Microsoft.Web/customApis/* | Criar e gerenciar uma API Personalizada. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Obter as propriedades em um Plano do Serviço de Aplicativo |
> | Microsoft.Web/sites/functions/listSecrets/action | Listar segredos de funções de aplicativos Web. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="logic-app-operator"></a>Operador de aplicativo lógico
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite que você leia, habilite e desabilite o aplicativo lógico. |
> | **ID** | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/*/read | Ler regras de alerta do Insights |
> | Microsoft.Insights/diagnosticSettings/*/read | Obter configurações de diagnóstico para Aplicativos Lógicos |
> | Microsoft.Insights/metricDefinitions/*/read | Obter as métricas disponíveis para Aplicativos Lógicos. |
> | Microsoft.Logic/*/read | Ler recursos de Aplicativos Lógicos. |
> | Microsoft.Logic/workflows/disable/action | Desabilitar o fluxo de trabalho. |
> | Microsoft.Logic/workflows/enable/action | Permitir o fluxo de trabalho. |
> | Microsoft.Logic/workflows/validate/action | Valida o fluxo de trabalho. |
> | Microsoft.Resources/deployments/operations/read | Obter ou lista operações de implantação. |
> | Microsoft.Resources/subscriptions/operationresults/read | Obter os resultados da operação da assinatura. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft.Web/connectionGateways/*/read | Ler gateways de conexão. |
> | Microsoft.Web/connections/*/read | Ler conexões. |
> | Microsoft.Web/customApis/*/read | Ler API personalizada. |
> | Microsoft.Web/serverFarms/read | Obter as propriedades em um Plano do Serviço de Aplicativo |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="managed-application-operator-role"></a>Função do Operador de Aplicativos Gerenciado
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite que você leia e execute as ações nos recursos de aplicativo gerenciado |
> | **ID** | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | **Ações** |  |
> | */leitura | Ler recursos de todos os tipos, exceto segredos. |
> | Microsoft.Solutions/applications/read | Recuperar uma lista de aplicativos. |
> | Microsoft.Solutions/*/action |  |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="managed-applications-reader"></a>Leitor de aplicativos gerenciados
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite ler os recursos de um aplicativo gerenciado e solicitar acesso JIT. |
> | **ID** | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | **Ações** |  |
> | */leitura | Ler recursos de todos os tipos, exceto segredos. |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Solutions/jitRequests/* |  |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="managed-identity-contributor"></a>Colaborador de Identidade Gerenciada
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Criar, ler, atualizar e excluir a identidade atribuída pelo usuário |
> | **ID** | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | **Ações** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/write |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/delete |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="managed-identity-operator"></a>Operador de Identidade Gerenciada
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Ler e atribuir identidade atribuída pelo usuário |
> | **ID** | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Ações** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="management-group-contributor"></a>Colaborador do Grupo de Gerenciamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Função de Colaborador do Grupo de Gerenciamento |
> | **ID** | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | **Ações** |  |
> | Microsoft.Management/managementGroups/delete | Excluir um grupo de gerenciamento. |
> | Microsoft.Management/managementGroups/read | Listar grupos de gerenciamento para o usuário autenticado. |
> | Microsoft.Management/managementGroups/subscriptions/delete | Desassociar a assinatura do grupo de gerenciamento. |
> | Microsoft.Management/managementGroups/subscriptions/write | Associar a assinatura existente ao grupo de gerenciamento. |
> | Microsoft.Management/managementGroups/write | Criar ou atualizar um grupo de gerenciamento. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="management-group-reader"></a>Leitor do Grupo de Gerenciamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Função de Leitor do Grupo de Gerenciamento |
> | **ID** | ac63b705-f282-497d-ac71-919bf39d939d |
> | **Ações** |  |
> | Microsoft.Management/managementGroups/read | Listar grupos de gerenciamento para o usuário autenticado. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="monitoring-contributor"></a>Colaborador de monitoramento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Pode ler todos os dados de monitoramento e editar configurações de monitoramento. Consulte também [Introdução às funções, permissões e segurança com o Azure Monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **ID** | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | **Ações** |  |
> | */leitura | Ler recursos de todos os tipos, exceto segredos. |
> | Microsoft.AlertsManagement/alerts/* |  |
> | Microsoft.AlertsManagement/alertsSummary/* |  |
> | Microsoft.Insights/actiongroups/* |  |
> | Microsoft.Insights/activityLogAlerts/* |  |
> | Microsoft.Insights/AlertRules/* | Leitura/gravação/exclusão de regras de alerta. |
> | Microsoft.Insights/components/* | Leia/grave/exclua componentes do Application Insights. |
> | Microsoft.Insights/DiagnosticSettings/* | Leitura/gravação/exclusão de configurações de diagnóstico. |
> | Microsoft.Insights/eventtypes/* | Listar eventos do Log de atividades (eventos de gerenciamento) em um assinatura. Essa permissão é aplicável ao acesso programático e ao portal para o Log de atividades. |
> | Microsoft.Insights/LogDefinitions/* | Essa permissão é necessária para usuários que precisam de acesso aos Logs de atividade por meio do portal. Liste as categorias de log no Log de Atividades. |
> | Microsoft.Insights/metricalerts/* |  |
> | Microsoft.Insights/MetricDefinitions/* | Ler definições de métricas (lista de tipos de métrica disponíveis para um recurso). |
> | Microsoft.Insights/Metrics/* | Ler as métricas para um recurso. |
> | Microsoft.Insights/Register/Action | Registrar o provedor do Microsoft Insights |
> | Microsoft.Insights/scheduledqueryrules/* |  |
> | Microsoft.Insights/webtests/* | Leia/grave/exclua testes da Web do Application Insights. |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Pacotes de solução de análise de log de leitura/gravação/exclusão. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* | Análise de log de leitura/gravação/exclusão de pesquisas salvas. |
> | Microsoft.OperationalInsights/workspaces/search/action | Executar uma consulta de pesquisa |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Recupera as chaves compartilhadas do workspace. Essas chaves são usadas para conectar agentes do Insights Operacionais da Microsoft ao workspace. |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Log de leitura/gravação/exclusão analytics as configurações de armazenamento. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft.WorkloadMonitor/monitors/* |  |
> | Microsoft.WorkloadMonitor/notificationSettings/* |  |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="monitoring-metrics-publisher"></a>Publicador de Métricas de Monitoramento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Habilita a publicação de métricas com base nos recursos do Azure |
> | **ID** | 3913510d-42f4-4e42-8a64-420c390055eb |
> | **Ações** |  |
> | Microsoft.Insights/Register/Action | Registrar o provedor do Microsoft Insights |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Insights/Metrics/Write | Gravar métricas |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="monitoring-reader"></a>Leitor de monitoramento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Pode ler todos os dados de monitoramento (métricas, logs, etc). Consulte também [Introdução às funções, permissões e segurança com o Azure Monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **ID** | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | **Ações** |  |
> | */leitura | Ler recursos de todos os tipos, exceto segredos. |
> | Microsoft.OperationalInsights/workspaces/search/action | Executar uma consulta de pesquisa |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="network-contributor"></a>Colaborador de rede
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite gerenciar redes, mas não acessá-las. |
> | **ID** | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.Network/* | Criar e gerenciar redes |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="new-relic-apm-account-contributor"></a>Colaborador de Conta APM do New Relic
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite que você gerencie contas e aplicativos do Gerenciamento de desempenho de aplicativos da New Relic, mas não tem acesso a eles. |
> | **ID** | 5d28c62d-5b37-4476-8438-e587778df237 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | NewRelic.APM/accounts/* |  |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="reader-and-data-access"></a>Acesso a Dados e Leitor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite que você exiba tudo, mas não permitirá que exclua ou crie uma conta de armazenamento ou um recurso contido. Ele também permitirá o acesso de leitura/gravação a todos os dados contidos em uma conta de armazenamento por meio de acesso às chaves de conta de armazenamento. |
> | **ID** | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Retornar as chaves de acesso da conta de armazenamento especificada. |
> | Microsoft.Storage/storageAccounts/read | Retornar a lista de contas de armazenamento ou obter as propriedades da conta de armazenamento especificada. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="redis-cache-contributor"></a>Colaborador do Cache Redis
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite gerenciar caches Redis, mas não acessá-los. |
> | **ID** | e0f68234-74aa-48ED-b826-c38b57376e17 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Cache/redis/* | Criar e gerenciar caches Redis |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="resource-policy-contributor-preview"></a>Colaborador da Política de Recursos (Versão prévia)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | (Versão prévia) Os usuários com aterramento da EA, com direitos para criar/modificar a política de recursos, criam um tíquete de suporte e recursos/hierarquia de leitura. |
> | **ID** | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | **Ações** |  |
> | */leitura | Ler recursos de todos os tipos, exceto segredos. |
> | Microsoft.Authorization/policyAssignments/* | Criar e gerenciar atribuições de política |
> | Microsoft.Authorization/policyDefinitions/* | Criar e gerenciar definições de política |
> | Microsoft.Authorization/policysetdefinitions/* | Criar e gerenciar conjuntos de política |
> | Microsoft.PolicyInsights/* |  |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="scheduler-job-collections-contributor"></a>Colaborador de Coleções de Trabalho do Agendador
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite gerenciar as coleções de trabalhos do Agendador, mas não acessá-las. |
> | **ID** | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Scheduler/jobcollections/* | Criar e gerenciar coleções de trabalhos |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="search-service-contributor"></a>Colaborador do Serviço de Pesquisa
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite gerenciar serviços de pesquisa, mas não acessá-las. |
> | **ID** | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Search/searchServices/* | Criar e gerenciar serviços de pesquisa |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="security-admin"></a>Administrador de Segurança
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Somente na Central de Segurança: Pode exibir políticas de segurança, exibir estados de segurança, editar políticas de segurança, exibir alertas e recomendações, ignorar alertas e recomendações |
> | **ID** | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Authorization/policyAssignments/* | Criar e gerenciar atribuições de política |
> | Microsoft.Authorization/policyDefinitions/* | Criar e gerenciar definições de política |
> | Microsoft.Authorization/policySetDefinitions/* | Criar e gerenciar conjuntos de política |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.Management/managementGroups/read | Listar grupos de gerenciamento para o usuário autenticado. |
> | Microsoft.operationalInsights/workspaces/*/read | Exibir dados de análise de log |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Security/* |  |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="security-manager-legacy"></a>Gerenciador de Segurança (Herdado)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Esta é uma função herdada. Em vez disso, use o Administrador de Segurança |
> | **ID** | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.ClassicCompute/*/read | Ler informações de configuração de máquinas virtuais clássicas |
> | Microsoft.ClassicCompute/virtualMachines/*/write | Escrever configurações para máquinas virtuais clássicas |
> | Microsoft.ClassicNetwork/*/read | Ler informações de configuração sobre a rede clássica |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Security/* | Criar e gerenciar políticas e componentes de segurança |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="security-reader"></a>Leitor de segurança
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Somente na Central de Segurança: Pode exibir recomendações e alertas, exibir políticas de segurança, exibir estados de segurança, mas não pode fazer alterações |
> | **ID** | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.operationalInsights/workspaces/*/read | Exibir dados de análise de log |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Security/*/read | Ler componentes de segurança e políticas |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft.Management/managementGroups/read | Listar grupos de gerenciamento para o usuário autenticado. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="site-recovery-contributor"></a>Colaborador do Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite gerenciar o serviço do Azure Site Recovery, exceto a criação de cofre e atribuição de função |
> | **ID** | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp é uma operação interna usada pelo serviço |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp é uma operação interna usada pelo serviço |
> | Microsoft.RecoveryServices/Vaults/certificates/write | A operação Atualizar certificado do recurso atualiza o certificado de credencial de cofre/recurso. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Criar e gerenciar informações estendidas relacionadas ao cofre |
> | Microsoft.RecoveryServices/Vaults/read | A operação Obter cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre' |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Criar e gerenciar identidades registradas |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Criar ou atualizar as configurações de alerta de replicação |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Ler quaisquer eventos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/* | Criar e gerenciar malhas de replicação |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Criar e gerenciar trabalhos de replicação |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/* | Criar e gerenciar políticas de replicação |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Criar e gerenciar planos de recuperação |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* | Criar e gerenciar a configuração de armazenamento do cofre de Serviços de Recuperação |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Retornar os detalhes de uso para um cofre dos Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A operação de token do cofre pode ser usada a fim de obter o token do cofre para operações de back-end no nível do cofre. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Obter os alertas para o cofre dos Serviços de Recuperação |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Storage/storageAccounts/read | Retornar a lista de contas de armazenamento ou obter as propriedades da conta de armazenamento especificada. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="site-recovery-operator"></a>Operador do Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite failover e failback, mas não executa outras operações de gerenciamento do Azure Site Recovery |
> | **ID** | 494ae006-db33-4328-bf46-533a6560a3ca |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp é uma operação interna usada pelo serviço |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp é uma operação interna usada pelo serviço |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
> | Microsoft.RecoveryServices/Vaults/read | A operação Obter cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre' |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A operação Obter resultados da operação pode ser usada para obter o status da operação e o resultado da operação enviada de forma assíncrona |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A operação Obter contêineres pode ser usada para obter os contêineres registrados para um recurso. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Ler configurações de alertas |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Ler quaisquer eventos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Verificar consistência da malha |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Ler quaisquer malhas |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Reassociar gateway |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Renovar Certificado para malha do Microsoft Azure |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Ler quaisquer redes |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Ler quaisquer mapeamentos de rede |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Ler quaisquer contêineres de proteção |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Ler quaisquer itens que podem ser protegidos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Aplicar ponto de recuperação |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Confirmação de failover |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Failover planejado |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Ler quaisquer itens protegidos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Ler quaisquer pontos de recuperação de replicação |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Reparar replicação |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Proteger item protegido novamente |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Failover de Teste |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Limpeza do Failover de teste |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Failover |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Atualizar serviço de mobilidade |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Ler quaisquer mapeamentos de contêiner de proteção |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Ler qualquer provedores de Serviços de Recuperação do Microsoft Azure |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Atualizar provedor |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Ler quaisquer classificações de armazenamento |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Ler quaisquer mapeamentos de classificação de armazenamento |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Ler quaisquer vCenters |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Criar e gerenciar trabalhos de replicação |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Ler quaisquer políticas |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Plano de recuperação de confirmação de failover |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Plano de recuperação de failover planejado |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Ler quaisquer planos de recuperação |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Proteja plano de recuperação novamente |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Testar plano de recuperação de failover |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Testar plano de recuperação de limpeza do failover |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Plano de recuperação de failover |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Obter os alertas para o cofre dos Serviços de Recuperação |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Retornar os detalhes de uso para um cofre dos Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A operação de token do cofre pode ser usada a fim de obter o token do cofre para operações de back-end no nível do cofre. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Storage/storageAccounts/read | Retornar a lista de contas de armazenamento ou obter as propriedades da conta de armazenamento especificada. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="site-recovery-reader"></a>Leitor do Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite visualizar o status do Azure Site Recovery, mas não executar outras operações de gerenciamento |
> | **ID** | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp é uma operação interna usada pelo serviço |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A operação Obter Informações Estendidas obtém informações estendidas de um objeto que representa o recurso do Azure do tipo ?vault? |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Obter os alertas para o cofre dos Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/read | A operação Obter cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre' |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A operação Obter resultados da operação pode ser usada para obter o status da operação e o resultado da operação enviada de forma assíncrona |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A operação Obter contêineres pode ser usada para obter os contêineres registrados para um recurso. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Ler configurações de alertas |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Ler quaisquer eventos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Ler quaisquer malhas |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Ler quaisquer redes |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Ler quaisquer mapeamentos de rede |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Ler quaisquer contêineres de proteção |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Ler quaisquer itens que podem ser protegidos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Ler quaisquer itens protegidos |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Ler quaisquer pontos de recuperação de replicação |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Ler quaisquer mapeamentos de contêiner de proteção |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Ler qualquer provedores de Serviços de Recuperação do Microsoft Azure |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Ler quaisquer classificações de armazenamento |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Ler quaisquer mapeamentos de classificação de armazenamento |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Ler quaisquer vCenters |
> | Microsoft.RecoveryServices/vaults/replicationJobs/read | Ler todos os trabalhos |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Ler quaisquer políticas |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Ler quaisquer planos de recuperação |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Retornar os detalhes de uso para um cofre dos Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A operação de token do cofre pode ser usada a fim de obter o token do cofre para operações de back-end no nível do cofre. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="spatial-anchors-account-contributor"></a>Colaborador da Conta de Âncoras Espaciais
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite que você gerencie âncoras espaciais em sua conta, mas não que as exclua |
> | **ID** | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | **Ações** |  |
> | *Nenhum* |  |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Criar âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Descobrir as próximas âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Obter propriedades espaciais âncoras |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Localize as âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Enviar dados de diagnóstico para ajudar a melhorar a qualidade do serviço âncoras espacial do Azure |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Atualizar propriedades espaciais âncoras |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="spatial-anchors-account-owner"></a>Proprietário da Conta de Âncoras Espaciais
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite gerenciar âncoras espaciais em sua conta, inclusive excluí-las |
> | **ID** | 70bbe301-9835-447d-afdd-19eb3167307c |
> | **Ações** |  |
> | *Nenhum* |  |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Criar âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | Excluir as âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Descobrir as próximas âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Obter propriedades espaciais âncoras |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Localize as âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Enviar dados de diagnóstico para ajudar a melhorar a qualidade do serviço âncoras espacial do Azure |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Atualizar propriedades espaciais âncoras |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="spatial-anchors-account-reader"></a>Leitor da Conta de Âncoras Espaciais
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite localizar e ler propriedades de âncoras espaciais em sua conta |
> | **ID** | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Ações** |  |
> | *Nenhum* |  |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Descobrir as próximas âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Obter propriedades espaciais âncoras |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Localize as âncoras espaciais |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Enviar dados de diagnóstico para ajudar a melhorar a qualidade do serviço âncoras espacial do Azure |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="sql-db-contributor"></a>Colaborador do banco de dados SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite gerenciar Bancos de Dados SQL, mas não acessá-los. Além disso, não é possível gerenciar as políticas relacionadas à segurança ou respectivos servidores SQL pai. |
> | **ID** | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/databases/* | Criar e gerenciar bancos de dados SQL |
> | Microsoft.Sql/servers/read | Retornar a lista de servidores ou obter as propriedades para o servidor especificado. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft.Insights/metrics/read | Ler métrica |
> | Microsoft.Insights/metricDefinitions/read | Ler definições de métrica |
> | **NotActions** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Editar políticas de auditoria |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Editar configurações de auditoria |
> | Microsoft.Sql/servers/databases/auditRecords/read | Recuperar os registros de auditoria do blob do banco de dados |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Editar políticas de conexão |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Editar políticas de mascaramento dos dados |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Editar políticas de alerta de segurança |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Editar métricas de segurança |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="sql-security-manager"></a>Gerenciador de Segurança do SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite você gerenciar as políticas relacionadas à segurança de servidores e bancos de dados SQL, mas não acessá-los. |
> | **ID** | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler autorização da Microsoft |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Adicionar recursos como conta de armazenamento ou banco de dados SQL a uma sub-rede. Não podem gerar alertas. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Criar e gerenciar políticas de auditoria do servidor SQL |
> | Microsoft.Sql/servers/auditingSettings/* | Criar e gerenciar a configuração de auditoria do servidor SQL |
> | Microsoft.Sql/servers/extendedAuditingSettings/read | Recuperar detalhes da política de auditoria de blob de servidor estendida configurada em um determinado servidor |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Criar e gerenciar políticas de auditoria de banco de dados do servidor SQL |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Criar e gerenciar configurações de auditoria de banco de dados do servidor SQL |
> | Microsoft.Sql/servers/databases/auditRecords/read | Ler registros de auditoria |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Criar e gerenciar políticas de conexão de banco de dados do servidor SQL |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Criar e gerenciar políticas de mascaramento de dados do banco de dados do servidor SQL |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Recuperar detalhes da política de auditoria de blob estendida configurada em um determinado banco de dados |
> | Microsoft.Sql/servers/databases/read | Retornar a lista de bancos de dados ou obter as propriedades para o banco de dados especificado. |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/read | Obter um esquema de banco de dados. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Obtenha uma coluna de banco de dados. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/read | Obtém uma tabela de banco de dados. |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Criar e gerenciar políticas de alerta de segurança do banco de dados do servidor SQL |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Criar e gerenciar métricas de segurança do banco de dados do servidor SQL |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/firewallRules/* |  |
> | Microsoft.Sql/servers/read | Retornar a lista de servidores ou obter as propriedades para o servidor especificado. |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Criar e gerenciar políticas de alerta de segurança de servidor SQL |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="sql-server-contributor"></a>Colaborador do SQL Server
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite gerenciar servidores e Bancos de Dados SQL, mas não acessá-los, nem as políticas relacionadas à segurança. |
> | **ID** | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/* | Criar e gerenciar servidores SQL |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft.Insights/metrics/read | Ler métrica |
> | Microsoft.Insights/metricDefinitions/read | Ler definições de métrica |
> | **NotActions** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Editar políticas de auditoria do servidor SQL |
> | Microsoft.Sql/servers/auditingSettings/* | Editar configurações de auditoria do servidor SQL |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Editar políticas de auditoria de banco de dados do servidor SQL |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Editar configurações de auditoria de banco de dados do servidor SQL |
> | Microsoft.Sql/servers/databases/auditRecords/read | Ler registros de auditoria |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Editar políticas de conexão de banco de dados do servidor SQL |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Editar políticas de mascaramento de banco de dados do servidor SQL |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Editar políticas de alerta de segurança de banco de dados do servidor SQL |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Editar métricas de segurança de banco de dados do servidor SQL |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Editar políticas de alerta de segurança de servidor SQL |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="storage-account-contributor"></a>Colaborador da Conta de Armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite um gerenciamento de contas de armazenamento. Não fornece acesso aos dados na conta de armazenamento. |
> | **ID** | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler todas as autorizações |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
> | Microsoft.Insights/diagnosticSettings/* | Gerenciar configurações de diagnóstico |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Adicionar recursos como conta de armazenamento ou banco de dados SQL a uma sub-rede. Não podem gerar alertas. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Storage/storageAccounts/* | Criar e gerenciar contas de armazenamento |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="storage-account-key-operator-service-role"></a>Função do Serviço de Operador da Chave da Conta de Armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite listar e regenerar chaves de acesso da conta de armazenamento. |
> | **ID** | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/listkeys/action | Retorne as chaves de acesso da conta de armazenamento especificado. |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | Regenere as chaves de acesso da conta de armazenamento especificado. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="storage-blob-data-contributor"></a>Colaborador de Dados do Storage Blob
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Ler, gravar e excluir blobs e contêineres de armazenamento do Azure. Para saber quais ações são necessárias para uma operação de dados específico, consulte [permissões para chamar o blob e fila de operações de dados](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations). |
> | **ID** | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Exclua um contêiner. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Retorne um contêiner ou uma lista de contêineres. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Modifica metadados ou nas propriedades de um contêiner. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Excluir um blob. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Retorne uma lista de blobs ou de um blob. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Grave em um blob. |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="storage-blob-data-owner"></a>Proprietário de Dados do Storage Blob
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Fornece acesso completo aos dados, incluindo a atribuição de controle de acesso do POSIX e contêineres de blob de armazenamento do Azure. Para saber quais ações são necessárias para uma operação de dados específico, consulte [permissões para chamar o blob e fila de operações de dados](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations). |
> | **ID** | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/* | Permissões totais em contêineres.  |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/* | Permissões totais em blobs. |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="storage-blob-data-reader"></a>Leitor de Dados do Storage Blob
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Ler e listar blobs e contêineres de armazenamento do Azure. Para saber quais ações são necessárias para uma operação de dados específico, consulte [permissões para chamar o blob e fila de operações de dados](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations). |
> | **ID** | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Retorne um contêiner ou uma lista de contêineres. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Retorne uma lista de blobs ou de um blob. |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="storage-queue-data-contributor"></a>Colaborador de Dados da Fila de Armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Ler, gravar e excluir filas de armazenamento do Azure e mensagens da fila. Para saber quais ações são necessárias para uma operação de dados específico, consulte [permissões para chamar o blob e fila de operações de dados](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations). |
> | **ID** | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Exclua uma fila. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Retorne uma fila ou uma lista de filas. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write | Modifica as propriedades ou metadados de fila. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Exclua uma ou mais mensagens de uma fila. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Inspecionar ou recuperar uma ou mais mensagens de uma fila. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Adicione uma mensagem para uma fila. |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="storage-queue-data-message-processor"></a>Processador de Mensagens de Dados da Fila de Armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Inspecionar, recuperar e excluir mensagens de uma fila de armazenamento do Azure. Para saber quais ações são necessárias para uma operação de dados específico, consulte [permissões para chamar o blob e fila de operações de dados](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations). |
> | **ID** | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | **Ações** |  |
> | *Nenhum* |  |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Inspecionar uma mensagem. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Recuperar e excluir uma mensagem. |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="storage-queue-data-message-sender"></a>Remetente da Mensagem de Dados da Fila de Armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Adicione mensagens a uma fila do armazenamento do Azure. Para saber quais ações são necessárias para uma operação de dados específico, consulte [permissões para chamar o blob e fila de operações de dados](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations). |
> | **ID** | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | **Ações** |  |
> | *Nenhum* |  |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Adicione uma mensagem para uma fila. |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="storage-queue-data-reader"></a>Leitor de Dados da Fila de Armazenamento
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Ler e listar as filas do armazenamento do Azure e mensagens da fila. Para saber quais ações são necessárias para uma operação de dados específico, consulte [permissões para chamar o blob e fila de operações de dados](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations). |
> | **ID** | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Ações** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Retornar uma fila ou uma lista de filas. |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Inspecionar ou recuperar uma ou mais mensagens de uma fila. |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="support-request-contributor"></a>Colaborador de solicitação de suporte
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite criar e gerenciar Solicitações de Suporte |
> | **ID** | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler autorização |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="traffic-manager-contributor"></a>Colaborador do Gerenciador de Tráfego
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite gerenciar perfis do Gerenciador de Tráfego, mas não permite controlar quem tem acesso a eles. |
> | **ID** | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler funções e atribuições de função |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
> | Microsoft.Network/trafficManagerProfiles/* |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="user-access-administrator"></a>Administrador de Acesso do Usuário
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite que você gerencie o acesso do usuário aos recursos do Azure. |
> | **ID** | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Ações** |  |
> | */leitura | Ler recursos de todos os tipos, exceto segredos. |
> | Microsoft.Authorization/* | Gerenciar autorização |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="virtual-machine-administrator-login"></a>Logon de administrador da Máquina Virtual
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Máquinas Virtuais do Microsoft Azure no portal e logon como administrador |
> | **ID** | 1c0163c0-47E6-4577-8991-ea5c82e286e4 |
> | **Ações** |  |
> | Microsoft.Network/publicIPAddresses/read | Obter uma definição de endereço IP público. |
> | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Microsoft.Network/loadBalancers/read | Obter uma definição de balanceador de carga |
> | Microsoft.Network/networkInterfaces/read | Obter uma definição de adaptador de rede.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Faça logon em uma máquina virtual como um usuário normal |
> | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Faça logon em uma máquina virtual com os privilégios de administrador do Windows ou de usuário raiz do Linux |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="virtual-machine-contributor"></a>Colaborador de Máquina Virtual
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite gerenciar máquinas virtuais, mas não o acesso a elas, nem à rede virtual ou conta de armazenamento à qual estão conectadas. |
> | **ID** | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler autorização |
> | Microsoft.Compute/availabilitySets/* | Criar e gerenciar conjuntos de disponibilidade de computação |
> | Microsoft.Compute/locations/* | Criar e gerenciar locais de computação |
> | Microsoft.Compute/virtualMachines/* | Criar e gerenciar máquinas virtuais |
> | Microsoft.Compute/virtualMachineScaleSets/* | Criar e gerenciar conjuntos de escala de máquina virtual |
> | Microsoft.DevTestLab/schedules/* |  |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Une um pool de endereços de back-end de gateway de aplicativo. Não podem gerar alertas. |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Une um pool de endereços de back-end de Balanceador de carga. Não podem gerar alertas. |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Une um balanceador de carga pool NAT de entrada. Não podem gerar alertas. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Une uma regra de nat de entrada do balanceador de carga. Não podem gerar alertas. |
> | Microsoft.Network/loadBalancers/probes/join/action | Permite usar investigações de um balanceador de carga. Por exemplo, com essa permissão, a propriedade healthProbe do conjunto de dimensionamento de VM pode referenciar a investigação. Não podem gerar alertas. |
> | Microsoft.Network/loadBalancers/read | Obter uma definição de balanceador de carga |
> | Microsoft.Network/locations/* | Criar e gerenciar locais de rede |
> | Microsoft.Network/networkInterfaces/* | Criar e gerenciar as interfaces de rede |
> | Microsoft.Network/networkSecurityGroups/join/action | Une um grupo de segurança de rede. Não podem gerar alertas. |
> | Microsoft.Network/networkSecurityGroups/read | Obter uma definição de um grupo de segurança de rede |
> | Microsoft.Network/publicIPAddresses/join/action | Une um endereço ip público. Não podem gerar alertas. |
> | Microsoft.Network/publicIPAddresses/read | Obter uma definição de endereço IP público. |
> | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Une a uma rede virtual. Não podem gerar alertas. |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Criar uma Intenção de Proteção de backup |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Retornar detalhes do objeto do item protegido |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Criar um item protegido de backup |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Retornar todas as políticas de proteção |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Criar uma política de proteção |
> | Microsoft.RecoveryServices/Vaults/read | A operação Obter cofre obtém um objeto que representa o recurso do Azure do tipo 'cofre' |
> | Microsoft.RecoveryServices/Vaults/usages/read | Retornar os detalhes de uso para um cofre dos Serviços de Recuperação. |
> | Microsoft.RecoveryServices/Vaults/write | A operação Criar cofre cria um recurso do Azure do tipo 'cofre' |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.SqlVirtualMachine/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Retornar as chaves de acesso da conta de armazenamento especificada. |
> | Microsoft.Storage/storageAccounts/read | Retornar a lista de contas de armazenamento ou obter as propriedades da conta de armazenamento especificada. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="virtual-machine-user-login"></a>Logon de usuário da Máquina Virtual
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Visualize as Máquinas Virtuais do Microsoft Azure no portal e faça logon como usuário. |
> | **ID** | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Ações** |  |
> | Microsoft.Network/publicIPAddresses/read | Obter uma definição de endereço IP público. |
> | Microsoft.Network/virtualNetworks/read | Obter a definição de rede virtual |
> | Microsoft.Network/loadBalancers/read | Obter uma definição de balanceador de carga |
> | Microsoft.Network/networkInterfaces/read | Obter uma definição de adaptador de rede.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Faça logon em uma máquina virtual como um usuário normal |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="web-plan-contributor"></a>Colaborador do Plano de Web
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite gerenciar os planos da Web para sites, mas não o acesso a eles. |
> | **ID** | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler autorização |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft.Web/serverFarms/* | Criar e gerenciar farms de servidores |
> | Microsoft.Web/hostingEnvironments/Join/Action | Ingressa em um ambiente do serviço de aplicativo |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="website-contributor"></a>Colaborador do Site
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **DESCRIÇÃO** | Permite gerenciar sites (não planos da Web), mas não acessá-los. |
> | **ID** | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Ações** |  |
> | Microsoft.Authorization/*/read | Ler autorização |
> | Microsoft.Insights/alertRules/* | Criar e gerenciar regras de alerta do Insights |
> | Microsoft.Insights/components/* | Criar e gerenciar componentes do Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Obter os status de disponibilidade para todos os recursos no escopo especificado |
> | Microsoft.Resources/deployments/* | Criar e gerenciar implantações do grupo de recursos |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Obter ou listar de grupos de recursos. |
> | Microsoft.Support/* | Criar e gerenciar tíquetes de suporte |
> | Microsoft.Web/certificates/* | Criar e gerenciar certificados de site da Web |
> | Microsoft.Web/listSitesAssignedToHostName/read | Obter nomes dos sites atribuídos ao nome de host. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Obter as propriedades em um Plano do Serviço de Aplicativo |
> | Microsoft.Web/sites/* | Criar e gerenciar sites (a criação de sites também requer permissões de gravação para o Plano do Serviço de Aplicativo associado) |
> | **NotActions** |  |
> | *Nenhum* |  |
> | **DataActions** |  |
> | *Nenhum* |  |
> | **NotDataActions** |  |
> | *Nenhum* |  |

## <a name="next-steps"></a>Próximas etapas

- [Funções personalizadas para recursos do Azure](custom-roles.md)
- [Gerenciar o acesso aos recursos do Azure usando o RBAC e o portal do Azure](role-assignments-portal.md)
- [Permissões na Central de Segurança do Azure](../security-center/security-center-permissions.md)
