<properties
   pageTitle="Diretrizes e limitações gerais do Banco de Dados SQL do Azure"
   description="Esta página descreve algumas limitações gerais para o Banco de Dados SQL do Azure, bem como áreas de interoperabilidade e suporte."
   services="sql-database"
   documentationCenter="na"
   authors="CarlRabeler"
   manager="jhubbard"
   editor="monicar" />
<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="06/21/2016"
   ms.author="carlrab" />

# Diretrizes e limitações gerais do Banco de Dados SQL do Azure

Este tópico fornece as diretrizes e limitações gerais do Banco de Dados SQL do Azure. Para obter uma compreensão completa das cotas, do gerenciamento de recursos e do suporte, consulte [recursos adicionais](#additional-guidelines) no final deste tópico.

## Conectividade e autenticação

  - A Autenticação do Windows não é suportada. Consulte [Gerenciamento de bancos de dados e logons no Banco de Dados SQL do Azure](sql-database-manage-logins.md) No entanto, a autenticação do Azure Active Directory tem suporte com certas limitações. Confira [Conectar-se ao Banco de Dados SQL com a autenticação do Azure Active Directory](sql-database-aad-authentication.md).

  - O Banco de Dados SQL do Microsoft Azure dá suporte ao cliente com protocolo TDS versão 7.3 ou posterior.

  - São permitidas apenas conexões TCP/IP.

  - Não há suporte para o navegador do SQL Server 2008 porque o Banco de Dados SQL do Microsoft Azure não tem portas dinâmicas, somente a porta 1433.

## SQL Server Agent/trabalhos

O Banco de Dados SQL do Microsoft Azure não dá suporte ao SQL Server Agent ou a trabalhos. No entanto, você pode executar o SQL Server Agent em seu SQL Server local e se conectar ao Banco de Dados SQL do Microsoft Azure.

## Suporte ao agrupamento do SQL Server

O agrupamento de banco de dados padrão usado pelo Banco de Dados SQL do Microsoft Azure é **SQL\_LATIN1\_GENERAL\_CP1\_CI\_AS**, em que **LATIN1\_GENERAL** é inglês (Estados Unidos), **CP1** é a página de código 1252, **CI** diferencia maiúsculas de minúsculas e **AS** diferencia acentos. Não é possível alterar o agrupamento dos bancos de dados V12. Para obter mais informações sobre como definir o agrupamento, veja [COLLATE (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx).

## Requisitos de nomenclatura

Certos nomes de usuário não são permitidos por motivos de segurança. Você não pode usar os seguintes nomes:

 - **admin**
 - **administrator**
 - **guest**
 - **root**
 - **sa**

Os nomes de todos os novos objetos devem ser compatíveis com as regras do SQL Server para identificadores. Para saber mais, veja [Identificadores](https://msdn.microsoft.com/library/ms175874.aspx).

Além disso, os nomes de usuário e logon não podem conter o caractere \\ (não há suporte para a Autenticação do Windows).

## Diretrizes adicionais

- Além das limitações gerais descritas neste artigo, o Banco de Dados SQL tem cotas e limitações de recursos específicas com base em sua **camada de serviço**. Para obter uma visão geral das camadas de serviço, consulte [Camadas de serviço do banco de dados SQL](sql-database-service-tiers.md).

- Para ver outros limites do Banco de Dados SQL, veja [Limites de recursos do Banco de Dados SQL do Azure](sql-database-resource-limits.md).

- Para ver diretrizes relacionadas à segurança, veja [Diretrizes e limitações de segurança do Banco de Dados SQL do Azure](sql-database-security-guidelines.md).

- Outra área relacionada envolve a compatibilidade do Banco de Dados SQL do Azure com versões locais do SQL Server, como o SQL Server 2014 e o SQL Server 2016. A versão mais recente V12 do Banco de Dados SQL do Azure tem muitas melhorias nessa área. Para obter mais detalhes, veja [Novidades no Banco de Dados SQL V12](sql-database-v12-whats-new.md).

- Para obter informações sobre a disponibilidade de drivers e o suporte ao Banco de Dados SQL, veja [Bibliotecas de conexões para Banco de Dados SQL e SQL Server](sql-database-libraries.md).

<!---HONumber=AcomDC_0803_2016-->