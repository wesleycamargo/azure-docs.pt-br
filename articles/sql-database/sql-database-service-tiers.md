---
title: "Níveis de serviço e desempenho do Banco de Dados SQL do Azure | Microsoft Docs"
description: "Comparar os níveis de serviço e desempenho do Banco de Dados SQL para bancos de dados únicos e apresentar pools elásticos de SQL"
keywords: "opções de banco de dados, desempenho do banco de dados"
services: sql-database
documentationcenter: 
author: janeng
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 06/30/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 68d55d2dd088ce6350bd65b79206f161f9d3d788
ms.contentlocale: pt-br
ms.lasthandoff: 07/06/2017


---
<a id="what-performance-options-are-available-for-an-azure-sql-database" class="xliff"></a>

# Quais opções de desempenho estão disponíveis para um Banco de Dados SQL do Azure

O [Banco de Dados SQL do Azure](sql-database-technical-overview.md) oferece quatro níveis de serviço para bancos de dados individuais e [em pool](sql-database-elastic-pool.md). Esses níveis de serviço são: **Básica**, **Standard**, **Premium** e **Premium RS**. Dentro de cada nível de serviço estão vários níveis de desempenho ([DTUs](sql-database-what-is-a-dtu.md)) e opções de armazenamento para lidar com cargas de trabalho e tamanhos de dados diferentes. Os níveis de desempenho mais elevados fornecem recursos adicionais de computação e armazenamento projetados para oferecer taxa de transferência e capacidade cada vez maiores. Você pode alterar os níveis de serviço, os níveis de desempenho e o armazenamento sem tempo de inatividade. 
- Os níveis serviço **Básico**, **Standard** e **Premium** têm um SLA de tempo de atividade de 99,99%, opções de continuidade dos negócios flexíveis, recursos de segurança e cobrança por hora. 
- O nível **Premium RS** oferece os mesmos níveis de desempenho que o nível Premium, com um SLA reduzido, pois ele é executado com uma quantidade menor de cópias redundantes do que um banco de dados nos outros níveis de serviço. Portanto, em caso de falha de serviço, você precisará recuperar seu banco de dados de um backup com um retardo de até 5 minutos.

> [!IMPORTANT]
> Um Banco de Dados SQL do Azure obtém um conjunto garantido de recursos e as características de desempenho esperadas de seu banco de dados não são afetadas por outro banco de dados no Azure. 

<a id="choosing-a-service-tier" class="xliff"></a>

## Como escolher uma camada de serviço
A tabela a seguir fornece exemplos das camadas mais adequadas para cargas de trabalho de aplicativos diferentes.

| Camada de serviço | Cargas de trabalho de destino |
| :--- | --- |
| **Básico** | Mais adequada para um banco de dados pequeno, suporta normalmente uma única operação ativa em um determinado momento. Os exemplos incluem bancos de dados usados para desenvolvimento ou teste ou aplicativos de pequena escala usados com pouca frequência. |
| **Standard** |Para ele ir para a opção para aplicativos em nuvem com requisitos de desempenho de E/S baixo a médio, oferecendo suporte a várias consultas simultâneas. Os exemplos incluem aplicativos da web ou de grupo de trabalho. |
| **Premium** | Projetado para volume transacional alto com requisitos de desempenho de e/s altos, suportando muitos usuários simultâneos. Os exemplos são bancos de dados com suporte a aplicativos de missão críticos. |
| **Premium RS** | Projetado para cargas de trabalho com uso intensivo de E/S que não exigem garantias de alta disponibilidade. Exemplos de cargas de trabalho de alto desempenho ou uma carga de trabalho analítica onde o banco de dados não é o sistema de registro. |
|||

Você pode criar bancos de dados individuais com recursos dedicados dentro de um nível de serviço em um [nível de desempenho](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels) específico ou você também pode criar bancos de dados dentro de um [pool elástico de SQL](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus). Em um pool elástico de SQL, os recursos de computação e armazenamento são compartilhados entre vários bancos de dados dentro de um único servidor lógico. 

Os recursos disponíveis para bancos de dados individuais são expressos em termos de DTUs (Unidades de Transmissão de Dados), e os recursos para pools elásticos de SQL são expressos em termos de eDTUs (Unidades de Transação de Banco de Dados Elástico). Para saber mais sobre DTUs e eDTUs, confira [O que são DTUs e eDTUs?](sql-database-what-is-a-dtu.md).

Para decidir sobre uma camada de serviço, inicie determinando os recursos de banco de dados mínimos que você precisa:

| **Recursos de camada de serviço** | **Básico** | **Standard** | **Premium** | **Premium RS**|
| :-- | --: | --: | --: | --: |
| Tamanho máximo de banco de dados individual | 2 GB | 250 GB | 4 TB*  | 500 GB  |
| Tamanho máximo do pool elástico | 156 GB | 2.9 TB | 4 TB* | 750 GB |
| Tamanho máximo do banco de dados em um pool elástico | 2 GB | 250 GB | 500 GB | 500 GB |
| Número máximo de bancos de dados por pool | 500  | 500 | 100 | 100 |
| Máximo de DTUs de um banco de dados individual | 5 | 100 | 4000 | 1000 |
| Máximo de DTUs por banco de dados em um pool elástico | 5 | 3000 | 4000 | 1000 |
| Período de retenção do backup de banco de dados | 7 dias | 35 dias | 35 dias | 35 dias |
||||||

> [!IMPORTANT]
> O armazenamento de até 4 TB está atualmente disponível nas seguintes regiões: Leste dos EUA 2, Oeste dos EUA, Gov. EUA - Virgínia, Europa Ocidental, Alemanha Central, Sudeste Asiático, Leste do Japão, Leste da Austrália, Canadá Central e Leste do Canadá. Consulte [Limitações atuais 4 TB](sql-database-service-tiers.md#current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize)
>

Depois de determinar o nível de serviço apropriado, você estará pronto para determinar o nível de desempenho (o número de DTUs) e a quantidade de armazenamento para o banco de dados. 

- Os níveis de desempenho S2 e S3 no nível **Standard** são quase sempre um bom ponto de partida. 
- Para bancos de dados com requisitos altos de CPU ou E/S, os níveis de desempenho no nível **Premium** são o ponto de partida correto. 
- O nível **Premium** oferece mais CPU e começa com 10 vezes mais E/S em comparação com o nível de desempenho mais alto no nível **Standard**.
- O nível **PremiumRS** oferece o desempenho do nível **Premium** por um custo menor, mas com um SLA reduzido.

> [!IMPORTANT]
> Examine o tópico [Pools elásticos de SQL](sql-database-elastic-pool.md) para obter os detalhes sobre o agrupamento de bancos de dados nos pools elásticos de SQL para compartilhar recursos de computação e armazenamento. O restante deste tópico se concentra em níveis de serviço e níveis de desempenho para bancos de dados único.
>

<a id="single-database-service-tiers-and-performance-levels" class="xliff"></a>

## Camadas de serviço e níveis de desempenho de banco de dados individual
Para bancos de dados individuais, há vários níveis de desempenho e quantidades de armazenamento dentro de cada nível de serviço. 

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

<a id="scaling-up-or-scaling-down-a-single-database" class="xliff"></a>

## Escalar verticalmente ou reduzir um banco de dados

Depois de escolher inicialmente um nível de desempenho e da camada de serviço, você pode dimensionar um banco de dados para cima ou para baixo dinamicamente com base na experiência real.  

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

A alteração da camada de serviço e/ou nível de desempenho de um banco de dados cria uma réplica do banco de dados original com o novo nível de desempenho e então faz a transição das conexões para réplica. Nenhum dado é perdido durante esse processo, mas durante o breve momento em que realizamos a transição para a réplica, conexões com o banco de dados são desabilitadas, então algumas transações em andamento podem ser revertidas. O período da transição varia, mas fica geralmente abaixo de 4 segundos e menos de 30 segundos 99% do tempo. Se houver muitas transações em andamento no momento de desabilitação das conexões, o período de transição poderá ser maior.  

A duração de todo o processo de expansão depende a camada tamanho e de serviço do banco de dados antes e após a alteração. Por exemplo, um banco de dados de 250 GB que está mudando para, de ou dentro de uma camada de serviço Standard deverá ser concluída dentro de 6 horas. Um banco de dados do mesmo tamanho cujos níveis de desempenho estão mudando dentro da camada de serviço Premium deverá ser concluído dentro de 3 horas.

* Se você estiver atualizando para um nível de serviço ou desempenho mais alto, o tamanho máximo do banco de dados não aumenta, a menos que você especifique explicitamente um tamanho máximo maior.
* Para fazer downgrade de um banco de dados, este deve ter um tamanho menor do que o máximo permitido para a camada de serviço de destino. 
* Ao atualizar um banco de dados com [replicação geográfica](sql-database-geo-replication-portal.md) habilitada, atualize seus bancos de dados secundários para o nível de desempenho desejado antes de atualizar o banco de dados primário (orientação geral).
* Ao fazer downgrade de um nível de serviço **Premium** para um nível de serviço inferior, primeiro você deve encerrar todos os relacionamentos de replicação geográfica. Você pode seguir as etapas descritas no tópico [Recuperação de uma interrupção](sql-database-disaster-recovery.md) para interromper o processo de replicação entre os bancos de dados primários e secundários.
* As ofertas de serviço de restauração são diferentes para as várias camadas de serviço. Se você estiver fazendo downgrade do nível **Básico**, você terá um período de retenção de backup inferior - confira [Backups de Banco de Dados SQL do Azure](sql-database-automated-backups.md).
* As novas propriedades do banco de dados não serão aplicadas até que as alterações sejam concluídas.


<a id="current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize" class="xliff"></a>

## Limitações atuais de bancos de dados P11 e P15 com tamanho máximo de 4 TB

Há suporte para um tamanho máximo de 4 TB para banco de dados P11 e P15 em algumas regiões (conforme discutido anteriormente). As seguintes considerações e limitações se aplicam aos bancos de dados P11 e P15 com tamanho máximo de 4 TB:

- Se você escolher a opção de tamanho máximo de 4 TB ao criar um banco de dados (usando um valor de 4 TB ou 4096 GB), o comando create falhará com um erro se o banco de dados for provisionado em uma região sem suporte.
- Para bancos de dados P11 e P15 existentes localizados em uma das regiões com suporte, você pode aumentar o maxsize do armazenamento para 4 TB. Isso pode ser verificado usando [SELECT DATABASEPROPERTYEX](https://msdn.microsoft.com/library/ms186823.aspx) ou inspecionando o tamanho do banco de dados no portal do Azure. A atualização de um banco de dados existente P11 ou P15 só pode ser executada por um logon principal no nível de servidor ou por membros da função de banco de dados dbmanager. 
- Se uma operação de atualização for executada em uma região com suporte, a configuração será atualizada imediatamente. O banco de dados permanece online durante o processo de atualização. No entanto, não é possível utilizar os 4 TB completos de armazenamento até que os arquivos de banco de dados reais sejam atualizados para o novo tamanho máximo. O período de tempo necessário depende do tamanho do banco de dados que está sendo atualizado.  
- Ao criar ou atualizar um banco de dados P11 ou P15, você só pode escolher entre 1 TB e 4 TB de tamanho máximo. Atualmente, não há suporte para tamanhos de armazenamento intermediários. Ao criar um P11/P15, a opção de armazenamento padrão de 1 TB é pré-selecionada. Para bancos de dados localizados em uma das regiões com suporte, você pode aumentar o número máximo de armazenamento para 4 TB para um banco de dados individual novo ou existente. Para todas as outras regiões, o tamanho máximo não pode ultrapassar 1 TB. O preço não é alterado quando você seleciona 4 TB de armazenamento incluído.
- O tamanho máximo do banco de dados de 4 TB não pode ser alterado para 1 TB, mesmo que o armazenamento real usado esteja abaixo de 1 TB. Portanto, não é possível transformar um P11-4TB/P15-4TB em um P11-1TB/P15-1TB ou um nível de desempenho mais baixo, por exemplo, para P1-P6, até que estejamos fornecendo opções adicionais de armazenamento para o restante dos níveis de desempenho. Esta restrição também se aplica aos cenários de restauração e de cópia, incluindo restauração pontual e geográfica, retenção de backup a longo prazo e cópia de banco de dados. Quando um banco de dados é configurado com a opção de 4 TB, todas as operações de restauração desse banco de dados devem ser executadas em um P11/P15 com tamanho máximo de 4 TB.
- Ao criar ou atualizar um banco de dados P11/P15 em uma região sem suporte, a operação de criação ou atualização falhará com a seguinte mensagem de erro: **Os bancos de dados P11 e P15 com até 4 TB de armazenamento estão disponíveis no Leste dos EUA 2, Oeste dos EUA, Gov. EUA - Virgínia, Europa Ocidental, Alemanha Central, Sudeste Asiático, Leste do Japão, Leste da Austrália, Canadá Central e Leste do Canadá.**
- Para cenários com replicação geográfica ativa:
   - Configurar uma relação de replicação geográfica: se o banco de dados primário for P11 ou P15, os secundários também devem ser P11 ou P15; os níveis de desempenho inferiores serão rejeitadas como secundários porque não são capazes de dar suporte a 4 TB.
   - Atualizando o banco de dados primário em uma relação de replicação geográfica: alterar o tamanho máximo de 4 TB em um banco de dados primário disparará a mesma alteração no banco de dados secundário. As duas atualizações devem ser bem-sucedidas para que a alteração no primário entre em vigor. Limitações de região para a opção de 4TB se aplicam (confira acima). Se o secundário estiver em uma região que não oferece suporte a 4 TB, o primário não será atualizado.
- Não há suporte para o uso do serviço de Importação/Exportação para carregar bancos de dados P11-4TB/P15-4TB. Use SqlPackage.exe para [importar](sql-database-import.md) e [exportar](sql-database-export.md) dados.

<a id="manage-single-database-service-tiers-and-performance-levels-using-the-azure-portal" class="xliff"></a>

## Gerenciar níveis de serviço e níveis de desempenho de banco de dados individual usando o Portal do Azure

Para definir ou alterar o nível de serviço, o nível de desempenho ou a quantidade de armazenamento para um banco de dados SQL do Azure novo ou existente usando o Portal do Azure, abra a janela **Configurar desempenho** para o banco de dados clicando em **Tipo de preço (DTUs de escala)** - conforme mostra a captura de tela a seguir. 

- Definir ou alterar o nível de serviço selecionando o nível de serviço para sua carga de trabalho. 
- Definir ou alterar o nível de desempenho (**DTUs**) dentro de um nível de serviço usando o controle deslizante de **DTU**.
- Definir ou alterar a quantidade de armazenamento para o nível de desempenho usando o controle deslizante de **Armazenamento**. 

  ![Definir o nível de serviço e o nível de desempenho](./media/sql-database-service-tiers/service-tier-performance-level.png)

> [!IMPORTANT]
> Revise [Limitações atuais de bancos de dados P11 e P15 com tamanho máximo de 4 TB](sql-database-service-tiers.md#current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize) ao selecionar um nível de serviço P11 ou P15.
>

<a id="manage-single-database-service-tiers-and-performance-levels-using-powershell" class="xliff"></a>

## Gerenciar níveis de serviço e níveis de desempenho de banco de dados individual usando o PowerShell

Para definir ou alterar os níveis de serviço, níveis de desempenho e a quantidade de armazenamento do Bancos de Dados SQL do Azure usando o PowerShell, use os seguintes cmdlets do PowerShell. Se você precisa instalar ou atualizar o PowerShell, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). 

| Cmdlet | Descrição |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Cria um banco de dados |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Obtém um ou mais bancos de dados|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Define propriedades para um banco de dados ou move um banco de dados existente para um pool, elástico|


> [!TIP]
> Para um script do PowerShell de exemplo que monitora as métricas de desempenho de um banco de dados, dimensiona-o para um nível de desempenho mais alto e cria uma regra de alerta em uma das métricas de desempenho, consulte [Monitorar e escalar um banco de dados SQL individual usando PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).

<a id="manage-single-database-service-tiers-and-performance-levels-using-the-azure-cli" class="xliff"></a>

## Gerenciar níveis de serviço e níveis de desempenho de banco de dados individual usando a CLI do Azure

Para definir ou alterar os níveis de serviço, níveis de desempenho e a quantidade de armazenamento do Bancos de Dados SQL do Azure usando a CLI do Azure, use os seguintes cmdlets da [CLI do Azure do Banco de Dados SQL](/cli/azure/sql/db). Use o [Cloud Shell](/azure/cloud-shell/overview) para executar a CLI no seu navegador ou [instale-o](/cli/azure/install-azure-cli) no macOS, Linux ou Windows. Para criar e gerenciar pools elásticos de SQL, consulte [Pools elásticos](sql-database-elastic-pool.md).

| Cmdlet | Descrição |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#create) |Cria um banco de dados|
|[az sql db list](/cli/azure/sql/db#list)|Lista todos os bancos de dados e data warehouses em um servidor, ou todos os bancos de dados em um pool elástico|
|[az sql db list-editions](/cli/azure/sql/db#list-editions)|Lista os objetivos de serviço disponíveis e os limites de armazenamento|
|[az sql db list-usages](/cli/azure/sql/db#list-usages)|Retorna os usos do banco de dados|
|[az sql db show](/cli/azure/sql/db#show)|Obtém um banco de dados ou data warehouse|
|[az sql db update](/cli/azure/sql/db#update)|Atualiza um banco de dados|

> [!TIP]
> Para um script de exemplo da CLI do Azure que dimensiona um Banco de Dados SQL do Azure individual para um nível de desempenho diferente depois de consultar as informações de tamanho do banco de dados, confira [Usar CLI para monitorar e escalar um banco de dados SQL individual](scripts/sql-database-monitor-and-scale-database-cli.md).
>

<a id="manage-single-database-service-tiers-and-performance-levels-using-transact-sql" class="xliff"></a>

## Gerenciar níveis de serviço e níveis de desempenho de banco de dados individual usando Transact-SQL

Para definir ou alterar os níveis de serviço, níveis de desempenho e a quantidade de armazenamento do Bancos de Dados SQL do Azure com Transact-SQL, use os seguintes comando T-SQL. Você pode emitir esses comandos usando o Portal do Azure, o [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), o [Visual Studio Code](https://code.visualstudio.com/docs) ou qualquer outro programa que pode se conectar a um servidor de Banco de Dados SQL e passar comandos Transact-SQL. 

| Command | Descrição |
| --- | --- |
|[CREATE DATABASE (Banco de Dados SQL do Azure)](/sql/t-sql/statements/create-database-azure-sql-database)|Cria um novo banco de dados. Você deve estar conectado ao banco de dados mestre para criar um novo banco de dados.|
| [ALTER DATABASE (Banco de Dados SQL do Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) |Modifica um Banco de Dados SQL do Azure. |
|[sys.database_service_objectives (Banco de Dados SQL do Azure)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Retorna a edição (nível de serviço), o objetivo do serviço (tipo de preço) e o nome do pool elástico, se houver um, para um Banco de Dados SQL do Azure ou um SQL Data Warehouse do Azure. Se estiver conectado ao banco de dados mestre em um servidor de Banco de Dados SQL do Azure, retornará informações sobre todos os bancos de dados. Para o SQL Data Warehouse do Azure, você deve estar conectado ao banco de dados mestre.|
|[sys.database_usage (Banco de Dados SQL do Azure)](/sql/relational-databases/system-catalog-views/sys-database-usage-azure-sql-database)|Lista o número, o tipo e a duração dos bancos de dados em um servidor de Banco de Dados SQL do Azure.|

O exemplo a seguir mostra o maxsize sendo alterado usando o comando ALTER DATABASE:

 ```sql
ALTER DATABASE <myDatabaseName> 
   MODIFY (MAXSIZE = 4096 GB);
```

<a id="manage-single-databases-using-the-rest-api" class="xliff"></a>

## Gerenciar bancos de dados individuais usando a API REST

Para definir ou alterar os níveis de serviço, níveis de desempenho e a quantidade de armazenamento do Bancos de Dados SQL do Azure usando a API REST, confira [API REST do Banco de Dados SQL do Azure](/rest/api/sql/).

<a id="next-steps" class="xliff"></a>

## Próximas etapas

* Saiba mais sobre [DTUs](sql-database-what-is-a-dtu.md).
* Para saber mais sobre como monitorar o uso de DTU, consulte [Monitoramento e ajuste de desempenho](sql-database-troubleshoot-performance.md).


