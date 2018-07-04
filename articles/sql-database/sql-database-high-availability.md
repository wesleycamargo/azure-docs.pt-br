---
title: Alta disponibilidade - Serviço do Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba mais sobre os recursos e capacidades de alta disponibilidade do serviço de Banco de Dados SQL do Microsoft Azure
services: sql-database
author: jovanpop-msft
manager: craigg
ms.service: sql-database
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: jovanpop
ms.reviewer: carlrab, sashan
ms.openlocfilehash: a9874681d59d193fc3c3d0fd4271e2a6a0fb0dc6
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060376"
---
# <a name="high-availability-and-azure-sql-database"></a>Banco de dados SQL do Microsoft Azure e de alta disponibilidade

O Azure SQL Database é uma Plataforma como Serviço de banco de dados altamente disponível que garante que seu banco de dados esteja ativo e em execução 99,99% do tempo, sem se preocupar com manutenção e tempos de inatividade. Esse é um processo de SQL Server Database Engine totalmente gerenciado hospedado na nuvem do Azure que garante que seu banco de dados SQL Server esteja sempre atualizado/com patch aplicado sem afetar sua carga de trabalho. O Banco de Dados SQL do Microsoft Azure pode se recuperar rapidamente, até nas circunstâncias mais críticas, garantindo que seus dados estejam sempre disponíveis.

A plataforma do Azure gerencia completamente cada Banco de Dados SQL do Azure e garante alta porcentagem de disponibilidade de dados sem perda de dados. O Azure controla automaticamente a aplicação de patches, potenciais falhas subjacentes de hardware, software ou rede, a implantação de correções de bug, failovers, upgrades de banco de dados e outras tarefas de manutenção. Engenheiros do SQL Server têm implementado as práticas recomendadas, garantindo que todas as operações de manutenção sejam concluídas em menos de 0,01% do tempo de vida útil de seu banco de dados. Essa arquitetura foi projetada para garantir que dados confirmados nunca sejam perdidos e que operações de manutenção sejam executadas sem afetar a carga de trabalho. Não há nenhuma janela de manutenção ou tempo de inatividade que deva exigir que você pare a carga de trabalho enquanto o banco de dados é atualizado ou está em manutenção. A alta disponibilidade interna no Banco de Dados SQL do Azure garante que o banco de dados nunca será um ponto único de falha em sua arquitetura de software.

Há dois modelos de alta disponibilidade aplicados ao SQL do Azure:

- Modelo de uso padrão/geral, que fornece 99,99% de disponibilidade, mas com alguma potencial de degradação de desempenho durante atividades de manutenção.
- Modelo premium/comercialmente crítico, que também fornece 99,99% de disponibilidade, com mínimo impacto de desempenho em sua carga de trabalho, mesmo durante atividades de manutenção.

O Azure atualiza e aplica patches subjacentes ao sistema operacional, drivers e Mecanismo de BD do SQL Server de forma transparente com mínimo tempo de inatividade para usuários finais. O Banco de Dados SQL do Azure é executado na versão estável mais recente do Mecanismo de BD do SQL Server e do SO Windows, e a maioria dos usuários não perceberiam que as atualizações são executadas continuamente.

## <a name="standard-availability"></a>Disponibilidade padrão

A disponibilidade padrão se refere a 99,99% de SLA que são aplicados nas camadas de Uso Padrão/Básico/Geral. A disponibilidade é obtida pela separação de camadas de computação e armazenamento. No modelo de disponibilidade padrão, temos duas camadas:

- Uma camada de computação sem estado que está executando o processo sqlserver.exe e contém apenas dados transitórios e armazenados em cache (por exemplo – cache de planos, pool de buffer, pool de armazenamento de colunas). Este nó do SQL Server sem estado é operado pelo Microsoft Azure Service Fabric, que inicializa o processo, controla a integridade do nó e executa o failover para outro local, se necessário.
- Uma camada de dados com estado com arquivos de banco de dados (.mdf/.ldf) que são armazenados em Discos de Armazenamento Premium do Azure. O Armazenamento do Azure garante que não haja perda de dados de nenhum registro colocados em qualquer arquivo de banco de dados. O Armazenamento do Azure tem disponibilidade/redundância de dados interna, que garante que cada registro no arquivo de log ou página no arquivo de dados será preservado mesmo se o processo do SQL Server falhar.

Sempre que o mecanismo de banco de dados ou sistema operacional é atualizado, ou se for detectado algum problema crítico no processo do SQL Server, o Azure Service Fabric moverá o processo do SQL Server sem estado para outro nó de computação sem estado. Os dados na camada de Armazenamento do Azure não são afetados, e arquivos de dados/log são anexados ao processo do SQL Server recém-inicializado. O tempo de failover esperado pode ser medido em segundos. Esse processo garante a disponibilidade de 99,99%, mas pode ter alguns impactos de desempenho na carga de trabalho pesada que está sendo executada devido ao tempo de transição e ao fato de o novo nó do SQL Server ser iniciado com cache frio.

## <a name="premium-availability"></a>Disponibilidade Premium

A disponibilidade Premium é habilitada na camada Premium do Banco de Dados SQL do Azure e foi projetada para cargas de trabalho intensivas que não podem tolerar nenhum impacto no desempenho devido a operações de manutenção em andamento.

No modelo Premium, o Banco de Dados SQL do Azure integra computação e armazenamento em um único nó. O processo do Mecanismo de Banco de Dados do SQL Server e arquivos mdf/ldf subjacentes são colocados no mesmo nó, com armazenamento SSD conectado localmente, fornecendo baixa latência para sua carga de trabalho.

A alta disponibilidade é implementada usando o padrão [Grupos de Disponibilidade AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Cada banco de dados é um cluster de nós de banco de dados com um banco de dados primário acessível para carga de trabalho do cliente e alguns processos secundários que contêm cópias dos dados. O nó primário efetua constantemente push das alterações para nos secundários para garantir que os dados estejam disponíveis em réplicas secundárias se o nó primário falhar por qualquer motivo. O failover é tratado pelo Mecanismo de Banco de Dados do SQL Server – uma réplica secundária se torna o nó primário e uma nova réplica secundária é criada para garantir nós suficientes no cluster. A carga de trabalho é automaticamente redirecionada para o novo nó primário. O tempo de failover é medido em milissegundos e a nova instância primária fica imediatamente pronta para continuar a atender solicitações.

## <a name="zone-redundant-configuration-preview"></a>Configuração com redundância de zona (versão prévia)

Por padrão, as réplicas do quorum para as configurações de armazenamento locais são criadas no mesmo datacenter. Com a introdução das [Zonas de Disponibilidade do Azure](../availability-zones/az-overview.md), é possível colocar as diferentes réplicas no quorum para diferentes zonas de disponibilidade na mesma região. Para eliminar um ponto único de falha, o anel de controle também é duplicado entre várias zonas como três GW (anéis de gateway). O roteamento para um anel de gateway específico é controlado pelo ATM [(Gerenciador de Tráfego do Microsoft Azure)](../traffic-manager/traffic-manager-overview.md). Como a configuração com redundância de zona não cria redundância de banco de dados adicional, o uso de Zonas de Disponibilidade nas camadas de serviço Premium ou Comercialmente Crítico (versão prévia) está disponível sem nenhum custo adicional. Ao selecionar um banco de dados com redundância de zona, você pode tornar seus bancos de dados Premium ou Comercialmente Crítico (versão prévia) resilientes a um conjunto muito maior de falhas, incluindo interrupções catastróficas do datacenter, sem quaisquer alterações na lógica de aplicativo. Além disso, é possível converter quaisquer pools ou bancos de dados Premium ou Comercialmente Crítico (versão prévia) existentes para a configuração com redundância de zona.

Como o quórum com redundância de zona tem réplicas em diferentes datacenters com alguma distância entre eles, a latência de rede aumentada pode aumentar o tempo de confirmação e, desse modo, afetar o desempenho de algumas cargas de trabalho OLTP. Sempre será possível retornar à configuração de única zona, desabilitando a configuração com redundância de zona. Esse processo é um tamanho de operação de dados e é semelhante à atualização do SLO (Objetivo do Nível de Serviço) regular. No final do processo, o pool ou banco de dados será migrado de um anel com redundância de zona para um anel de única zona ou vice-versa.

> [!IMPORTANT]
> Os bancos de dados com redundância de zona e os pools elásticos só têm suporte no momento na camada de serviço Premium. Durante a visualização pública, os backups e registros de auditoria são armazenados no armazenamento RA-GRS e, portanto, podem não ser disponibilizados automaticamente no caso de uma interrupção de toda a zona. 

A versão com redundância de zona da arquitetura de alta disponibilidade é ilustrada pelo diagrama a seguir:
 
![Arquitetura de alta disponibilidade com redundância de zona](./media/sql-database-high-availability/high-availability-architecture-zone-redundant.png)

## <a name="read-scale-out"></a>Expansão de leitura
Conforme descrito, as camadas de serviço Premium e Comercialmente Crítico (versão prévia) utilizam os conjuntos de quorum e a tecnologia Always ON para Alta Disponibilidade em configurações com redundância de zona e única zona. Um dos benefícios do AlwaysON é que as réplicas estão sempre no estado consistente transicionalmente. Como as réplicas têm o mesmo nível de desempenho que o primário, o aplicativo pode aproveitar essa capacidade adicional para atender às cargas de trabalho somente leitura sem custo adicional (expansão de leitura). Dessa forma, as consultas somente leitura serão isoladas da carga de trabalho principal de leitura/gravação e não afetarão o desempenho. O recurso de expansão de leitura destina-se aos aplicativos que incluem cargas de trabalho somente leitura logicamente separadas, como análises e, portanto, poderiam aproveitar essa capacidade adicional sem conexão ao primário. 

Para usar o recurso Expansão de leitura com um determinado banco de dados, você deve habilitá-lo ao criar o banco de dados ou posteriormente, alterando a configuração usando o PowerShell invocando os cmdlets [et-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) ou [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) ou por meio da API REST do Azure Resource Manager usando o método [Bancos de dados - Criar ou Atualizar](/rest/api/sql/databases/createorupdate).

Após a Expansão de Leitura ser habilitada para um banco de dados, aplicativos que se conectam ao banco de dados serão direcionados para a réplica de leitura-gravação ou para uma réplica somente leitura desse banco de dados de acordo com a propriedade `ApplicationIntent` configurada na cadeia de conexão do aplicativo. Para obter informações sobre a propriedade `ApplicationIntent`, consulte [Especificando a intenção do aplicativo](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent). 

Se a expansão de leitura está desabilitada ou você definir a propriedade ReadScale em uma camada de serviço sem suporte, todas as conexões são direcionadas para a réplica de leitura-gravação, independentemente da propriedade `ApplicationIntent`.  

> [!NOTE]
> É possível ativar a expansão de leitura em um banco de dados Básico, Standard ou Uso Geral, mesmo se não resultar no roteamento da sessão pretendia apenas leitura em uma réplica separada. Isso é feito para oferecer suporte a aplicativos existentes que são dimensionados para cima e para baixo entre as camadas de finalidade Standard/Uso Geral e Premium/Comercialmente Crítico.  

O recurso de Expansão de Leitura dá suporte à consistência de nível de sessão. Se a sessão somente leitura reconectar após uma causa de erro de conexão pela indisponibilidade de réplica, ela poderá ser redirecionada para uma réplica diferente. Embora improvável, isso pode resultar no processamento do conjunto de dados que está obsoleto. Da mesma forma, se um aplicativo gravar dados usando uma sessão de leitura/gravação e a ler imediatamente usando a sessão somente leitura, é possível que os novos dados não fiquem imediatamente visíveis.

## <a name="conclusion"></a>Conclusão
O Banco de Dados SQL do Azure está totalmente integrado com a plataforma do Azure e é altamente dependente do Service Fabric para detecção e recuperação de falhas, no Azure Storage Blob para proteção de dados e Zonas de Disponibilidade para tolerância a falhas mais elevada. Ao mesmo tempo, o Banco de Dados SQL do Azure alavanca totalmente a tecnologia de Grupos de Disponibilidade AlwaysOn do SQL Server para replicação e failover. A combinação dessas tecnologias permite que os aplicativos percebam em sua totalidade os benefícios de um modelo de armazenamento misto e deem suporte aos SLAs mais exigentes. 

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as [Zonas de Disponibilidade do Azure](../availability-zones/az-overview.md)
- Saiba mais sobre o [Service Fabric](../service-fabric/service-fabric-overview.md)
- Saiba mais sobre o [Gerenciador de Tráfego do Microsoft Azure](../traffic-manager/traffic-manager-overview.md) 
