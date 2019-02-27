---
title: Diferenças de T-SQL da Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: Este artigo aborda as diferenças do T-SQL entre uma Instância Gerenciada do Banco de Dados SQL do Azure e no SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlrab, bonova
manager: craigg
ms.date: 02/20/2019
ms.openlocfilehash: 942b1423583f663f22ced6ea8399409778b2f6de
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56455120"
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Diferenças de T-SQL da Instância Gerenciada do Banco de Dados SQL do Azure em relação ao SQL Server

A opção de implantação de Instância Gerenciada fornece alta compatibilidade com o Mecanismo de Banco de Dados do SQL Server local. A maioria dos recursos do mecanismo de banco de dados do SQL Server é compatível com uma Instância Gerenciada.

![migração](./media/sql-database-managed-instance/migration.png)

Como ainda há algumas diferenças no comportamento e na sintaxe, este artigo resume e explica essas diferenças. <a name="Differences"></a>
- [Disponibilidade](#availability), incluindo as diferenças no [AlwaysOn](#always-on-availability) e em [Backups](#backup);
- [Segurança](#security), incluindo as diferenças em [Auditoria](#auditing), [Certificados](#certificates), [Credenciais](#credential), [Provedores de criptografia](#cryptographic-providers), [Logons/usuários](#logins--users) e [Chave de serviço e chave mestra de serviço](#service-key-and-service-master-key);
- [Configuração](#configuration), incluindo as diferenças em [Extensão do pool de buffers](#buffer-pool-extension), [Ordenação](#collation), [Níveis de compatibilidade](#compatibility-levels), [Espelhamento de banco de dados](#database-mirroring), [Opções de banco de dados](#database-options), [SQL Server Agent](#sql-server-agent) e [Opções de tabela](#tables);
- [Funcionalidades](#functionalities), incluindo [BULK INSERT/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [Transações distribuídas](#distributed-transactions), [Eventos estendidos](#extended-events), [Bibliotecas externas](#external-libraries), [Fluxo de arquivos e FileTable](#filestream-and-filetable), [Pesquisa semântica de texto completo](#full-text-semantic-search), [Servidores vinculados](#linked-servers), [PolyBase](#polybase), [Replicação](#replication), [RESTORE](#restore-statement), [Service Broker](#service-broker) e [Procedimentos armazenados, funções e gatilhos](#stored-procedures-functions-triggers);
- [Recursos que têm um comportamento diferente em Instâncias Gerenciadas](#Changes)
- [Limitações temporárias e problemas conhecidos](#Issues)

## <a name="availability"></a>Disponibilidade

### <a name="always-on-availability"></a>AlwaysOn

A [Alta disponibilidade](sql-database-high-availability.md) é um recurso interno à instância gerenciada e não pode ser controlada por usuários. As instruções a seguir não têm suporte:

- [CRIAR ENDPOINT … PARA DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
- [CRIAR GRUPO DE DISPONIBILIDADE](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTERAR GRUPO DE DISPONIBILIDADE](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DESCARTAR GRUPO DE DISPONIBILIDADE](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
- Cláusula [SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) da instrução [ALTERAR BANCO DE DADOS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql)

### <a name="backup"></a>Backup

As instâncias gerenciadas têm backups automáticos e permitem que os usuários criem backups `COPY_ONLY` de bancos de dados completos. Não há suporte para backups de backups de instantâneo de arquivo, logs e diferenciais.

- Com uma Instância Gerenciada, você pode fazer backup de uma instância de banco de dados somente em uma conta do Armazenamento de Blobs do Azure:
  - Apenas `BACKUP TO URL` tem suporte
  - `FILE`, `TAPE` e dispositivos de backup não têm suporte  
- A maioria das opções gerais `WITH` têm suporte
  - `COPY_ONLY` é obrigatório
  - `FILE_SNAPSHOT` não tem suporte
  - Opções de fita: `REWIND`, `NOREWIND`, `UNLOAD` e `NOUNLOAD` não são compatíveis
  - Opções específicas de log: `NORECOVERY`, `STANDBY` e `NO_TRUNCATE` não são compatíveis

 Limitações:  

- Com uma Instância Gerenciada, você pode fazer backup de um banco de dados de instâncias em um backup com até 32 faixas, o que é suficiente para bancos de dados de até 4 TB, caso a compactação de backup esteja em uso.
- O tamanho máximo de faixa de backup é 195 GB (tamanho máximo de blob). Aumente o número de faixas no comando de backup para reduzir o tamanho de faixas individuais e permanecer dentro desse limite.

> [!TIP]
> Para contornar essa limitação no local, faça backup para `DISK` em vez de backup para `URL`, carregue o arquivo de backup para o blob e, em seguida, restaure. A restauração oferece suporte a arquivos maiores porque um tipo de blob diferente é usado.  

Para obter informações sobre backups usando o T-SQL, consulte [BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Segurança

### <a name="auditing"></a>Auditoria

As principais diferenças entre a auditoria em bancos de dados no Banco de Dados SQL do Azure e em bancos de dados no SQL Server são:

- Com a opção de implantação de Instância Gerenciada no Banco de Dados SQL do Azure, a auditoria funciona no nível do servidor e armazena os arquivos de log `.xel` no armazenamento de Blobs do Azure.
- Com as opções de implantação de banco de dados individual e pool elástico no Banco de Dados SQL do Azure, a auditoria funciona no nível do banco de dados.
- No SQL Server local/em máquinas virtuais, a auditoria funciona no nível do servidor, mas armazena os eventos em logs de eventos do Windows/do sistema de arquivos.
  
A auditoria XEvent na Instância Gerenciada oferece suporte a destinos de armazenamento de blobs do Azure. Não há suporte para logs de arquivo e do Windows.

As principais diferenças na sintaxe `CREATE AUDIT` para a auditoria do armazenamento de blobs do Azure são:

- Uma nova sintaxe `TO URL` é fornecida e permite que você especifique a URL do contêiner de armazenamento de blobs do Azure onde arquivos `.xel` serão colocados
- Não há suporte para a sintaxe `TO FILE` porque uma Instância Gerenciada não pode acessar compartilhamentos de arquivo do Windows.

Para obter mais informações, consulte:  

- [CRIAR AUDITORIA DE SERVIDOR](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)  
- [ALTERAR AUDITORIA DE SERVIDOR](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Auditoria](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certificados

Uma Instância Gerenciada não pode acessar compartilhamentos de arquivo nem pastas do Windows e, portanto, as seguintes restrições se aplicam:

- O arquivo `CREATE FROM`/`BACKUP TO` não é compatível com certificados
- Não há suporte para o certificado `CREATE`/`BACKUP` do `FILE` / `ASSEMBLY`. Arquivos de chave privada não podem ser usados.  

Consulte [CRIAR CERTIFICADO](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) e [CERTIFICADO DE BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql).  
  
**Solução alternativa**: certificado de script / chave privada, armazene como arquivo .sql e crie a partir do binário:

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Credencial

Somente o Azure Key Vault e identidades `SHARED ACCESS SIGNATURE` têm suporte. Não há suporte para usuários do Windows.

Consulte [CRIAR CREDENCIAL](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) e [ALTERAR CREDENCIAL](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Provedores criptográficos

Uma Instância Gerenciada não pode acessar arquivos e, portanto, os provedores de criptografia não podem ser criados:

- Não há suporte para `CREATE CRYPTOGRAPHIC PROVIDER`. Consulte [CRIAR PROVEDOR CRIPTOGRÁFICO](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- Não há suporte para `ALTER CRYPTOGRAPHIC PROVIDER`. Consulte [ALTERAR PROVEDOR CRIPTOGRÁFICO](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins--users"></a>Logons / usuários

- Logons do SQL criados `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY`, e `FROM SID` têm suporte. Consulte [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql).
- Há suporte para entidades de segurança do servidor do Azure Active Directory (Azure AD) (logons) criadas com a sintaxe [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) ou [CREATE USER FROM LOGIN [Azure AD Login]](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) (**visualização pública**). Esses são logons criados no nível do servidor.
    - A Instância Gerenciada oferece suporte a entidades de segurança do banco de dados do Azure AD com a sintaxe `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`. Isso também é conhecido como usuários de banco de dados contidos do Azure AD.
- Logons do Windows criados com a sintaxe `CREATE LOGIN ... FROM WINDOWS` não têm suporte. Use logons e usuário do Microsoft Azure Active Directory.
- O usuário do Azure AD que criou a instância tem [privilégios irrestritos de administrador](sql-database-manage-logins.md#unrestricted-administrative-accounts).
- Usuários de nível de banco de dados não administradores do Microsoft Azure Active Directory (Azure AD) podem ser criados usando a sintaxe `CREATE USER ... FROM EXTERNAL PROVIDER`. Consulte [CRIAR USUÁRIO ... FROM EXTERNAL PROVIDER](sql-database-manage-logins.md#non-administrator-users).
- Entidades de segurança do servidor do Azure AD (logons) oferecem suporte a recursos do SQL dentro de uma única instância de Instância Gerenciada. Não há suporte para recursos que exigem a interação entre instâncias, dentro do mesmo locatário do Azure AD ou em locatários diferentes, para usuários do Azure AD. Exemplos de recursos desse tipo são:
    - Replicação transacional de SQL e
    - Servidor de Link
- Não há suporte para logons do Azure AD mapeados para um grupo do Azure AD como proprietário do banco de dados.
- Não há suporte para a representação de entidades de segurança no nível do servidor do Azure AD usando outras entidades de segurança do Azure AD, como a cláusula [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql). Limitação de EXECUTE AS:
    - não há suporte para EXECUTE AS USER para usuários do Azure AD quando o nome é diferente do nome de logon. Por exemplo, quando o usuário é criado por meio da sintaxe CREATE USER [myAadUser] FROM LOGIN [john@contoso.com], e a tentativa de representação é por meio de EXEC AS USER = _myAadUser_. Ao criar um **USER** a partir de uma entidade de segurança do servidor (logon) do Azure AD, especifique o user_name como o mesmo login_name de **LOGON**.
    - Somente as entidades de segurança no nível do SQL Server (logons) que fazem parte do `sysadmin` podem executar as seguintes operações direcionadas a entidades de segurança do Azure AD: 
        - EXECUTE AS USER
        - EXECUTE AS LOGIN
- Limitações da **Visualização pública** para entidades de segurança do servidor do Azure AD (logons):
    - Limitações do administrador do Active Directory para Instância Gerenciada:
        - O administrador do Azure AD usado para configurar a Instância Gerenciada não pode ser usado para criar uma entidade de servidor (logon) do Azure AD dentro da Instância Gerenciada. Você deve criar a primeira entidade de servidor (logon) do Azure AD usando uma conta do SQL Server que é um `sysadmin`. Essa é uma limitação temporária que será removida depois que as entidades de segurança do servidor (logons) do Azure AD se tornarem GA. Se você tentar usar uma conta de administrador do Azure AD para criar o logon, verá o seguinte erro: `Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`
        - Atualmente, o primeiro logon do Azure AD criado no BD mestre deve ser criado pela conta padrão do SQL Server (não do Azure AD) que é um `sysadmin` usando [ CREATE LOGIN ](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) FROM EXTERNAL PROVIDER. Após o GA, essa limitação será removida e um logon inicial do Azure AD poderá ser criado pelo administrador do Active Directory para a Instância Gerenciada.
    - Não há suporte para DacFx (exportar/importar) usado com o SQL Server Management Studio (SSMS) ou SqlPackage para logons do Azure AD. Essa limitação será removida depois que as entidades de segurança do servidor (logons) do Azure AD se tornarem GA.
    - Usar entidades de segurança do servidor do Azure AD (logons) com SSMS
        - Não há suporte para script de logons do Azure AD (usando qualquer logon autenticado).
        - O IntelliSense não reconhece a instrução **CREATE LOGIN FROM EXTERNAL PROVIDER** e mostra um sublinhado vermelho.
- Somente o logon principal no nível do servidor (criado pelo processo de provisionamento de Instância Gerenciada), membros das funções de servidor (`securityadmin` ou `sysadmin`) ou outros logons com permissão ALTER ANY LOGIN no nível do servidor podem criar entidades do servidor do Azure AD (logons) no banco de dados mestre da Instância Gerenciada.
- Se o logon for uma Entidade de Segurança SQL, somente os logons que fizerem parte da função `sysadmin` poderão usar o comando create para criar logons para uma conta do Azure AD.
- O logon do Azure AD deve ser um membro do Azure AD dentro do mesmo diretório usado para a Instância Gerenciada do SQL do Azure.
- Entidades de segurança do servidor do Azure AD (logons) são visíveis no Pesquisador de Objetos a partir do SSMS 18.0 versão prévia 5.
- É permitida a sobreposição de entidades de segurança do servidor (logons) do Azure AD com uma conta de administrador do Azure AD. As entidades de segurança do servidor do Azure AD (logons) têm precedência sobre o administrador do Azure AD ao resolver a entidade de segurança e aplicar permissões à Instância Gerenciada.
- Durante a autenticação, a sequência a seguir é aplicada para resolver a entidade de segurança de autenticação:
    1. Se a conta do Azure AD existir como mapeada diretamente para a entidade de segurança do servidor do Azure AD (logon) (presente em sys.server_principals como tipo "E"), conceda acesso e aplique permissões da entidade de segurança do servidor do Azure AD (logon).
    2. Se a conta do Azure AD for um membro de um grupo do Azure AD mapeado para a entidade de segurança do servidor do Azure AD (logon) (presente em sys.server_principals como tipo "E"), conceda permissões de acesso e aplicação do logon do grupo do Azure AD.
    3. Se a conta do Azure AD for uma administrador especial do Azure AD configurado para o portal para Instância Gerenciada (não existe nas exibições do sistema da Instância Gerenciada), aplique permissões fixas especiais do administrador do Azure AD para a Instância Gerenciada (modo herdado).
    4. Se a conta do Azure AD existir como mapeada diretamente para um usuário do Azure AD em um banco de dados (em sys.database_principals como tipo "E"), conceda permissões de acesso e aplicação do usuário do banco de dados do Azure AD.
    5. Se a conta do Azure AD for membro de um grupo do Azure AD mapeado para um usuário do Azure AD em um banco de dados (em sys.database_principals como tipo "X"), conceda permissões de acesso e aplicação do logon do grupo do Azure AD.
    6. Se houver um logon do Azure AD mapeado para uma conta de usuário do Azure AD ou uma conta do grupo do Azure AD, resolvendo a autenticação do usuário, todas as permissões desse logon do Azure AD serão aplicadas.






### <a name="service-key-and-service-master-key"></a>Chave de serviço e chave mestra de serviço

- [Backup da chave mestra](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql) não tem suporte (gerenciado pelo serviço de banco de dados SQL)
- [Restauração da chave mestra](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql) não tem suporte (gerenciado pelo serviço de banco de dados SQL)
- [Backup da chave mestra de serviço](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql) não tem suporte (gerenciado pelo serviço de banco de dados SQL)
- [Restauração da chave mestra de serviço](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql) não tem suporte (gerenciado pelo serviço de banco de dados SQL)

## <a name="configuration"></a>Configuração

### <a name="buffer-pool-extension"></a>Extensão do pool de buffers

- A [extensão de pool de buffers](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) não é compatível.
- Não há suporte para `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION`. Consulte [ALTERAR CONFIGURAÇÃO DO SERVIDOR](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Ordenação

O agrupamento de instância padrão é `SQL_Latin1_General_CP1_CI_AS` e pode ser especificado como um parâmetro de criação. Consulte [Ordenações](https://docs.microsoft.com/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Níveis de compatibilidade

- Os níveis de compatibilidade com suporte são: 100, 110, 120, 130, 140  
- Não há suporte para níveis de compatibilidade abaixo de 100.
- O nível de compatibilidade padrão para novos bancos de dados é 140. Para bancos de dados restaurados, o nível de compatibilidade permanecerá inalterado se ele for 100 ou superior.

Consulte [ALTERAR Nível de Compatibilidade do BANCO DE DADOS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Espelhamento de banco de dados

Não há suporte para espelhamento de banco de dados.

- As opções `ALTER DATABASE SET PARTNER` e `SET WITNESS` não são suportadas.
- Não há suporte para `CREATE ENDPOINT … FOR DATABASE_MIRRORING`.

Para obter mais informações, consulte [ALTER DATABASE SET PARTNER e SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) e [CREATE ENDPOINT... FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Opções de banco de dados

- Não há suporte para vários arquivos de log.
- Não há suporte para objetos na memória na camada de serviço de Uso Geral.  
- Há um limite de 280 arquivos por instância implicando no máximo 280 arquivos por banco de dados. Os arquivos de log e de dados são contados para esse limite.  
- O banco de dados não pode conter grupos de arquivos que contenham dados de fluxo de arquivos.  A restauração falhará se .bak contiver dados `FILESTREAM`.  
- Cada arquivo é colocado no Armazenamento de Blobs do Azure. A E/S e a taxa de transferência por arquivo dependem do tamanho de cada arquivo individual.  

#### <a name="create-database-statement"></a>Instrução CRIAR BANCO DE DADOS

As limitações `CREATE DATABASE` são apresentadas abaixo:

- Arquivos e grupos de arquivos não podem ser definidos.  
- Não há suporte para a opção `CONTAINMENT`.  
- Não há suporte para as opções `WITH`.  
   > [!TIP]
   > Como alternativa, use `ALTER DATABASE` depois `CREATE DATABASE` para definir opções de banco de dados para adicionar arquivos ou para definir a contenção.  

- Não há suporte para a opção `FOR ATTACH`
- Não há suporte para a opção `AS SNAPSHOT OF`

Para saber mais, confira [CRIAR BANCO DE DADOS](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>Instrução ALTERAR BANCO DE DADOS

Algumas propriedades de arquivo não podem ser definidas ou alteradas:

- O caminho do arquivo não pode ser especificado na instrução `ALTER DATABASE ADD FILE (FILENAME='path')` T-SQL. Remova `FILENAME` do script porque uma Instância Gerenciada coloca os arquivos automaticamente.  
- O nome do arquivo não pode ser alterado usando a instrução `ALTER DATABASE`.

As opções a seguir são definidas por padrão e não podem ser alteradas:

- `MULTI_USER`
- `ENABLE_BROKER ON`
- `AUTO_CLOSE OFF`

As seguintes opções não podem ser modificadas:

- `AUTO_CLOSE`
- `AUTOMATIC_TUNING(CREATE_INDEX=ON|OFF)`
- `AUTOMATIC_TUNING(DROP_INDEX=ON|OFF)`
- `DISABLE_BROKER`
- `EMERGENCY`
- `ENABLE_BROKER`
- `FILESTREAM`
- `HADR`
- `NEW_BROKER`
- `OFFLINE`
- `PAGE_VERIFY`
- `PARTNER`
- `READ_ONLY`
- `RECOVERY BULK_LOGGED`
- `RECOVERY_SIMPLE`
- `REMOTE_DATA_ARCHIVE`  
- `RESTRICTED_USER`
- `SINGLE_USER`
- `WITNESS`

Não há suporte para modificação de nome.

Para saber mais, confira [ALTERAR BANCO DE DADOS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="sql-server-agent"></a>SQL Server Agent

- As configurações do agente SQL são somente leitura. Não há suporte para o procedimento `sp_set_agent_properties` na Instância Gerenciada.  
- Trabalhos
  - As etapas de trabalho T-SQL têm suporte.
  - Os trabalhos de replicação a seguir têm suporte:
    - Leitor do log de transações
    - Instantâneo
    - Distribuidor
  - As etapas de trabalho SSIS têm suporte
  - Não há suporte para outros tipos de etapas de trabalho no momento, incluindo:
    - Não há suporte para a etapa de trabalho de replicação de mesclagem.  
    - Não há suporte para leitor de fila.  
    - Ainda não há suporte para o shell de comando.
  - As instâncias gerenciadas não podem acessar recursos externos (por exemplo, compartilhamentos de rede por meio do Robocopy).  
  - Ainda não há suporte para o PowerShell.
  - Não há suporte para o Analysis Services.
- Há suporte parcial para notificações.
- Há suporte para notificação de email, é necessário configurar um perfil do Database Mail. Só pode haver um perfil do Database Mail e ele deve ser chamado de `AzureManagedInstance_dbmail_profile` na versão prévia pública (limitação temporária).  
  - Não há suporte para o Pager.  
  - Não há suporte para o NetSend.
  - Ainda não há suporte para alertas.
  - Não há suporte para proxies.  
- Não há suporte para o Eventlog.

Atualmente, os recursos a seguir não têm suporte, mas serão habilitados no futuro:

- Proxies
- Agendamento de trabalhos em CPU ociosa
- Habilitar/desabilitar o agente
- Alertas

Para obter informações sobre o SQL Server Agent, consulte [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Tabelas

Não há suporte para o seguinte:

- `FILESTREAM`
- `FILETABLE`
- `EXTERNAL TABLE`
- `MEMORY_OPTIMIZED`  

Para obter informações sobre a criação e alteração de tabelas, consulte [CRIAR TABELA](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) e [ALTERAR TABELA](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Funcionalidades

### <a name="bulk-insert--openrowset"></a>Inserção em massa / openrowset

Uma Instância Gerenciada não pode acessar compartilhamentos de arquivo e pastas do Windows e, portanto, os arquivos precisam ser importados do Armazenamento de Blobs do Azure:

- `DATASOURCE` é necessário no comando `BULK INSERT` durante a importação de arquivos do armazenamento de blobs do Azure. Consulte [INSERÇÃO EM MASSA](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` é necessário na função `OPENROWSET` quando você ler um conteúdo de um arquivo do armazenamento de blobs do Azure. Consulte [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).

### <a name="clr"></a>CLR

Uma Instância Gerenciada não pode acessar compartilhamentos de arquivo nem pastas do Windows e, portanto, as seguintes restrições se aplicam:

- Apenas `CREATE ASSEMBLY FROM BINARY` tem suporte. Consulte [CRIAR ASSEMBLY A PARTIR DE BINÁRIO](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).  
- Não há suporte para `CREATE ASSEMBLY FROM FILE`. Consulte [CRIAR ASSEMBLY A PARTIR DE ARQUIVO](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` não pode referenciar arquivos. Consulte [ALTERAR ASSEMBLY](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql).


### <a name="dbcc"></a>DBCC

Não há suporte para instruções DBCC não documentadas que estão habilitadas no SQL Server em Instâncias Gerenciadas.

- Não há suporte para `Trace Flags`. Consulte [Sinalizadores de rastreamento](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- Não há suporte para `DBCC TRACEOFF`. Consulte [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql).
- Não há suporte para `DBCC TRACEON`. Consulte [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql).

### <a name="distributed-transactions"></a>Transações distribuídas

Atualmente, não há suporte para o MSDTC e [Transações Elásticas](sql-database-elastic-transactions-overview.md) em Instâncias Gerenciadas.

### <a name="extended-events"></a>Eventos Estendidos

Não há suporte para alguns destinos específicos do Windows para XEvents:

- Não há suporte para `etw_classic_sync target`. Armazene arquivos `.xel` no armazenamento de blobs do Azure. Consulte [etw_classic_sync target](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- Não há suporte para `event_file target`. Armazene arquivos `.xel` no armazenamento de blobs do Azure. Consulte [event_file target](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Bibliotecas externas

Bibliotecas externas R e Python no banco de dados ainda não têm suporte. Consulte [Serviços de Machine Learning do SQL Server](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>FileStream e Filetable

- Não há suporte para dados de fluxo de arquivos.
- O banco de dados não pode conter grupos de arquivos com dados `FILESTREAM`.
- Não há suporte para `FILETABLE`.
- As tabelas não podem ter tipos `FILESTREAM`.
- Não há suporte para as seguintes funções:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

Para obter mais informações, consulte [FILESTREAM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) e [FileTables](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Pesquisa semântica de texto completo

Não há suporte para [Pesquisa semântica](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server).

### <a name="linked-servers"></a>Servidores vinculados

Os servidores vinculados em Instâncias Gerenciadas oferecem suporte a um número limitado de destinos:

- Destinos com suporte: SQL Server e Banco de Dados SQL
- Destinos sem suporte: arquivos, Analysis Services e outros RDBMS.

Operações

- Não há suporte para transações de gravação entre instâncias.
- `sp_dropserver` é compatível com o descarte um servidor vinculado. Consulte [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- A função `OPENROWSET` pode ser usada para executar consultas apenas em instâncias do SQL Server (gerenciado, local ou em Máquinas Virtuais). Consulte [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
- A função `OPENDATASOURCE` pode ser usada para executar consultas apenas em instâncias do SQL Server (gerenciado, local ou em máquinas virtuais). Somente os valores `SQLNCLI`, `SQLNCLI11` e `SQLOLEDB` têm suporte como provedor. Por exemplo: `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Consulte [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql).

### <a name="polybase"></a>Polybase

Não há suporte para tabelas externas que fazem referência aos arquivos no Armazenamento de Blobs do Azure ou HDFS. Para obter informações sobre o Polybase, consulte [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replicação

A replicação está disponível para a visualização pública em Instâncias Gerenciadas. Para saber mais sobre a Replicação, consulte [Replicação do SQL Server](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance).

### <a name="restore-statement"></a>Instrução RESTAURAR

- Sintaxe compatível
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Sintaxe incompatível
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Fonte  
  - `FROM URL` (Armazenamento de Blobs do Azure) é a única opção compatível.
  - Não há suporte para `FROM DISK`/`TAPE`/dispositivo de backup.
  - Conjuntos de backup não são compatíveis.
- Não há suporte para as opções `WITH` (No `DIFFERENTIAL`, `STATS` etc.)
- `ASYNC RESTORE` - Restaurar continuará mesmo se houver quedas de conexão do cliente. Se a conexão for interrompida, você pode verificar a exibição `sys.dm_operation_status` do status de uma operação de restauração (bem como para CRIAR e DESCARTAR o banco de dados). Consulte [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database).  

As seguintes opções de banco de dados são definidas/substituídas e não podem ser alteradas posteriormente:  

- `NEW_BROKER` (se o agente não estiver habilitado no arquivo .bak)  
- `ENABLE_BROKER` (se o agente não estiver habilitado no arquivo .bak)  
- `AUTO_CLOSE=OFF` (se um banco de dados no arquivo .bak tiver `AUTO_CLOSE=ON`)  
- `RECOVERY FULL` (se um banco de dados no arquivo .bak tiver `SIMPLE` ou modo de recuperação `BULK_LOGGED`)
- O grupo de arquivos otimizados por memória é adicionado e chamado de XTP caso não estivesse no arquivo .bak de origem  
- Qualquer grupo de arquivos otimizado por memória já existente é renomeado para XTP  
- As opções `SINGLE_USER` e `RESTRICTED_USER` são convertidas em `MULTI_USER`

 Limitações:  

- Os arquivos `.BAK` contendo vários conjuntos de backup não podem ser restaurados.
- Os arquivos `.BAK` contendo vários arquivos de log não podem ser restaurados.
- A restauração falhará se .bak contiver dados `FILESTREAM`.
- Backups que contêm bancos de dados com objetos na memória ativos não podem ser restaurados no momento.  
- Backups que contêm bancos de dados nos quais em algum momento existiram objetos na memória não podem ser restaurados no momento.
- Backups que contêm bancos de dados em modo somente leitura não podem ser restaurados no momento. Essa limitação será removida em breve.

Para obter informações sobre instruções de restauração, consulte [instruções RESTAURAR](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql).

### <a name="service-broker"></a>Service broker

Não há suporte para agente de serviços entre instâncias:

- `sys.routes` - Pré-requisito: selecione o endereço em sys.routes. O endereço deve ser LOCAL em toda rota. Consulte [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: não é possível usar `CREATE ROUTE` com `ADDRESS` além de `LOCAL`. Consulte [CRIAR ROTA](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: não é possível usar `ALTER ROUTE` com `ADDRESS` além de `LOCAL`. Consulte [ALTERAR ROTA](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql).  

### <a name="stored-procedures-functions-triggers"></a>Procedimentos, funções, gatilhos armazenados

- Atualmente, não há suporte para `NATIVE_COMPILATION`.
- Não há suporte para as seguintes opções [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql):
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- Não há suporte para `sp_execute_external_scripts`. Consulte [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- Não há suporte para `xp_cmdshell`. Consulte [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- Não há suporte para `Extended stored procedures`, incluindo `sp_addextendedproc` e `sp_dropextendedproc`. Consulte [Procedimentos armazenados estendidos](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql)
- Não há suporte para `sp_attach_db`, `sp_attach_single_file_db` e `sp_detach_db`. Consulte [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) e [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).
- Não há suporte para `sp_renamedb`. Consulte [sp_renamedb](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-renamedb-transact-sql).

## <a name="Changes"></a> Alterações de comportamento

As seguintes variáveis, funções e exibições retornam resultados diferentes:

- `SERVERPROPERTY('EngineEdition')` retorna um valor 8. Essa propriedade identifica exclusivamente uma Instância Gerenciada. Consulte [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` retorna NULL, porque o conceito de instância existente para o SQL Server não se aplica a uma Instância Gerenciada. Consulte [SERVERPROPERTY('InstanceName')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` returna o nome completo 'conectável' do DNS, por exemplo, my-managed-instance.wcus17662feb9ce98.database.windows.net. Consulte [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql).  
- `SYS.SERVERS` - retorna o nome completo 'conectável' do DNS, como `myinstance.domain.database.windows.net`, para propriedades 'name' e 'data_source'. Consulte [SYS.SERVERS](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` retorna NULL, porque o conceito de serviço existente para o SQL Server não se aplica a uma Instância Gerenciada. Consulte [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql).
- Há suporte para `SUSER_ID`. Retornará NULL se o logon do Azure AD não estiver em sys.syslogins. Consulte [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql).  
- Não há suporte para `SUSER_SID`. Retorna dados incorretos (problema temporário conhecido). Consulte [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql).
- `GETDATE()` e outras funções internas de data/hora sempre retornam a hora no fuso horário UTC. Consulte [GETDATE](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql).

## <a name="Issues"></a> Problemas e limitações conhecidos

### <a name="tempdb-size"></a>Tamanho de TEMPDB

`tempdb` é dividido em 12 arquivos de no máximo 14 GB de tamanho por arquivo. Esse tamanho máximo por arquivo não pode ser alterado e não possível adicionar novos arquivos a `tempdb`. Essa limitação será removida em breve. Algumas consultas podem retornar um erro se eles precisarem de mais de 168 GB em `tempdb`.

### <a name="exceeding-storage-space-with-small-database-files"></a>Excedendo o espaço de armazenamento com arquivos de banco de dados pequenos

Cada Instância Gerenciada tem até 35 TB de armazenamento reservado para o espaço em Disco Premium do Azure e cada arquivo de banco de dados é colocado em um disco físico separado. Tamanhos de disco podem ser 128 GB, 256 GB, 512 GB, 1 TB ou 4 TB. O espaço não utilizado no disco não é cobrado, mas a soma total dos tamanhos de Disco Premium do Azure não pode exceder 35 TB. Em alguns casos, uma Instância Gerenciada que não precise de 8 TB no total pode exceder o limite de 35 TB do Azure em tamanho de armazenamento, devido à fragmentação interna.

Por exemplo, uma Instância Gerenciada pode ter um arquivo de 1,2 TB de tamanho colocado em um disco de 4 TB e 248 arquivos a cada 1 GB de tamanho colocados em discos separados de 128 GB. Neste exemplo:

- O tamanho do armazenamento em disco total alocado é de 1 x 4 TB + 248 x 128 GB = 35 TB.
- O total de espaço reservado para os bancos de dados na instância é de 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

Isso ilustra que, em determinadas circunstâncias, devido a uma distribuição específica de arquivos, uma Instância Gerenciada pode alcançar os 35TB reservados para o Disco Premium do Azure onde você não esperaria.

Neste exemplo bancos de dados existentes continuarão a funcionar e pode crescer sem problemas, desde que não sejam adicionados novos arquivos. No entanto os novos bancos de dados não pode ser criados ou restaurados porque não há espaço suficiente para novas unidades de disco, mesmo se o tamanho total de todos os bancos de dados não alcançar o limite de tamanho de instância. O erro retornado nesse caso não é claro.

### <a name="incorrect-configuration-of-sas-key-during-database-restore"></a>Configuração incorreta da chave SAS durante a restauração do banco de dados

`RESTORE DATABASE` que lê o arquivo .bak pode estar constantemente tentando ler o arquivo .bak e retornar um erro após um longo período de tempo se a Assinatura de Acesso Compartilhado em `CREDENTIAL` estiver incorreto. Execute RESTAURAR HEADERONLY antes de restaurar um banco de dados para certificar-se de que a chave SAS está correta.
Certifique-se de remover `?` à esquerda da chave de SAS gerada usando o portal do Azure.

### <a name="tooling"></a>Ferramentas

O SSMS (SQL Server Management Studio) e o SSDT (SQL Server Data Tools) podem ter alguns problemas ao acessar uma Instância Gerenciada.

- Atualmente, não há suporte para o uso de entidade de segurança do servidor do Azure AD (logons) e usuários do Azure AD (**visualização pública**) com SSDT.
- Não há suporte para scripts para entidades de segurança do servidor do Azure AD (logons) do Microsoft Azure Active Directory e usuários (**visualização pública**) no SSMS.

### <a name="incorrect-database-names-in-some-views-logs-and-messages"></a>Nomes de banco de dados incorretos em algumas exibições, logs e mensagens

Várias entradas de exibições do sistema, contadores de desempenho, mensagens de erro, XEvents e logs de erros exibem identificadores do banco de dados GUID em vez dos nomes reais do banco de dados. Não dependa desses identificadores GUID porque eles serão substituídos por nomes de banco de dados reais no futuro.

### <a name="database-mail-profile"></a>Perfil do Database Mail

Só pode haver apenas um perfil do Database Mail e ele deve ser chamado de `AzureManagedInstance_dbmail_profile`.

### <a name="error-logs-are-not-persisted"></a>Os logs de erros não são persistentes

Os logs de erros disponíveis na Instância Gerenciada não são persistentes e seu tamanho não está incluído no limite de armazenamento máximo. Os logs de erros podem ser apagados automaticamente no caso de failover.

### <a name="error-logs-are-verbose"></a>Os logs de erro são detalhados

Uma Instância Gerenciada coloca informações detalhadas em logs de erro e muitas delas não são relevantes. A quantidade de informações nos logs de erro será reduzida no futuro.

**Solução alternativa**: Usar um procedimento personalizado para a leitura de logs de erros que filtra algumas entradas não relevantes. Para saber mais, consulte [Instância Gerenciada – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/).

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>O escopo de transação em dois bancos de dados dentro da mesma instância não é compatível

A classe `TransactionScope` no .Net não funciona se duas consultas são enviadas para os dois bancos de dados dentro da mesma instância no mesmo escopo de transação:

```C#
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

Embora esse código funcione com os dados na mesma instância, ele precisou do MSDTC.

**Solução alternativa**: Usar [SqlConnection.ChangeDatabase(String)](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) para usar outro banco de dados no contexto de conexão em vez de usar duas conexões.

### <a name="clr-modules-and-linked-servers-sometime-cant-reference-local-ip-address"></a>Os módulos CLR e os servidores vinculados às vezes não podem fazer referência ao endereço IP local

Os módulos CLR colocados em uma Instância Gerenciada e em servidores vinculados/consultas distribuídas que referenciam a instância atual às vezes não podem resolver o IP da instância local. Esse é um problema temporário.

**Solução alternativa**: Usar conexões de contexto no módulo CLR, se possível.

### <a name="tde-encrypted-databases-dont-support-user-initiated-backups"></a>Os bancos de dados criptografados por TDE não oferecem suporte a backups iniciados pelo usuário

Não é possível executar `BACKUP DATABASE ... WITH COPY_ONLY` em um banco de dados que esteja criptografado com TDE (Transparent Data Encryption). A TDE força os backups a serem criptografados com chaves TDE internas, e a chave não pode ser exportada, de modo que não é possível restaurar o backup.

**Solução alternativa**: Use backups automáticos e a restauração pontual, ou desabilite a criptografia no banco de dados.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre Instâncias Gerenciadas, consulte [O que é uma Instância Gerenciada?](sql-database-managed-instance.md)
- Para obter uma lista de recursos e de comparação, consulte [Recursos comuns do SQL](sql-database-features.md).
- Para obter um início rápido mostrando como criar uma nova Instância Gerenciada, confira [Criando uma Instância Gerenciada](sql-database-managed-instance-get-started.md).
