---
title: Diferenças do banco de dados gerenciado instância T-SQL do Azure | Microsoft Docs
description: Este artigo aborda as diferenças do T-SQL entre uma instância gerenciada no Banco de Dados SQL do Azure e no SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova
manager: craigg
ms.date: 03/13/2019
ms.custom: seoapril2019
ms.openlocfilehash: 08920a25fc7213a773ef0d76a5daddbab3f765c2
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866859"
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Diferenças de T-SQL da Instância Gerenciada do Banco de Dados SQL do Azure em relação ao SQL Server

Este artigo resume e explica as diferenças de sintaxe e o comportamento entre o banco de dados de instância gerenciada do SQL e o mecanismo de banco de dados SQL Server local. Os seguintes assuntos são abordados: <a name="Differences"></a>

- [Disponibilidade](#availability) inclui as diferenças nos [Always-On](#always-on-availability) e [backups](#backup).
- [Segurança](#security) inclui as diferenças nos [auditoria](#auditing), [certificados](#certificates), [credenciais](#credential), [provedores criptográficos](#cryptographic-providers), [logons e usuários](#logins-and-users)e o [chave de serviço e a chave mestra de serviço](#service-key-and-service-master-key).
- [Configuração](#configuration) inclui as diferenças nos [extensão do pool de buffers](#buffer-pool-extension), [agrupamento](#collation), [níveis de compatibilidade](#compatibility-levels), [espelhamento de banco de dados ](#database-mirroring), [opções de banco de dados](#database-options), [SQL Server Agent](#sql-server-agent), e [opções de tabela](#tables).
- [As funcionalidades](#functionalities) inclui [BULK INSERT/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [transações distribuídas](#distributed-transactions), [eventos estendidos](#extended-events), [bibliotecas externas](#external-libraries), [filestream e FileTable](#filestream-and-filetable), [pesquisa de semântica de texto completo](#full-text-semantic-search), [servidores vinculados](#linked-servers), [PolyBase](#polybase), [replicação](#replication), [RESTAURAR](#restore-statement), [Service Broker](#service-broker), [procedimentos armazenados, funções e disparadores](#stored-procedures-functions-and-triggers).
- [Instâncias gerenciadas de recursos que têm um comportamento diferente em](#Changes).
- [Problemas conhecidos e limitações temporárias](#Issues).

A opção de implantação de Instância Gerenciada fornece alta compatibilidade com o Mecanismo de Banco de Dados do SQL Server local. A maioria dos recursos do mecanismo de banco de dados do SQL Server é compatível com uma instância gerenciada.

![Migração](./media/sql-database-managed-instance/migration.png)

## <a name="availability"></a>Disponibilidade

### <a name="always-on-availability"></a>Sempre ativo

[Alta disponibilidade](sql-database-high-availability.md) baseia-se na instância gerenciada e não pode ser controlado por usuários. Não há suporte para as instruções a seguir:

- [CRIAR ENDPOINT … PARA DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
- [CRIAR GRUPO DE DISPONIBILIDADE](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTERAR GRUPO DE DISPONIBILIDADE](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DESCARTAR GRUPO DE DISPONIBILIDADE](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
- O [SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) cláusula das [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql) instrução

### <a name="backup"></a>Backup

Instâncias gerenciadas têm backups automáticos, para que os usuários podem criar banco de dados completo `COPY_ONLY` backups. Diferencial, log e backups de instantâneo de arquivo não são suportados.

- Com uma instância gerenciada, você pode fazer backup de uma instância de banco de dados somente para uma conta de armazenamento de BLOBs do Azure:
  - Apenas `BACKUP TO URL` tem suporte.
  - `FILE`, `TAPE`, e não há suporte para dispositivos de backup.
- A maioria das geral `WITH` opções têm suporte.
  - `COPY_ONLY` é obrigatório.
  - Não há suporte para `FILE_SNAPSHOT`.
  - As opções de fita: `REWIND`, `NOREWIND`, `UNLOAD`, e `NOUNLOAD` não são suportados.
  - Opções específicas de log: `NORECOVERY`, `STANDBY`, e `NO_TRUNCATE` não são suportados.

 Limitações: 

- Com uma instância gerenciada, você pode fazer backup de uma instância de banco de dados para um backup com até 32 faixas, que é o suficiente para bancos de dados de até 4 TB se a compactação de backup for usada.
- O tamanho máximo de backup distribuído usando o `BACKUP` comando em uma instância gerenciada é 195 GB, que é o tamanho máximo do blob. Aumente o número de faixas no comando de backup para reduzir o tamanho de faixas individuais e permanecer dentro desse limite.

    > [!TIP]
    > Para contornar essa limitação, quando você faz backup de um banco de dados do SQL Server em um ambiente local ou em uma máquina virtual, você pode:
    >
    > - Fazer backup em `DISK` em vez de fazer backup para `URL`.
    > - Carregue os arquivos de backup no armazenamento de Blob.
    > - Restaure para a instância gerenciada.
    >
    > O `Restore` comando em uma instância gerenciada oferece suporte a tamanhos maiores de blob nos arquivos de backup como um tipo de blob diferente é usado para o armazenamento de arquivos de backup carregados.

Para obter informações sobre backups usando o T-SQL, consulte [BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Segurança

### <a name="auditing"></a>Auditoria

As principais diferenças entre a auditoria em bancos de dados no Banco de Dados SQL do Azure e em bancos de dados no SQL Server são:

- Com a opção de implantação de instância gerenciada SQL do Azure, a auditoria funciona no nível do servidor. O `.xel` arquivos de log são armazenados no armazenamento de BLOBs do Azure.
- Com as opções de implantação de banco de dados individual e pool elástico no Banco de Dados SQL do Azure, a auditoria funciona no nível do banco de dados.
- No SQL Server local ou em máquinas virtuais, a auditoria funciona no nível do servidor. Eventos são armazenados no sistema de arquivos ou os logs de eventos do Windows.
 
A auditoria XEvent na Instância Gerenciada oferece suporte a destinos de armazenamento de blobs do Azure. Não há suporte para logs de arquivo e do Windows.

As principais diferenças na sintaxe `CREATE AUDIT` para a auditoria do armazenamento de Blobs do Azure são:

- Uma nova sintaxe `TO URL` é desde que você pode usar para especificar a URL do contêiner de armazenamento de BLOBs do Azure onde o `.xel` arquivos são colocados.
- A sintaxe `TO FILE` não tem suporte porque uma instância gerenciada não pode acessar compartilhamentos de arquivos do Windows.

Para obter mais informações, consulte: 

- [CRIAR AUDITORIA DE SERVIDOR](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTERAR AUDITORIA DE SERVIDOR](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Auditoria](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certificados

Uma instância gerenciada não pode acessar compartilhamentos de arquivos e pastas do Windows, portanto, as seguintes restrições se aplicam:

- O `CREATE FROM` / `BACKUP TO` arquivo não tem suporte para certificados.
- O `CREATE` / `BACKUP` de certificado do `FILE` / `ASSEMBLY` não é suportado. Arquivos de chave privada não podem ser usados. 

Consulte [CRIAR CERTIFICADO](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) e [CERTIFICADO DE BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql). 
 
**Solução alternativa**: Script para o certificado ou a chave privada, armazene como arquivo. SQL e criar binários:

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Credencial

Somente o Azure Key Vault e identidades `SHARED ACCESS SIGNATURE` têm suporte. Não há suporte para usuários do Windows.

Consulte [CRIAR CREDENCIAL](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) e [ALTERAR CREDENCIAL](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Provedores criptográficos

Uma instância gerenciada não pode acessar arquivos, portanto, os provedores criptográficos não podem ser criados:

- Não há suporte para `CREATE CRYPTOGRAPHIC PROVIDER`. Consulte [CRIAR PROVEDOR CRIPTOGRÁFICO](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- Não há suporte para `ALTER CRYPTOGRAPHIC PROVIDER`. Consulte [ALTERAR PROVEDOR CRIPTOGRÁFICO](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Logons e usuários

- Logons do SQL criados usando `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY`, e `FROM SID` têm suporte. Consulte [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql).
- Criado com entidades do Azure Active Directory (Azure AD) servidor (logons) a [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) sintaxe ou o [criar usuário de logon [logon do Azure AD]](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) sintaxe têm suporte (visualização pública). Esses logons são criados no nível do servidor.

    A Instância Gerenciada oferece suporte a entidades de segurança do banco de dados do Azure AD com a sintaxe `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`. Esse recurso também é conhecido como usuários de banco de dados independente do AD do Azure.

- Logons do Windows criados com o `CREATE LOGIN ... FROM WINDOWS` sintaxe não têm suporte. Use logons e usuário do Microsoft Azure Active Directory.
- O usuário do AD do Azure que criou a instância tem [privilégios irrestritos de administrador](sql-database-manage-logins.md#unrestricted-administrative-accounts).
- Usuários de nível de banco de dados de não-administradores do Azure AD podem ser criados usando o `CREATE USER ... FROM EXTERNAL PROVIDER` sintaxe. Consulte [CRIAR USUÁRIO ... FROM EXTERNAL PROVIDER](sql-database-manage-logins.md#non-administrator-users).
- Principais de servidor do Azure AD (logons) dão suporte a recursos SQL em somente uma instância gerenciada. Recursos que exigem a interação entre instâncias, não importa se ele estiver dentro do mesmo Azure AD de locatário ou locatários diferentes, não há suporte para usuários do Azure AD. Exemplos de recursos desse tipo são:

  - Replicação transacional do SQL.
  - Servidor de link.

- Não há suporte para logons do Azure AD mapeados para um grupo do Azure AD como proprietário do banco de dados.
- A representação de entidades de nível de servidor do Azure AD com o uso de outras entidades de segurança do AD do Azure tem suporte, como o [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql) cláusula. EXECUTE AS limitações são:

  - EXECUTE AS USER não tem suporte para usuários do AD do Azure, quando o nome é diferente do nome de logon. Um exemplo é quando o usuário é criado por meio da sintaxe de CREATE USER [myAadUser] de logon [john@contoso.com] e a tentativa de representação é por meio de EXEC AS USER = _myAadUser_. Quando você cria um **usuário** de uma entidade de servidor do Azure AD (logon), especifique o user_name como o mesmo login_name de **LOGIN**.
  - Somente as nível do SQL Server entidades de segurança (logons) que fazem parte do `sysadmin` podem executar as seguintes operações que se destinam a entidades de segurança do Azure AD:

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

- Limitações da visualização pública para entidades de segurança do servidor do AD do Azure (logons):

  - Limitações de administrador de diretório Active Directory para a instância gerenciada:

    - O administrador do AD do Azure usado para configurar a instância gerenciada não pode ser usado para criar um servidor do Azure AD principal (logon) dentro da instância gerenciada. Você deve criar o Azure primeiro entidade de segurança do servidor do AD (logon) usando uma conta do SQL Server que um `sysadmin` função. Essa limitação temporária será removida depois que será disponibilizado principais de servidor (logons) do Azure AD. Se você tentar usar uma conta de administrador do AD do Azure para criar o logon, você verá o seguinte erro: `Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`
      - Atualmente, o primeiro logon do AD do Azure criado no banco de dados mestre deve ser criado por conta do SQL Server standard (não do Azure AD) que um `sysadmin` função usando [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) do provedor externo. Após a disponibilidade geral, essa limitação será removida. Em seguida, você pode criar um inicial logon do Azure AD usando o administrador do Active Directory para a instância gerenciada.
    - Não há suporte para DacFx (exportar/importar) usado com o SQL Server Management Studio ou o SqlPackage para logons do AD do Azure. Essa limitação será removida após a entidades de segurança do servidor do AD do Azure (logons) será disponibilizado.
    - Usando entidades de servidor (logons) do Azure AD com o SQL Server Management Studio:

      - Não há suporte a logons do AD do Azure que usam qualquer logon autenticado de script.
      - O IntelliSense não reconhece a instrução criar logon do provedor externo e mostra um sublinhado vermelho.

- Somente o servidor de logon principal no nível, que é criado pela instância gerenciada, como o processo, os membros das funções de servidor de provisionamento `securityadmin` ou `sysadmin`, ou outros logons com permissão ALTER ANY LOGIN no nível do servidor podem criar o Azure AD principais de servidor (logons) no banco de dados mestre da instância gerenciada.
- Se o logon for uma entidade de segurança do SQL, somente os logons que fazem parte de `sysadmin` função pode usar o comando create para criar logons para uma conta do AD do Azure.
- O logon do AD do Azure deve ser um membro de um AD do Azure no mesmo diretório que é usado para o banco de dados de instância gerenciada do SQL.
- Principais de servidor do Azure AD (logons) são visíveis no Pesquisador de objetos a partir do SQL Server Management Studio 18.0 preview 5.
- É permitida a sobreposição de entidades de segurança do servidor (logons) do Azure AD com uma conta de administrador do Azure AD. Principais de servidor do Azure AD (logons) têm precedência sobre o administrador do Azure AD ao resolver a entidade de segurança e aplicar permissões para a instância gerenciada.
- Durante a autenticação, a sequência a seguir é aplicada para resolver a entidade de segurança de autenticação:

    1. Se a conta do AD do Azure existe como diretamente mapeado para a entidade de servidor do Azure AD (logon), que está presente em sys. server_principals como tipo "E", conceder acesso e aplique permissões da entidade de servidor do Azure AD (logon).
    2. Se a conta do AD do Azure é um membro de um grupo do AD do Azure que é mapeado para a entidade de servidor do Azure AD (logon), que está presente em sys. server_principals conforme digita "X", conceder acesso e aplique permissões do logon de grupo do AD do Azure.
    3. Se a conta do AD do Azure é um especial configurada pelo portal de administrador do AD do Azure para instância gerenciada, que não existe nas exibições do sistema de instância gerenciada, se aplicam permissões especiais fixas da administração do AD do Azure para instância gerenciada (modo herdado).
    4. Se a conta do AD do Azure existe como mapeada diretamente para um usuário do Azure AD em um banco de dados, que está presente em sys. database_principals como tipo "E", conceder acesso e aplicar permissões de usuário de banco de dados do Azure AD.
    5. Se a conta do AD do Azure é um membro de um grupo do AD do Azure que é mapeado para um usuário do Azure AD em um banco de dados, que está presente em sys. database_principals como tipo de "X", conceder acesso e aplique permissões do logon de grupo do AD do Azure.
    6. Se houver um logon do AD do Azure mapeado para uma conta de usuário do AD do Azure ou uma conta de grupo do AD do Azure, que resolve para o usuário que está se autenticando, são aplicadas a todas as permissões de que esse logon do Azure AD.

### <a name="service-key-and-service-master-key"></a>Chave de serviço e chave mestra de serviço

- [Backup da chave mestra](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql) não tem suporte (gerenciado pelo serviço de banco de dados SQL).
- [Restauração da chave mestra](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql) não tem suporte (gerenciado pelo serviço de banco de dados SQL).
- [Backup da chave mestra de serviço](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql) não tem suporte (gerenciado pelo serviço de banco de dados SQL).
- [Restauração da chave mestra de serviço](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql) não tem suporte (gerenciado pelo serviço de banco de dados SQL).

## <a name="configuration"></a>Configuração

### <a name="buffer-pool-extension"></a>Extensão do pool de buffers

- [Extensão do pool de buffers](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) não tem suporte.
- Não há suporte para `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION`. Consulte [ALTERAR CONFIGURAÇÃO DO SERVIDOR](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Ordenação

O agrupamento de instância padrão é `SQL_Latin1_General_CP1_CI_AS` e pode ser especificado como um parâmetro de criação. Consulte [Ordenações](https://docs.microsoft.com/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Níveis de compatibilidade

- Níveis de compatibilidade com suporte são 100, 110, 120, 130 e 140.
- Não há suporte para níveis de compatibilidade abaixo de 100.
- O nível de compatibilidade padrão para novos bancos de dados é 140. Para bancos de dados restaurados, o nível de compatibilidade permanecerá inalterado se ele for 100 e superior.

Consulte [ALTERAR Nível de Compatibilidade do BANCO DE DADOS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Espelhamento de banco de dados

Não há suporte para espelhamento de banco de dados.

- As opções `ALTER DATABASE SET PARTNER` e `SET WITNESS` não são suportadas.
- Não há suporte para `CREATE ENDPOINT … FOR DATABASE_MIRRORING`.

Para obter mais informações, consulte [ALTER DATABASE SET PARTNER e SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) e [CREATE ENDPOINT... FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Opções de banco de dados

- Não há suporte para vários arquivos de log.
- Não há suporte para objetos na memória na camada de serviço de Uso Geral. 
- Há um limite de 280 arquivos por instância de finalidade geral, o que implica um máximo de 280 arquivos por banco de dados. Arquivos de log e de dados na camada de finalidade geral são contados para esse limite. [A camada comercialmente crítico dá suporte a 32.767 arquivos por banco de dados](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).
- O banco de dados não pode conter grupos de arquivos que contêm dados filestream. Restauração falhará se. bak contiver `FILESTREAM` dados. 
- Cada arquivo é colocado no Armazenamento de Blobs do Azure. A E/S e a taxa de transferência por arquivo dependem do tamanho de cada arquivo individual.

#### <a name="create-database-statement"></a>Instrução CRIAR BANCO DE DADOS

As seguintes limitações se aplicam a `CREATE DATABASE`:

- Arquivos e grupos de arquivos não podem ser definidos. 
- O `CONTAINMENT` opção não tem suporte. 
- `WITH` Não há suporte para as opções. 
   > [!TIP]
   > Como alternativa, use `ALTER DATABASE` depois de `CREATE DATABASE` para definir as opções para adicionar arquivos ou para definir a contenção do banco de dados. 

- O `FOR ATTACH` opção não tem suporte.
- O `AS SNAPSHOT OF` opção não tem suporte.

Para saber mais, confira [CRIAR BANCO DE DADOS](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>Instrução ALTERAR BANCO DE DADOS

Algumas propriedades de arquivo não podem ser definidas ou alteradas:

- Um caminho de arquivo não pode ser especificado o `ALTER DATABASE ADD FILE (FILENAME='path')` instrução T-SQL. Remova `FILENAME` do script porque uma instância gerenciada coloca os arquivos automaticamente. 
- Um nome de arquivo não pode ser alterado usando o `ALTER DATABASE` instrução.

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

Para saber mais, confira [ALTERAR BANCO DE DADOS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="sql-server-agent"></a>SQL Server Agent

- Configurações do SQL Server Agent são somente leitura. O procedimento `sp_set_agent_properties` não tem suporte na instância gerenciada. 
- Trabalhos
  - As etapas de trabalho T-SQL têm suporte.
  - Os trabalhos de replicação a seguir têm suporte:
    - Leitor do log de transações
    - Instantâneo
    - Distribuidor
  - As etapas de trabalho SSIS têm suporte.
  - Atualmente, não há suporte para outros tipos de etapas de trabalho:
    - Não há suporte para a etapa de trabalho de replicação de mesclagem. 
    - Não há suporte para leitor de fila. 
    - Ainda não há suporte para o shell de comando.
  - Instâncias gerenciadas não podem acessar recursos externos, por exemplo, compartilhamentos de rede via robocopy. 
  - Não há suporte para SQL Server Analysis Services.
- Há suporte parcial para notificações.
- Notificação por email é suportada, embora exija que você configure um perfil do Database Mail. SQL Server Agent pode usar apenas um perfil do Database Mail e ele deve ser chamado `AzureManagedInstance_dbmail_profile`. 
  - Não há suporte para o Pager. 
  - Não há suporte para o NetSend.
  - Ainda não há suporte para alertas.
  - Não há suporte para proxies. 
- Não há suporte para log de eventos.

Os seguintes recursos atualmente não têm suporte, mas serão habilitados no futuro:

- Proxies
- Agendamento de trabalhos em uma CPU ociosa
- Habilitar ou desabilitar um agente
- Alertas

Para obter informações sobre o SQL Server Agent, consulte [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Tabelas

Não há suporte para as tabelas a seguir:

- `FILESTREAM`
- `FILETABLE`
- `EXTERNAL TABLE`
- `MEMORY_OPTIMIZED` 

Para obter informações sobre como criar e alterar as tabelas, consulte [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) e [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Funcionalidades

### <a name="bulk-insert--openrowset"></a>Inserção em massa / openrowset

Uma instância gerenciada não pode acessar compartilhamentos de arquivos e pastas do Windows, então os arquivos devem ser importados do armazenamento de BLOBs do Azure:

- `DATASOURCE` é necessário o `BULK INSERT` comando durante a importação de arquivos do armazenamento de BLOBs do Azure. Consulte [INSERÇÃO EM MASSA](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` é necessário o `OPENROWSET` funcionar quando você ler o conteúdo de um arquivo de armazenamento de BLOBs do Azure. Consulte [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).

### <a name="clr"></a>CLR

Uma instância gerenciada não pode acessar compartilhamentos de arquivos e pastas do Windows, portanto, as seguintes restrições se aplicam:

- Apenas `CREATE ASSEMBLY FROM BINARY` tem suporte. Consulte [CRIAR ASSEMBLY A PARTIR DE BINÁRIO](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql). 
- Não há suporte para `CREATE ASSEMBLY FROM FILE`. Consulte [CRIAR ASSEMBLY A PARTIR DE ARQUIVO](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` não pode referenciar arquivos. Consulte [ALTERAR ASSEMBLY](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="dbcc"></a>DBCC

Instruções DBCC não documentadas habilitadas no SQL Server não têm suporte em instâncias gerenciadas.

- Não há suporte para `Trace flags`. Ver [sinalizadores de rastreamento](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- Não há suporte para `DBCC TRACEOFF`. Consulte [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql).
- Não há suporte para `DBCC TRACEON`. Consulte [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql).

### <a name="distributed-transactions"></a>Transações distribuídas

MSDTC e [transações elásticas](sql-database-elastic-transactions-overview.md) atualmente não têm suporte em instâncias gerenciadas.

### <a name="extended-events"></a>Eventos Estendidos

Não há suporte para alguns destinos específicos do Windows para eventos estendidos (XEvents):

- O `etw_classic_sync` destino não tem suporte. Store `.xel` arquivos no armazenamento de BLOBs do Azure. Consulte [etw_classic_sync target](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- O `event_file` destino não tem suporte. Store `.xel` arquivos no armazenamento de BLOBs do Azure. Consulte [event_file target](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Bibliotecas externas

No banco de dados R e Python, bibliotecas externas não são ainda tem suporte. Consulte [Serviços de Machine Learning do SQL Server](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>FileStream e FileTable

- Não há suporte para dados FILESTREAM.
- O banco de dados não pode conter grupos de arquivos com `FILESTREAM` dados.
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

- Destinos com suporte são o SQL Server e banco de dados SQL.
- Os destinos que não têm suporte são arquivos, Analysis Services e outros RDBMS.

Operações

- Não há suporte para transações de gravação entre instâncias.
- `sp_dropserver` é compatível com o descarte um servidor vinculado. Consulte [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- O `OPENROWSET` função pode ser usada para executar consultas apenas em instâncias do SQL Server. Eles podem ser gerenciado, no local, ou em máquinas virtuais. Consulte [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
- O `OPENDATASOURCE` função pode ser usada para executar consultas apenas em instâncias do SQL Server. Eles podem ser gerenciado, no local, ou em máquinas virtuais. Somente o `SQLNCLI`, `SQLNCLI11`, e `SQLOLEDB` valores têm suporte como um provedor. Um exemplo é `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Consulte [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql).

### <a name="polybase"></a>PolyBase

As tabelas externas que fazem referência que os arquivos no armazenamento de BLOBs do Azure ou HDFS não têm suporte. Para obter informações sobre o PolyBase, consulte [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replicação

A replicação está disponível para a visualização pública em Instâncias Gerenciadas. Para obter informações sobre a replicação, consulte [replicação do SQL Server](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance).

### <a name="restore-statement"></a>Instrução RESTAURAR 

- Sintaxe com suporte:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Não há suporte para sintaxe:
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Origem: 
  - `FROM URL` (Armazenamento de BLOBs do azure) é a única opção com suporte.
  - Não há suporte para `FROM DISK`/`TAPE`/dispositivo de backup.
  - Conjuntos de backup não são compatíveis.
- `WITH` as opções não são suportadas, como nenhum `DIFFERENTIAL` ou `STATS`.
- `ASYNC RESTORE`: Restauração continuará mesmo que interrompe a conexão de cliente. Se sua conexão for interrompida, você pode verificar o `sys.dm_operation_status` exibir o status de uma operação de restauração e um banco de dados CREATE e DROP. Consulte [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

As seguintes opções de banco de dados são definidas ou substituídas e não pode ser alterado posteriormente: 

- `NEW_BROKER` Se o agente não está habilitado no arquivo. bak. 
- `ENABLE_BROKER` Se o agente não está habilitado no arquivo. bak. 
- `AUTO_CLOSE=OFF` Se um banco de dados no arquivo. bak tiver `AUTO_CLOSE=ON`. 
- `RECOVERY FULL` Se tiver um banco de dados no arquivo. bak `SIMPLE` ou `BULK_LOGGED` modo de recuperação.
- Um grupo de arquivos com otimização de memória é adicionado e chamado de XTP caso não estava no arquivo. bak de origem. 
- Qualquer grupo de arquivos existente com otimização de memória é renomeado para XTP. 
- `SINGLE_USER` e `RESTRICTED_USER` as opções são convertidas em `MULTI_USER`.

 Limitações: 

- `.BAK` arquivos que contêm vários conjuntos de backup não podem ser restaurados. 
- `.BAK` arquivos que contêm vários arquivos de log não podem ser restaurados.
- Restauração falhará se. bak contiver `FILESTREAM` dados.
- Os backups que contêm bancos de dados com objetos do Active Directory na memória não podem ser restaurados em uma instância de finalidade geral. Para obter informações sobre instruções de restauração, consulte [instruções RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql).

### <a name="service-broker"></a>Service broker

Não há suporte para agente de serviços entre instâncias:

- `sys.routes`: Como pré-requisito, você deve selecionar o endereço de routes. O endereço deve ser o LOCAL em cada rota. Consulte [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: Não é possível usar `CREATE ROUTE` com `ADDRESS` diferente de `LOCAL`. Consulte [CRIAR ROTA](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: Não é possível usar `ALTER ROUTE` com `ADDRESS` diferente de `LOCAL`. Consulte [ALTERAR ROTA](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql). 

### <a name="stored-procedures-functions-and-triggers"></a>Procedimentos armazenados, gatilhos e funções

- `NATIVE_COMPILATION` não é suportado na camada de uso geral.
- Não há suporte para as seguintes opções [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql): 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- Não há suporte para `sp_execute_external_scripts`. Consulte [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- Não há suporte para `xp_cmdshell`. Consulte [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures` não são compatíveis, que inclui `sp_addextendedproc`  e `sp_dropextendedproc`. Ver [procedimentos armazenados estendidos](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- Não há suporte para `sp_attach_db`, `sp_attach_single_file_db` e `sp_detach_db`. Consulte [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) e [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

## <a name="Changes"></a> Alterações de comportamento

As seguintes variáveis, funções e exibições retornam resultados diferentes:

- `SERVERPROPERTY('EngineEdition')` Retorna o valor 8. Essa propriedade identifica exclusivamente uma instância gerenciada. Consulte [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` retorna NULL porque o conceito de instância como ela existe para o SQL Server não se aplica a uma instância gerenciada. Consulte [SERVERPROPERTY('InstanceName')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` Retorna um nome de "conectável" DNS completo, por exemplo, instance.wcus17662feb9ce98.database.windows.net meu gerenciados. Consulte [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS` Retorna um nome de "conectável" DNS completo, como `myinstance.domain.database.windows.net` para as propriedades "nome" e "data_source." Consulte [SYS.SERVERS](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` retorna NULL porque o conceito de serviço como ela existe para o SQL Server não se aplica a uma instância gerenciada. Consulte [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql).
- Há suporte para `SUSER_ID`. Ele retorna NULL se o logon do AD do Azure não está em sys. syslogins. Consulte [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql). 
- Não há suporte para `SUSER_SID`. Dados incorretos são retornados, que é um problema conhecido de temporário. Consulte [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="Issues"></a> Problemas e limitações conhecidos

### <a name="tempdb-size"></a>Tamanho de TEMPDB

O tamanho máximo do arquivo de `tempdb` não pode ser maior que 24 GB por núcleo de uma camada de uso geral. O máximo `tempdb` tamanho em uma camada comercialmente crítico é limitado com o tamanho do armazenamento de instância. O `tempdb` banco de dados sempre é dividido em arquivos de dados de 12. Esse tamanho máximo por arquivo não pode ser alterado, e novos arquivos podem ser adicionados ao `tempdb`. Algumas consultas podem retornar um erro se eles precisarem de mais de 24 GB por núcleo em `tempdb`.

### <a name="cant-restore-contained-database"></a>Não é possível restaurar o banco de dados independente

Não é possível restaurar a instância gerenciada [bancos de dados independentes](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases). Restauração point-in-time dos bancos de dados independentes existentes não funciona na instância gerenciada. Esse problema será resolvido em breve. Enquanto isso, é recomendável que você remova a opção de contenção de seus bancos de dados que são colocados na instância gerenciada. Não use a opção de contenção para os bancos de dados de produção. 

### <a name="exceeding-storage-space-with-small-database-files"></a>Excedendo o espaço de armazenamento com arquivos de banco de dados pequenos

`CREATE DATABASE`, `ALTER DATABASE ADD FILE`, e `RESTORE DATABASE` instruções podem falhar porque a instância pode atingir o limite de armazenamento do Azure.

Cada instância de finalidade geral gerenciada tem até 35 TB de armazenamento reservado para o espaço em disco Premium do Azure. Cada arquivo de banco de dados é colocado em um disco físico separado. Tamanhos de disco podem ser 128 GB, 256 GB, 512 GB, 1 TB ou 4 TB. Espaço não utilizado no disco não é cobrado, mas a soma total dos tamanhos de disco Premium do Azure não pode exceder 35 TB. Em alguns casos, uma instância gerenciada que não precise de 8 TB no total pode exceder o limite de 35 TB do Azure em tamanho de armazenamento devido à fragmentação interna.

Por exemplo, uma instância gerenciada de finalidade geral pode ter um arquivo que é 1,2 TB de tamanho colocado em um disco de 4 TB. Ele também pode ter 248 arquivos que são a cada 1 GB de tamanho que são colocados em discos separados de 128 GB. Neste exemplo:

- O tamanho do armazenamento em disco total alocado é de 1 x 4 TB + 248 x 128 GB = 35 TB.
- O total de espaço reservado para os bancos de dados na instância é de 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

Este exemplo ilustra que, em determinadas circunstâncias, devido a uma distribuição específica de arquivos, uma instância gerenciada pode alcançar o limite de 35 TB é reservado para um disco anexado de Premium do Azure quando você não esperado.

Neste exemplo, bancos de dados existentes continuam a funcionar e pode crescer sem problemas, desde novos arquivos não são adicionados. Novos bancos de dados não podem ser criados ou restaurados porque não há espaço suficiente para novas unidades de disco, mesmo se o tamanho total de todos os bancos de dados não atinge o limite de tamanho de instância. O erro retornado nesse caso, não está claro.

Você pode [identifique o número de arquivos restantes](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) usando exibições do sistema. Se você atingir esse limite, tente [vazios e excluir alguns arquivos menores, usando a instrução DBCC SHRINKFILE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) ou alternar para o [camada comercialmente crítico, que não tem esse limite](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="incorrect-configuration-of-the-sas-key-during-database-restore"></a>Restaurar a configuração incorreta da chave SAS durante o banco de dados

`RESTORE DATABASE` que lê o arquivo. bak pode estar constantemente tentando para ler o. bak de arquivo e retornar um erro após um longo período de tempo se a assinatura de acesso compartilhado no `CREDENTIAL` está incorreto. Execute RESTAURAR HEADERONLY antes de restaurar um banco de dados para certificar-se de que a chave SAS está correta.
Certifique-se de que você remova o entrelinhamento `?` da chave de SAS que é gerada usando o portal do Azure.

### <a name="tooling"></a>Ferramentas

SQL Server Management Studio e o SQL Server Data Tools podem ter alguns problemas enquanto eles acessarem uma instância gerenciada.

- Usando entidades de segurança do servidor do AD do Azure (logons) e usuários (visualização pública) com SQL Server Data Tools no momento não tem suporte.
- Script para entidades de segurança do servidor do AD do Azure (logons) e usuários (visualização pública) não tem suporte no SQL Server Management Studio.

### <a name="incorrect-database-names-in-some-views-logs-and-messages"></a>Nomes de banco de dados incorretos em algumas exibições, logs e mensagens

Várias entradas de exibições do sistema, contadores de desempenho, mensagens de erro, XEvents e logs de erros exibem identificadores do banco de dados GUID em vez dos nomes reais do banco de dados. Não confie nesses identificadores GUID porque eles são substituídos com nomes de banco de dados real no futuro.

### <a name="database-mail"></a>Database Mail

O `@query` parâmetro na [sp_send_db_mail](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) procedimento não funciona.

### <a name="database-mail-profile"></a>Perfil do Database Mail

O perfil de email de banco de dados usado pelo SQL Server Agent deve ser chamado `AzureManagedInstance_dbmail_profile`. Não há nenhuma restrição para outros nomes de perfil do Database Mail.

### <a name="error-logs-arent-persisted"></a>Logs de erros não são persistidos

Logs de erros que estão disponíveis na instância gerenciada não são persistentes, e seu tamanho não está incluído no limite máximo de armazenamento. Logs de erros podem ser apagados automaticamente se ocorrer failover.

### <a name="error-logs-are-verbose"></a>Os logs de erro são detalhados

Uma instância gerenciada coloca informações detalhadas nos logs de erro, e muitas delas não é relevante. A quantidade de informações nos logs de erro terão uma redução no futuro.

**Solução alternativa:** Use um procedimento personalizado para ler os logs de erros que filtra algumas entradas irrelevantes. Para obter mais informações, consulte [instância gerenciada – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/).

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Não há suporte para o escopo de transação em dois bancos de dados na mesma instância do

O `TransactionScope` classe no .NET não funcionará se duas consultas são enviadas para dois bancos de dados dentro da mesma instância sob o mesmo escopo de transação:

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

Embora esse código funcione com dados na mesma instância, ele necessário MSDTC.

**Solução alternativa:** Use [SqlConnection.ChangeDatabase(String)](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) usar outro banco de dados em um contexto de conexão em vez de usar duas conexões.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>Módulos CLR e, às vezes, os servidores vinculados não podem fazer referência a um endereço IP local

Módulos CLR colocados em uma instância gerenciada e servidores vinculados ou consultas distribuídas que fazem referência a uma instância atual, às vezes, não é possível resolver o IP de uma instância local. Esse é um problema temporário.

**Solução alternativa:** Se possível, use conexões de contexto em um módulo CLR.

### <a name="tde-encrypted-databases-with-a-service-managed-key-dont-support-user-initiated-backups"></a>Bancos de dados criptografado com TDE com uma chave de serviço gerenciado não dão suporte a backups iniciados pelo usuário

Você não é possível executar `BACKUP DATABASE ... WITH COPY_ONLY` em um banco de dados é criptografado com serviços gerenciados criptografia TDE (Transparent Data). TDE gerenciada por serviço força backups a serem criptografados com uma chave interna de TDE. A chave não pode ser exportada, portanto, é possível restaurar o backup.

**Solução alternativa:** Use backups automáticos e restauração point-in-time ou use [gerenciadas pelo cliente (BYOK) TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#customer-managed-transparent-data-encryption---bring-your-own-key) em vez disso. Você também pode desabilitar a criptografia no banco de dados.

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre instâncias gerenciadas, consulte [o que é uma instância gerenciada?](sql-database-managed-instance.md)
- Para uma de recursos e a lista de comparação, consulte [comparação de recursos do banco de dados SQL](sql-database-features.md).
- Para um início rápido que mostra como criar uma nova instância gerenciada, consulte [criar uma instância gerenciada](sql-database-managed-instance-get-started.md).
