<properties 
   pageTitle="Diretrizes e limitações gerais do Banco de Dados SQL do Azure"
   description="Esta página descreve algumas limitações gerais para o Banco de Dados SQL do Azure, bem como áreas de interoperabilidade e suporte."
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
   ms.date="10/28/2015"
   ms.author="jroth" />

# Diretrizes e limitações gerais do Banco de Dados SQL do Azure

Este tópico fornece as diretrizes e limitações gerais do Banco de Dados SQL do Azure. Para obter uma compreensão completa das cotas, do gerenciamento de recursos e do suporte, consulte [recursos adicionais](#additional-guidelines) no final deste tópico.

## Conectividade

 - A Autenticação do Windows não é suportada. Consulte [Gerenciamento de bancos de dados e logons no Banco de Dados SQL do Azure](sql-database-manage-logins.md) 

 - O Banco de Dados SQL do Microsoft Azure dá suporte ao cliente com protocolo TDS versão 7.3 ou posterior.

 - São permitidas apenas conexões TCP/IP.

 - Não há suporte para o navegador do SQL Server 2008 porque o Banco de Dados SQL do Microsoft Azure não tem portas dinâmicas, somente a porta 1433.

## SQL Server Agent/trabalhos

O Banco de Dados SQL do Microsoft Azure não dá suporte ao SQL Server Agent ou a trabalhos. No entanto, você pode executar o SQL Server Agent em seu SQL Server local e se conectar ao Banco de Dados SQL do Microsoft Azure.

## Transações

O Banco de Dados SQL do Azure não dá suporte a transações distribuídas, que são transações que afetam vários recursos. Para obter mais informações, consulte [Transações distribuídas (ADO.NET)](https://msdn.microsoft.com/library/ms254973.aspx). O Banco de Dados SQL pode não preservar os valores de carimbo de hora não confirmados do banco de dados atual (DBTS) entre failovers.

> [AZURE.NOTE]Em determinadas situações, uma transação pode ser promovida automaticamente a transação distribuída. Para obter mais informações, consulte [Integração de System.Transactions com o SQL Server](https://msdn.microsoft.com/library/ms172070.aspx).

## Suporte ao agrupamento do SQL Server

O agrupamento de banco de dados padrão usado pelo Banco de Dados SQL do Microsoft Azure é **SQL\_LATIN1\_GENERAL\_CP1\_CI\_AS**, em que **LATIN1\_GENERAL** é inglês (Estados Unidos), **CP1** é a página de código 1252, **CI** diferencia maiúsculas de minúsculas e **AS** diferencia acentos.

Ao usar um SQL Server local, você poderá definir agrupamentos nos níveis do servidor, do banco de dados, da coluna e da expressão. O Banco de Dados SQL do Microsoft Azure não permite configurar o agrupamento no nível do servidor. Para usar um agrupamento não padrão com o Banco de Dados SQL do Microsoft Azure, configure o agrupamento com a opção Criar Agrupamento de Banco de Dados ou no nível de coluna ou da expressão. O Banco de Dados SQL não dá suporte à opção Agrupar com o comando Alterar Banco de Dados. Por padrão, no Banco de Dados SQL, dados temporários têm o mesmo agrupamento que o banco de dados. Para obter mais informações sobre como definir o agrupamento, consulte [COLLATE (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx).

## Requisitos de nomenclatura

Certos nomes de usuário não são permitidos por motivos de segurança. Você não pode usar os seguintes nomes:

 - **admin** 
 - **administrator** 
 - **guest** 
 - **root** 
 - **sa** 

Os nomes de todos os novos objetos devem ser compatíveis com as regras do SQL Server para identificadores. Para obter mais informações, consulte [Identificadores](https://msdn.microsoft.com/library/ms175874.aspx).

Além disso, os nomes de usuário e logon não podem conter o caractere \\ (não há suporte para a Autenticação do Windows).

## Diretrizes adicionais

- Além das limitações gerais descritas neste artigo, o Banco de Dados SQL tem cotas e limitações de recursos específicas com base em sua [camada de serviço](sql-database-service-tiers.md). Para obter uma descrição detalhada dos limites de camada de serviço, consulte [Limites e recursos de camada de serviço](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).

- Para ver outros limites do Banco de Dados SQL, consulte [Limites de recursos do Banco de Dados SQL do Azure](sql-database-limits.md).

- Para ver diretrizes relacionadas à segurança, consulte [Diretrizes e limitações de segurança do Banco de Dados SQL do Azure](sql-database-security-guidelines.md).

- Outra área relacionada envolve a compatibilidade do Banco de Dados SQL do Azure com versões locais do SQL Server, como o SQL Server 2014. A versão mais recente V12 do Banco de Dados SQL do Azure tem muitas melhorias nessa área. Para obter mais detalhes, consulte [O que há de novo no Banco de Dados SQL V12](sql-database-v12-whats-new.md).

- Para obter informações sobre a disponibilidade de drivers e suporte para o Banco de Dados SQL, consulte [Bibliotecas de conexões para Banco de Dados SQL e SQL Server](sql-database-libraries.md).

<!---HONumber=Nov15_HO1-->