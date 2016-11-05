---
title: Diretrizes e limitações do Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba mais sobre as Limitações e diretrizes de Banco de dados SQL do Microsoft Azure relacionadas à segurança.
services: sql-database
documentationcenter: ''
author: BYHAM
manager: jhubbard
editor: ''
tags: ''

ms.service: sql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 08/02/2016
ms.author: rickbyh

---
# Limitações e diretrizes de segurança de Banco de Dados SQL do Azure
Este tópico descreve as limitações e diretrizes de Banco de dados SQL do Microsoft Azure relacionadas à segurança. Considere os seguintes pontos ao gerenciar a segurança de seus Bancos de dados SQL do Azure.

## Acesso ao banco de dados mestre virtual
Normalmente, somente os administradores precisam acessar o banco de dados mestre. O acesso de rotina a cada banco de dados do usuário deve ser por meio de usuários de banco de dados independente não administradores criados em cada banco de dados. Quando você usar os usuários de banco de dados independente,não precisará criar logons no banco de dados mestre. Para obter mais informações, consulte [Usuários do banco de dados independente - Tornando o banco de dados portátil](https://msdn.microsoft.com/library/ff929188.aspx).

## Firewall
O firewall do SQL Server com o escopo para o servidor inteiro do SQL Azure geralmente é configurado por meio do portal e só deve admitir os endereços IP usados por administradores. Conecte-se a um banco de dados de usuário e, em seguida, use a instrução [sp\_set\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270010.aspx) Transact-SQL para criar uma regra de firewall no escopo do banco de dados que abrirá o intervalo necessário de endereços IP para cada banco de dados.

O serviço de Banco de dados SQL do Azure só está disponível por meio da porta TCP 1433. Para acessar um Banco de dados SQL do seu computador, certifique-se de que o firewall do computador cliente permita a comunicação TCP de saída na porta TCP 1433. Se não for necessário a outros aplicativos, bloqueie as conexões de entrada na porta TCP 1433.

Como parte do processo de conexão, as conexões de máquinas virtuais do Azure são redirecionadas para um endereço IP e uma porta diferentes, exclusivos para cada função de trabalho. O número da porta estará na faixa de 11000 a 11999. Para saber mais sobre as portas TCP, veja [Portas além da 1433 para o ADO.NET 4.5 e o Banco de Dados SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md).

## Autenticação
Usar autenticação do Active Directory (segurança integrada) sempre que possível. Para saber mais sobre como configurar a autenticação do AD, veja [Conectar-se ao Banco de Dados SQL usando a autenticação do Azure Active Directory](sql-database-aad-authentication.md) e [Escolha de um modo de autenticação](https://msdn.microsoft.com/library/ms144284.aspx) nos Manuais Online do SQL Server.

Utilize os usuários de banco de dados independente para melhorar a escalabilidade. Para obter mais informações, consulte [Usuários do Banco de dados Independente - Tornado portátil o banco de dados](https://msdn.microsoft.com/library/ff929188.aspx), [CREATE USER (Transact-SQL)](https://technet.microsoft.com/library/ms173463.aspx), e [Bancos de dados independentes](https://technet.microsoft.com/library/ff929071.aspx).

O Mecanismo do Banco de dados fecha conexões permanecem ociosas por mais de 30 minutos. A conexão deve efetuar logon novamente para que possa ser usada.

As conexões ativas continuamente para o banco de dados SQL requerem nova autorização (realizada pelo Mecanismo de Banco de dados) pelo menos a cada 10 horas. O Mecanismo de Banco de dados tenta uma nova autorização usando a senha enviada originalmente e não é necessária nenhuma entrada do usuário. Por motivos de desempenho, quando uma senha é redefinida no banco de dados SQL, a conexão não será autenticada novamente, mesmo se a conexão for redefinida devido ao pooling de conexão. Isso é diferente do comportamento do SQL Server no local. Se a senha foi alterada desde que a conexão foi autorizada inicialmente, a conexão deverá ser encerrada e uma nova conexão deverá ser feita usando a nova senha. Um usuário com a permissão KILL DATABASE CONNECTION pode terminar explicitamente uma conexão ao Banco de dados SQL usando o comando [KILL](https://msdn.microsoft.com/library/ms173730.aspx).

## Logons e usuários
Ao gerenciar logons e usuários no banco de dados SQL, há restrições.

* Você deve estar conectado ao banco de dados **mestre** ao executar as instruções ``CREATE/ALTER/DROP DATABASE``. - O usuário de banco de dados no banco de dados mestre correspondente para o logon principal no nível de servidor não pode ser alterado ou descartado.
* O inglês (EUA) é o idioma padrão do logon principal do nível do servidor.
* Para acessar o banco de dados **mestre**, cada logon deve ser mapeado para uma conta de usuário no banco de dados **mestre**. O banco de dados **mestre** não dá suporte aos usuários de banco de dados independente.
* Somente o logon na entidade de segurança no nível do servidor e os membros da função **dbmanager** do banco de dados no banco de dados **mestre** têm permissão para executar as instruções ``CREATE DATABASE`` e ``DROP DATABASE``.
* Você deve estar conectado ao banco de dados mestre ao executar as instruções ``CREATE/ALTER/DROP LOGIN``. No entanto, não é recomendado usar logons. Utilize os usuários de bancos de dados independentes.
* Para se conectar a um banco de dados do usuário, você deve fornecer o nome do banco de dados na cadeia de conexão.
* Somente o logon na entidade de segurança no nível do servidor e os membros da função **loginmanager** do banco de dados no banco de dados **mestre** têm permissão para executar as instruções ``CREATE LOGIN``, ``ALTER LOGIN`` e ``DROP LOGIN``.
* Ao executar as instruções ``CREATE/ALTER/DROP LOGIN`` e ``CREATE/ALTER/DROP DATABASE`` em um aplicativo do ADO.NET, o uso de comandos parametrizados não é permitido. Para obter mais informações, veja [Comandos e parâmetros](https://msdn.microsoft.com/library/ms254953.aspx).
* Ao executar as instruções ``CREATE/ALTER/DROP DATABASE`` e ``CREATE/ALTER/DROP LOGIN``, cada uma dessas instruções deve ser a única instrução em um lote do Transact-SQL. Caso contrário, ocorrerá um erro. Por exemplo, o Transact-SQL a seguir verifica se o banco de dados existe. Se ele existir, uma instrução ``DROP DATABASE`` é chamada para remover o banco de dados. Como a instrução ``DROP DATABASE`` não é a única instrução no lote, a execução da seguinte instrução Transact-SQL resultará em um erro.

```
IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
     DROP DATABASE [database_name];
GO
```

* Ao executar a instrução ``CREATE USER`` com a opção ``FOR/FROM LOGIN``, ela deve ser a única instrução em um lote do Transact-SQL.
* Ao executar a instrução ``ALTER USER`` com a opção ``WITH LOGIN``, ela deve ser a única instrução em um lote do Transact-SQL.
* Para o ``CREATE/ALTER/DROP``, um usuário requer a permissão ``ALTER ANY USER`` no banco de dados.
* Quando o proprietário de uma função de banco de dados tenta adicionar ou remover outro usuário de banco de dados de ou para essa função de banco de dados, pode ocorrer o seguinte erro: **O usuário ou a função “Nome” não existe neste banco de dados.** Esse erro ocorre porque o usuário não está visível para o proprietário. Para resolver esse problema, conceda ao proprietário da função a permissão ``VIEW DEFINITION`` no usuário.

Para obter mais informações sobre logons e usuários, veja [Gerenciando bancos de dados e logons no Banco de Dados SQL do Azure](sql-database-manage-logins.md).

## Consulte também
[Firewall do Banco de Dados SQL do Azure](sql-database-firewall-configure.md)

[Como definir as configurações de firewall (Banco de Dados SQL do Azure)](sql-database-configure-firewall-settings.md)

[Gerenciamento de bancos de dados e logons no Banco de Dados SQL do Azure](sql-database-manage-logins.md)

[Central de segurança do mecanismo de banco de dados do SQL Server e banco de dados SQL do Azure](https://msdn.microsoft.com/library/bb510589)

<!---HONumber=AcomDC_0803_2016-->