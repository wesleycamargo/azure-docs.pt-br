<properties
   pageTitle="Diretrizes e limitações do Banco de Dados SQL do Azure | Microsoft Azure"
   description="Saiba mais sobre as Limitações e diretrizes de Banco de dados SQL do Microsoft Azure relacionadas à segurança."
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jeffreyg"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="11/24/2015"
   ms.author="rickbyh"/>

# Limitações e diretrizes de segurança de Banco de Dados SQL do Azure

Este tópico descreve as limitações e diretrizes de Banco de dados SQL do Microsoft Azure relacionadas à segurança. Considere os seguintes pontos ao gerenciar a segurança de seus Bancos de dados SQL do Azure.

## Firewall

O serviço de Banco de dados SQL do Azure só está disponível por meio da porta TCP 1433. Para acessar um Banco de dados SQL do seu computador, certifique-se de que o firewall permita a comunicação TCP de saída na porta TCP 1433. Como parte do processo de conexão, as conexões de máquinas virtuais do Azure são redirecionadas para um endereço IP e uma porta diferentes, exclusivos para cada função de trabalho. O número da porta estará na faixa de 11000 a 11999.

Antes de conectar-se, pela primeira vez, ao servidor de banco de dados SQL do Azure, você deve usar o [Portal do Azure](https://portal.azure.com) ou [Portal de gerenciamento de plataforma do Azure](https://manage.windowsazure.com/microsoft.onmicrosoft.com#Workspaces/All/dashboard) para configurar o firewall do banco de dados SQL do Azure. Será necessário criar uma configuração de firewall no nível de servidor que habilite as tentativas de conexão do seu computador ou do Azure ao servidor de Banco de dados SQL do Azure. Além disso, se você quiser controlar o acesso a determinados bancos de dados em seu servidor de banco de dados SQL, crie regras de firewall no nível de banco de dados para os respectivos bancos de dados. Para obter mais informações, consulte [Firewall do Banco de Dados SQL do Azure](sql-database-firewall-configure.md).

## Criptografia da conexão e validação de certificado

Todas as comunicações entre o Banco de dados SQL e o seu aplicativo exigem criptografia (SSL) em todos os momentos. Se seu aplicativo cliente não valida os certificados durante a conexão, a conexão com o banco de dados SQL é suscetível a ataques de "man in the middle".

Para validar certificados com o código do aplicativo ou as ferramentas, solicite explicitamente uma conexão criptografada e não confie nos certificados do servidor. Se seu código ou as ferramentas do aplicativo não solicitam uma conexão criptografada, elas ainda receberão conexões criptografadas. No entanto, eles não podem validar certificados de servidor e, portanto, ficarão suscetíveis a ataques de "man in the middle".

Para validar certificados com o código de aplicativo ADO.NET, defina ``Encrypt=True`` e ``TrustServerCertificate=False`` na cadeia de conexão de banco de dados. Para obter mais informações, consulte o [Exemplo de código: repetir a lógica para se conectar ao Banco de Dados SQL do Azure com o ADO.NET](https://msdn.microsoft.com/library/azure/ee336243.aspx).

O SQL Server Management Studio também oferece suporta à validação do certificado. Na caixa de diálogo **Conectar ao servidor**, clique em **Criptografar conexão** na guia **Propriedades de conexão**.

> [AZURE.NOTE]O SQL Server Management Studio não oferece suporte a conexões de banco de dados SQL em versões anteriores ao SQL Server 2008 R2.

Embora o SQLCMD ofereceu suporte ao Banco de dados SQL começando com o SQL Server 2008, ele não oferece suporte à validação de certificado em versões anteriores ao SQL Server 2008 R2. Para validar certificados com o SQLCMD a partir do SQL Server 2008 R2, use a opção ``-N`` da linha de comando e não use a opção ``-C``. Ao utilizar a opção -N, o SQLCMD solicita uma conexão criptografada. Ao não utilizar a opção ``-C``, o SQLCMD não confia implicitamente o certificado do servidor e é forçado a validar o certificado.

Para obter mais informações técnicas, consulte [Segurança de conexão de banco de dados SQL do Azure](http://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#comment-4847) artigo no site do TechNet Wiki.

## Autenticação

A autenticação do Active Directory (segurança integrada) está disponível como uma visualização no Banco de Dados SQL V12. Para obter mais informações sobre como configurar a autenticação do AD, veja [Conectar-se ao Banco de Dados SQL usando a autenticação do Active Directory do Azure](sql-database-aad-authentication.md). Quando não estiverem usando a visualização, os usuários devem fornecer credenciais (logon e senha) sempre que se conectarem ao Banco de Dados SQL. Para obter mais informações sobre a Autenticação do SQL Server, veja [Escolhendo um modo de autenticação](https://msdn.microsoft.com/library/ms144284.aspx) nos Manuais Online do SQL Server.

O [Banco de dados SQL V12](sql-database-v12-whats-new.md) permite que os usuários façam a autenticação no banco de dados usando os usuários do banco de dados independente. Para obter mais informações, veja [Usuários do banco de dados independente – Tornando portátil o banco de dados](https://msdn.microsoft.com/library/ff929188.aspx), [CREATE USER (Transact-SQL)](https://technet.microsoft.com/library/ms173463.aspx) e [Bancos de dados independentes](https://technet.microsoft.com/library/ff929071.aspx).

> [AZURE.NOTE]A Microsoft recomenda usar usuários de banco de dados independentes para melhorar a escalabilidade.

O Mecanismo do Banco de dados fecha conexões permanecem ociosas por mais de 30 minutos. A conexão deve efetuar logon novamente para que possa ser usada.

As conexões ativas continuamente para o banco de dados SQL requerem nova autorização (realizada pelo Mecanismo de Banco de dados) pelo menos a cada 10 horas. O Mecanismo de Banco de dados tenta uma nova autorização usando a senha enviada originalmente e não é necessária nenhuma entrada do usuário. Por motivos de desempenho, quando uma senha é redefinida no banco de dados SQL, a conexão não será autenticada novamente, mesmo se a conexão for redefinida devido ao pooling de conexão. Isso é diferente do comportamento do SQL Server no local. Se a senha foi alterada desde que a conexão foi autorizada inicialmente, a conexão deverá ser encerrada e uma nova conexão deverá ser feita usando a nova senha. Um usuário com a permissão KILL DATABASE CONNECTION pode terminar explicitamente uma conexão ao Banco de dados SQL usando o comando [KILL](https://msdn.microsoft.com/library/ms173730.aspx).

## Logons e usuários

Ao gerenciar logons e usuários no banco de dados SQL, há restrições.

Para o logon principal no nível de servidor, as seguintes restrições se aplicam:

- O usuário de banco de dados no banco de dados mestre correspondente para o logon principal no nível de servidor não pode ser alterado ou descartado. 
- Embora o logon na entidade de segurança no nível do servidor não seja um membro das duas funções de banco de dados, **dbmanager** e **loginmanager**, no banco de dados **mestre**, ele tem todas as permissões concedidas para essas duas funções.

> [AZURE.NOTE]Esse logon é criado durante o provisionamento do servidor e é semelhante ao logon **sa** em uma instância do SQL Server.

Para todos os logons, as seguintes restrições se aplicam:

- Inglês é o idioma padrão.
- Para acessar o banco de dados **mestre**, cada logon deve ser mapeado para uma conta de usuário no banco de dados **mestre**. O banco de dados **mestre** não dá suporte aos usuários de banco de dados independente.
- Se você não especificar um banco de dados na cadeia de conexão, você será conectado ao banco de dados **mestre** por padrão.
- Você deve estar conectado ao banco de dados **mestre** ao executar as instruções ``CREATE/ALTER/DROP LOGIN`` e ``CREATE/ALTER/DROP DATABASE``. 
- Ao executar as instruções ``CREATE/ALTER/DROP LOGIN`` e ``CREATE/ALTER/DROP DATABASE`` em um aplicativo do ADO.NET, o uso de comandos parametrizados não é permitido. Para obter mais informações, veja [Comandos e parâmetros](https://msdn.microsoft.com/library/ms254953.aspx).
- Ao executar as instruções ``CREATE/ALTER/DROP DATABASE`` e ``CREATE/ALTER/DROP LOGIN``, cada uma dessas instruções deve ser a única instrução em um lote do Transact-SQL. Caso contrário, ocorrerá um erro. Por exemplo, o Transact-SQL a seguir verifica se o banco de dados existe. Se ele existir, uma instrução ``DROP DATABASE`` é chamada para remover o banco de dados. Como a instrução ``DROP DATABASE`` não é a única instrução no lote, a execução da seguinte instrução Transact-SQL resultará em um erro.

```
IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
     DROP DATABASE [database_name];
GO
```

- Ao executar a instrução ``CREATE USER`` com a opção ``FOR/FROM LOGIN``, ela deve ser a única instrução em um lote do Transact-SQL.
- Ao executar a instrução ``ALTER USER`` com a opção ``WITH LOGIN``, ela deve ser a única instrução em um lote do Transact-SQL.
- Somente o logon na entidade de segurança no nível do servidor e os membros da função **dbmanager** do banco de dados no banco de dados **mestre** têm permissão para executar as instruções ``CREATE DATABASE`` e ``DROP DATABASE``.
- Somente o logon na entidade de segurança no nível do servidor e os membros da função **loginmanager** do banco de dados no banco de dados **mestre** têm permissão para executar as instruções ``CREATE LOGIN``, ``ALTER LOGIN`` e ``DROP LOGIN``.
- Para o ``CREATE/ALTER/DROP``, um usuário requer a permissão ``ALTER ANY USER`` no banco de dados.
- Quando o proprietário de uma função de banco de dados tenta adicionar ou remover outro usuário de banco de dados de ou para essa função de banco de dados, pode ocorrer o seguinte erro: **O usuário ou a função “Nome” não existe neste banco de dados.** Esse erro ocorre porque o usuário não está visível para o proprietário. Para resolver esse problema, conceda ao proprietário da função a permissão ``VIEW DEFINITION`` no usuário. 

Para obter mais informações sobre logons e usuários, veja [Gerenciando bancos de dados e logons no Banco de Dados SQL do Azure](sql-database-manage-logins.md).

## Práticas recomendadas de segurança

Considere os seguintes pontos para tornar seus aplicativos de Banco de dados SQL do Azure menos vulneráveis a ameaças de segurança:

- Use sempre as atualizações mais recentes: ao se conectar ao banco de dados SQL, sempre use a versão mais recente das ferramentas e bibliotecas para evitar vulnerabilidades de segurança.
- Bloquear conexões de entrada na porta TCP 1433: somente conexões de saída na porta TCP 1433 são necessárias para os aplicativos que se comunicam com o banco de dados SQL. Se as comunicações de entrada não são necessárias em outros aplicativos no computador, certifique-se de que o firewall continue a bloquear conexões de entrada na porta TCP 1433.
- Evite vulnerabilidades de injeção: para certificar-se de que seus aplicativos não tenham vulnerabilidades de injeção de SQL, use consultas parametrizadas onde possível. Além disso, certifique-se de revisar o código cuidadosamente e executar um teste de penetração antes de implantar seu aplicativo.


## Consulte também

[Firewall do Banco de Dados SQL do Azure](sql-database-firewall-configure.md)

[Como definir as configurações de firewall (Banco de Dados SQL do Azure)](sql-database-configure-firewall-settings.md)

[Gerenciamento de bancos de dados e logons no Banco de Dados SQL do Azure](sql-database-manage-logins.md)

[Central de segurança do mecanismo de banco de dados do SQL Server e banco de dados SQL do Azure](https://msdn.microsoft.com/library/bb510589)

<!---HONumber=AcomDC_1125_2015-->