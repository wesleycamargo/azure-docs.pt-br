---
title: "Desempenho do Banco de Dados SQL: camadas de serviço | Microsoft Docs"
description: "Compare as camadas de serviço do Banco de Dados SQL."
keywords: "opções de banco de dados, desempenho do banco de dados"
services: sql-database
documentationcenter: 
author: janeng
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: resources
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
wms.date: 04/26/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: a3ca1527eee068e952f81f6629d7160803b3f45a
ms.openlocfilehash: 0ab804ee1dc25f1e44be856564ac8ffa87c54dea
ms.contentlocale: pt-br
ms.lasthandoff: 04/27/2017


---
# <a name="sql-database-options-and-performance-understand-whats-available-in-each-service-tier"></a>Opções e desempenho de Banco de Dados SQL: compreender o que está disponível em cada camada de serviço

[Banco de dados SQL do Azure](sql-database-technical-overview.md) oferece quatro camadas de serviço: **Basic**, **Standard**, **Premium** e **Premium RS**. Cada camada de serviço tem vários níveis de desempenho para lidar com cargas de trabalho diferentes. Os níveis de desempenho mais elevados fornecem recursos adicionais, projetados para oferecer uma taxa de transferência crescente. Você pode alterar as camadas de serviços e os níveis de desempenho sem tempo de inatividade. Todas as camadas de serviço, Basic, Standard e Premium têm um SLA de tempo de atividade de 99,99%, opções de continuidade dos negócios flexíveis, recursos de segurança e cobrança por hora. A camada Premium RS fornece os mesmos níveis de desempenho, recursos de segurança e recursos de continuidade de negócios que a camada Premium, mas com um SLA reduzido.

> [!IMPORTANT]
> Bancos de dados Premium RS operam com um número menor de cópias redundantes que bancos de dados Premium ou Standard. Portanto, em caso de falha de serviço, você precisará recuperar seu banco de dados de um backup com um retardo de até 5 minutos.
>

Você pode criar bancos de dados individuais com recursos dedicados dentro de uma camada de serviço em um [nível de desempenho](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels) específico. Você também pode criar bancos de dados em um [pool elástico](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus) no qual os recursos são compartilhados entre os bancos de dados. Os recursos disponíveis para bancos de dados individuais são expressos em termos de DTUs (unidades de transação de banco de dados) e para pools elásticos em termos de eDTUs (unidades de transação de banco de dados elásticos). Para saber mais sobre DTUs e eDTUs, confira [O que é uma DTU?](sql-database-what-is-a-dtu.md) 

## <a name="choosing-a-service-tier"></a>Como escolher uma camada de serviço
A tabela a seguir fornece exemplos das camadas mais adequadas para cargas de trabalho de aplicativos diferentes.

| Camada de serviço | Cargas de trabalho de destino |
| :--- | --- |
| **Básico** | Mais adequada para um banco de dados pequeno, suporta normalmente uma única operação ativa em um determinado momento. Os exemplos incluem bancos de dados usados para desenvolvimento ou teste ou aplicativos de pequena escala usados com pouca frequência. |
| **Standard** |Para ele ir para a opção para aplicativos em nuvem com requisitos de desempenho de E/S baixo a médio, oferecendo suporte a várias consultas simultâneas. Os exemplos incluem aplicativos da web ou de grupo de trabalho. |
| **Premium** | Projetado para volume transacional alto com requisitos de desempenho de e/s altos, suportando muitos usuários simultâneos. Os exemplos são bancos de dados com suporte a aplicativos de missão críticos. |
| **Premium RS** | Projetado para cargas de trabalho com uso intensivo de E/S que não exigem garantias de alta disponibilidade. Exemplos de cargas de trabalho de alto desempenho ou uma carga de trabalho analítica onde o banco de dados não é o sistema de registro. |
|||

Primeiro, decida se deseja executar um único banco de dados com uma quantidade definida de recursos dedicados ou se você deseja compartilhar um pool de recursos em um grupo de bancos de dados. Examine as [considerações de pool elástico](sql-database-elastic-pool.md). Para decidir sobre uma camada de serviço, inicie determinando os recursos de banco de dados mínimos que você precisa:

| **Recursos de camada de serviço** | **Básico** | **Standard** | **Premium** | **Premium RS**|
| :-- | --: | --: | --: | --: |
| Tamanho máximo de banco de dados individual | 2 GB | 250 GB | 4 TB*  | 500 GB  |
| Tamanho máximo do banco de dados em um pool elástico | 156 GB | 2.9 TB | 500 GB | 500 GB |
| Número máximo de bancos de dados por pool | 500  | 500 | 100 | 100 |
| Período de retenção do backup de banco de dados | 7 dias | 35 dias | 35 dias | 35 dias |
||||||

> [!IMPORTANT]
> Essas opções de armazenamento adicionais já estão disponíveis nas seguintes regiões: Leste dos EUA 2, Oeste dos EUA, Europa Ocidental, Sudeste Asiático, Leste do Japão, Leste da Austrália, Central do Canadá e Leste do Canadá. Consulte [Limitações atuais 4 TB](sql-database-service-tiers.md#current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize)
>

Depois de determinar a camada de serviço mínima, você estará pronto para determinar o nível de desempenho do banco de dados (o número de DTUs). Os níveis de desempenho do S2 e S3 padrão são quase sempre um bom ponto de partida. Para bancos de dados com altas exigências de CPU ou E/S, os níveis de desempenho Premium são o ponto de partida correto. O Premium oferece mais CPU e começa em 10 vezes mais E/S em comparação com o nível de desempenho Standard mais alto.

## <a name="single-database-service-tiers-and-performance-levels"></a>Camadas de serviço e níveis de desempenho de banco de dados individual
Para bancos de dados individuais, há vários níveis de desempenho dentro de cada camada de serviço. Você tem a flexibilidade de escolher o nível que melhor atenda às demandas de sua carga de trabalho, usando o [Portal do Azure](scripts/sql-database-monitor-and-scale-database-powershell.md), o [PowerShell](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database), o Transact-SQL, o C# e a API REST.  

Independentemente do número de bancos de dados hospedados, o banco de dados ainda obterá um conjunto garantido de recursos e as características de desempenho esperadas de seu banco de dados não são afetadas.

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> Para obter uma explicação detalhada de todas as outras linhas nesta tabela de camadas de serviço, consulte [Limites e recursos da camada de serviço](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).
> 

## <a name="scaling-up-or-scaling-down-a-single-database"></a>Escalar verticalmente ou reduzir um banco de dados

Depois de escolher inicialmente um nível de desempenho e da camada de serviço, você pode dimensionar um banco de dados para cima ou para baixo dinamicamente com base na experiência real. Se precisar expandir ou reduzir, você poderá alterar facilmente as camadas do banco de dados usando o Portal do Azure, o [PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md), o [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database), o C# e a API REST. 

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

A alteração da camada de serviço e/ou nível de desempenho de um banco de dados cria uma réplica do banco de dados original com o novo nível de desempenho e então faz a transição das conexões para réplica. Nenhum dado é perdido durante esse processo, mas durante o breve momento em que realizamos a transição para a réplica, conexões com o banco de dados são desabilitadas, então algumas transações em andamento podem ser revertidas. Essa janela varia, mas é em média inferior a quatro segundos e, em mais de 99% dos casos, de menos de 30 segundos. Se houver grandes números de transações em andamento no momento em que as conexões estiverem desabilitadas, esta janela poderá ser maior.  

A duração de todo o processo de expansão depende a camada tamanho e de serviço do banco de dados antes e após a alteração. Por exemplo, um banco de dados de 250 GB que está mudando para, de ou dentro de uma camada de serviço Standard deverá ser concluída dentro de 6 horas. Um banco de dados do mesmo tamanho cujos níveis de desempenho estão mudando dentro da camada de serviço Premium deverá ser concluído dentro de 3 horas.

* Para fazer downgrade de um banco de dados, este deve ter um tamanho menor do que o máximo permitido para a camada de serviço de destino. 
* Ao atualizar um banco de dados com [Replicação Geográfica](sql-database-geo-replication-portal.md) habilitada, é necessário primeiro atualizar seus bancos de dados secundários para o nível de desempenho desejado antes de atualizar o banco de dados primário.
* Ao fazer downgrade de uma camada de serviço Premium, primeiro, você deve encerrar todos os relacionamentos de Replicação Geográfica. Você pode seguir as etapas descritas no tópico [Recuperação de uma interrupção](sql-database-disaster-recovery.md) para interromper o processo de replicação entre os bancos de dados primários e secundários ativos.
* As ofertas de serviço de restauração são diferentes para as várias camadas de serviço. Se estiver fazendo downgrade, talvez você perca a capacidade de fazer uma restauração pontual ou tenha um período menor de retenção do backup. Para saber mais, confira [Backup e restauração do Banco de dados SQL do Azure](sql-database-business-continuity.md).
* As novas propriedades do banco de dados não serão aplicadas até que as alterações sejam concluídas.

## <a name="elastic-pool-service-tiers-and-performance-in-edtus"></a>Camadas de serviço e desempenho em eDTUs do pool elástico

Os pools permitem que esses bancos de dados compartilhem e consumam os recursos de eDTU sem a necessidade de atribuir um nível de desempenho específico para todos os bancos de dados no pool. Por exemplo, um banco de dados individual em um pool Standard pode usar de 0 eDTUs até o máximo de eDTU de banco de dados configurado por você durante a definição do pool. Os pools permitem que vários bancos de dados com diferentes cargas de trabalho usem os recursos de eDTU disponíveis para todo o pool de forma eficiente. Confira [Considerações de preço e desempenho para um pool elástico](sql-database-elastic-pool.md) para obter detalhes.

A tabela a seguir descreve as características das camadas de serviço do pool.

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

Cada banco de dados dentro de um pool também cumpre as características do banco de dados individual para essa camada. Por exemplo, o pool Basic tem um limite máximo de sessões por pool de 4800 a 28800, mas um banco de dados individual dentro de um pool Básico tem um limite de banco de dado de 300 sessões.

## <a name="scaling-up-or-scaling-down-an-elastic-pool"></a>Escalar verticalmente ou reduzir um pool Elástico

Depois de escolher inicialmente uma camada de serviço e um nível de desempenho, você poderá ampliar ou reduzir o pool elástico dinamicamente com base na experiência real. 

* Normalmente, a alteração das eDTUs mínimas ou das eDTUs máximas por banco de dados é um processo concluído em cinco minutos ou menos.
* O tempo levado para alterar o tamanho do pool (eDTUs) depende do tamanho combinado de todos os bancos de dados no pool. As alterações levam, em média, 90 minutos ou menos a cada 100 GB. Por exemplo, se o espaço total de todos os bancos de dados no pool for de 200 GB, a latência prevista para alterar os eDTUs do pool será de 3 horas por pool ou menos.

Para obter etapas detalhadas, confira [Gerenciamento de um pool elástico no portal do Azure](sql-database-elastic-pool-manage-portal.md), [Gerenciamento de um pool elástico com o Powershell](scripts/sql-database-monitor-and-scale-pool-powershell.md), [Gerenciamento de um pool Elástico com Transact-SQL](sql-database-elastic-pool-manage-tsql.md), ou [Gerenciamento de um pool Elástico com C#](sql-database-elastic-pool-manage-csharp.md).

## <a name="creating-or-upgrading-to-4tb"></a>Criação ou atualização de 4 TB

As seções a seguir discutem os detalhes de implementação para a opção de 4 TB.

### <a name="creating-in-the-azure-portal"></a>Criação no portal do Azure

Ao criar um P11/P15, a opção de armazenamento padrão de 1TB é pré-selecionada. Para bancos de dados localizados em uma das regiões com suporte, você pode aumentar o número máximo de armazenamento para 4 TB. Para todas as outras regiões, o controle deslizante de armazenamento não pode ser alterado. O preço não é alterado quando você seleciona 4 TB de armazenamento incluído.

### <a name="creating-using-powershell-or-transact-sql"></a>Criação e uso do PowerShell ou Transact-SQL

Ao criar um banco de dados P11/P15, você pode definir o valor do maxsize para 1 TB (padrão) ou 4 TB. Valores de "1024" e "4096 GB" também são aceitos. Se você escolher a opção maxsize de 4 TB, o comando create falhará com um erro se o banco de dados for provisionado em uma região sem suporte.

### <a name="upgrading-to-4tb"></a>Atualização para 4 TB 

Para bancos de dados P11 e P15 existentes localizados em uma das regiões com suporte, você pode aumentar o maxsize do armazenamento para 4 TB. Isso pode ser feito no portal do Azure, no PowerShell ou com o Transact-SQL. O exemplo a seguir mostra o maxsize sendo alterado usando o comando ALTER DATABASE:

 ```sql
ALTER DATABASE <myDatabaseName> 
   MODIFY (MAXSIZE = 4096 GB);
```

A atualização de um banco de dados existente P11 ou P15 só pode ser executada por um logon principal no nível de servidor ou por membros da função de banco de dados dbmanager. Se executada em uma região com suporte a configuração será atualizada imediatamente. Isso pode ser verificado usando [SELECT DATABASEPROPERTYEX](https://msdn.microsoft.com/library/ms186823.aspx) ou inspecionando o tamanho do banco de dados no portal do Azure. O banco de dados permanecerá online durante o processo de atualização. No entanto, você não poderá utilizar os 4 TB completos de armazenamento até que os arquivos de banco de dados reais sejam atualizados para o novo tamanho máximo. O período de tempo necessário depende do tamanho do banco de dados que está sendo atualizado.  

### <a name="error-messages"></a>Mensagens de erro
Ao criar ou atualizar um banco de dados P11/P15 em uma região sem suporte, a operação de criação ou atualização falhará com a seguinte mensagem de erro: **Os bancos de dados P11 e P15 com até 4 TB de armazenamento estão disponíveis no Leste dos EUA 2, Oeste dos EUA, Sudeste Asiático, Europa Ocidental, Leste do Canadá, Central do Canadá, Leste do Japão e Leste da Austrália.**

## <a name="current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize"></a>Limitações atuais de bancos de dados P11 e P15 com tamanho máximo de 4 TB

- Ao criar ou atualizar um banco de dados P11 ou P15, você só pode escolher entre 1 TB e 4 TB de tamanho máximo. Atualmente, não há suporte para tamanhos de armazenamento intermediários.
- O tamanho máximo do banco de dados de 4 TB não pode ser alterado para 1 TB, mesmo que o armazenamento real usado esteja abaixo de 1 TB. Portanto, não é possível transformar um P11-4TB/P15-4TB em um P11-1TB/P15-1TB ou um nível de desempenho mais baixo (por exemplo, para P1-P6) até que estejamos fornecendo opções adicionais de armazenamento para o restante dos níveis de desempenho. Esta restrição também se aplica aos cenários de restauração e de cópia, incluindo restauração pontual e geográfica, retenção de backup a longo prazo e cópia de banco de dados. Quando um banco de dados é configurado com a opção de 4 TB, todas as operações de restauração desse banco de dados devem estar em um P11/P15 com tamanho máximo de 4 TB.
- Para cenários com Replicação Geográfica Ativa:
   - Configurar uma relação de replicação geográfica: se o banco de dados primário for P11 ou P15, os secundários também devem ser P11 ou P15; os níveis de desempenho inferiores serão rejeitadas como secundários porque não são capazes de dar suporte a 4 TB.
   - Atualizando o banco de dados primário em uma relação de replicação geográfica: alterar o tamanho máximo de 4 TB em um banco de dados primário disparará a mesma alteração no banco de dados secundário. As duas atualizações devem ser bem-sucedidas para que a alteração no primário entre em vigor. Limitações de região para a opção de 4TB se aplicam (confira acima). Se o secundário estiver em uma região que não oferece suporte a 4 TB, o primário não será atualizado.
- Não há suporte para o uso do serviço de Importação/Exportação para carregar bancos de dados P11-4TB/P15-4TB. Use SqlPackage.exe para [importar](sql-database-import.md) e [exportar](sql-database-export.md) dados.

## <a name="next-steps"></a>Próximas etapas

* Conheça os detalhes dos [pools elásticos](sql-database-elastic-pool.md) e [considerações sobre o preço e o desempenho dos pools elásticos](sql-database-elastic-pool.md).
* Saiba como [Monitorar, gerenciar e redimensionar pools elásticos](sql-database-elastic-pool-manage-portal.md) e [Monitorar o desempenho de bancos de dados individuais](sql-database-single-database-monitor.md).
* Agora que você conhece as camadas do Banco de Dados SQL, teste-as usando uma versão de [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) e [aprenda a criar seu primeiro banco de dados SQL](sql-database-get-started-portal.md).
* Para cenários de migração, use a [Calculadora de DTU](http://dtucalculator.azurewebsites.net/) para ter uma ideia aproximada do número de DTUs necessários. 


