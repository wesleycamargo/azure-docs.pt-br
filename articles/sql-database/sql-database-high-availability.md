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
ms.date: 10/15/2018
ms.openlocfilehash: 9d80f4e7422d881393c8e626ddfc75c4067ef1e2
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53250341"
---
# <a name="high-availability-and-azure-sql-database"></a>Banco de dados SQL do Microsoft Azure e de alta disponibilidade

O Azure SQL Database é uma Plataforma como Serviço de banco de dados altamente disponível que garante que seu banco de dados esteja ativo e em execução 99,99% do tempo, sem se preocupar com manutenção e tempos de inatividade. Esse é um processo de SQL Server Database Engine totalmente gerenciado hospedado na nuvem do Azure que garante que seu banco de dados SQL Server esteja sempre atualizado/com patch aplicado sem afetar sua carga de trabalho. Quando uma instância é corrigida ou falha, o tempo de inatividade geralmente não é perceptível se você [empregar a lógica de repetição](sql-database-develop-overview.md#resiliency) no seu aplicativo. Se o tempo para concluir um failover for maior que 60 segundos, você deverá abrir um caso de suporte. O Banco de Dados SQL do Microsoft Azure pode se recuperar rapidamente, até nas circunstâncias mais críticas, garantindo que seus dados estejam sempre disponíveis.

A plataforma do Azure gerencia completamente cada Banco de Dados SQL do Azure e garante alta porcentagem de disponibilidade de dados sem perda de dados. O Azure controla automaticamente a aplicação de patches, potenciais falhas subjacentes de hardware, software ou rede, a implantação de correções de bug, failovers, upgrades de banco de dados e outras tarefas de manutenção. Engenheiros do SQL Server têm implementado as práticas recomendadas, garantindo que todas as operações de manutenção sejam concluídas em menos de 0,01% do tempo de vida útil de seu banco de dados. Essa arquitetura foi projetada para garantir que dados confirmados nunca sejam perdidos e que operações de manutenção sejam executadas sem afetar a carga de trabalho. Não há nenhuma janela de manutenção ou tempo de inatividade que deva exigir que você pare a carga de trabalho enquanto o banco de dados é atualizado ou está em manutenção. A alta disponibilidade interna no Banco de Dados SQL do Azure garante que o banco de dados nunca será um ponto único de falha em sua arquitetura de software.

Banco de dados SQL do Azure baseia-se na arquitetura de mecanismo de banco de dados do SQL Server que é ajustada para o ambiente de nuvem para garantir a disponibilidade de 99,99%, até mesmo no caso de falhas de infraestrutura. Há dois modelos de arquitetura de alta disponibilidade que são usados no Banco de Dados SQL do Azure (ambos garantindo a disponibilidade de 99,99%):

- O modelo da camada de serviço Standard/Uso Geral que se baseia em uma separação entre computação e armazenamento. Esse modelo de arquitetura se baseia em alta disponibilidade e confiabilidade de camada de armazenamento, mas ele pode ter alguma degradação de desempenho potencial durante as atividades de manutenção.
- O modelo da camada de serviço Premium/Comercialmente Crítica que se baseia em um cluster de processos do mecanismo de banco de dados. Esse modelo de arquitetura se baseia no fato de que há sempre um quorum de nós de mecanismo de banco de dados disponíveis e tem impacto mínimo no desempenho em sua carga de trabalho, mesmo durante as atividades de manutenção.

O Azure atualiza e aplica patches subjacentes ao sistema operacional, drivers e Mecanismo de BD do SQL Server de forma transparente com mínimo tempo de inatividade para usuários finais. O Banco de Dados SQL do Azure é executado na versão estável mais recente do Mecanismo de BD do SQL Server e do SO Windows, e a maioria dos usuários não perceberiam que as atualizações são executadas continuamente.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Disponibilidade de nível de serviço básico, padrão e de uso geral

A disponibilidade padrão se refere ao SLA de 99,99% que é aplicado nos níveis de serviço Básico, Padrão e de Propósito Geral. A alta disponibilidade nesse modelo de arquitetura é obtida pela separação das camadas de computação e armazenamento e a replicação de dados na camada de armazenamento.

A figura a seguir mostra quatro nós no modelo de arquitetura padrão com as camadas de computação e armazenamento separadas.

![Separação de computação e armazenamento](media/sql-database-managed-instance/general-purpose-service-tier.png)

No modelo de disponibilidade padrão, há duas camadas:

- Uma camada de computação sem estado que está executando o processo `sqlserver.exe` e contém apenas dados temporários e armazenados em cache (por exemplo - cache de plano, conjunto de buffers, conjunto de armazenamentos de colunas). Este nó do SQL Server sem estado é operado pelo Microsoft Azure Service Fabric, que inicializa o processo, controla a integridade do nó e executa o failover para outro local, se necessário.
- Uma camada de dados com estado com arquivos de banco de dados (.mdf/.ldf) que são armazenados no Armazenamento Premium do Azure. O Armazenamento do Azure garante que não haja perda de dados de nenhum registro colocados em qualquer arquivo de banco de dados. O Armazenamento do Azure tem disponibilidade/redundância de dados interna, que garante que cada registro no arquivo de log ou página no arquivo de dados será preservado mesmo se o processo do SQL Server falhar.

Sempre que o mecanismo de banco de dados ou sistema operacional é atualizado, alguma parte da infraestrutura subjacente falhar ou se for detectado algum problema crítico no processo do SQL Server, o Azure Service Fabric moverá o processo do SQL Server sem estado para outro nó de computação sem estado. Há um conjunto de nós sobressalentes que está esperando a execução do novo serviço de computação no caso de failover para minimizar o tempo de failover. Os dados na camada de Armazenamento do Azure não são afetados, e arquivos de dados/log são anexados ao processo do SQL Server recém-inicializado. Esse processo garante a disponibilidade de 99,99%, mas pode ter alguns impactos de desempenho na carga de trabalho pesada que está sendo executada devido ao tempo de transição e ao fato de o novo nó do SQL Server ser iniciado com cache frio.

## <a name="premium-and-business-critical-service-tier-availability"></a>Disponibilidade de nível de serviço Premium e Business Critical

A disponibilidade Premium está habilitada nas camadas de serviço Premium e Business Critical do Banco de Dados SQL do Azure e foi projetada para cargas de trabalho intensivas que não toleram qualquer impacto no desempenho devido às operações de manutenção em andamento.

No modelo Premium, o Banco de Dados SQL do Azure integra computação e armazenamento em um único nó. A alta disponibilidade neste modelo de arquitetura é obtida pela replicação de computação (processo do Mecanismo de Banco de Dados do SQL Server) e armazenamento (SSD conectado localmente) implantada em cluster de quatro nós, usando tecnologia semelhante ao SQL Server [Always On Availability Groups](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server).

![Cluster de nós de mecanismo de banco de dados](media/sql-database-managed-instance/business-critical-service-tier.png)

O processo do mecanismo de banco de dados SQL e os arquivos mdf / ldf subjacentes são colocados no mesmo nó com armazenamento SSD conectado localmente, fornecendo baixa latência à sua carga de trabalho. A alta disponibilidade é implementada usando tecnologia semelhante ao SQL Server [Always On Availability Groups](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Cada banco de dados é um cluster de nós de banco de dados com um banco de dados primário acessível para carga de trabalho do cliente e três processos secundários que contêm cópias dos dados. O nó primário efetua constantemente push das alterações para nos secundários para garantir que os dados estejam disponíveis em réplicas secundárias se o nó primário falhar por qualquer motivo. O failover é manipulado pelo Azure Service Fabric – uma réplica secundária torna-se o nó primário e uma nova réplica secundária é criada para garantir a presença de nós suficientes no cluster. A carga de trabalho é automaticamente redirecionada para o novo nó primário.

Além disso, o cluster Business Critical possui o recurso interno [Expansão Scale-Out](sql-database-read-scale-out.md) que fornece um nó somente leitura interno gratuito que pode ser usado para executar consultas somente leitura (por exemplo, relatórios) não deve afetar o desempenho de sua carga de trabalho principal.

## <a name="zone-redundant-configuration"></a>Configuração com redundância de zona

Por padrão, as réplicas do quorum para as configurações de armazenamento locais são criadas no mesmo datacenter. Com a introdução das [Zonas de Disponibilidade do Azure](../availability-zones/az-overview.md), é possível colocar as diferentes réplicas no quorum para diferentes zonas de disponibilidade na mesma região. Para eliminar um ponto único de falha, o anel de controle também é duplicado entre várias zonas como três GW (anéis de gateway). O roteamento para um anel de gateway específico é controlado pelo ATM [(Gerenciador de Tráfego do Microsoft Azure)](../traffic-manager/traffic-manager-overview.md). Como a configuração redundante da zona não cria redundância de banco de dados adicional, o uso de Zonas de Disponibilidade nas camadas de serviço Premium ou Business Critical está disponível sem nenhum custo extra. Ao selecionar um banco de dados com redundância de zona, você pode tornar os bancos de dados Premium ou Comercialmente Crítico resilientes a um conjunto muito maior de falhas, incluindo interrupções catastróficas do datacenter, sem nenhuma alteração na lógica de aplicativo. Além disso, é possível converter quaisquer pools ou bancos de dados Premium ou Comercialmente Crítico existentes para a configuração com redundância de zona.

Como o quórum com redundância de zona tem réplicas em diferentes datacenters com alguma distância entre eles, a latência de rede aumentada pode aumentar o tempo de confirmação e, desse modo, afetar o desempenho de algumas cargas de trabalho OLTP. Sempre será possível retornar à configuração de única zona, desabilitando a configuração com redundância de zona. Esse processo é um tamanho de operação de dados e é semelhante à atualização da camada de serviço regular. No final do processo, o pool ou banco de dados será migrado de um anel com redundância de zona para um anel de única zona ou vice-versa.

> [!IMPORTANT]
> Os bancos de dados com redundância de zona e os pools elásticos só têm suporte no momento na camada de serviço Premium. Por padrão, os backups e os registros de auditoria são armazenados no armazenamento do RA-GRS e, portanto, podem não estar disponíveis automaticamente no caso de uma indisponibilidade em toda a zona. 

A versão com redundância de zona da arquitetura de alta disponibilidade é ilustrada pelo diagrama a seguir:

![Arquitetura de alta disponibilidade com redundância de zona](./media/sql-database-high-availability/high-availability-architecture-zone-redundant.png)

## <a name="accelerated-database-recovery-adr"></a>Recuperação Acelerada de Banco de Dados (ADR)

[ADR (Accelerated Database Recovery - Recuperação Acelerada de Banco de Dados)](sql-database-accelerated-database-recovery.md) é um novo recurso de mecanismo de banco de dados SQL que melhora bastante a disponibilidade do banco de dados, especialmente na presença de transações de longa duração, reprojetando o processo de recuperação do mecanismo de banco de dados SQL. ADR está atualmente disponível para o Azure SQL Data Warehouse, pools Elásticos e bancos de dados individuais.

## <a name="conclusion"></a>Conclusão

O Banco de Dados SQL do Azure está totalmente integrado com a plataforma do Azure e é altamente dependente do Service Fabric para detecção e recuperação de falhas, no Azure Storage Blob para proteção de dados e Zonas de Disponibilidade para tolerância a falhas mais elevada. Ao mesmo tempo, o Banco de Dados SQL do Azure alavanca totalmente a tecnologia de Grupos de Disponibilidade AlwaysOn do SQL Server para replicação e failover. A combinação dessas tecnologias permite que os aplicativos percebam em sua totalidade os benefícios de um modelo de armazenamento misto e deem suporte aos SLAs mais exigentes.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as [Zonas de Disponibilidade do Azure](../availability-zones/az-overview.md)
- Saiba mais sobre o [Service Fabric](../service-fabric/service-fabric-overview.md)
- Saiba mais sobre o [Gerenciador de Tráfego do Microsoft Azure](../traffic-manager/traffic-manager-overview.md)
- Para obter mais opções de alta disponibilidade e recuperação de desastres, consulte [Continuidade de negócios](sql-database-business-continuity.md)
