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
	ms.date="09/22/2015" 
	ms.author="genemi"/>


# Mensagens de erro para programas cliente do Banco de Dados SQL


<!--
Old Title on MSDN:  Error Messages (Azure SQL Database)
ShortId on MSDN:  ff394106.aspx
Dx 4cff491e-9359-4454-bd7c-fb72c4c452ca
-->


Este tópico lista várias categorias de mensagens de erro. A maioria das categorias específicas do Banco de Dados SQL do Azure e não se aplicam ao Microsoft SQL Server.


Em seu programa cliente, você tem a opção de fornecer ao usuário uma mensagem alternativa, personalizada por você, para qualquer um dos erros.


**Dica:** a seção de erros de *falha transitória* é muito importante. Esses erros devem fazer com seu programa cliente execute a lógica de *repetição* que você criou para repetir a operação.


<a id="bkmk_connection_errors" name="bkmk_connection_errors">&nbsp;</a>


## Falhas transitórias, perda de conexão e outros erros temporários

A tabela a seguir aborda os erros de perda de conexão e outros erros transitórios com que você pode se deparar ao trabalhar na Internet com o Banco de Dados SQL do Azure.

Erros transitórios também são chamados de falhas transitórias. Quando o programa captura um `SqlException`, seu programa pode verificar se o valor `sqlException.Number` é um valor listado nesta seção de falhas transitórias. Se o valor `Number` indicar uma falha transitória, seu programa pode repetir a tentativa de estabelecer uma conexão e tentar novamente a consulta através da conexão. Para obter exemplos de código de lógica de repetição, consulte:


- [Desenvolvimento do cliente e exemplos de código de início rápido para o Banco de Dados SQL](sql-database-develop-quick-start-client-code-samples.md)

- [Como conectar-se de forma confiável ao Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/azure/dn864744.aspx)


| Número do erro | Severidade | Descrição |
| ---: | ---: | :--- |
| 4060 | 16 | Não é possível abrir o banco de dados "%.&#x2a;ls" solicitado pelo logon. Houve falha no logon. |
|10928|20|ID do recurso: %d. O limite de %s para o banco de dados é %d e foi atingido. Para saber mais, consulte [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637).<br/><br/>A ID do recurso qual dos recursos atingiu o limite. Para threads de trabalho, a ID do recurso é igual a 1. Para sessões, a ID do recurso é igual a 2.<br/><br/>*Observação:* para obter mais informações sobre esse erro e como resolvê-lo, consulte:<br/>•[Governança de recursos do Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/azure/dn338078.aspx). |
|10929|20|ID do recurso: %d. A garantia mínima de %s é %d, o limite máximo é %d e o uso atual do banco de dados é %d. No entanto, o servidor está muito ocupado para dar suporte a solicitações maiores que %d para este banco de dados. Para saber mais, consulte [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637). Caso contrário, tente novamente mais tarde.<br/><br/>A ID do recurso indica qual dos recursos atingiu o limite. Para threads de trabalho, a ID do recurso é igual a 1. Para sessões, a ID do recurso é igual a 2.<br/><br/>*Observação:* para obter mais informações sobre esse erro e como resolvê-lo, consulte:<br/>•[Governança de recursos do Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/azure/dn338078.aspx).|
|40197|17|O serviço encontrou um erro ao processar sua solicitação. Tente novamente. Código de erro %d.<br/><br/>Você receberá este erro quando o serviço ficar inativo devido a atualizações de software ou hardware, falhas de hardware ou quaisquer outros problemas de failover. O código de erro (%d) inserido na mensagem de erro 40197 fornece informações adicionais sobre o tipo de falha ou failover que ocorreu. Alguns exemplos de códigos que são inseridos na mensagem de erro 40197 são 40020, 40143, 40166 e 40540.<br/><br/>Reconectar-se ao servidor do Banco de Dados SQL conectará você automaticamente a uma cópia íntegra do banco de dados. Seu aplicativo deve capturar o erro 40197, registrar o código de erro inserido (%d) na mensagem para solução do problema e tentar se reconectar ao Banco de Dados SQL até que os recursos estejam disponíveis e a conexão seja restabelecida.|
|40501|20|O serviço está ocupado. Repita a solicitação depois de 10 segundos. ID do incidente: %ls. Código: %d.<br/><br/>*Observação:* para obter mais informações sobre esse erro e como resolvê-lo, consulte:<br/>• [Limitação do Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/azure/dn338079.aspx).
|40613|17|O banco de dados “%.&#x2a;ls” no servidor “%.&#x2a;ls” não está disponível momento. Tente a conexão novamente mais tarde. Se o problema persistir, entre em contato com o atendimento ao cliente e forneça a ID de rastreamento da sessão “%.&#x2a;ls”.|
|49918|16|Não é possível processar a solicitação. Não há recursos suficientes para processar a solicitação.<br/><br/>O serviço está ocupado no momento. Tente fazer novamente a solicitação. |
|49919|16|Não é possível criar o processo ou atualizar a solicitação. Muitas operações de criação ou atualização em andamento para a assinatura "%ld".<br/><br/>O serviço está ocupado processando várias solicitações de criação ou atualização para a assinatura ou o servidor. As solicitações estão bloqueadas no momento para a otimização de recursos. Consulte [sys.dm\_operation\_status](https://msdn.microsoft.com/library/dn270022.aspx) para operações pendentes. Espere até que as solicitações pendentes de criação ou atualização sejam concluídas ou exclua uma das suas solicitações pendentes e tente a solicitação novamente mais tarde. |
|49920|16|Não é possível processar a solicitação. Muitas operações em andamento para assinatura "% ld".<br/><br/>O serviço está ocupado processando várias solicitações para essa assinatura. As solicitações estão bloqueadas no momento para a otimização de recursos. Consulte [sys.dm\_operation\_status](https://msdn.microsoft.com/library/dn270022.aspx) para status de operação. Espere até que as solicitações pendentes estejam concluídas ou exclua uma das suas solicitações pendentes e tente a solicitação novamente mais tarde. |

**Observação:** Talvez seja necessário incluir os erros de federação 10053 e 10054 em sua lógica de repetição.


## Erros de cópia de banco de dados


A tabela a seguir abrange vários erros que você pode encontrar ao copiar um banco de dados no Banco de Dados SQL do Azure. Para saber mais, consulte [Copiando Bancos de Dados no Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/azure/ff951624.aspx).


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


## Erros de governança de recursos


A tabela a seguir abrange os erros causados pelo uso excessivo de recursos enquanto você trabalha com o Banco de Dados SQL do Azure. Por exemplo:


- Sua transação pode ter ficado aberta por muito tempo.
- Sua transação pode estar mantendo bloqueios demais.
- Seu programa pode estar consumindo muita memória.
- Seu programa pode estar consumindo muito espaço de `TempDb`.


**Dica:** o link a seguir oferece mais informações que se aplicam à maioria ou a todos os erros nesta seção:


- [Limites de Recurso de Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/azure/dn338081.aspx).


|Número do erro|Severidade|Descrição|
|---:|---:|:---|
|40544|20|O banco de dados atingiu sua cota de tamanho. Particione ou exclua dados, descarte índices ou consulte a documentação para conhecer as possíveis resoluções.|
|40549|16|A sessão foi encerrada porque você tem uma transação de longa duração. Tente encurtar a transação.|
|40550|16|A sessão foi encerrada porque adquiriu muitos bloqueios. Tente ler ou modificar menos linhas em uma única transação.|
|40551|16|A sessão foi encerrada devido ao uso excessivo de `TEMPDB`. Tente modificar a consulta para reduzir o uso temporário de espaço da tabela.<br/><br/>*Dica:* se você estiver usando objetos temporários, conserve espaço no banco de dados `TEMPDB` removendo os objetos temporários após eles deixarem de ser necessários para a sessão.|
|40552|16|A sessão foi encerrada devido a uso excessivo do espaço de log de transação. Tente modificar menos linhas em uma única transação.<br/><br/>*Dica:* se você executar inserções em massa usando o utilitário `bcp.exe` ou a classe `System.Data.SqlClient.SqlBulkCopy`, tente usar as opções `-b batchsize` ou `BatchSize` para limitar o número de linhas copiadas para o servidor em cada transação. Se você estiver recriando um índice com a instrução `ALTER INDEX`, tente usar a opção `REBUILD WITH ONLINE = ON`.|
|40553|16|A sessão foi encerrada devido ao uso excessivo de memória. Tente modificar a consulta para processar menos linhas.<br/><br/>*Dica:* reduzir o número de operações `ORDER BY` e `GROUP BY` em seu código Transact-SQL reduz os requisitos de memória de sua consulta.|


Para ver uma discussão adicional sobre a governança de recursos e os erros associados a ela, consulte:


- [Governança de Recursos do Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/azure/dn338078.aspx).


## Erros de federação


A tabela a seguir abrange os erros que você pode encontrar ao trabalhar com federações. Para saber mais, consulte [Gerenciando Federações de Bancos de Dados (Banco de Dados SQL do Azure)](http://msdn.microsoft.com/library/azure/hh597455.aspx).


> [AZURE.IMPORTANT]A implementação atual das Federações será descontinuada com as camadas de serviço Web e Business. A versão V12 do Banco de Dados SQL do Azure não dá suporte às camadas de serviço Web e Business.
> 
> O recurso de Dimensionamento elástico foi projetado para criar aplicativos de fragmentação com um esforço mínimo.
> 
> Para obter mais informações sobre o Dimensionamento Elástico, consulte [Tópicos sobre o Dimensionamento Elástico do Banco de Dados SQL do Azure](sql-database-elastic-scale-documentation-map.md). Considere a implantação de soluções personalizadas de fragmentação para maximizar a escalabilidade, a flexibilidade e o desempenho. Para obter mais informações sobre a fragmentação personalizada, consulte [Colocando em Escala Horizontalmente o Bancos de Dados SQL do Azure](http://msdn.microsoft.com/library/azure/dn495641.aspx).


|Número do erro|Severidade|Descrição|Redução|
|---:|---:|:---|:---|
|266|16|A instrução <statement> não é permitida em transações com várias instruções|Verifique se `@@trancount` é 0 na conexão antes de emitir a instrução.|
|2072|16|O banco de dados “%.&#x2a;ls” não existe|Verifique `sys.databases` quanto ao estado do banco de dados antes de emitir `USE FEDERATION`.|
|2209|16|%s Erro de sintaxe próximo a '%ls'|`FEDERATED ON` só pode ser usado ao criar tabelas em membros da federação.|
|2714|16|Já existe um objeto chamado '%.&#x2a;ls' no banco de dados|O nome da federação já existe.|
|10054, 10053|20|Ocorreu um erro de nível de transporte ao receber os resultados do servidor. Uma conexão estabelecida foi anulada pelo software no computador host|Implemente a lógica de repetição em seu aplicativo.|
|40530|15|<statement> deve ser a única instrução no lote|Certifique-se de que não haja nenhuma outra instrução no lote|
|40604|16|Não foi possível `CREATE DATABASE`, pois isso excederia a cota do servidor|Expanda a cota de contagem do banco de dados do servidor|
|45000|16|Falha na operação de <statement>. O nome da federação especificado <federation_name> não é válido|Federation\_name não está em conformidade com as regras de nome de federação ou não é um identificador válido|
|45001|16|Falha na operação de <statement>. O nome da federação especificado não existe|O nome da federação não existe|
|45002|16|Falha na operação de <statement>. O nome da chave de federação especificado <distribution_name> não é válido|Chave de federação inválida ou inexistente|
|45004|16|Falha na operação de <statement>. O valor especificado não é válido para a chave da federação <distribution_name> e a federação <federation_name>|`USE FEDERATION`: use um valor de limite que esteja no domínio do tipo de dados da chave de federação ou que não seja NULL.<br/><br/>`ALTER FEDERATION SPLIT`: use um valor válido no domínio da chave de federação que ainda não seja um ponto de divisão existente.<br/><br/>`ALTER FEDERATION DROP`: use um valor válido no domínio da chave de federação que já seja um ponto de divisão.|
|45005|16|<statement> não pode ser executado enquanto outra operação de federação estiver em andamento na federação <federation_name> e no membro com a id <member_id>|Aguarde a operação simultânea ser concluída.|
|45006|16|Falha nas operações de <statement>. Relações de chave estrangeira em tabelas de referência que se referem a tabelas federadas não são permitidas em membros da federação|Sem suporte.|
|45007|16|Falha na operação de <statement>. Relações de chave estrangeira entre tabelas federadas devem incluir as colunas de chave de federação.|Sem suporte|
|45008|16|Falha na operação de <statement>. O tipo de dados da chave de federação não coincide com o tipo de dados da coluna|Sem suporte.|
|45009|16|Falha na operação de <statement>. A operação não tem suporte em conexões de filtragem|Sem suporte.|
|45010|16|Falha na operação de <statement>. Não é possível atualizar a chave da federação|Sem suporte.|
|45011|16|Falha na operação de <statement>. Não é possível atualizar o esquema da chave da federação|Sem suporte.|
|45012|16|O valor especificado para a chave de federação não é válido|O valor deve estar no intervalo que a conexão está tratando.<br/><br/>Se filtrado, o valor da chave de federação especificado.<br/><br/>Se não filtrado, o intervalo coberto pelo membro da federação.|
|45013|16|O SID já existe com um nome de usuário diferente|O SID de um usuário em um membro da federação é copiado do SID a mesma conta de usuário na raiz da federação. Sob certas condições, o SID já pode estar em uso.|
|45014|16|Não há suporte para %Is em %Is.|Operação sem suporte.|
|45022|16|Falha na operação de <statement>. O valor limite especificado já existe para a chave de federação <distribution_name> e a federação <federation_name>|Especifique um valor que já seja um valor de limite.|
|45023|16|Falha na operação de <statement>. O valor limite especificado não existe para a chave de federação <distribution_name> e a federação <federation_name>|Especifique um valor que não seja um valor de limite.|


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
|40553|16|A sessão foi encerrada devido ao uso excessivo de memória. Tente modificar a consulta para processar menos linhas.<br/><br/>*Observação:* reduzir o número de operações `ORDER BY` e `GROUP BY` em seu código Transact-SQL ajuda a reduzir os requisitos de memória de sua consulta.|
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
|40639|16|A solicitação não corresponde ao esquema: <schema error>.|
|40640|20|O servidor encontrou uma exceção inesperada.|
|40641|16|O local especificado é inválido.|
|40642|17|O servidor está muito ocupado no momento. Tente novamente mais tarde.|
|40643|16|O valor de cabeçalho x-ms-version especificado é inválido.|
|40644|14|Falha ao autorizar o acesso à assinatura especificada.|
|40645|16|O nome do servidor <servername> não pode ficar nulo ou vazio. Ele pode ser composto apenas por letras minúsculas de “a” a “z”, números de 0 a 9 e o hífen. O hífen não pode ser o primeiro ou o último caractere do nome.|
|40646|16|A ID da assinatura não pode ficar vazia.|
|40647|16|A assinatura <id da assinatura> não tem um nome de servidor.|
|40648|17|Muitas solicitações foram executadas. Tente novamente mais tarde.|
|40649|16|Tipo de conteúdo inválido especificado. Somente aplicativo/xml tem suporte.|
|40650|16|A assinatura <subscription-id> não existe ou não está pronta para a operação.|
|40651|16|Falha ao criar o servidor porque a assinatura <subscription-id> está desabilitada.|
|40652|16|Não é possível mover ou criar o servidor. A assinatura <subscription-id> ultrapassará a cota do servidor.|
|40671|17|Falha de comunicação entre o gateway e o serviço de gerenciamento. Tente novamente mais tarde.|
|45168|16|O sistema do SQL Azure está sob carga e está estabelecendo um limite superior para operações CRUD de BD simultâneas para um único servidor (por exemplo, criar banco de dados). O servidor especificado na mensagem de erro ultrapassou o número máximo de conexões simultâneas. Tente novamente mais tarde.|
|45169|16|O sistema SQL Azure está sob carga e está estabelecendo um limite superior para o número de operações CRUD de servidor simultâneas para uma única assinatura (por exemplo, criar servidor). A assinatura especificada na mensagem de erro ultrapassou o número máximo de conexões simultâneas e a solicitação foi negada. Tente novamente mais tarde.|


## Links relacionados

- [Limitações e diretrizes de gerais do Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/azure/ee336245.aspx)
- [Gerenciamento de recursos](http://msdn.microsoft.com/library/azure/dn338083.aspx)

<!---HONumber=Oct15_HO3-->