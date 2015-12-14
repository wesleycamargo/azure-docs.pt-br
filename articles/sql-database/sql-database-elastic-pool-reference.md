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
	ms.date="12/01/2015" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# Referência de pool de banco de dados elástico do Banco de Dados SQL

Para desenvolvedores de SaaS com dezenas, centenas ou até milhares de bancos de dados, um pool de bancos de dados elásticos simplificará o processo de criação, manutenção e gerenciamento de desempenho e custos entre todo o grupo de bancos de dados.

Esta referência fornece links e detalhes para artigos sobre pools de bancos de dados elásticos e informações de programação.

## Visão geral

Um pool de banco de dados elástico é um conjunto de unidades de taxa de transferência de banco de dados elástico (eDTUs) e armazenamento (GBs) que são compartilhadas por vários bancos de dados. Bancos de dados elásticos podem ser adicionados e removidos do pool a qualquer momento. Os bancos de dados elásticos no pool utilizam apenas os recursos necessários do pool, liberando os recursos disponíveis para apenas os bancos de dados ativos que precisam deles. Para obter assistência na determinação de se seus bancos de dados se beneficiariam em um pool de banco de dados elástico, consulte [Considerações de preço e desempenho para um pool de banco de dados elástico](sql-database-elastic-pool-guidance.md).



## Pré-requisitos para criar e gerenciar pools de banco de dados elástico


- Os pools de banco de dados elástico só estão disponíveis em servidores V12 do Banco de Dados SQL do Azure.   
- Há suporte para a criação e gerenciamento de pools de banco de dados elástico usando o [Portal do Azure](https://portal.azure.com), PowerShell e uma biblioteca de cliente .NET (wrapper para APIs REST) somente para o Gerenciador de Recursos do Azure; não há suporte, portanto, para o [portal clássico](https://manage.windowsazure.com/) e para os comandos de gerenciamento de serviço.
- Além disso, a criação de novos bancos de dados flexíveis, e a movimentação de bancos de dados existentes dentro e fora de pools de banco de dados elásticos é suportado usando o Transact-SQL.



## Considerações sobre a visualização atual


- Cada pool tem um número máximo de bancos de dados e eDTUs de pool:

    | Camada de serviço | Número máximo de bancos de dados por pool* | Número máximo de eDTUs por pool* |
    | :-- | :-- | :-- |
    | Basic | 200 | 1\.200 | 
    | Padrão | 200 | 1\.200 |
    | Premium | 50 | 1500 |

    ****Espera-se que os limites atuais para o número de bancos de dados por pool e o número de eDTUs do pool aumentem.***




## Lista de artigos

Os artigos a seguir o ajudarão a começar a usar bancos de dados elásticos e trabalhos elásticos:

| Artigo | Descrição |
| :-- | :-- |
| [Pools de bancos de dados elásticos do Banco de Dados SQL](sql-database-elastic-pool.md) | Visão geral de pools de banco de dados elásticos |
| [Considerações sobre preço e desempenho](sql-database-elastic-pool-guidance.md) | Como avaliar se é econômico usar um pool de banco de dados elástico |
| [Criar e gerenciar um pool de banco de dados elástico do Banco de Dados SQL com o portal do Azure](sql-database-elastic-pool-portal.md) | Como criar e gerenciar um pool de banco de dados elástico usando o portal do Azure |
| [Criar e gerenciar um pool de banco de dados elástico do Banco de Dados SQL com PowerShell](sql-database-elastic-pool-powershell.md) | Como criar e gerenciar um pool de banco de dados elástico usando cmdlets do PowerShell |
| [Criar e gerenciar um Banco de Dados SQL com a Biblioteca de Banco de Dados SQL do Azure para .NET](sql-database-elastic-pool-powershell.md) | Como criar e gerenciar um pool de banco de dados elástico usando C# |
| [Visão geral de trabalhos de bancos de dados elásticos](sql-database-elastic-jobs-overview.md) | Uma visão geral do serviço de trabalhos elásticos que permite a execução de scripts T-SQL em todos os bancos de dados elásticos de um pool |
| [Instalando o componente de trabalho de banco de dados elástico](sql-database-elastic-jobs-service-installation.md) | Como instalar o serviço de trabalhos de bancos de dados elásticos |
| [Criando o usuário necessário para o serviço de trabalhos elásticos](sql-database-elastic-jobs-add-logins-to-dbs.md) | Para executar um script de trabalho de banco de dados elástico, um usuário com as permissões apropriadas deverá ser adicionado a todos os bancos de dados no pool. |
| [Como desinstalar os componentes de trabalho do banco de dados elástico](sql-database-elastic-jobs-uninstall.md) | Recuperar de falhas ao tentar instalar o serviço de trabalho de banco de dados elástico |



## Detalhes sobre namespaces e pontos de extremidade
Um pool de banco de dados elástico é um recurso do Gerenciador de Recursos do Azure do tipo "ElasticPool" no Banco de Dados SQL do Microsoft Azure.

- **namespace**: Microsoft.Sql/ElasticPool
- **management-endpoint** para chamadas da API REST (Gerenciador de Recursos): https://management.azure.com



## Propriedades do pool de bancos de dados elásticos

| Propriedade | Descrição |
| :-- | :-- |
| creationDate | Data em que o pool é criado. |
| databaseDtuMax | Número máximo de eDTUs que um banco de dados no pool pode usar. O máximo de eDTUs do banco de dados não é uma garantia de recursos. O máximo de eDTUs se aplica a todos os bancos de dados no pool. |
| databaseDtuMin | O número mínimo de eDTUs garantido para um único banco de dados no pool. O mínimo de eDTUs do banco de dados pode ser definido como 0. O mínimo de eDTUs aplica-se a todos os bancos de dados no pool. Observe que o produto do número de bancos de dados no pool e o mínimo de eDTUs do banco de dados não pode exceder as eDTUs do próprio pool. |
| Dtu | O número de eDTUs compartilhadas por todos os bancos de dados no pool. |
| edition | Camada de serviço do pool. Todos os bancos de dados do pool têm esta edição. |
| elasticPoolId | GUID da instância do pool. |
| elasticPoolName | Nome do pool. O nome é exclusivo em relação ao servidor pai. |
| location | Local do data center em que o pool foi criado. |
| state | O estado é "Desabilitado" se o pagamento da fatura para assinatura estiver inadimplente ou, caso contrário, "Pronto". |
| storageMB | Limite de armazenamento do pool em MB. Qualquer banco de dados individual no pool pode usar até o limite de armazenamento da Standard Edition (250 GB), mas o total de armazenamento usado por todos os bancos de dados no pool não pode exceder esse limite. |


## Limites de eDTU e armazenamento para pools elásticos e bancos de dados elásticos

O limite de armazenamento do pool é determinado pela quantidade de eDTUs do pool.

| propriedade | Basic | Standard | Premium |
| :-- | :-- | :-- | :-- |
| dtu | **100**, 200, 400, 800, 1200 | **100**, 200, 400, 800, 1200 | **125**, 250, 500, 1000, 1500 |
| databaseDtuMax | **5** | 10, 20, 50, **100** | **125**, 250, 500, 1000 |
| databaseDtuMin | **0**, 5 | **0**, 10, 20, 50, 100 | **0**, 125, 250, 500, 1000 |
| storageMB* | **10.000 MB**, 20.000 MB, 40.000 MB, 80.000 MB, 120.000 MB | **100 GB**, 200 GB, 400 GB, 800 GB, 1200 GB | **62,5 GB**, 125 GB, 250 GB, 500 GB, 750 GB |
| armazenamento por DTU | 100 MB | 1 GB | 0,5 GB |
| número máximo de bancos de dados por pool | 200 | 200 | 50 |

Os valores padrão estão em **negrito**.

**as unidades na API estão em MB e não em GB.





## Limites de sessão e de trabalho

O número máximo de trabalhados simultâneos e sessões simultâneas permitido para todos os bancos de dados em um pool elástico depende da configuração de eDTUs do pool:

| eDTUs | Máximo de trabalhos simultâneos | Máximo de sessões simultâneas |
| :-- | :-- | :-- |
| 100 (Basic/Standard), 125 (Premium) | 200 | 2\.400 |
| 200 (Basic/Standard), 250 (Premium) | 400 | 4\.800 |
| 400 (Basic/Standard), 500 (Premium) | 800 | 9\.600 |
| 800 (Basic/Standard), 1.000 (Premium) | 1\.600 | 19\.200 |
| 1\.200 (Basic/Standard), 1.500 (Premium) | 2\.400 | 28\.800 |


## Limitações do Gerenciador de Recursos do Azure

Os servidores V12 de Banco de Dados SQL do Azure estão localizados em grupos de recursos.

- Cada grupo de recursos pode ter no máximo 800 servidores.
- Cada servidor pode ter no máximo 800 pools elásticos.


## Latência de operações do pool elástico

- Geralmente, a alteração das eDTUs garantidas por banco de dados (databaseDtuMin) ou do máximo de eDTUs por banco de dados (databaseDtuMax) é um processo concluído em 5 minutos ou menos. 
- A alteração do limite de eDTUs/armazenamento (storageMB) do pool depende da quantidade total de espaço usado por todos os bancos de dados no pool. As alterações levam, em média, 90 minutos ou menos a cada 100 GB. Por exemplo, se o espaço total usado por todos os bancos de dados no pool for de 200 GB, a latência prevista para alterar o limite de eDTUs/armazenamento do pool é de 3 horas ou menos. 



## Biblioteca de cliente .NET, API REST e PowerShell

Vários comandos da API REST e os cmdlets do PowerShell estão disponíveis para criar e gerenciar pools elásticos. Para obter detalhes e exemplos de código, consulte [Criar e gerenciar um pool de banco de dados elástico do banco de dados SQL usando o PowerShell](sql-database-elastic-pool-powershell.md), e [Criar e gerenciar o banco de dados SQL com o C#](sql-database-client-library.md).

> [AZURE.IMPORTANT]A partir da liberação da Visualização do Azure PowerShell 1.0, o cmdlet Switch-AzureMode não está mais disponível, e os cmdlets contidos no módulo do Gerenciador de Recursos do Azure foram renomeados. Para obter informações detalhadas, veja [Substituição de Switch-AzureMode no Azure PowerShell](https://github.com/Azure/azure-powershell/wiki/Deprecation-of-Switch-AzureMode-in-Azure-PowerShell).

| [Cmdlets do PowerShell](https://msdn.microsoft.com/library/mt163521.aspx) | [Comandos da API REST](https://msdn.microsoft.com/library/mt163571.aspx) |
| :-- | :-- |
| [New-AzureRMSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx) | [Criar um pool de banco de dados elástico](https://msdn.microsoft.com/library/mt163596.aspx) |
| [Set-AzureRMSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511.aspx) | [Definir as configurações de desempenho de um pool de banco de dados elástico](https://msdn.microsoft.com/library/mt163641.aspx) |
| [Remove-AzureRMSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619355.aspx) | [Excluir um pool de banco de dados elástico](https://msdn.microsoft.com/library/mt163672.aspx) |
| [Get-AzureRMSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603517.aspx) | [Obtém os pools de banco de dados elástico e seus valores de propriedade](https://msdn.microsoft.com/pt-BR/library/mt163646.aspx) |
| [Get-AzureRMSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812.aspx) | [Obter o status de operações do pool de banco de dados elástico](https://msdn.microsoft.com/library/mt163669.aspx) |
| [Get-AzureRMSqlElasticPoolDatabase](https://msdn.microsoft.com/library/azure/mt619484.aspx) | [Obter bancos de dados em um pool de banco de dados elástico](https://msdn.microsoft.com/library/mt163646.aspx) |
| [Get-AzureRMSqlElasticPoolDatabaseActivity]() | [Obtém o status de mover bancos de dados dentro e fora de um pool](https://msdn.microsoft.com/library/mt163669.aspx) |

## Transact-SQL

É possível usar o Transact-SQL para fazer as tarefas a seguir de gerenciamento do banco de dados elástico:

| Tarefa | Detalhes |
| :-- | :-- |
| Criar um banco de dados elástico novo (diretamente em um pool) | [CREATE DATABASE (Banco de Dados SQL do Azure)](https://msdn.microsoft.com/library/dn268335.aspx) |
| Mover bancos de dados existentes dentro e fora de um pool | [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/ms174269.aspx) |
| Obter estatísticas de uso de recursos do pool | [sys.elastic\_pool\_resource\_stats (Banco de Dados SQL do Azure)](https://msdn.microsoft.com/library/mt280062.aspx) |


## Informações sobre preço e cobrança

Pools de bancos de dados elásticos são cobrados de acordo com as seguintes características:

- Um pool elástico é cobrado após sua criação, mesmo quando ele não contém bancos de dados. 
- Um pool elástico é cobrado por hora. Essa frequência de medição é a mesma dos níveis de desempenho de bancos de dados individuais. 
- Se um pool elástico for redimensionado para um novo valor de eDTUs, ele não será cobrado de acordo com esse novo valor até que a operação de redimensionamento seja concluída. Isso segue o mesmo padrão que a alteração do nível de desempenho de bancos de dados autônomos. 


- O preço de um pool elástico baseia-se no número de eDTUs do pool. O preço de um pool elástico é independente da utilização dos bancos de dados elásticos dentro dele.
- O preço é calculado por (número de eDTUs do pool) x (preço unitário por eDTU).

O preço unitário por eDTU de um pool elástico é maior que o preço unitário por DTU de um banco de dados autônomo na mesma camada de serviço. Para obter detalhes, confira [Preços de Banco de Dados SQL](http://azure.microsoft.com/pricing/details/sql-database/).

## Erros do pool de bancos de dados elásticos

| ErrorNumber | ErrorSeverity | ErrorFormat | ErrorInserts | ErrorCause | ErrorCorrectiveAction |
| :-- | :-- | :-- | :-- | :-- | :-- |
| 1132 | EX\_RESOURCE | O pool elástico atingiu seu limite de armazenamento. O uso do armazenamento do pool elástico não pode exceder (%d) MB. | O limite de espaço do pool elástico é medido em MB. | Tentando gravar dados em um banco de dados quando o limite de armazenamento do pool elástico foi atingido. | Considere aumentar as DTUs do pool elástico, se possível, para aumentar seu limite de armazenamento, reduzir o armazenamento usado por bancos de dados individuais dentro do pool elástico ou remover bancos de dados do pool elástico. |
| 10929 | EX\_USER | A garantia mínima de %s é %d, o limite máximo é %d e o uso atual do banco de dados é %d. No entanto, o servidor está muito ocupado para dar suporte a solicitações maiores que %d para este banco de dados. Consulte [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637) para obter assistência. Caso contrário, tente novamente mais tarde. | Mínimo de DTUs por banco de dados; máximo de DTUs por banco de dados | O número total de trabalhos simultâneos (solicitações) em todos os bancos de dados no pool elástico tentou exceder o limite do pool. | Considere aumentar as DTUs do pool elástico, se possível, para aumentar o limite de trabalhos ou remover bancos de dados do pool elástico. |
| 40844 | EX\_USER | O banco de dados '%ls' no servidor '%ls' é um banco de dados edição '%ls' em um pool elástico e não pode ter um relacionamento de cópia contínuo. | nome do banco de dados, edição do banco de dados, nome do servidor | Um comando StartDatabaseCopy é emitido para um banco de dados não premium em um pool elástico. | Em breve |
| 40857 | EX\_USER | Pool elástico não encontrado para o servidor: '%ls', nome do pool elástico: '%ls'. | nome do servidor; nome do pool elástico | O pool elástico especificado não existe no servidor especificado. | Forneça um nome de pool elástico válido. |
| 40858 | EX\_USER | O pool elástico '%ls' já existe no servidor: '%ls' | nome do pool elástico, nome do servidor | O pool elástico especificado já existe no servidor lógico especificado. | Forneça um novo nome de pool elástico. |
| 40859 | EX\_USER | O pool elástico não dá suporte à camada de serviço '%ls'. | camada de serviço do pool elástico | A camada de serviço especificada não dá suporte ao provisionamento de pool elástico. | Forneça a edição correta ou deixe a camada de serviço em branco para o padrão. |
| 40860 | EX\_USER | A combinação de pool elástico '%ls' e objetivo de serviço '%ls' é inválida. | nome do pool elástico; nome de objetivo de nível de serviço | O pool elástico e o objetivo de serviço podem ser especificados juntos somente se o objetivo de serviço for especificado como “ElasticPool”. | Especifique a combinação correta de pool elástico e objetivo de serviço. |
| 40861 | EX\_USER | A edição do banco de dados '%.*ls' não pode ser diferente da camada de serviço do pool elástico, que é '%.*ls'. | edição do banco de dados, camada de serviço do pool elástico | A edição do banco de dados é diferente da camada de serviço do pool elástico. | Não especifique uma edição de banco de dados diferente da camada de serviço do pool elástico. Observe que a edição do banco de dados não precisa ser especificada. | | 40862 | EX\_USER | O nome do pool elástico deve ser especificado se o objetivo do serviço de pool elástico for especificado. | Nenhum | O objetivo do serviço de pool elástico não identifica exclusivamente um pool elástico. | Especifique o nome do pool elástico se estiver usando o objetivo do serviço de pool elástico. | | 40864 | EX\_USER | As DTUs para o pool elástico devem ser de pelo menos (%d) DTUs para a camada de serviço ' %. * ls'. | DTUs para pool elástico; camada de serviço do pool elástico. | Tentativa de definir os DTUs para o pool elástico abaixo do limite mínimo. | Tente novamente a configuração de DTUs para o pool elástico pelo menos com o limite mínimo. | | 40865 | EX\_USER | Os DTUs para o pool elástico não podem exceder (%d) DTUs para a camada de serviço ' %. *ls'. | DTUs para o pool elástico; camada de serviço do pool elástico. | Tentativa de definir DTUs para o pool elástico acima do limite máximo. | Tente definir DTUs para o pool elástico acima do limite máximo. | | 40867 | EX\_USER | O DTU máximo por banco de dados deve ser no mínimo (%d) para a camada de serviço ' %.*ls'. | DTU máximo por banco de dados; camada de serviço do pool elástico | Tentativa de definir o máximo DTU por banco de dados abaixo do limite com suporte. | Considere o uso da camada de serviço do pool elástico que ofereça suporte à configuração desejada. | | 40868 | EX\_USER | A DTU máxima por banco de dados não pode exceder (%d) para a camada de serviço ' %.*ls'. | DTU máximo por banco de dados; camada de serviço do pool elástico. | Tentativa de definir o DTU máximo por banco de dados acima do limite com suporte. | Considere o uso da camada de serviço do pool elástico que ofereça suporte à configuração desejada. | | 40870 | EX\_USER | O DTU mínimo por banco de dados não pode exceder (%d) para a camada de serviço ' %.*ls'. | DTU mínimo por banco de dados; camada de serviço do pool elástico. | Tentativa de definir o DTU mínimo por banco de dados além do limite com suporte. | Considere usar a camada de serviço do pool elástico que ofereça suporte à configuração desejada. | | 40873 | EX\_USER | O número de bancos de dados (%d) e DTU mínimo por banco de dados (%d) não pode exceder os DTUs do pool elástico (%d). | Número de bancos de dados no pool elástico; DTU mínimo por banco de dados; DTUs do pool elástico. | Tentativa de especificar um DTU mínimo para bancos de dados no pool elástico que excede os DTUs do pool elástico. | Considere o aumento dos DTUs do pool elástico, ou a diminuição do DTU mínimo por banco de dados, ou diminuir o número de bancos de dados no pool elástico. | | 40877 | EX\_USER | Não é possível excluir um pool elástico, a menos que ele não contenha banco de dados. | Nenhum | O pool elástico contém um ou mais bancos de dados e, portanto, não pode ser excluído. | Remova os bancos de dados do pool elástico para excluí-lo. | | 40881 | EX\_USER | O pool elástico '%.*ls' atingiu seu limite de contagem de banco de dados. O limite de contagem do banco de dados para o pool elástico não pode exceder (%d) para um pool elástico com (%d) DTUs. | Nome do pool elástico; limite de contagem de banco de dados do pool elástico; eDTUs para o pool de recursos. | Tentativa de criar ou adicionar um banco de dados ao pool elástico quando o limite de contagem de banco de dados do pool elástico foi atingido. | Considere aumentar as DTUs do pool elástico, se possível, para aumentar o limite de bancos de dados ou remover bancos de dados do pool elástico. | | 40889 | EX\_USER | O limite de DTUs ou de armazenamento para o pool elástico '%.*ls' não pode ser reduzido, pois não haveria espaço de armazenamento suficiente para seus bancos de dados. | Nome do pool elástico. | Tentativa de diminuir o limite de armazenamento do pool elástico abaixo de seu uso de armazenamento. | Considere reduzir o uso do armazenamento de bancos de dados individuais no pool elástico ou remover bancos de dados do pool para reduzir seu limite de DTUs ou de armazenamento. | | 40891 | EX\_USER | O DTU mínimo por banco de dados (%d) não pode exceder o DTU máximo por banco de dados (%d). | DTU mínimo por banco de dados; DTU máximo por banco de dados. | Tentativa de definir o DTU mínimo por banco de dados superior ao DTU máximo por banco de dados. | Certifique-se de que o DTU mínimo por banco de dados não exceda o DTU máximo por banco de dados. | | TBD | EX\_USER | O tamanho do armazenamento para um banco de dados individual em um pool elástico não pode exceder o tamanho máximo permitido pelo pool elástico da camada de serviço '%.*ls'. | camada de serviço do pool elástico | O tamanho máximo do banco de dados excede o tamanho máximo permitido pela camada de serviço do pool elástico. | Defina o tamanho máximo do banco de dados dentro dos limites do tamanho máximo permitido pela camada de serviço do pool elástico. |

<!---HONumber=AcomDC_1203_2015-->