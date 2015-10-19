<properties 
	pageTitle="Suporte a clientes de versão anterior do Banco de Dados SQL para auditoria e Mascaramento dinâmico de dados | Microsoft Azure" 
	description="Saiba mais sobre o Suporte a clientes de versão anterior do Banco de Dados SQL para auditoria e Mascaramento dinâmico de dados" 
	services="sql-database" 
	documentationCenter="" 
	authors="nadavhelfman" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/01/2015" 
	ms.author="nadavhelfman"/>
 
# Banco de Dados SQL - Suporte a clientes de nível inferior para auditoria e Mascaramento dinâmico de dados 


A [Auditoria](sql-database-auditing-get-started.md) e o [Mascaramento de Dados Dinâmicos](sql-database-dynamic-data-masking-get-started.md) funcionam com clientes SQL que oferecem suporte a redirecionamento de TDS.

Qualquer cliente que implemente o protocolo TDS 7.4 também deve dar suporte a redirecionamento. As exceções incluem o JDBC 4.0, no qual o recurso de redirecionamento não tem suporte completo, e o Tedious para Node.JS, no qual o redirecionamento não foi implementado.

Para "clientes de versão anterior", ou seja, que oferecem suporte ao TDS versão 7.3 e inferior - o FQDN do servidor na cadeia de conexão deve ser modificado:

FQDN original do servidor na cadeia de conexão: <*nome do servidor*>.database.windows.net

FQDN do servidor modificado na cadeia de conexão: <*nome do servidor*>.database.**secure**.windows.net

Uma lista parcial de "Clientes de versão anterior" inclui:

- .NET 4.0 e inferior,
- ODBC 10.0 e inferior.
- JDBC (embora o JDBC dê suporte ao TDS 7.4, o recurso de redirecionamento de TDS não recebe suporte total)
- Tedious (para o Node.JS)

**Comentário:** a modificação do FQDN do servidor acima pode ser útil também para aplicar uma política de Auditoria no Nível do SQL Server sem a necessidade de uma etapa de configuração em cada banco de dados (redução temporária).

 

<!---HONumber=Oct15_HO2-->