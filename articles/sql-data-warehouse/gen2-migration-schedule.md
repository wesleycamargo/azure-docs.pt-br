---
title: Migrar seu SQL Data Warehouse do Azure existente para Gen2 | Microsoft Docs
description: Instruções para migrar um data warehouse existente para Gen2 e o agendamento de migração por região.
services: sql-data-warehouse
author: mlee3gsd
ms.author: anumjs
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 1f645a9c9161fb2fd71b2fd9af9ade65bff0be94
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361138"
---
# <a name="upgrade-your-data-warehouse-to-gen2"></a>Atualizar o seu data warehouse para Gen2

A Microsoft está ajudando a reduzir o custo da execução de um data warehouse de nível básico.  Quanto menor de computação camadas capazes de tratar mais exigentes de consultas agora estão disponíveis para o Azure SQL Data Warehouse. Leia o comunicado completo [inferior computação suporte de camada para Gen2](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/). A nova oferta está disponível nas regiões observados na tabela a seguir. Para regiões compatíveis, data warehouses Gen1 existentes podem ser atualizados para Gen2 por um destes processos:

- **O processo de atualização automática:** As atualizações automáticas não comece assim que o serviço está disponível em uma região.  Quando as atualizações automáticas são iniciadas em uma região específica, as atualizações individuais do data warehouse ocorrem durante o agendamento de manutenção selecionado.
- [**Atualização automática para Gen2:**](#self-upgrade-to-gen2) Você pode controlar quando a atualização, fazendo uma atualização automática para Gen2. Se ainda não há suporte para a sua região, você pode restaurar de um ponto de restauração diretamente a uma instância de Gen2 em uma região com suporte.

## <a name="automated-schedule-and-region-availability-table"></a>Agendamento automatizado e tabela de disponibilidade na região

A tabela a seguir resume por região quando a camada de computação inferior Gen2 estará disponível e quando as atualizações automáticas serão iniciadas. As datas estão sujeitas à alteração. Consulte a tabela com frequência para saber quando a sua região estará disponível.

\* indica que um agendamento específico para a região está indisponível no momento.

| **Região** | **Inferior Gen2 disponíveis** | **Começam a atualizações automáticas** |
|:--- |:--- |:--- |
| Leste da Austrália |Disponível |1 de junho de 2019 |
| Sudeste da Austrália |15 de abril de 2019 |1º de maio de 2019 |
| Sul do Brasil |15 de maio de 2019 |1 de junho de 2019 |
| Canadá Central |Disponível |1 de junho de 2019 |
| Leste do Canadá |\* |\* |
| Centro dos EUA |Disponível |1 de junho de 2019 |
| Leste da China |\* |\* |
| Leste da China 2 |\* |\* |
| Norte da China |\* |\* |
| Norte da China 2 |\* |\* |
| Ásia Oriental |Disponível |1 de junho de 2019 |
| Leste dos EUA |Disponível |1 de junho de 2019 |
| Leste dos EUA 2 |Disponível |1 de junho de 2019 |
| França Central |\* |1 de junho de 2019 |
| Alemanha Central |\* |\* |
| Centro-oeste da Alemanha |1 de setembro de 2019|2 de janeiro de 2020 |
| Centro da Índia |Disponível |1 de junho de 2019 |
| Sul da Índia |15 de abril de 2019 |1 de junho de 2019 |
| Leste do Japão |Disponível |1 de junho de 2019 |
| Oeste do Japão |Disponível |1º de maio de 2019 |
| Coreia Central |15 de abril de 2019 |1 de junho de 2019 |
| Sul da Coreia |15 de abril de 2019 |1º de maio de 2019 |
| Centro-Norte dos EUA |15 de abril de 2019 |1º de maio de 2019 |
| Norte da Europa |Disponível |1 de junho de 2019 |
| Centro-Sul dos Estados Unidos |Disponível |1 de junho de 2019 |
| Sudeste da Ásia |Disponível |1 de junho de 2019 |
| Sul do Reino Unido |15 de abril de 2019 |1 de junho de 2019 |
| Oeste do Reino Unido |\*|\* |
| Centro-Oeste dos EUA |2 de setembro de 2019 |2 de janeiro de 2020|
| Europa Ocidental |Disponível |1 de junho de 2019 |
| Oeste dos EUA |15 de abril de 2019 |1 de junho de 2019 |
| Oeste dos EUA 2 |Disponível |1 de junho de 2019 |

## <a name="automatic-upgrade-process"></a>Processo de atualização automática

Com base no gráfico de disponibilidade acima, podemos vai ser agendar atualizações automatizadas para suas instâncias Gen1. Para evitar interrupções inesperadas de disponibilidade do data warehouse, as atualizações automatizadas serão agendadas durante o seu agendamento de manutenção. Para saber mais sobre agendamentos, consulte [Exibir um agendamento de manutenção](viewing-maintenance-schedule.md)

O processo de atualização envolverá uma breve queda na conectividade (aproximadamente 5 min) como podemos reiniciar seu data warehouse.  Depois que o data warehouse for reiniciado, ele estará totalmente disponível para uso. No entanto, você pode enfrentar uma degradação no desempenho durante o processo de atualização continua a atualizar os arquivos de dados em segundo plano. O tempo total para a degradação do desempenho varia de acordo com o tamanho dos seus arquivos de dados.

Você também pode agilizar o processo de atualização do arquivo de dados executando [Alter Index rebuild](sql-data-warehouse-tables-index.md) em todas as tabelas columnstore primárias usando uma classe maior de SLO e recursos após a reinicialização.

> [!NOTE]
> Alter Index rebuild é uma operação offline e as tabelas não estarão disponíveis até que a recompilação esteja concluída.

## <a name="self-upgrade-to-gen2"></a>Autoatualização para Gen2

Você pode optar por atualizar automaticamente, seguindo estas etapas em um data warehouse existente do Gen1. Se você optar por atualizar automaticamente, você deve concluí-lo antes do início do processo de atualização automática em sua região. Isso garante que você evite qualquer risco das atualizações automáticas, causando um conflito.

Há duas opções ao realizar uma autoatualização.  Você pode atualizar o seu data warehouse atual in-loco ou restaurar um data warehouse Gen1 em uma instância Gen2.

- [Atualização in-loco](upgrade-to-latest-generation.md): essa opção atualizará o seu data warehouse Gen1 existente para Gen2. O processo de atualização envolverá uma breve queda na conectividade (aproximadamente 5 min) como podemos reiniciar seu data warehouse.  Depois que o data warehouse for reiniciado, ele estará totalmente disponível para uso. Se você enfrentar problemas durante a atualização, abra uma [solicitação de suporte](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) e fazer referência a "Atualização de Gen2" como a possível causa.
- [Atualização do ponto de restauração](sql-data-warehouse-restore.md): crie um ponto de restauração definido pelo usuário no seu data warehouse Gen1 atual e, em seguida, restaure diretamente em uma instância Gen2. O data warehouse Gen1 existente permanecerá em vigor. Depois que a restauração for concluída, o seu data warehouse Gen2 estará completamente disponível para uso.  Depois de executar todos os processos de teste e validação na instância Gen2 restaurada, a instância Gen1 original pode ser excluída.

   - Etapa 1: No portal do Azure, [crie um ponto de restauração definido pelo usuário](sql-data-warehouse-restore.md#create-a-user-defined-restore-point-using-the-azure-portal).
   - Etapa 2: Ao restaurar usando um ponto de restauração definido pelo usuário, configure o "nível de desempenho" para a camada preferencial de Gen2.

Talvez você perceba um período de degradação no desempenho enquanto o processo continua atualizando os arquivos de dados em segundo plano. O tempo total para a degradação do desempenho varia de acordo com o tamanho dos seus arquivos de dados.

Para acelerar o processo de migração de dados em segundo plano, você pode forçar imediatamente a movimentação de dados executando [Alter Index rebuild](sql-data-warehouse-tables-index.md) em todas as tabelas de columnstore primárias que você estaria consultando em uma classe de recursos e SLO maior.

> [!NOTE]
> Alter Index rebuild é uma operação offline e as tabelas não estarão disponíveis até que a recompilação esteja concluída.

Se houver algum problema com o data warehouse, crie uma [solicitação de suporte](sql-data-warehouse-get-started-create-support-ticket.md) e faça referência à "atualização de Gen2" como a possível causa.

Para saber mais, confira [Atualizar para Gen2](upgrade-to-latest-generation.md).

## <a name="migration-frequently-asked-questions"></a>Perguntas frequentes sobre migração

**P: Gen2 custo igual Gen1?**

- R: Sim.

**P: Como as atualizações afetará meus scripts de automação?**

- R: Qualquer script de automação que faça referência a um Objetivo de Nível de Serviço deve ser alterado para corresponder ao equivalente de Gen2.  Veja os detalhes [aqui](upgrade-to-latest-generation.md#sign-in-to-the-azure-portal).

**P: Quanto tempo uma atualização automática normalmente demora?**

- R: Você pode fazer a atualização in-loco ou usando um ponto de restauração.  
   - A atualização in-loco fará com que o data warehouse seja pausado momentaneamente e retomado.  Um processo em segundo plano continuará enquanto o data warehouse estiver online.  
   - Será mais demorado se você atualizar usando um ponto de restauração, pois a atualização passará pelo processo completo de restauração.

**P: Quanto tempo levará a atualização automática?**

- R: O tempo de inatividade real para a atualização é apenas o tempo necessário para pausar e retomar o serviço, que fica entre 5 e 10 minutos. Após esse breve tempo de inatividade, um processo em segundo plano executará uma migração de armazenamento. A duração do processo em segundo plano depende do tamanho do seu data warehouse.

**P: Quando a atualização automática ocorrerá?**

- R: Durante o seu agendamento de manutenção. Aproveitar o agendamento de manutenção escolhido minimizará a interrupção nos seus negócios.

**P: O que devo fazer se meu processo de atualização em segundo plano parece estar travados?**

 - R: Inicie uma reindexação de suas tabelas Columnstore. Observe que a reindexação da tabela será offline durante essa operação.

**P: E se Gen2 não tem o objetivo de nível de serviço tem no Gen1?**
- R: Se você estiver executando um DW600 ou DW1200 em Gen1, é aconselhável usar DW500c ou DW1000c, respectivamente, pois Gen2 fornece mais memória e recursos, além de um desempenho mais alto do que Gen1.

**P: Desabilitar backup geográfico?**
- R:  Não. O backup geográfico é um recurso corporativo para preservar a disponibilidade do seu data warehouse no caso de uma região se tornar indisponível. Abra uma [solicitação de suporte](sql-data-warehouse-get-started-create-support-ticket.md) em caso de outras dúvidas.

**P: Há uma diferença na sintaxe do T-SQL entre Gen1 e Gen2?**

- R: Não há nenhuma alteração na sintaxe da linguagem T-SQL de Gen1 para Gen2.

**P: Gen2 dá suporte à manutenção do Windows?**

- R: Sim.

**P: Será capaz de criar uma nova instância de Gen1 após atualização minha região?**

- R:  Não. Depois que uma região for atualizada, a criação de novas instâncias de Gen1 será desabilitada.

## <a name="next-steps"></a>Próximas etapas

- [Etapas de atualização](upgrade-to-latest-generation.md)
- [Janelas de manutenção](maintenance-scheduling.md)
- [Monitor de integridade de recursos](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Revisão antes de iniciar uma migração](upgrade-to-latest-generation.md#before-you-begin)
- [Atualização in-loco e atualização de um ponto de restauração](upgrade-to-latest-generation.md)
- [Crie um ponto restauração definido pelo usuário](sql-data-warehouse-restore.md#restore-through-the-azure-portal)
- [Saiba como restaurar para Gen2](sql-data-warehouse-restore.md#restore-an-active-or-paused-database-using-the-azure-portal)
- [Abra uma solicitação de suporte do SQL Data Warehouse](https://go.microsoft.com/fwlink/?linkid=857950)
