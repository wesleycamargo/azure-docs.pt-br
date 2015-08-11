<properties 
	pageTitle="Referência do pool elástico do banco de dados SQL do Azure" 
	description="Esta referência fornece links e detalhes para artigos sobre pools de bancos de dados elásticos e informações de programação." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="07/14/2015" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# Referência de pools de bancos de dados elásticos do Banco de Dados do SQL (visualização)

Para desenvolvedores de SaaS com dezenas, centenas ou até milhares de bancos de dados, um pool de bancos de dados elásticos simplificará o processo de criação, manutenção e gerenciamento de desempenho e custos entre todo o grupo de bancos de dados.

Esta referência fornece links e detalhes para artigos sobre pools de bancos de dados elásticos e informações de programação.

## Visão geral

Um pool de banco de dados elástico é um conjunto de unidades de taxa de transferência de banco de dados (DTUs) e armazenamento (GBs) compartilhados por vários bancos de dados. Bancos de dados elásticos podem ser adicionados e removidos do pool a qualquer momento. Os bancos de dados elásticos no pool utilizam apenas os recursos necessários do pool, liberando os recursos disponíveis para apenas os bancos de dados ativos que precisam deles. Para obter assistência na determinação de se seus bancos de dados se beneficiariam em um pool de banco de dados elástico, consulte [Considerações de preço e desempenho para um pool de banco de dados elástico](sql-database-elastic-pool-guidance.md).



## Pré-requisitos para criar e gerenciar pools de banco de dados elástico


- Os pools elásticos só estão disponíveis em servidores V12 de Bancos de Dados SQL do Azure.   
- O PowerShell e as APIs REST para pools elásticos são compatíveis apenas com o Azure Resource Manager (ARM). Não há suporte para comandos de gerenciamento de serviço (RDFE). 
- Só é possível criar e gerenciar pools elásticos no [portal do Microsoft Azure](https:portal.azure.com). 


## Restrições para a visualização atual

- A faixa de preços para um pool de banco de dados elástico na visualização atual é a Standard.  
- Não é possível importar um banco de dados diretamente em um pool elástico. Você pode importar para um banco de dados autônomo e, em seguida, mover o banco de dados para um pool. É possível exportar um banco de dados de dentro de um pool.
- Cada pool pode ter um máximo de 100 bancos de dados.


## Lista de artigos

Os artigos a seguir o ajudarão a começar a usar bancos de dados elásticos e trabalhos elásticos:

| Artigo | Descrição |
| :-- | :-- |
| [Pools de bancos de dados elásticos do Banco de Dados SQL](sql-database-elastic-pool.md) | Visão geral dos pools elásticos |
| [Criar e gerenciar um pool de banco de dados elástico do Banco de Dados SQL com o portal do Azure](sql-database-elastic-pool-portal.md) | Como criar e gerenciar um pool elástico usando o portal do Azure |
| [Criar e gerenciar um pool de banco de dados elástico do Banco de Dados SQL com PowerShell](sql-database-elastic-pool-powershell.md) | Como criar e gerenciar um pool elástico usando cmdlets do PowerShell |
| [Visão geral de trabalhos de bancos de dados elásticos](sql-database-elastic-jobs-overview.md) | Uma visão geral do serviço de trabalhos elásticos que permite a execução de scripts T-SQL em todos os bancos de dados elásticos de um pool |
| [Instalando o componente de trabalho de banco de dados elástico](sql-database-elastic-jobs-service-installation.md) | Como instalar o serviço de trabalhos de bancos de dados elásticos |
| [Criando o usuário necessário para o serviço de trabalhos elásticos](sql-database-elastic-jobs-add-logins-to-dbs.md) | Para executar um script de trabalho de banco de dados elástico, um usuário com as permissões apropriadas deverá ser adicionado a todos os bancos de dados no pool. |
| [Como desinstalar os componentes de trabalho do banco de dados elástico](sql-database-elastic-jobs-uninstall.md) | Recuperar de falhas ao tentar instalar o serviço de trabalho de banco de dados elástico |



## Detalhes sobre namespaces e pontos de extremidade
Um pool elástico é um recurso do ARM do tipo "ElasticPool" no Banco de Dados SQL do Microsoft Azure.

- **namespace**: Microsoft.Sql/ElasticPool
- **management-endpoint** para chamadas da API REST (gerenciador de recursos do Azure): https://management.azure.com



## Propriedades do pool de bancos de dados elásticos

| Propriedade | Descrição |
| :-- | :-- |
| creationDate | Data em que o pool é criado. |
| databaseDtuMax | Número máximo de DTUs que um banco de dados no pool pode usar. O máximo de DTUs do banco de dados não é uma garantia de recursos. Esse valor se aplica a todos os bancos de dados no pool. |
| databaseDtuMin | O número mínimo de DTUs garantido para um banco de dados no pool. O mínimo de DTUs do banco de dados pode ser definido como 0. O mínimo de DTU se aplica a todos os bancos de dados no pool. Observe que o produto do número de bancos de dados no pool e o mínimo de DTUs do banco de dados não pode exceder as DTUs do próprio pool. |
| Dtu | O número de DTUs compartilhadas por todos os bancos de dados no pool. |
| edition | Camada de serviço do pool. Todos os bancos de dados do pool têm esta edição. |
| elasticPoolId | GUID da instância do pool. |
| elasticPoolName | Nome do pool. O nome é exclusivo em relação ao servidor pai. |
| location | Local do data center em que o pool foi criado. |
| state | O estado é "Desabilitado" se o pagamento da fatura para assinatura estiver inadimplente ou, caso contrário, "Pronto". |
| storageMB | Limite de armazenamento do pool em MB. Qualquer banco de dados individual no pool pode usar até o limite de armazenamento da Standard Edition (250 GB), mas o total de armazenamento usado por todos os bancos de dados no pool não pode exceder esse limite. |


## Limites de DTU e armazenamento para pools elásticos e bancos de dados elásticos

O limite de armazenamento do pool é determinado pela quantidade de DTUs do pool. Cada DTU equivale a 1 GB de armazenamento. Por exemplo, um pool de 200 DTUs tem um limite de 200 GB de armazenamento.

| propriedade | valor padrão | valores válidos |
| :-- | :-- | :-- |
| Dtu | 100 | 100, 200, 400, 800, 1200 |
| databaseDtuMax | 100 | 10, 20, 50 100 |
| databaseDtuMin | 0 | 0, 10, 20, 50 |
| storageMB | 100 GB\* | 100 GB, 200 GB, 400 GB, 800 GB, 1200 GB |

*as unidades na API estão em MB, não em GB

## Limites de sessão e de trabalho

O número máximo de trabalhados simultâneos e sessões simultâneas permitido para todos os bancos de dados em um pool elástico depende da configuração de DTUs do pool:

| DTUs | Máximo de trabalhos simultâneos | Máximo de sessões simultâneas |
| :-- | :-- | :-- |
| 100 | 200 | 2.400 |
| 200 | 400 | 4.800 |
| 400 | 800 | 9.600 |
| 800 | 1.600 | 19.200 |
| 1.200 | 2.400 | 28.800 |


## Limitações do Gerenciador de Recursos do Azure

Os servidores V12 de Banco de Dados SQL do Azure estão localizados em grupos de recursos.

- Cada grupo de recursos pode ter no máximo 800 servidores.
- Cada servidor pode ter no máximo 800 pools elásticos.


## Latência de operações do pool elástico

- Geralmente, a alteração as DTUs garantidas por banco de dados (databaseDtuMin) ou o máximo de DTUs por banco de dados (databaseDtuMax) é concluída em 5 minutos ou menos. 
- A alteração do limite de DTUs/armazenamento (storageMB) do pool depende da quantidade total de espaço usado por todos os bancos de dados no pool. As alterações levam, em média, 90 minutos ou menos a cada 100 GB. Por exemplo, se o espaço total usado por todos os bancos de dados no pool for de 200 GB, a latência prevista para alterar o limite de DTUs/armazenamento do pool é de 3 horas ou menos. 



## Cmdlets do PowerShell e comandos da API REST para o pool de bancos de dados elásticos (somente no Gerenciador de Recursos do Azure)

Os comandos da API REST e os cmdlets do PowerShell a seguir estão disponíveis para criar e gerenciar pools elásticos:

| [Cmdlets do PowerShell](https://msdn.microsoft.com/library/mt125356.aspx) | [Comandos da API REST](https://msdn.microsoft.com/library/azure/mt163571.aspx) |
| :-- | :-- |
| Get-AzureSqlDatabase | Obter banco de dados SQL do Azure |
| Get-AzureSqLElasticPool | Obter pool de bancos de dados elásticos do Banco de Dados SQL do Azure |
| Get-AzureSqlElasticPoolActivity | Obter operações do pool de bancos de dados elásticos do Banco de Dados SQL do Azure |
| Get-AzureSqlElasticPoolDatabase | Obter banco de dados elástico do Banco de Dados SQL do Azure |
| Get-AzureSqlElasticPoolDatabaseActivity | Obter operações do banco de dados elásticos do banco de dados SQL do Azure |
| Get-AzureSqlServer | Obter servidor do banco de dados SQL do Azure |
| Get-AzureSqlServerFirewallRule | Obter regra de firewall do servidor do banco de dados SQL do Azure |
| Get-AzureSqlServerServiceObjective | Obter objetivo de serviço do servidor do banco de dados SQL do Azure |
| New-AzureSqlDatabase | Criar um banco de dados SQL do Azure |
| New-AzureSqlElasticPool | Criar um pool de bancos de dados elásticos do Banco de Dados SQL do Azure |
| New-AzureSqlServer | Criar um servidor do banco de dados SQL do Azure |
| New-AzureSqlServerFirewallRule | Criar uma regra de firewall do servidor do banco de dados SQL do Azure) |
| Remove-AzureSqlDatabase | Remover banco de dados SQL do Azure |
| Remove-AzureSqlElasticPool | Remover um pool de bancos de dados elásticos do Banco de Dados SQL do Azure |
| Remove-AzureSqlServer | Remover um servidor do banco de dados SQL do Azure |
| Set-AzureSqlDatabase | Definir um banco de dados SQL do Azure |
| Set-AzureSqlElasticPool | Definir pool de bancos de dados elásticos do Banco de Dados SQL do Azure |
| Set-AzureSqlServer | Definir servidor do banco de dados SQL do Azure |
| Set-AzureSqlServerFirewallRule | Definir regra de firewall do servidor do banco de dados SQL do Azure |
| Get-Metrics | Obter métricas |


## Informações sobre preço e cobrança

Pools de bancos de dados elásticos são cobrados de acordo com as seguintes características:

- Um pool elástico é cobrado após sua criação, mesmo quando ele não contém bancos de dados. 
- Um pool elástico é cobrado por hora. Essa frequência de medição é a mesma dos níveis de desempenho de bancos de dados autônomos. 
- Se um pool elástico for redimensionado para um novo valor de DTUs, ele não será cobrado de acordo com o novo valor até que a operação de redimensionamento seja concluída. Isso segue o mesmo padrão que a alteração do nível de desempenho de bancos de dados autônomos. 


- O preço de um pool elástico baseia-se no número de DTUs do pool e o número de bancos de dados no pool.
- O preço é calculado por (número de DTUs do pool) x (preço unitário por DTU) + (número de bancos de dados) x (preço unitário por banco de dados)

O preço unitário por DTU de um pool elástico é maior que o preço unitário por DTU de um banco de dados autônomo na mesma camada de serviço. Para obter detalhes, confira [Preços de Banco de Dados SQL](http://azure.microsoft.com/pricing/details/sql-database/).

## Erros do pool de bancos de dados elásticos

| ErrorNumber | ErrorSeverity | ErrorFormat | ErrorInserts | ErrorCause | ErrorCorrectiveAction |
| :-- | :-- | :-- | :-- | :-- | :-- |
| 1132 | EX_RESOURCE | O pool elástico atingiu seu limite de armazenamento. O uso do armazenamento do pool elástico não pode exceder (%d) MB. | O limite de espaço do pool elástico é medido em MB. | Tentando gravar dados em um banco de dados quando o limite de armazenamento do pool elástico foi atingido. | Considere aumentar as DTUs do pool elástico, se possível, para aumentar seu limite de armazenamento, reduzir o armazenamento usado por bancos de dados individuais dentro do pool elástico ou remover bancos de dados do pool elástico. |
| 10929 | EX_USER | A garantia mínima de %s é %d, o limite máximo é %d e o uso atual do banco de dados é %d. No entanto, o servidor está muito ocupado para dar suporte a solicitações maiores que %d para este banco de dados. Consulte [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637) para obter assistência. Caso contrário, tente novamente mais tarde. | Mínimo de DTUs por banco de dados; máximo de DTUs por banco de dados | O número total de trabalhos simultâneos (solicitações) em todos os bancos de dados no pool elástico tentou exceder o limite do pool. | Considere aumentar as DTUs do pool elástico, se possível, para aumentar o limite de trabalhos ou remover bancos de dados do pool elástico. |
| 40844 | EX_USER | O banco de dados '%ls' no servidor '%ls' é um banco de dados edição '%ls' em um pool elástico e não pode ter um relacionamento de cópia contínuo. | nome do banco de dados, edição do banco de dados, nome do servidor | Um comando StartDatabaseCopy é emitido para um banco de dados não premium em um pool elástico. | Em breve |
| 40857 | EX_USER | Pool elástico não encontrado para o servidor: '%ls', nome do pool elástico: '%ls'. | nome do servidor; nome do pool elástico | O pool elástico especificado não existe no servidor especificado. | Forneça um nome de pool elástico válido. |
| 40858 | EX_USER | O pool elástico '%ls' já existe no servidor: '%ls' | nome do pool elástico, nome do servidor | O pool elástico especificado já existe no servidor lógico especificado. | Forneça um novo nome de pool elástico. |
| 40859 | EX_USER | O pool elástico não dá suporte à camada de serviço '%ls'. | camada de serviço do pool elástico | A camada de serviço especificada não dá suporte ao provisionamento de pool elástico. | Forneça a edição correta ou deixe a camada de serviço em branco para o padrão. |
| 40860 | EX_USER | A combinação de pool elástico '%ls' e objetivo de serviço '%ls' é inválida. | nome do pool elástico; nome de objetivo de nível de serviço | O pool elástico e o objetivo de serviço podem ser especificados juntos somente se o objetivo de serviço for especificado como “ElasticPool”. | Especifique a combinação correta de pool elástico e objetivo de serviço. |
| 40861 | EX_USER | A edição do banco de dados '%.*ls' não pode ser diferente da camada de serviço do pool elástico, que é '%.*ls'. | edição do banco de dados, camada de serviço do pool elástico | A edição do banco de dados é diferente da camada de serviço do pool elástico. | Não especifique uma edição de banco de dados diferente da camada de serviço do pool elástico. Observe que a edição do banco de dados não precisa ser especificada. |
| 40862 | EX_USER | O nome do pool elástico deverá ser especificado se o objetivo de serviço do pool elástico for especificado. | Nenhum | O objetivo de serviço do pool elástico não identifica exclusivamente um pool elástico. | Especifique o nome do pool elástico se você estiver usando o objetivo de serviço do pool elástico. |
| 40864 | EX_USER | A quantidade mínima de DTUs para o pool elástico deve ser de (%d) DTUs para a camada de serviço '%.*ls'. | DTUs para o pool elástico; camada de serviço do pool elástico. | Tentando definir uma quantidade de DTUs para o pool elástico abaixo do limite mínimo. | Tente configurar novamente as DTUs para o pool elástico de acordo com o limite mínimo. |
| 40865 | EX_USER | A quantidade máxima de DTUs para o pool elástico deve ser de (%d) DTUs para a camada de serviço '%.*ls'. | DTUs para o pool elástico; camada de serviço do pool elástico. | Tentando definir uma quantidade de DTUs para o pool elástico acima do limite máximo. | Tente configurar novamente as DTUs para o pool elástico de acordo com o limite máximo. |
| 40867 | EX_USER | O máximo de DTUs por banco de dados deve ser de, no mínimo, (%d) para a camada de serviço '%.*ls'. | máximo de DTUs por banco de dados, camada de serviço do pool elástico | Tentando definir o máximo de DTUs por banco de dados abaixo do limite permitido. | Considere usar a camada de serviço do pool elástico que dê suporte à configuração desejada. |
| 40868 | EX_USER | O máximo de DTUs por banco de dados não deve exceder (%d) para a camada de serviço '%.*ls'. | máximo de DTUs por banco de dados, camada de serviço do pool elástico. | Tentando definir o máximo de DTUs por banco de dados acima do limite permitido. | Considere usar a camada de serviço do pool elástico que dê suporte à configuração desejada. |
| 40870 | EX_USER | O mínimo de DTUs por banco de dados deve ser de, no máximo, (%d) para a camada de serviço '%.*ls'. | mínimo de DTUs por banco de dados, camada de serviço do pool elástico. | Tentando definir o mínimo de DTUs por banco de dados acima do limite permitido. | Considere usar a camada de serviço do pool elástico que dê suporte à configuração desejada. |
| 40873 | EX_USER | O número de bancos de dados (%d) e o mínimo de DTUs por banco de dados (%d) não podem exceder as DTUs do pool elástico (%d). | Número de bancos de dados no pool elástico; mínimo de DTUs por banco de dados; DTUs do pool elástico. | Tentando especificar uma quantidade mínima de DTUs para bancos de dados no pool elástico que excede as DTUs do pool elástico. | Considere aumentar as DTUs do pool elástico, diminuir o mínimo de DTUs por banco de dados ou diminuir o número de bancos de dados no pool elástico. |
| 40877 | EX_USER | Não é possível excluir um pool elástico, a menos que ele não contenha bancos de dados. | Nenhum | O pool elástico contém um ou mais bancos de dados e, portanto, não pode ser excluído. | Remova os bancos de dados do pool elástico para excluí-lo. |
| 40881 | EX_USER | O pool elástico '%.*ls' atingiu o limite de banco de dados. O limite de bancos de dados para o pool elástico não pode exceder (%d) para um pool elástico com (%d) DTUs. | Nome do pool elástico; limite de bancos de dados do pool elástico; DTUs para pool de recursos. | Tentando criar ou adicionar um banco de dados ao pool elástico quando o limite de bancos de dados do pool elástico foi atingido. | Considere aumentar as DTUs do pool elástico, se possível, para aumentar o limite de bancos de dados ou remover bancos de dados do pool elástico. |
| 40889 | EX_USER | O limite de DTUs ou de armazenamento para o pool elástico '%.*ls' não pode ser reduzido, pois não haveria espaço de armazenamento suficiente para seus bancos de dados. | Nome do pool elástico. | Tentando reduzir o limite de armazenamento do pool elástico abaixo do seu uso de armazenamento. | Considere reduzir o uso do armazenamento de bancos de dados individuais no pool elástico ou remover bancos de dados do pool para reduzir seu limite de DTUs ou de armazenamento. |
| 40891 | EX_USER | O mínimo de DTUs por banco de dados (%d) não pode ser superior ao máximo de DTUs por banco de dados (%d). | Mínimo de DTUs por banco de dados; máximo de DTUs por banco de dados. | Tentando definir o mínimo de DTUs por banco de dados acima do máximo de DTUs por banco de dados. | Certifique-se de que o mínimo de DTUs por bancos de dados não seja superior ao máximo de DTUs por banco de dados. |
| TBD | EX_USER | O tamanho do armazenamento para um banco de dados individual em um pool elástico não pode exceder o tamanho máximo permitido pelo pool elástico da camada de serviço '%.*ls'. | camada de serviço do pool elástico | O tamanho máximo do banco de dados excede o tamanho máximo permitido pela camada de serviço do pool elástico. | Defina o tamanho máximo do banco de dados dentro dos limites do tamanho máximo permitido pela camada de serviço do pool elástico. |

<!-----HONumber=July15_HO5-->