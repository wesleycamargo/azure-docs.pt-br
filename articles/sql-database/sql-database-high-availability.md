---
title: Alta disponibilidade - Serviço do Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba mais sobre os recursos e capacidades de alta disponibilidade do serviço de Banco de Dados SQL do Microsoft Azure
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: sashan
ms.reviewer: carlrab, sashan
manager: craigg
ms.date: 04/17/2019
ms.openlocfilehash: ec9f5aa8163ea9bb838b1a95ab8ad49233a72643
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698222"
---
# <a name="high-availability-and-azure-sql-database"></a>Banco de dados SQL do Microsoft Azure e de alta disponibilidade

O objetivo da arquitetura de alta disponibilidade no banco de dados SQL é a garantia de que seu banco de dados está ativo e em execução 99,99% do tempo, sem se preocupar sobre o impacto das operações de manutenção e interrupções. Azure trata automaticamente tarefas de manutenção críticas, como aplicação de patches, backups, atualizações do Windows e SQL, bem como eventos não planejados, como falhas de hardware, software ou de rede subjacentes.  Quando a instância do SQL subjacente for corrigida ou faz o failover, o tempo de inatividade não é perceptível se você [empregar uma lógica de repetição](sql-database-develop-overview.md#resiliency) em seu aplicativo. O Banco de Dados SQL do Microsoft Azure pode se recuperar rapidamente, até nas circunstâncias mais críticas, garantindo que seus dados estejam sempre disponíveis.

A solução de alta disponibilidade é projetada para garantir que os dados confirmados nunca não perdidos devido a falhas, que as operações de manutenção não afeta sua carga de trabalho, e que o banco de dados não será um ponto único de falha em sua arquitetura de software. Não há nenhuma janela de manutenção ou tempo de inatividade que deva exigir que você pare a carga de trabalho enquanto o banco de dados é atualizado ou está em manutenção. 

Há dois modelos de arquiteturas de alta disponibilidade que são usados no banco de dados SQL:

- Modelo de disponibilidade padrão com base em uma separação de computação e armazenamento.  Ele se baseia em alta disponibilidade e confiabilidade da camada de armazenamento remoto. Essa arquitetura destina-se aplicativos de negócios voltados a orçamento que podem tolerar alguma degradação no desempenho durante as atividades de manutenção.
- Modelo de disponibilidade do Premium com base em um cluster de processos do mecanismo de banco de dados. Ele se baseia no fato de que haja sempre um quorum de nós de mecanismo de banco de dados disponíveis. Essa arquitetura se destina a aplicativos de missão crítica com alto desempenho de e/s, taxa alta de transação e garantias de impacto mínimo no desempenho para sua carga de trabalho durante as atividades de manutenção.

Banco de dados SQL do Azure executa a versão estável mais recente do mecanismo de banco de dados do SQL Server e do sistema operacional do Windows e a maioria dos usuários não percebam que as atualizações são realizadas continuamente.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Disponibilidade de nível de serviço básico, padrão e de uso geral

Essas camadas de serviço aproveitam a arquitetura de disponibilidade padrão. A figura a seguir mostra quatro nós diferentes, com as camadas de computação e armazenamento separadas.

![Separação de computação e armazenamento](media/sql-database-high-availability/general-purpose-service-tier.png)

O modelo de disponibilidade padrão inclui duas camadas:

- Uma camada de computação sem monitoração de estado que executa o `sqlserver.exe` processar e contém apenas transitórios e armazenados em cache dados na SSD anexado, como pool de armazenamento do TempDB, banco de dados modelo, o cache de plano, pool de buffers e coluna. Esse nó sem monitoração de estado é operado pelo Azure Service Fabric que inicializa `sqlserver.exe`, controla a integridade do nó e realiza failover para outro nó, se necessário.
- Uma camada de dados com monitoração de estado com os arquivos de banco de dados (.mdf/.ldf) que são armazenados no armazenamento de BLOBs do Azure. O armazenamento de BLOBs do Azure tem recursos de redundância e disponibilidade de dados internos. Ele garante que todos os registros no arquivo de log ou na página no arquivo de dados serão preservado mesmo se a falha do processo do SQL Server.

Sempre que o mecanismo de banco de dados ou o sistema operacional for atualizado ou uma falha for detectada, Azure Service Fabric moverá o processo do SQL Server sem monitoração de estado para outro nó de computação sem monitoração de estado com capacidade livre suficiente. Os dados no armazenamento de BLOBs do Azure não são afetados pela mudança, e os arquivos de dados/log estão conectados ao processo do SQL Server recentemente inicializado. Esse processo garante a disponibilidade de 99,99%, mas uma pesada carga de trabalho poderá observar alguma degradação de desempenho durante a transição, pois a nova instância do SQL Server começa com cache frio.

## <a name="premium-and-business-critical-service-tier-availability"></a>Disponibilidade de nível de serviço Premium e Business Critical

Premium e comercialmente crítico aproveitamento de camadas de serviço o modelo de disponibilidade do Premium, que se integra recursos de computação (processo de mecanismo de banco de dados do SQL Server) e SSD (armazenamento conectado localmente) em um único nó. Alta disponibilidade é obtida por meio da replicação de computação e armazenamento para nós adicionais criando um cluster de três a quatro - nó. 

![Cluster de nós de mecanismo de banco de dados](media/sql-database-high-availability/business-critical-service-tier.png)

Os arquivos de banco de dados subjacente (.mdf/.ldf) são colocados no armazenamento de SSD anexado para fornecer latência muito baixa e/s para sua carga de trabalho. Alta disponibilidade é implementada usando uma tecnologia semelhante ao SQL Server [grupos de disponibilidade AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). O cluster inclui uma única réplica primária (processo do SQL Server) que seja acessível para cargas de trabalho de cliente de leitura / gravação e até três réplicas secundárias (computação e armazenamento) que contém cópias dos dados. O nó principal constantemente envia as alterações para os nós secundários na ordem e garante que os dados são sincronizados para pelo menos uma réplica secundária antes de confirmar cada transação. Esse processo garante que se o nó primário falhar por algum motivo, sempre há um nó totalmente sincronizado para failover. O failover é iniciado pela malha de serviço do Azure. Depois que a réplica secundária se torna o novo nó primário, outra réplica secundária é criada para garantir que o cluster tiver nós suficientes (conjunto de quorum). Depois que o failover estiver concluído, as conexões de SQL são automaticamente redirecionadas para o novo nó primário.

Como um benefício extra, o modelo de disponibilidade premium inclui a capacidade de redirecionar conexões de SQL de somente leitura para uma das réplicas secundárias. Esse recurso é chamado [expansão de leitura](sql-database-read-scale-out.md). Ele fornece a capacidade sem nenhum custo adicional para descarregar operações somente leitura, como cargas de trabalho analíticas, da réplica primária de computação em 100% adicionais.

## <a name="zone-redundant-configuration"></a>Configuração com redundância de zona

Por padrão, o cluster de nós para o modelo de disponibilidade premium é criado no mesmo datacenter. Com a introdução da [zonas de disponibilidade do Azure](../availability-zones/az-overview.md), banco de dados SQL pode colocar as diferentes réplicas no cluster para diferentes zonas de disponibilidade na mesma região. Para eliminar um ponto único de falha, o anel de controle também é duplicado entre várias zonas como três GW (anéis de gateway). O roteamento para um anel de gateway específico é controlado pelo ATM [(Gerenciador de Tráfego do Microsoft Azure)](../traffic-manager/traffic-manager-overview.md). Como a configuração com redundância de zona nas camadas de serviço Premium ou comercialmente crítico não cria redundância de banco de dados adicionais, você poderá habilitá-lo sem nenhum custo extra. Ao selecionar uma configuração com redundância de zona, você pode tornar seus bancos de dados Premium ou comercialmente crítico resilientes a um conjunto muito maior de falhas, incluindo interrupções catastróficas do datacenter, sem nenhuma alteração para a lógica do aplicativo. Além disso, é possível converter quaisquer pools ou bancos de dados Premium ou Comercialmente Crítico existentes para a configuração com redundância de zona.

Como os bancos de dados com redundância de zona tem réplicas em diferentes datacenters com alguma distância entre eles, a latência de rede aumentada pode aumentar o tempo de confirmação e, portanto, afetar o desempenho de algumas cargas de trabalho OLTP. Sempre será possível retornar à configuração de única zona, desabilitando a configuração com redundância de zona. Esse processo é uma operação online semelhante para a atualização da camada de serviço normal. No final do processo, o pool ou banco de dados será migrado de um anel com redundância de zona para um anel de única zona ou vice-versa.

> [!IMPORTANT]
> Pools Elásticos e bancos de dados com redundância de zona estão atualmente só tem suporte nas camadas de serviço Premium e comercialmente crítico. Por padrão, os backups e os registros de auditoria são armazenados no armazenamento do RA-GRS e, portanto, podem não estar disponíveis automaticamente no caso de uma indisponibilidade em toda a zona. 

A versão com redundância de zona da arquitetura de alta disponibilidade é ilustrada pelo diagrama a seguir:

![Arquitetura de alta disponibilidade com redundância de zona](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>Recuperação Acelerada de Banco de Dados (ADR)

[Acelerada de recuperação de banco de dados (ADR)](sql-database-accelerated-database-recovery.md) é um novo recurso de mecanismo de banco de dados do SQL que melhora significativamente a disponibilidade do banco de dados, especialmente na presença de longa execução de transações. ADR está atualmente disponível para o Azure SQL Data Warehouse, pools Elásticos e bancos de dados individuais.

## <a name="conclusion"></a>Conclusão

Banco de dados SQL do Azure apresenta uma solução de alta disponibilidade interna, que está profundamente integrada com a plataforma Azure. Ele é dependente do Service Fabric para recuperação e detecção de falhas, no armazenamento de BLOBs do Azure para proteção de dados e em zonas de disponibilidade para maior tolerância a falhas. Além disso, o banco de dados SQL do Azure aproveita a tecnologia de grupo de disponibilidade AlwaysOn do SQL Server para replicação e failover. A combinação dessas tecnologias permite que aplicativos totalmente perceber os benefícios de um armazenamento combinado de modelos e dar suporte a SLAs mais exigentes.

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre as [Zonas de Disponibilidade do Azure](../availability-zones/az-overview.md)
- Saiba mais sobre o [Service Fabric](../service-fabric/service-fabric-overview.md)
- Saiba mais sobre o [Gerenciador de Tráfego do Microsoft Azure](../traffic-manager/traffic-manager-overview.md)
- Para obter mais opções de alta disponibilidade e recuperação de desastres, consulte [Continuidade de negócios](sql-database-business-continuity.md)
