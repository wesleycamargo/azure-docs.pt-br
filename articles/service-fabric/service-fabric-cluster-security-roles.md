
<properties
   pageTitle="Segurança de cluster do Service Fabric: funções de cliente | Microsoft Azure"
   description="Este artigo descreve as duas funções de clientes e as permissões fornecidas para as funções." 
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="coreysa"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/13/2015"
   ms.author="subramar"/>



# Controle de acesso baseado em função (para clientes do Service Fabric)

O Service Fabric oferece suporte a dois tipos de controle de acesso diferentes para clientes conectados a um cluster do Service Fabric: administrador e usuário. O controle de acesso permite que o administrador de cluster limite o acesso a determinadas operações de cluster para diferentes grupos de usuários, tornando o cluster mais seguro. Os administradores têm acesso completo aos recursos de gerenciamento (incluindo recursos de leitura/gravação), e os usuários, por padrão, têm acesso somente de leitura ao gerenciamento (por exemplo, consulta) e a capacidade de resolver os aplicativos e serviços.

As duas funções de clientes (administrador ou cliente) são especificadas no momento da criação do cluster, por meio do fornecimento de certificados separados para cada um. Consulte [Segurança de cluster do Service Fabric](service-fabric-cluster-security.md) para obter detalhes sobre como configurar um cluster do Service Fabric.


## Configurações padrão de controle de acesso


O tipo de controle de acesso de administrador tem acesso total a todas as APIs FabricClient. Com ele é possível executar qualquer operação de leitura e gravação no cluster do Service Fabric, incluindo as seguintes:

### Operações de aplicativos e serviço 
* **CreateService**: criação de serviço 							
* **CreateServiceFromTemplate**: criação de serviço a partir do modelo 							
* **UpdateService**: atualizações de serviço 							
* **DeleteService**: exclusão de serviço 							
* **ProvisionApplicationType**: provisionamento do tipo de aplicativo 							
* **CreateApplication**: criação de aplicativos 							
* **DeleteApplication**: exclusão de aplicativos 							
* **UpgradeApplication**: iniciar ou interromper atualizações de aplicativo 							
* **UnprovisionApplicationType**: desprovisionar o tipo de aplicativo 							
* **MoveNextUpgradeDomain**: retomar as atualizações de aplicativo com um Domínio de Atualização explícito 							
* **ReportUpgradeHealth**: retomar as atualizações de aplicativo com o progresso de atualização atual 							
* **ReportHealth**: relatório de integridade 							
* **PredeployPackageToNode**: API de pré-implantação 							
* **CodePackageControl**: reiniciar pacotes de código 							
* **RecoverPartition**: recuperar uma partição 							
* **RecoverPartitions**: recuperar partições 							
* **RecoverServicePartitions**: recuperar partições de serviço 							
* **RecoverSystemPartitions**: recuperar partições de serviço do sistema 							


### Operações de cluster
* **ProvisionFabric**: provisionamento de manifesto de MSI e/ou de cluster 							
* **UpgradeFabric**: iniciar atualizações de cluster 							
* **UnprovisionFabric**: desprovisionamento de manifesto MSI e/ou de cluster 							
* **MoveNextFabricUpgradeDomain**: retomar as atualizações de cluster com um Domínio de Atualização explícito 							
* **ReportFabricUpgradeHealth**: retomar as atualizações de cluster com o progresso de atualização atual 							
* **StartInfrastructureTask**: iniciar tarefas de infraestrutura 							
* **FinishInfrastructureTask**: concluir tarefas de infraestrutura 							
* **InvokeInfrastructureCommand**: comandos de gerenciamento de tarefas de infraestrutura 							
* **ActivateNode**: ativação de um nó 							
* **DeactivateNode**: desativação de nó 							
* **DeactivateNodesBatch**: desativação de múltiplos nós 							
* **RemoveNodeDeactivations**: desativação de reversão em vários nós 							
* **GetNodeDeactivationStatus**: verificação do status de desativação 							
* **NodeStateRemoved**: relatar o estado do nó removido 							
* **ReportFault**: falha de relatórios 							
* **FileContent**: transferência de arquivos de cliente de armazenamento de imagem (externo ao cluster) 							
* **FileDownload**: inicialização de download de arquivos de cliente de armazenamento de imagem (externo ao cluster) 							
* **InternalList**: operação de lista de arquivos de cliente de armazenamento de imagem (interno) 							
* **Delete**: operação de exclusão do cliente de armazenamento de imagens 							
* **Upload**: operação de carregamento do cliente de armazenamento de imagens 							
* **NodeControl**: iniciar; interromper; e reiniciar nós 							
* **MoveReplicaControl**: mover réplicas de um nó para outro 							

### Operações diversas
* **Ping**: aplicar ping em cliente 							
* **Query**: todas as consultas permitidas
* **NameExists**: verificações de existência do URI de nomenclatura 							



O tipo de controle de acesso do usuário é limitados por padrão para as seguintes operações (o controle de acesso de administração também permite acesso a essas operações:

* **EnumerateSubnames**: nomenclatura de enumeração de URI 							
* **EnumerateProperties**: nomenclatura de enumeração de propriedade 							
* **PropertyReadBatch**: nomenclatura de operações de leitura de propriedade 							
* ****GetServiceDescription: notificações de serviço de sondagem longa e descrições do serviço de leitura
* **ResolveService**: resolução de serviço baseada em reclamações 							
* **ResolveNameOwner**: resolução de proprietário de URI de nomenclatura 							
* **ResolvePartition**: resolução de serviços do sistema 							
* **ServiceNotifications**: notificações de serviço baseadas em evento 							
* **GetUpgradeStatus**: status de atualização do aplicativo de pesquisa 							
* **GetFabricUpgradeStatus**: status de atualização do cluster de pesquisa 							
* **InvokeInfrastructureQuery**: tarefas da infraestrutura de consulta 							
* **List**: operação de lista de arquivos de cliente de armazenamento de imagem 							
* **ResetPartitionLoad**: redefinição de carga para um failoverUnit 							
* ****ToggleVerboseServicePlacementHealthReporting: alternar posicionamento do relatório de integridade do serviço detalhado

## Alterando as configurações padrão para funções do cliente

No arquivo de manifesto do cluster, é possível fornecer recursos de administração ao cliente, se for necessário. Você pode alterar os padrões acessando a opção **Configurações do Fabric* durante a [criação do cluster](service-fabric-cluster-creation-via-portal.md) e fornecendo as configurações nomeadas, conforme indicado acima, no campo **Nome**, e **admin, usuário** no campo Valor.

## Próximas etapas

[Segurança do Cluster de Malha de Serviço](service-fabric-cluster-security.md)

[Criação de cluster do Service Fabric](service-fabric-cluster-creation-via-portal.md)

<!---HONumber=Nov15_HO4-->