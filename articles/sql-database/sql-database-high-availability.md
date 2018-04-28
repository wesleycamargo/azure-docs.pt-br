---
title: Alta disponibilidade - Serviço do Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba mais sobre os recursos e capacidades de alta disponibilidade do serviço de Banco de Dados SQL do Microsoft Azure
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.topic: article
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: e85db04206927eaf17cf52c11b536c75a47a088e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/19/2018
---
# <a name="high-availability-and-azure-sql-database"></a>Banco de dados SQL do Microsoft Azure e de alta disponibilidade
Desde o início da oferta de PaaS do Banco de Dados SQL do Azure, a Microsoft prometeu aos seus clientes de que a HA (Alta Disponibilidade) seria compilada no serviço e os clientes não seriam obrigados a operar, adicionar lógica especial ou tomar decisões em torno de HA. A Microsoft mantém o controle total sobre a configuração e operação do sistema de HA, oferecendo um SLA aos clientes. O SLA de HA aplica-se a um Banco de Dados SQL em uma região e não oferece proteção nos casos de uma falha total da região devido a fatores fora do controle razoável da Microsoft (por exemplo, desastre natural, guerra, atos de terrorismo, tumultos, ação governamental ou uma falha de rede ou dispositivo externa aos data centers da Microsoft, inclusive em sites de clientes ou entre sites de clientes e data center da Microsoft).

Para simplificar o problema de espaço de alta disponibilidade, a Microsoft usa as seguintes suposições:
1.  Falhas de hardware e software são inevitáveis
2.  A equipe operacional comete erros que levam a falhas
3.  As operações de manutenção planejadas causam interrupções 

Embora esses eventos individuais sejam incomuns em escala de nuvem, eles ocorrem toda semana, se não todos os dias. 

## <a name="fault-tolerant-sql-databases"></a>Bancos de dados SQL tolerantes a falhas
Os clientes estão mais interessados na resiliência de seus próprios bancos de dados e menos interessados na resiliência do serviço de banco de dados SQL como um todo. 99,99% de tempo produtivo para um serviço é inútil se o "meu banco de dados" for parte do 0,01% dos bancos de dados que estão inativos. Cada banco de dados precisa ser tolerante a falhas, e a mitigação de falhas nunca deve resultar na perda de uma transação confirmada. 

Para dados, o Banco de Dados SQL usa armazenamento local (LS) com base em discos/VHDs anexados diretos e o armazenamento remoto (RS) com base em blobs de páginas de Armazenamento Premium do Azure. 
- O armazenamento local é usado nos bancos de dados Premium ou Comercialmente Crítico (versão prévia) e pools elásticos, que são projetados para aplicativos de OLTP críticos com altos requisitos de IOPS. 
- O armazenamento remoto é utilizado para camadas de serviço Básico e Standard, projetados para cargas de trabalho comerciais orientadas para o orçamento que exigem potência de computação e armazenamento para escalar de forma independente. Eles usam um único blob de páginas para arquivos de log e banco de dados, e mecanismos de replicação e failover de armazenamento interno.

Em ambos os casos, os mecanismos de replicação, detecção de falhas e failover do Banco de Dados SQL são totalmente automatizados e operam sem intervenção humana. Essa arquitetura foi projetada para garantir que os dados confirmados nunca serão perdidos e que a durabilidade dos dados tenha prioridade sobre tudo.

Principais benefícios:
- Os clientes recebem todos os benefícios dos bancos de dados replicados sem a necessidade de configurar ou fazer manutenção de hardware, software, sistemas operacionais ou ambientes de virtualização complicados.
- Propriedades ACID completas de bancos de dados relacionais são mantidas pelo sistema.
- Failovers são completamente automatizados sem perda dos dados confirmados.
- O roteamento de conexões para a réplica primária é gerenciado dinamicamente pelo serviço sem nenhuma lógica de aplicativo necessária.
- O alto nível de redundância automatizada é fornecido sem custo adicional.

> [!NOTE]
> A arquitetura de alta disponibilidade descrita está sujeita a alterações sem aviso prévio. 

## <a name="data-redundancy"></a>Redundância de dados

A solução de alta disponibilidade no Banco de Dados SQL é baseada na tecnologia de [Grupos de Disponibilidade AlwaysON](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) do SQL Server e funciona com bancos de dados LS e RS com diferenças mínimas. Na configuração LS, a tecnologia de grupo de disponibulidade AlwaysON é usada para persistência, enquanto na configuração RS, ela é usado para disponibilidade (baixo RTO pela replicação geográfica ativa). 

## <a name="local-storage-configuration"></a>Configuração de armazenamento local

Nessa configuração, cada banco de dados é colocado online pelo MS (serviço de gerenciamento) dentro do anel de controle. Uma réplica primária e pelo menos duas réplicas secundárias (conjunto de quorum) estão localizadas em um anel de locatário que abrange três subsistemas físicos independentes dentro do mesmo centro de dados. Todas as leituras e gravações são enviadas pelo gateway (GW) à réplica primária e as gravações são replicadas assincronamente para as réplicas secundárias. O Banco de Dados SQL usa um esquema de confirmação baseado em quorum em que os dados são gravados na réplica primária e em uma secundária antes de que a transação seja considerada confirmada.

O sistema de failover [Service Fabric](../service-fabric/service-fabric-overview.md) recompila réplicas automaticamente como falhas de nós e mantém a associação do conjunto de quorum conforme os nós saem e se unem ao sistema. A manutenção planejada é cuidadosamente coordenada para evitar que o conjunto de quorum fique abaixo da contagem mínima de réplicas (normalmente é 2). Esse modelo funciona bem para bancos de dados Premium e Comercialmente Crítico (versão prévia), mas requer redundância de componentes de computação e armazenamento e resulta em um custo mais alto.

## <a name="remote-storage-configuration"></a>Configuração de armazenamento remoto

Para configurações de armazenamento remoto (camadas Básico e Standard), exatamente uma cópia é mantida no armazenamento de blobs remoto, usando os recursos dos sistemas de armazenamento para detecção de bits corrompidos, redundância e durabilidade. 

A arquitetura de alta disponibilidade é ilustrada pelo diagrama a seguir:
 
![Arquitetura da alta disponibilidade](./media/sql-database-high-availability/high-availability-architecture.png)

## <a name="failure-detection-and-recovery"></a>Detecção e recuperação de falhas 
Um sistema distribuído em larga escala precisa de um sistema de detecção de falhas altamente confiável, que possa detectar falhas de forma confiável, rápida e de forma mais próxima possível do cliente. Para o Banco de Dados SQL, esse sistema se baseia no Azure Service Fabric. 

Com a réplica primária, ela fica imediatamente evidente se e quando a réplica primária falhar e o trabalho não puder continuar porque todas as leituras e gravações ocorreram primeiro na réplica primária. Esse processo de promover uma réplica secundária para o status da primária tem o objetivo de tempo de recuperação (RTO)=30 segundos e o objetivo de ponto de recuperação (RPO)=0. Para atenuar o impacto do RTO de 30 segundos, a melhor prática é tentar se reconectar várias vezes com um menor tempo de espera para tentativas de falha de conexão.

Quando uma réplica secundária falha, o banco de dados é reduzido a um conjunto de quorum mínimo, sem nenhum extra. O Service Fabric inicia o processo de reconfiguração semelhante ao processo que segue a falha da réplica primária; portanto, após uma breve espera para determinar se a falha é permanente, outra réplica secundária é criada. Em casos de estado fora de serviço temporário, como uma falha do sistema operacional ou uma atualização, uma nova réplica não é criada imediatamente para permitir que o nó com falha seja reiniciado. 

Para as configurações de armazenamento remoto, o Banco de Dados SQL usa a funcionalidade AlwaysON para os bancos de dados de failover durante atualizações. Para isso, uma nova instância do SQL é transferida com antecedência como parte do evento de atualização planejado, e anexa e recupera o arquivo do banco de dados do armazenamento remoto. No caso de falhas de processo ou outros eventos não planejados, o Windows Fabric gerencia a disponibilidade da instância e, como uma última etapa de recuperação, anexa o arquivo do banco de dados remoto.

## <a name="zone-redundant-configuration-preview"></a>Configuração com redundância de zona (versão prévia)

Por padrão, as réplicas do quorum para as configurações de armazenamento locais são criadas no mesmo datacenter. Com a introdução das [Zonas de Disponibilidade do Azure](../availability-zones/az-overview.md), é possível colocar as diferentes réplicas no quorum para diferentes zonas de disponibilidade na mesma região. Para eliminar um ponto único de falha, o anel de controle também é duplicado entre várias zonas como três GW (anéis de gateway). O roteamento para um anel de gateway específico é controlado pelo ATM [(Gerenciador de Tráfego do Microsoft Azure)](../traffic-manager/traffic-manager-overview.md). Como a configuração com redundância de zona não cria redundância de banco de dados adicional, o uso de Zonas de Disponibilidade nas camadas de serviço Premium ou Comercialmente Crítico (versão prévia) está disponível sem nenhum custo adicional. Ao selecionar um banco de dados com redundância de zona, você pode tornar seus bancos de dados Premium ou Comercialmente Crítico (versão prévia) resilientes a um conjunto muito maior de falhas, incluindo interrupções catastróficas do datacenter, sem quaisquer alterações na lógica de aplicativo. Além disso, é possível converter quaisquer pools ou bancos de dados Premium ou Comercialmente Crítico (versão prévia) existentes para a configuração com redundância de zona.

Como o quórum com redundância de zona tem réplicas em diferentes datacenters com alguma distância entre eles, a latência de rede aumentada pode aumentar o tempo de confirmação e, desse modo, afetar o desempenho de algumas cargas de trabalho OLTP. Sempre será possível retornar à configuração de única zona, desabilitando a configuração com redundância de zona. Esse processo é um tamanho de operação de dados e é semelhante à atualização do SLO (Objetivo do Nível de Serviço) regular. No final do processo, o pool ou banco de dados será migrado de um anel com redundância de zona para um anel de única zona ou vice-versa.

> [!IMPORTANT]
> Bancos de dados com redundância de zona e pools elásticos somente têm suporte nas camadas de serviço Premium e Comercialmente Crítico (versão prévia). Durante a visualização pública, os backups e registros de auditoria são armazenados no armazenamento RA-GRS e, portanto, podem não ser disponibilizados automaticamente no caso de uma interrupção de toda a zona. 

A versão com redundância de zona da arquitetura de alta disponibilidade é ilustrada pelo diagrama a seguir:
 
![Arquitetura de alta disponibilidade com redundância de zona](./media/sql-database-high-availability/high-availability-architecture-zone-redundant.png)

## <a name="read-scale-out"></a>Expansão de leitura
Conforme descrito, as camadas de serviço Premium e Comercialmente Crítico (versão prévia) utilizam os conjuntos de quorum e a tecnologia AlwaysON para Alta Disponibilidade em configurações com redundância de zona e única zona. Um dos benefícios do AlwasyON é que as réplicas estão sempre no estado consistente transicionalmente. Como as réplicas têm o mesmo nível de desempenho que o primário, o aplicativo pode aproveitar essa capacidade adicional para atender às cargas de trabalho somente leitura sem custo adicional (expansão de leitura). Dessa forma, as consultas somente leitura serão isoladas da carga de trabalho principal de leitura/gravação e não afetarão o desempenho. O recurso de expansão de leitura destina-se aos aplicativos que incluem cargas de trabalho somente leitura logicamente separadas, como análises e, portanto, poderiam aproveitar essa capacidade adicional sem conexão ao primário. 

Para usar o recurso Expansão de leitura com um determinado banco de dados, você deve habilitá-lo ao criar o banco de dados ou posteriormente, alterando a configuração usando o PowerShell invocando os cmdlets [et-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) ou [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) ou por meio da API REST do Azure Resource Manager usando o método [Bancos de dados - Criar ou Atualizar](/rest/api/sql/databases/createorupdate).

Após a Expansão de Leitura ser habilitada para um banco de dados, aplicativos que se conectam ao banco de dados serão direcionados para a réplica de leitura-gravação ou para uma réplica somente leitura desse banco de dados de acordo com a propriedade `ApplicationIntent` configurada na cadeia de conexão do aplicativo. Para obter informações sobre a propriedade `ApplicationIntent`, consulte [Especificando a intenção do aplicativo](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent) 

O recurso de Expansão de Leitura dá suporte à consistência de nível de sessão. Se a sessão somente leitura reconectar após uma causa de erro de conexão pela indisponibilidade de réplica, ela poderá ser redirecionada para uma réplica diferente. Embora improvável, isso pode resultar no processamento do conjunto de dados que está obsoleto. Da mesma forma, se um aplicativo gravar dados usando uma sessão de leitura/gravação e a ler imediatamente usando a sessão somente leitura, é possível que os novos dados não fiquem imediatamente visíveis.

## <a name="conclusion"></a>Conclusão
O Banco de Dados SQL do Azure está totalmente integrado com a plataforma do Azure e é altamente dependente do Service Fabric para detecção e recuperação de falhas, no Azure Storage Blob para proteção de dados e Zonas de Disponibilidade para tolerância a falhas mais elevada. Ao mesmo tempo, o Banco de Dados SQL do Azure alavanca totalmente a tecnologia Always On do SQL Server para replicação e failover. A combinação dessas tecnologias permite que os aplicativos percebam em sua totalidade os benefícios de um modelo de armazenamento misto e deem suporte aos SLAs mais exigentes. 

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as [Zonas de Disponibilidade do Azure](../availability-zones/az-overview.md)
- Saiba mais sobre o [Service Fabric](../service-fabric/service-fabric-overview.md)
- Saiba mais sobre o [Gerenciador de Tráfego do Microsoft Azure](../traffic-manager/traffic-manager-overview.md) 
