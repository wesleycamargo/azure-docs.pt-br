<properties 
	pageTitle="Mensagens de erro para programas cliente do Banco de Dados SQL"
	description="Para cada erro, são fornecidos a ID numérica e a mensagem de texto. Fique à vontade para fazer referência cruzada com seus próprios textos de mensagens de erro mais amigáveis, se desejar."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor="" />


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/24/2015" 
	ms.author="genemi"/>


# Mensagens de erro para programas cliente do Banco de Dados SQL


<!--
Old Title on MSDN:  Error Messages (Azure SQL Database)
ShortId on MSDN:  ff394106.aspx
Dx 4cff491e-9359-4454-bd7c-fb72c4c452ca
-->


Este tópico lista várias categorias de mensagens de erro. A maioria das categorias específicas do Banco de Dados SQL do Azure e não se aplicam ao Microsoft SQL Server.


Em seu programa cliente, você tem a opção de fornecer ao usuário uma mensagem alternativa, personalizada por você, para qualquer um dos erros.


> [AZURE.TIP]A seção a seguir, sobre erros de [*falha transitória*](#bkmk_connection_errors), é muito importante.



<a id="bkmk_connection_errors" name="bkmk_connection_errors">&nbsp;</a>


## Falhas transitórias, perda de conexão e outros erros temporários

A tabela a seguir aborda os erros de perda de conexão e outros erros transitórios com que você pode se deparar ao trabalhar na Internet com o Banco de Dados SQL do Azure.


### Falhas transitórias mais comuns


Erros de falhas transitórias normalmente se manifestam como uma das seguintes mensagens de erro de seus programas de cliente:

- O banco de dados <db_name> no servidor <Azure_instance> não está disponível atualmente. Tente a conexão novamente mais tarde. Se o problema persistir, entre em contato com o atendimento ao cliente e forneça a ID de rastreamento da sessão de <session_id>

- O banco de dados <db_name> no servidor <Azure_instance> não está disponível atualmente. Tente a conexão novamente mais tarde. Se o problema persistir, entre em contato com o atendimento ao cliente e forneça a ID de rastreamento da sessão de <session_id> (Microsoft SQL Server, erro: 40613)

- Uma conexão existente foi fechada à força pelo host remoto.

- System.Data.Entity.Core.EntityCommandExecutionException: erro ao executar a definição de comando. Consulte a exceção interna para obter detalhes. ---> System.Data.SqlClient.SqlException: erro de nível de transporte ao receber os resultados do servidor. (provedor: Provedor de Sessão, erro: 19 - a conexão física não é utilizável)

Erros de falha transitória devem solicitar que o programa cliente execute a *lógica de repetição* que você projetar para tentar a operação novamente. Para obter exemplos de código de lógica de repetição, consulte:


- [Desenvolvimento do cliente e exemplos de código de início rápido para o Banco de Dados SQL](sql-database-develop-quick-start-client-code-samples.md)

- [Ações para corrigir erros e falhas transitórias no Banco de Dados SQL](sql-database-connectivity-issues.md)


### Números de erros de falha transitória


| Número do erro | Severidade | Descrição |
| ---: | ---: | :--- |
| 4060 | 16 | Não é possível abrir o banco de dados "%.&#x2a;ls" solicitado pelo logon. Houve falha no logon. |
|40197|17|O serviço encontrou um erro ao processar sua solicitação. Tente novamente. Código de erro %d.<br/><br/>Você receberá este erro quando o serviço ficar inativo devido a atualizações de software ou hardware, falhas de hardware ou quaisquer outros problemas de failover. O código de erro (%d) inserido na mensagem de erro 40197 fornece informações adicionais sobre o tipo de falha ou failover que ocorreu. Alguns exemplos de códigos que são inseridos na mensagem de erro 40197 são 40020, 40143, 40166 e 40540.<br/><br/>Reconectar-se ao servidor do Banco de Dados SQL conectará você automaticamente a uma cópia íntegra do banco de dados. Seu aplicativo deve capturar o erro 40197, registrar o código de erro inserido (%d) na mensagem para solução do problema e tentar se reconectar ao Banco de Dados SQL até que os recursos estejam disponíveis e a conexão seja restabelecida.|
|40501|20|O serviço está ocupado. Repita a solicitação depois de 10 segundos. ID do incidente: %ls. Código: %d.<br/><br/>*Observação:* para obter mais informações, consulte:<br/>•[Limites de recursos do Banco de Dados SQL do Azure](sql-database-resource-limits.md).
|40613|17|O banco de dados “%.&#x2a;ls” no servidor “%.&#x2a;ls” não está disponível momento. Tente a conexão novamente mais tarde. Se o problema persistir, entre em contato com o atendimento ao cliente e forneça a ID de rastreamento da sessão “%.&#x2a;ls”.|
|49918|16|Não é possível processar a solicitação. Não há recursos suficientes para processar a solicitação.<br/><br/>O serviço está ocupado no momento. Tente fazer novamente a solicitação. |
|49919|16|Não é possível criar o processo ou atualizar a solicitação. Muitas operações de criação ou atualização em andamento para a assinatura "%ld".<br/><br/>O serviço está ocupado processando várias solicitações de criação ou atualização para a assinatura ou o servidor. As solicitações estão bloqueadas no momento para a otimização de recursos. Consulte [sys.dm\_operation\_status](https://msdn.microsoft.com/library/dn270022.aspx) para operações pendentes. Espere até que as solicitações pendentes de criação ou atualização sejam concluídas ou exclua uma das suas solicitações pendentes e tente a solicitação novamente mais tarde. |
|49920|16|Não é possível processar a solicitação. Muitas operações em andamento para assinatura "% ld".<br/><br/>O serviço está ocupado processando várias solicitações para essa assinatura. As solicitações estão bloqueadas no momento para a otimização de recursos. Consulte [sys.dm\_operation\_status](https://msdn.microsoft.com/library/dn270022.aspx) para status de operação. Espere até que as solicitações pendentes estejam concluídas ou exclua uma das suas solicitações pendentes e tente a solicitação novamente mais tarde. |

**Observação:** erros 10053 e 10054 podem também necessitar de inclusão em sua lógica de repetição.


<a id="bkmk_b_database_copy_errors" name="bkmk_b_database_copy_errors">&nbsp;</a>

## Erros de cópia de banco de dados


A tabela a seguir abrange vários erros que você pode encontrar ao copiar um banco de dados no Banco de Dados SQL do Azure. Para obter mais informações, consulte [Copiar um banco de dados SQL do Azure](sql-database-copy.md).


|Número do erro|Severidade|Descrição|
|---:|---:|:---|
|40635|16|O cliente com endereço IP “%.&#x2a;ls” está desabilitado temporariamente.|
|40637|16|A criação da cópia do banco de dados está desabilitada no momento.|
|40561|16|Falha na cópia do banco de dados. O banco de dados de origem ou de destino não existe.|
|40562|16|Falha na cópia do banco de dados. O banco de dados de origem foi descartado.|
|40563|16|Falha na cópia do banco de dados. O banco de dados de destino foi descartado.|
|40564|16|Falha na cópia do banco de dados devido a um erro interno. Remova o banco de dados de destino e tente novamente.|
|40565|16|Falha na cópia do banco de dados. Não é permitida mais de uma cópia simultânea do banco de dados com a mesma origem. Remova o banco de dados de destino e tente novamente mais tarde.|
|40566|16|Falha na cópia do banco de dados devido a um erro interno. Remova o banco de dados de destino e tente novamente.|
|40567|16|Falha na cópia do banco de dados devido a um erro interno. Remova o banco de dados de destino e tente novamente.|
|40568|16|Falha na cópia do banco de dados. O banco de dados de origem tornou-se indisponível. Remova o banco de dados de destino e tente novamente.|
|40569|16|Falha na cópia do banco de dados. O banco de dados de destino tornou-se indisponível. Remova o banco de dados de destino e tente novamente.|
|40570|16|Falha na cópia do banco de dados devido a um erro interno. Remova o banco de dados de destino e tente novamente mais tarde.|
|40571|16|Falha na cópia do banco de dados devido a um erro interno. Remova o banco de dados de destino e tente novamente mais tarde.|


<a id="bkmk_c_resource_gov_errors" name="bkmk_c_resource_gov_errors">&nbsp;</a>

## Erros de governança de recursos


A tabela a seguir abrange os erros causados pelo uso excessivo de recursos enquanto você trabalha com o Banco de Dados SQL do Azure. Por exemplo:


- Sua transação pode ter ficado aberta por muito tempo.
- Sua transação pode estar mantendo bloqueios demais.
- Seu programa pode estar consumindo muita memória.
- Seu programa pode estar consumindo muito espaço de `TempDb`.


**Dica:** o link a seguir oferece mais informações que se aplicam à maioria ou a todos os erros nesta seção:


- [Limites de recursos do Banco de Dados SQL do Azure](sql-database-resource-limits.md)


|Número do erro|Severidade|Descrição|
|---:|---:|:---|
|10928|20|ID do recurso: %d. O limite de %s para o banco de dados é %d e foi atingido. Para saber mais, consulte [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637).<br/><br/>A ID do recurso qual dos recursos atingiu o limite. Para threads de trabalho, a ID do recurso é igual a 1. Para sessões, a ID do recurso é igual a 2.<br/><br/>*Observação:* para obter mais informações sobre esse erro e como resolvê-lo, consulte:<br/>•[Limites de recursos do Banco de Dados SQL do Azure](sql-database-resource-limits.md). |
|10929|20|ID do recurso: %d. A garantia mínima de %s é %d, o limite máximo é %d e o uso atual do banco de dados é %d. No entanto, o servidor está muito ocupado para dar suporte a solicitações maiores que %d para este banco de dados. Para saber mais, consulte [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637). Caso contrário, tente novamente mais tarde.<br/><br/>A ID do recurso indica qual dos recursos atingiu o limite. Para threads de trabalho, a ID do recurso é igual a 1. Para sessões, a ID do recurso é igual a 2.<br/><br/>*Observação:* para obter mais informações sobre esse erro e como resolvê-lo, consulte:<br/>•[Limites de recursos do Banco de Dados SQL do Azure](sql-database-resource-limits.md).|
|40544|20|O banco de dados atingiu sua cota de tamanho. Particione ou exclua dados, descarte índices ou consulte a documentação para conhecer as possíveis resoluções.|
|40549|16|A sessão foi encerrada porque você tem uma transação de longa duração. Tente encurtar a transação.|
|40550|16|A sessão foi encerrada porque adquiriu muitos bloqueios. Tente ler ou modificar menos linhas em uma única transação.|
|40551|16|A sessão foi encerrada devido ao uso excessivo de `TEMPDB`. Tente modificar a consulta para reduzir o uso temporário de espaço da tabela.<br/><br/>*Dica:* se você estiver usando objetos temporários, conserve espaço no banco de dados `TEMPDB` removendo os objetos temporários após eles deixarem de ser necessários para a sessão.|
|40552|16|A sessão foi encerrada devido a uso excessivo do espaço de log de transação. Tente modificar menos linhas em uma única transação.<br/><br/>*Dica:* se você executar inserções em massa usando o utilitário `bcp.exe` ou a classe `System.Data.SqlClient.SqlBulkCopy`, tente usar as opções `-b batchsize` ou `BatchSize` para limitar o número de linhas copiadas para o servidor em cada transação. Se você estiver recriando um índice com a instrução `ALTER INDEX`, tente usar a opção `REBUILD WITH ONLINE = ON`.|
|40553|16|A sessão foi encerrada devido ao uso excessivo de memória. Tente modificar a consulta para processar menos linhas.<br/><br/>*Dica:* reduzir o número de operações `ORDER BY` e `GROUP BY` em seu código Transact-SQL reduz os requisitos de memória de sua consulta.|


Para ver uma discussão adicional sobre a governança de recursos e os erros associados a ela, consulte:


- [Limites de Recurso de Banco de Dados SQL do Azure](sql-database-resource-limits.md).



<a id="bkmk_e_general_errors" name="bkmk_e_general_errors">&nbsp;</a>

## Erros gerais


A tabela a seguir lista todos os erros gerais que não se enquadram em nenhuma categoria anterior.


|Número do erro|Severidade|Descrição|
|---:|---:|:---|
|15006|16|<AdministratorLogin> não é um nome válido porque contém caracteres inválidos.|
|18452|14|Falha no logon. O logon é de um domínio não confiável e não pode ser usado com a autenticação do Windows.%.&#x2a;ls (logons do Windows não têm suporte nesta versão do SQL Server.)|
|18456|14|Falha no logon do usuário '%.&#x2a;ls'.%.&#x2a;ls%.&#x2a;ls(Falha de logon do usuário "%.&#x2a;ls". Falha na alteração da senha do usuário. A alteração de senha durante o logon não tem suporte nesta versão do SQL Server.)|
|18470|14|Falha no logon do usuário '%.&#x2a;ls'. Motivo: A conta está desabilitada.%.&#x2a;ls|
|40014|16|Não é possível usar vários bancos de dados na mesma transação.|
|40054|16|Tabelas sem um índice clusterizado não têm suporte nesta versão do SQL Server. Criar um índice clusterizado e tente novamente.|
|40133|15|Esta operação não tem suporte nesta versão do SQL Server.|
|40506|16|O SID especificado é inválido para esta versão do SQL Server.|
|40507|16|'%.&#x2a;ls' não pode ser invocado com parâmetros nesta versão do SQL Server.|
|40508|16|Não há suporte para a instrução USE para alternar entre bancos de dados. Use uma nova conexão para se conectar a um banco de dados diferente.|
|40510|16|A instrução '%.&#x2a;ls' não tem suporte nesta versão do SQL Server|
|40511|16|A função incorporada '%.&#x2a;ls' não tem suporte nesta versão do SQL Server.|
|40512|16|O recurso substituído '%ls' não tem suporte nesta versão do SQL Server.|
|40513|16|A variável do servidor '%.&#x2a;ls' não tem suporte nesta versão do SQL Server.|
|40514|16|'ls' não tem suporte nesta versão do SQL Server.|
|40515|16|Referências ao nome do banco de dados e/ou servidor em '%.&#x2a;ls' não têm suporte nesta versão do SQL Server.|
|40516|16|Objetos temporários globais não têm suporte nesta versão do SQL Server.|
|40517|16|A opção de instrução ou palavra-chave '%.&#x2a;ls' não tem suporte nesta versão do SQL Server.|
|40518|16|O comando DBCC '%.&#x2a;ls' não tem suporte nesta versão do SQL Server.|
|40520|16|A classe protegível '%S\_MSG' não tem suporte nesta versão do SQL Server.|
|40521|16|A classe protegível '%S\_MSG' não tem suporte no escopo do servidor nesta versão do SQL Server.|
|40522|16|O banco de dados com tipo de entidade de segurança '%.&#x2a;ls' não tem suporte nesta versão do SQL Server.|
|40523|16|A criação de usuário implícito '%.&#x2a;ls' não tem suporte nesta versão do SQL Server. Crie explicitamente o usuário antes de usá-lo.|
|40524|16|O tipo de dados '%.&#x2a;ls' não tem suporte nesta versão do SQL Server.|
|40525|16|WITH 'ls' não tem suporte nesta versão do SQL Server.|
|40526|16|O provedor de conjunto de linhas '%.&#x2a;ls' não tem suporte nesta versão do SQL Server.|
|40527|16|Os servidores vinculados não têm suporte nesta versão do SQL Server.|
|40528|16|Usuários não podem ser mapeados para certificados, chaves assimétricas ou logons do Windows nesta versão do SQL Server.|
|40529|16|A função incorporada '%.&#x2a;ls' no contexto de representação não tem suporte nesta versão do SQL Server.|
|40532|11|Não é possível abrir o servidor "%.&#x2a;ls" solicitado pelo logon. Houve falha no logon.|
|40553|16|A sessão foi encerrada devido ao uso excessivo de memória. Tente modificar sua consulta para processar menos linhas.<br/><br/>*Observação:* reduzir o número de operações `ORDER BY` e `GROUP BY` em seu código Transact-SQL ajuda a reduzir os requisitos de memória de sua consulta.|
|40604|16|Não foi possível CRIAR/ALTERAR O BANCO DE DADOS pois isso excederia a cota do servidor.|
|40606|16|Anexar bancos de dados não tem suporte nesta versão do SQL Server.|
|40607|16|Logons do Windows não têm suporte nesta versão do SQL Server.|
|40611|16|Os servidores podem ter no máximo 128 regras de firewall definidas.|
|40614|16|O endereço IP inicial da regra de firewall não pode ultrapassar o endereço IP final.|
|40615|16|Não é possível abrir o servidor '{0}' solicitado pelo logon. O cliente com o endereço IP '{1}' não tem permissão para acessar o servidor. Para habilitar o acesso, use o Portal do Banco de Dados SQL ou execute sp\_set\_firewall\_rule no banco de dados mestre para criar uma regra de firewall este endereço ou intervalo de endereços IP. Pode levar até cinco minutos para que essa alteração tenha efeito.|
|40617|16|O nome da regra de firewall que começa com <rule name> é muito longo. O comprimento máximo é 128.|
|40618|16|O nome da regra de firewall não pode estar vazio.|
|40620|16|Falha no logon do usuário “%.&#x2a;ls”. Falha na alteração da senha do usuário. A alteração de senha durante o logon não tem suporte nesta versão do SQL Server.|
|40627|20|A operação no servidor '{0}' e banco de dados '{1}' está em andamento. Aguarde alguns minutos antes de tentar novamente.|
|40630|16|Falha na validação da senha. A senha não atende aos requisitos da política porque é muito curta.|
|40631|16|A senha que você especificou é muito longa. A senha deve ter no máximo 128 caracteres.|
|40632|16|Falha na validação da senha. A senha não atende aos requisitos da política porque não é complexa o suficiente.|
|40636|16|Não é possível usar um nome de banco de dados reservado '%.&#x2a;ls' nesta operação.|
|40638|16|Id de assinatura inválida <subscription-id>. A assinatura não existe.|
|40639|16|Solicitação não corresponde ao esquema: <schema error>.|
|40640|20|O servidor encontrou uma exceção inesperada.|
|40641|16|O local especificado é inválido.|
|40642|17|O servidor está muito ocupado no momento. Tente novamente mais tarde.|
|40643|16|O valor de cabeçalho x-ms-version especificado é inválido.|
|40644|14|Falha ao autorizar o acesso à assinatura especificada.|
|40645|16|Servername <servername> não pode ser nulo ou vazio. Ele pode ser composto apenas por letras minúsculas de “a” a “z”, números de 0 a 9 e o hífen. O hífen não pode ser o primeiro ou o último caractere do nome.|
|40646|16|A ID da assinatura não pode ficar vazia.|
|40647|16|A assinatura <id da assinatura> não tem um nome de servidor.|
|40648|17|Muitas solicitações foram executadas. Tente novamente mais tarde.|
|40649|16|Tipo de conteúdo inválido especificado. Somente aplicativo/xml tem suporte.|
|40650|16|Assinatura <subscription-id> não existe ou não está pronta para a operação.|
|40651|16|Falha ao criar o servidor porque a assinatura <subscription-id> está desabilitada.|
|40652|16|Não é possível mover ou criar o servidor. Assinatura <subscription-id> excederá a cota do servidor.|
|40671|17|Falha de comunicação entre o gateway e o serviço de gerenciamento. Tente novamente mais tarde.|
|45168|16|O sistema do SQL Azure está sob carga e está estabelecendo um limite superior para operações CRUD de BD simultâneas para um único servidor (por exemplo, criar banco de dados). O servidor especificado na mensagem de erro ultrapassou o número máximo de conexões simultâneas. Tente novamente mais tarde.|
|45169|16|O sistema SQL Azure está sob carga e está estabelecendo um limite superior para o número de operações CRUD de servidor simultâneas para uma única assinatura (por exemplo, criar servidor). A assinatura especificada na mensagem de erro ultrapassou o número máximo de conexões simultâneas e a solicitação foi negada. Tente novamente mais tarde.|


## Links relacionados

- [Diretrizes e limitações gerais do Banco de Dados SQL do Azure](sql-database-general-limitations.md)
- [Limites de recursos do Banco de Dados SQL do Azure](sql-database-resource-limits.md)

<!---HONumber=AcomDC_1125_2015-->