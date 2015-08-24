<properties
	pageTitle="Limites de Recurso de Banco de Dados SQL do Azure"
	description="Esta página descreve alguns limites de recurso comuns para o Banco de Dados SQL do Azure."
	services="sql-database"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" />


<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="08/10/2015"
	ms.author="jroth" />


# Limites de Recurso de Banco de Dados SQL do Azure


O Banco de Dados SQL do Azure monitora a utilização dos recursos compartilhados, como o log de transações, E/S e muitos outros recursos. Isso habilita o banco de dados SQL do Azure para manter os bancos de dados dentro dos limites do conjunto de recursos. Esse limite de recursos ou limite é chamado de limite de recursos. Quando o uso de recursos pelos clientes excede esses limites, tanto em nível de nó físico ou locatário, o banco de dados SQL do Azure responde ao gerenciar o uso de recursos, o que resulta em perdas de conexão ou negações de solicitação.

> [AZURE.NOTE]Quando os limites de recurso impedem que consultas analisem problemas de desempenho do banco de dados, talvez seja necessário usar a conexão de administrador dedicada (DAC), que está disponível a partir da V12 no Banco de Dados SQL do Azure. Para obter mais informações sobre como usar a DAC, consulte [Conexão de diagnóstico para administradores de banco de dados](https://msdn.microsoft.com/library/ms189595.aspx).

## Tabela de resumo de limites de recursos

A tabela a seguir fornece um resumo dos limites para cada recurso além dos quais o banco de dados SQL do Azure nega a solicitação ou encerra as conexões com os recursos afetados e um código de erro é retornado. Em alguns casos, a [camada de serviço (Basic, Standard e Premium)](sql-database-service-tiers.md) e o nível de desempenho determinam o limite exato. Nesses casos, consulte [Camadas de serviço de banco de dados SQL do Azure e níveis de desempenho](https://msdn.microsoft.com/library/azure/dn741336.aspx).

[AZURE.INCLUDE [azure-sql-database-limits](../../includes/azure-sql-database-limits.md)]

## Noções básicas sobre códigos de erro

Às vezes, o mesmo código de erro é retornado para várias condições de limitação de um recurso. Essas condições são identificadas como estados na mensagem de erro. Por exemplo, as duas seguintes mensagens de erro são exibidas para o recurso de tamanho de log de transação. Embora o texto da mensagem seja o mesmo, eles têm diferentes valores de estado (1 e 2 respectivamente) com base nas condições de limitação diferentes.

Mensagem de erro 1:

	Msg 40552, Level 17, State 1, Line 1
	The session has been terminated because of excessive transaction log space usage.
	Try modifying fewer rows in a single transaction.

Mensagem de erro 2:

	Msg 40552, Level 17, State 2, Line 1
	The session has been terminated because of excessive transaction log space usage.
	Try modifying fewer rows in a single transaction.

A mesma situação se aplica à ID do recurso que aparece em algumas mensagens. A ID de recurso é convertida em um recurso do sistema que está apresentando o limite.

O restante deste tópico explica os possíveis códigos de erro mais detalhadamente, incluindo instâncias nas quais o valor de estado e a ID de recurso fornecem informações adicionais sobre o erro.

## Tamanho do banco de dados

| &nbsp; | Mais informações |
| :--- | :--- |
| **Condição** | Quando o espaço de banco de dados alocado para um banco de dados do usuário está cheio, o usuário obtém um erro de banco de dados completo. |
| **Código do erro** | **40544**: o banco de dados atingiu sua cota de tamanho. Particione ou exclua dados, descarte índices ou consulte a documentação para conhecer as possíveis resoluções. |
| **Limite** | Depende do [nível de desempenho e da camada de serviço](https://msdn.microsoft.com/library/azure/dn741336.aspx). |
| **Tipo de solicitações negadas** | DML não selecionado (Insert, Update, Merge que insere ou atualiza). |
| **Recomendações** | Use as instruções DELETE/DROP para remover dados do banco de dados até que o tamanho do banco de dados esteja abaixo do limite. |

## Logons

| &nbsp; | Mais informações |
| :--- | :--- |
| **Condição** | O banco de dados SQL gerencia o limite no número de logons simultâneos que podem ser estabelecidos com um banco de dados. Quando o limite de logon simultâneo para um banco de dados é atingido, novas solicitações de logon no banco de dados são negadas e o código de erro 10928 é retornado. |
| **Código do erro** | **10928**: ID de recurso: 3. O limite de %s para o banco de dados é %d e foi atingido. Consulte http://go.microsoft.com/fwlink/?LinkId=267637 para obter assistência. |
| **Limite** | Depende do [nível de desempenho e da camada de serviço](https://msdn.microsoft.com/library/azure/dn741336.aspx). |
| **Recomendações** | Verifique dm\_exec\_connections para exibir quais conexões de usuário estão ativas no momento.<br><br>Retirada e repetição de logon após 10 segundos. |

> [AZURE.NOTE]O valor da ID de recurso na mensagem de erro indica que o recurso para limite foi atingido. Para logons, ID de recurso = 3.

## Uso de Memória

| &nbsp; | Mais informações |
| :--- | :--- |
| **Condição** | Quando há sessões aguardando concessões de memória por 20 segundos ou mais, sessões que consomem mais de 16 MB de concessão de memória por mais de 20 segundos serão encerradas em ordem decrescente de tempo que o recurso foi mantido, para que a sessão mais antiga seja encerrada primeiro. O encerramento das sessões é interrompido assim que a memória necessária se torna disponível. |
| **Código do erro** | **40553**: a sessão foi encerrada devido ao uso excessivo de memória. Tente modificar sua consulta para processar menos linhas. |
| **Limite** | Conceder mais de 16 MB de memória por mais de 20 segundos. |
| **Tipo de solicitações negadas** | Consultas que consomem concessões de memória, que incluem consultas que usam classificações e junções de hash. |
| **Recomendações** | Execute o ajuste de consulta em consultas que exigem classificações e/ou junções de hash. |

## Sessões

| &nbsp; | Mais informações |
| :--- | :--- |
| **Condição** | O banco de dados SQL gerencia o limite no número de sessões simultâneas que podem ser estabelecidas com um banco de dados. Quando é atingido o limite de sessão simultânea para um banco de dados, novas conexões com o banco de dados são negadas e o usuário receberá o código de erro 10928. No entanto, as sessões existentes no banco de dados não são encerradas. |
| **Código do erro** | **10928**: ID de recurso: 2. O limite de %s para o banco de dados é %d e foi atingido. Consulte http://go.microsoft.com/fwlink/?LinkId=267637 para obter assistência. |
| **Limite** | Depende do [nível de desempenho e da camada de serviço](https://msdn.microsoft.com/library/azure/dn741336.aspx). |
| **Recomendações** | Verifique se dm\_exec\_requests exibe quais solicitações de usuário estão em execução atualmente. |

> [AZURE.NOTE]O valor da ID de recurso na mensagem de erro indica que o recurso para limite foi atingido. Para sessões, ID de recursos = 2.

## Tempdb

| &nbsp; | Mais informações |
| :--- | :--- |
| **Condição** | Suas solicitações no tempdb podem ser negadas devido a qualquer uma das três condições a seguir:<br><br>** Estado 1:** quando uma sessão usa mais de 5 GB de espaço em tempdb, a sessão é encerrada.<br><br>** Estado 2:** as transações no tempdb com logs cujo tamanho é superior a 2 GB são truncadas. Operações de exemplo que podem consumir o espaço de log em tempdb: inserir, atualizar, excluir, mesclar, criar índice.<br><br>**Estado 3:** as transações não confirmadas no tempdb podem bloquear o truncamento dos arquivos de log. Para evitar isso, a distância do número da sequência de log da transação ativa mais antiga (LSN) até o final do log (LSN atual) em tempdb não pode exceder 20% do tamanho do arquivo de log. Quando violada, a transação incorreta no tempdb é encerrada e revertida de modo que o log possa ser truncado. |
| **Código do erro** | **40551**: a sessão foi encerrada devido ao uso excessivo de tempdb. Tente modificar sua consulta para reduzir o uso de espaço de tabela temporária. |
| **Limite** | **Estado 1:** 5 GB de espaço em tempdb <br><br> **Estado 2:** 2 GB por transação em tempdb <br><br>**Estado 3:** 20% do espaço total de log em tempdb |
| **Tipo de solicitações negadas** | Algumas instruções DDL ou DML em tempdb. |
| **Recomendações** | Modifique as consultas para reduzir o uso de espaço de tabela temporária, descarte os objetos temporários depois que eles não são mais necessários, trunque as tabelas ou remova tabelas não usadas. Reduza o tamanho dos dados na sua transação no tempdb reduzindo o número de linhas ou dividindo a operação em várias transações. |

## Duração da transação

| &nbsp; | Mais informações |
| :--- | :--- |
| **Condição** | As transações solicitam bloqueios em recursos, como linhas, páginas ou tabelas, em que a transação é dependente e, então, liberam os bloqueios quando não têm uma dependência dos recursos bloqueados. Suas solicitações podem ser negadas devido a qualquer uma das seguinte duas condições: Estado 1: se uma transação estiver sendo executada por mais de 24 horas, ela será encerrada. Estado 2: se uma transação bloquear um recurso exigido por uma tarefa do sistema subjacente por mais de 20 segundos, ela será encerrada. |
| **Código do erro** | **40549**: a sessão é encerrada porque você tem uma transação de longa duração. Tente encurtar a transação. |
| **Limite** | **Estado 1:** 24 horas<br><br>**Estado 2:** 20 segundos se uma transação bloquear um recurso exigido por uma tarefa do sistema subjacente |
| **Tipo de solicitações negadas** | Qualquer transação que tenha sido executada por mais de 24 horas ou quaisquer instruções DDL ou DML que usam um bloqueio, o que resulta em bloqueio de uma tarefa do sistema. |
| **Recomendações** | Operações no banco de dados SQL não devem bloquear a entrada do usuário ou ter outras dependências que resultam em transações de longa execução. |

## Contagem de bloqueio de transação

| &nbsp; | Mais informações |
| :--- | :--- |
| **Condição** | As sessões que consomem mais de um milhão de bloqueios são encerradas. |
| **Código do erro** | **40550**: a sessão foi encerrada porque adquiriu um número excessivo de bloqueios. Tente ler ou modificar menos linhas em uma única transação. |
| **Limite** | 1 milhão de bloqueios por transação |
| **Tipo de solicitações negadas** | Algumas instruções DDL ou DML. |
| **Recomendações** | Os DMVs a seguir podem ser usados para monitorar transações: **sys.dm\_tran\_active\_transactions**, **sys.dm\_tran\_database\_transactions**, **sys.dm\_tran\_locks** e **sys.dm\_tran\_session\_transactions**. Dependendo do tipo de aplicativo, talvez seja possível usar dicas de bloqueio de granulação mais espessa, como **PAGLOCK** ou **TABLOCK**, para reduzir o número de bloqueios adquiridos em uma determinada instrução/transação. Observe que isso pode afetar negativamente a simultaneidade do aplicativo. |


## Comprimento do log de transação


Na versão V12 do Banco de Dados SQL do Azure nas faixas de preço Standard ou Premium, o tamanho máximo do log de transação não é mais limitado a 2 GB.

A tabela a seguir descreve o limite do log de transação para a versão V11.


| &nbsp; | Mais informações |
| :--- | :--- |
| **Condição** | Suas solicitações podem ser negadas devido a qualquer uma das duas condições a seguir:<br><br>**Estado 1:** o Banco de Dados SQL dá suporte a transações que geram logs com tamanho de até 2 GB. As transações com logs além desse limite serão truncadas. Operações de exemplo que podem consumir o espaço de log neste volume: inserir, atualizar, excluir, mesclar, criar índice.<br><br>**Estado 2:** as transações não confirmadas podem bloquear o truncamento dos arquivos de log. Para evitar isso, a distância do número da sequência de log da transação ativa mais antiga (LSN) até o final do log (LSN atual) não pode exceder 20% do tamanho do arquivo de log. Quando violada, a transação incorreta é encerrada e revertida de modo que o log possa ser truncado. |
| **Código do erro** | **40552**: a sessão foi encerrada devido a uso excessivo do espaço de log de transação. Tente modificar menos linhas em uma única transação. |
| **Limite** | **Estado 1:** 2 GB por transação<br><br>**Estado 2:** 20% do espaço total de log |
| **Tipo de solicitações negadas** | Algumas instruções DDL ou DML. |
| **Recomendações** | Para operações de linha, reduza o tamanho dos dados na sua transação, por exemplo, reduzindo o número de linhas ou dividindo a operação em várias transações. Para operações de tabela/índice que exigem uma única transação, certifique-se de que a fórmula a seguir está em conformidade com: número de linhas afetadas na tabela * (tamanho médio do campo que está sendo atualizado em bytes + 80) < 2 GB (no caso de recriação de índice, o tamanho médio do campo que está sendo atualizado deve ser substituído pelo tamanho médio do índice). |

## Threads de trabalho (solicitações simultâneas máx.)

| &nbsp; | Mais informações |
| :--- | :--- |
| **Condição** | O banco de dados SQL gerencia o limite no número de threads de trabalho (solicitações simultâneas) para um banco de dados. Qualquer banco de dados com mais do que o limite permitido de solicitações simultâneas receberá o erro 10928, e solicitações adicionais neste banco de dados podem ser negadas. |
| **Códigos do Erro** | **10928**: ID de recurso: 1. O limite de %s para o banco de dados é %d e foi atingido. Consulte http://go.microsoft.com/fwlink/?LinkId=267637 para obter assistência.<br><br>**10929**: ID de recurso: 1. A garantia mínima de %s é %d, o limite máximo é %d e o uso atual do banco de dados é %d. No entanto, o servidor está muito ocupado para dar suporte a solicitações maiores que %d para este banco de dados. Consulte http://go.microsoft.com/fwlink/?LinkId=267637 para obter assistência. Caso contrário, tente novamente mais tarde. |
| **Limite** | Para as camadas Basic, Standard e Premium, isso depende do [nível de desempenho](https://msdn.microsoft.com/library/azure/dn741336.aspx). Para bancos de dados de Web/Business Edition antigos, o limite máximo de solicitações simultâneas é 180 e pode ser menor, dependendo da atividade do sistema. |
| **Recomendações** | Verifique dm\_exec\_requests para exibir quais solicitações de usuário estão em execução atualmente.<br><br>Retire e repita a solicitação depois de 10 segundos. |

> [AZURE.NOTE]O valor da ID de recurso na mensagem de erro indica que o recurso para limite foi atingido. Para threads de trabalhador, ID de recursos = 1.

Erros devido à governança em threads de trabalhador (10928/10929) substituem a limitação de mecanismo original de erro (40501) para threads de trabalhador. Em condições normais, os usuários não devem receber o erro de limitação de mecanismo para threads de trabalhador.

Em determinados cenários, como o uso do recurso de banco de dados agrupado, é possível receber o erro da extremidade do thread de trabalhador (10928) no momento do logon em um banco de dados, pois essa operação utilizaria um thread de trabalho abaixo da chamada Connection.Open. Isso pode colocar o aplicativo acima do limite de extremidade de thread de trabalho. Os aplicativos devem ter lógica interna para tratar esse erro adequadamente nesses casos.

## Consulte também

[Gerenciamento de Recursos do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/dn338083.aspx)

[Mensagens de erro para programas cliente do Banco de Dados SQL](sql-database-develop-error-messages.md)

[Práticas recomendadas do banco de dados SQL do Azure para evitar negações de solicitação ou encerramento da conexão](https://msdn.microsoft.com/library/azure/dn338082.aspx)

<!---HONumber=August15_HO7-->