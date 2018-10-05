---
title: Alta disponibilidade - Serviço do Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba mais sobre os recursos e capacidades de alta disponibilidade do serviço de Banco de Dados SQL do Microsoft Azure
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlrab, sashan
manager: craigg
ms.date: 09/14/2018
ms.openlocfilehash: 9c06a028df098874a1ec12d83a362e01a5f4a711
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47161889"
---
# <a name="high-availability-and-azure-sql-database"></a>Banco de dados SQL do Microsoft Azure e de alta disponibilidade

O Azure SQL Database é uma Plataforma como Serviço de banco de dados altamente disponível que garante que seu banco de dados esteja ativo e em execução 99,99% do tempo, sem se preocupar com manutenção e tempos de inatividade. Esse é um processo de SQL Server Database Engine totalmente gerenciado hospedado na nuvem do Azure que garante que seu banco de dados SQL Server esteja sempre atualizado/com patch aplicado sem afetar sua carga de trabalho. Ao aplicar patch ou fazer failover de uma instância, o tempo de inatividade geralmente não será notado se você [empregar a lógica de repetição](sql-database-develop-overview.md#resiliency) no aplicativo. Se o tempo para concluir um failover for maior que 60 segundos, você deverá abrir um caso de suporte. O Banco de Dados SQL do Microsoft Azure pode se recuperar rapidamente, até nas circunstâncias mais críticas, garantindo que seus dados estejam sempre disponíveis.

A plataforma do Azure gerencia completamente cada Banco de Dados SQL do Azure e garante alta porcentagem de disponibilidade de dados sem perda de dados. O Azure controla automaticamente a aplicação de patches, potenciais falhas subjacentes de hardware, software ou rede, a implantação de correções de bug, failovers, upgrades de banco de dados e outras tarefas de manutenção. Engenheiros do SQL Server têm implementado as práticas recomendadas, garantindo que todas as operações de manutenção sejam concluídas em menos de 0,01% do tempo de vida útil de seu banco de dados. Essa arquitetura foi projetada para garantir que dados confirmados nunca sejam perdidos e que operações de manutenção sejam executadas sem afetar a carga de trabalho. Não há nenhuma janela de manutenção ou tempo de inatividade que deva exigir que você pare a carga de trabalho enquanto o banco de dados é atualizado ou está em manutenção. A alta disponibilidade interna no Banco de Dados SQL do Azure garante que o banco de dados nunca será um ponto único de falha em sua arquitetura de software.

Banco de dados SQL do Azure baseia-se na arquitetura de mecanismo de banco de dados do SQL Server que é ajustada para o ambiente de nuvem para garantir a disponibilidade de 99,99%, até mesmo no caso de falhas de infraestrutura. Há dois modelos de arquitetura de alta disponibilidade que são usados no Banco de Dados SQL do Azure (ambos garantindo a disponibilidade de 99,99%):
- O modelo da camada de serviço Standard/Uso Geral que se baseia em uma separação entre computação e armazenamento. Esse modelo de arquitetura se baseia em alta disponibilidade e confiabilidade de camada de armazenamento, mas ele pode ter alguma degradação de desempenho potencial durante as atividades de manutenção.
- O modelo da camada de serviço Premium/Comercialmente Crítica que se baseia em um cluster de processos do mecanismo de banco de dados. Esse modelo de arquitetura se baseia no fato de que há sempre um quorum de nós de mecanismo de banco de dados disponíveis e tem impacto mínimo no desempenho em sua carga de trabalho, mesmo durante as atividades de manutenção.

O Azure atualiza e aplica patches subjacentes ao sistema operacional, drivers e Mecanismo de BD do SQL Server de forma transparente com mínimo tempo de inatividade para usuários finais. O Banco de Dados SQL do Azure é executado na versão estável mais recente do Mecanismo de BD do SQL Server e do SO Windows, e a maioria dos usuários não perceberiam que as atualizações são executadas continuamente.

## <a name="standardgeneral-purpose-availability"></a>Disponibilidade de uso geral/padrão

A disponibilidade padrão se refere a 99,99% de SLA que são aplicados nas camadas de Uso Padrão/Básico/Geral. A alta disponibilidade nesse modelo de arquitetura é obtida pela separação das camadas de computação e armazenamento e a replicação de dados na camada de armazenamento.

A figura a seguir mostra quatro nós no modelo de arquitetura padrão com as camadas de computação e armazenamento separadas.

![Separação de computação e armazenamento](media/sql-database-managed-instance/general-purpose-service-tier.png)

No modelo de disponibilidade padrão, há duas camadas:

- Uma camada de computação sem estado que está executando o processo sqlserver.exe e contém apenas dados transitórios e armazenados em cache (por exemplo – cache de planos, pool de buffer, pool de armazenamento de colunas). Este nó do SQL Server sem estado é operado pelo Microsoft Azure Service Fabric, que inicializa o processo, controla a integridade do nó e executa o failover para outro local, se necessário.
- Uma camada de dados com estado com arquivos de banco de dados (.mdf/.ldf) que são armazenados no Armazenamento Premium do Azure. O Armazenamento do Azure garante que não haja perda de dados de nenhum registro colocados em qualquer arquivo de banco de dados. O Armazenamento do Azure tem disponibilidade/redundância de dados interna, que garante que cada registro no arquivo de log ou página no arquivo de dados será preservado mesmo se o processo do SQL Server falhar.

Sempre que o mecanismo de banco de dados ou sistema operacional é atualizado, alguma parte da infraestrutura subjacente falhar ou se for detectado algum problema crítico no processo do SQL Server, o Azure Service Fabric moverá o processo do SQL Server sem estado para outro nó de computação sem estado. Há um conjunto de nós sobressalentes que está esperando a execução do novo serviço de computação no caso de failover para minimizar o tempo de failover. Os dados na camada de Armazenamento do Azure não são afetados, e arquivos de dados/log são anexados ao processo do SQL Server recém-inicializado. Esse processo garante a disponibilidade de 99,99%, mas pode ter alguns impactos de desempenho na carga de trabalho pesada que está sendo executada devido ao tempo de transição e ao fato de o novo nó do SQL Server ser iniciado com cache frio.

## <a name="premiumbusiness-critical-availability"></a>Disponibilidade Premium/Comercialmente Crítica

A disponibilidade Premium é habilitada na camada Premium do Banco de Dados SQL do Azure e foi projetada para cargas de trabalho intensivas que não podem tolerar nenhum impacto no desempenho devido a operações de manutenção em andamento.

No modelo Premium, o Banco de Dados SQL do Azure integra computação e armazenamento em um único nó. Nesse modelo de arquitetura, a alta disponibilidade é obtida pela replicação de computação (processo do Mecanismo de Banco de Dados do SQL Server) e de armazenamento (SSD conectado localmente) implantados no cluster [Grupos de Disponibilidade Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) de quatro nós.

![Cluster de nós de mecanismo de banco de dados](media/sql-database-managed-instance/business-critical-service-tier.png)

O processo do Mecanismo de Banco de Dados do SQL Server e arquivos mdf/ldf subjacentes são colocados no mesmo nó, com armazenamento SSD conectado localmente, fornecendo baixa latência para sua carga de trabalho. A alta disponibilidade é implementada usando o padrão [Grupos de Disponibilidade AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Cada banco de dados é um cluster de nós de banco de dados com um banco de dados primário acessível para carga de trabalho do cliente e três processos secundários que contêm cópias dos dados. O nó primário efetua constantemente push das alterações para nos secundários para garantir que os dados estejam disponíveis em réplicas secundárias se o nó primário falhar por qualquer motivo. O failover é tratado pelo Mecanismo de Banco de Dados do SQL Server – uma réplica secundária se torna o nó primário e uma nova réplica secundária é criada para garantir nós suficientes no cluster. A carga de trabalho é automaticamente redirecionada para o novo nó primário.

Além disso, o cluster Comercialmente Crítico fornece nó interno somente leitura que pode ser usado para executar consultas somente leitura (por exemplo, relatórios) que não devem afetar o desempenho da carga de trabalho principal. 

## <a name="zone-redundant-configuration-preview"></a>Configuração com redundância de zona (versão prévia)

Por padrão, as réplicas do quorum para as configurações de armazenamento locais são criadas no mesmo datacenter. Com a introdução das [Zonas de Disponibilidade do Azure](../availability-zones/az-overview.md), é possível colocar as diferentes réplicas no quorum para diferentes zonas de disponibilidade na mesma região. Para eliminar um ponto único de falha, o anel de controle também é duplicado entre várias zonas como três GW (anéis de gateway). O roteamento para um anel de gateway específico é controlado pelo ATM [(Gerenciador de Tráfego do Microsoft Azure)](../traffic-manager/traffic-manager-overview.md). Como a configuração com redundância de zona não cria uma redundância de banco de dados adicional, o uso de Zonas de Disponibilidade (versão prévia) nas camadas de serviço Premium ou Comercialmente Crítica está disponível sem nenhum custo adicional. Ao selecionar um banco de dados com redundância de zona, você pode tornar os bancos de dados Premium ou Comercialmente Crítico resilientes a um conjunto muito maior de falhas, incluindo interrupções catastróficas do datacenter, sem nenhuma alteração na lógica de aplicativo. Além disso, é possível converter quaisquer pools ou bancos de dados Premium ou Comercialmente Crítico existentes para a configuração com redundância de zona.

Como o quórum com redundância de zona tem réplicas em diferentes datacenters com alguma distância entre eles, a latência de rede aumentada pode aumentar o tempo de confirmação e, desse modo, afetar o desempenho de algumas cargas de trabalho OLTP. Sempre será possível retornar à configuração de única zona, desabilitando a configuração com redundância de zona. Esse processo é um tamanho de operação de dados e é semelhante à atualização da camada de serviço regular. No final do processo, o pool ou banco de dados será migrado de um anel com redundância de zona para um anel de única zona ou vice-versa.

> [!IMPORTANT]
> Os bancos de dados com redundância de zona e os pools elásticos só têm suporte no momento na camada de serviço Premium. Durante a visualização pública, os backups e registros de auditoria são armazenados no armazenamento RA-GRS e, portanto, podem não ser disponibilizados automaticamente no caso de uma interrupção de toda a zona. 

A versão com redundância de zona da arquitetura de alta disponibilidade é ilustrada pelo diagrama a seguir:
 
![Arquitetura de alta disponibilidade com redundância de zona](./media/sql-database-high-availability/high-availability-architecture-zone-redundant.png)

## <a name="read-scale-out"></a>Expansão de leitura
Conforme descrito, as camadas de serviço Premium e Comercialmente Crítico utilizam conjuntos de quorum e tecnologia Always ON para Alta Disponibilidade tanto em configurações com redundância de zona quanto única zona. Um dos benefícios do AlwaysON é que as réplicas estão sempre no estado consistente transicionalmente. Como as réplicas têm o mesmo tamanho de computação que o primário, o aplicativo pode aproveitar essa capacidade adicional para atender a cargas de trabalho somente leitura sem custo adicional (expansão de leitura). Dessa forma, as consultas somente leitura serão isoladas da carga de trabalho principal de leitura/gravação e não afetarão o desempenho. O recurso de expansão de leitura destina-se aos aplicativos que incluem cargas de trabalho somente leitura logicamente separadas, como análises e, portanto, poderiam aproveitar essa capacidade adicional sem conexão ao primário. 

Para usar o recurso Expansão de leitura com um determinado banco de dados, você deve habilitá-lo ao criar o banco de dados ou posteriormente, alterando a configuração usando o PowerShell invocando os cmdlets [et-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) ou [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) ou por meio da API REST do Azure Resource Manager usando o método [Bancos de dados - Criar ou Atualizar](/rest/api/sql/databases/createorupdate).

Após a Expansão de Leitura ser habilitada para um banco de dados, aplicativos que se conectam ao banco de dados serão direcionados para a réplica de leitura-gravação ou para uma réplica somente leitura desse banco de dados de acordo com a propriedade `ApplicationIntent` configurada na cadeia de conexão do aplicativo. Para obter informações sobre a propriedade `ApplicationIntent`, consulte [Especificando a intenção do aplicativo](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent). 

Se a expansão de leitura está desabilitada ou você definir a propriedade ReadScale em uma camada de serviço sem suporte, todas as conexões são direcionadas para a réplica de leitura-gravação, independentemente da propriedade `ApplicationIntent`.

## <a name="conclusion"></a>Conclusão
O Banco de Dados SQL do Azure está totalmente integrado com a plataforma do Azure e é altamente dependente do Service Fabric para detecção e recuperação de falhas, no Azure Storage Blob para proteção de dados e Zonas de Disponibilidade para tolerância a falhas mais elevada. Ao mesmo tempo, o Banco de Dados SQL do Azure alavanca totalmente a tecnologia de Grupos de Disponibilidade AlwaysOn do SQL Server para replicação e failover. A combinação dessas tecnologias permite que os aplicativos percebam em sua totalidade os benefícios de um modelo de armazenamento misto e deem suporte aos SLAs mais exigentes. 

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as [Zonas de Disponibilidade do Azure](../availability-zones/az-overview.md)
- Saiba mais sobre o [Service Fabric](../service-fabric/service-fabric-overview.md)
- Saiba mais sobre o [Gerenciador de Tráfego do Microsoft Azure](../traffic-manager/traffic-manager-overview.md) 
