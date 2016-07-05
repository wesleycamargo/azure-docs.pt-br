<properties
	pageTitle="Visão geral do desenvolvimento do Banco de Dados SQL | Microsoft Azure"
	description="Saiba mais sobre bibliotecas de conectividade disponíveis e práticas recomendadas para aplicativos que se conectam ao Banco de Dados SQL."
	services="sql-database"
	documentationCenter=""
	authors="annemill"
	manager="jhubbard"
	editor="genemi"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/02/2016"
	ms.author="annemill"/>

# Visão geral do desenvolvimento de Banco de Dados SQL
Este artigo apresenta as considerações básicas que um desenvolvedor deve fazer ao escrever código para se conectar ao Banco de Dados SQL do Azure.

## Linguagem e plataforma
Há exemplos de código disponíveis para uma variedade de plataformas e linguagens de programação. Você pode encontrar links de exemplos de código em:

* Mais informações: [bibliotecas de conexão para Banco de Dados SQL e SQL Server](sql-database-libraries.md)

## Limitações de recursos
O Banco de Dados SQL do Azure gerencia os recursos disponíveis para um banco de dados usando dois mecanismos diferentes: Governança de Recursos e Imposição de Limites.

* Mais informações: [limites de recursos do Banco de Dados SQL do Azure](sql-database-resource-limits.md)

## Segurança
O Banco de Dados SQL do Azure fornece recursos para limitar o acesso, proteger os dados e monitorar atividades em um Banco de Dados SQL.

* Mais informações: [proteger seu Banco de Dados SQL](sql-database-security.md)

## Autenticação
* O Banco de Dados SQL do Azure permite logons e usuários da autenticação do SQL Server, bem como usuários e logons da [autenticação do Azure Active Directory](sql-database-aad-authentication.md).
* Você precisará especificar um determinado banco de dados, em vez de padronizar para o banco de dados *mestre*.
* Não é possível usar a instrução **USE myDatabaseName;** do Transact-SQL no Banco de Dados SQL para alternar para outro banco de dados.
* Mais informações: [segurança do Banco de Dados SQL: gerenciar o acesso ao banco de dados e a segurança de logon](sql-database-manage-logins.md)

## Resiliência
Quando ocorre um erro transitório ao se conectar ao Banco de Dados SQL, seu código deverá repetir a chamada. Recomendamos que a lógica de repetição use a lógica de retirada, de modo que ela não sobrecarregue desnecessariamente o Banco de Dados SQL com vários clientes realizando novas tentativas ao mesmo tempo.

* Exemplos de código: para obter exemplos de código que ilustram a lógica de repetição, confira os exemplos para o idioma de sua preferência em: [Bibliotecas de conexão para Banco de Dados SQL e SQL Server](sql-database-libraries.md)
* Mais informações: [Mensagens de erro para programas cliente do Banco de Dados SQL](sql-database-develop-error-messages.md)

## Gerenciando conexões
* Em sua lógica de conexão de cliente, substitua o tempo limite padrão para ser 30 segundos. O padrão de 15 segundos é muito curto para conexões que dependem da Internet.
* Se você estiver usando um [pool de conexões](http://msdn.microsoft.com/library/8xx3tyca.aspx), feche a conexão no instante em que o programa não a estiver utilizando ativamente e não estiver se preparando para reutilizá-la.

## Considerações de rede
* No computador que hospeda o programa cliente, certifique-se de que o firewall permite comunicação TCP de saída na porta 1433. Mais informações: [Configurar um firewall de Banco de Dados SQL do Azure](sql-database-configure-firewall-settings.md)
* Se o seu programa cliente se conectar ao Banco de Dados SQL V12 enquanto seu cliente estiver em execução em uma VM (máquina virtual) do Azure, será necessário abrir determinados intervalos de porta na VM. Mais informações: [Portas além da 1433 para ADO.NET 4.5 e Banco de Dados SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md)
* Às vezes, as conexões de cliente para o Banco de Dados SQL do Azure V12 ignoram o proxy e interagem diretamente com o banco de dados. Outras portas diferentes da 1433 se tornam importantes. Mais informações: [Portas além da 1433 para ADO.NET 4.5 e Banco de Dados SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md)

## Fragmentação de dados com a escala elástica
A escala elástica simplifica o processo de escalar horizontalmente (e de reduzir horizontalmente).

[Padrões de design para aplicativos SaaS multilocatários com o Banco de Dados SQL do Azure]
* (./sql-database/sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [Roteamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md)
* [Introdução à visualização da Escala Elástica do Banco de Dados SQL do Azure](sql-database-elastic-scale-get-started.md)

## Próximas etapas

Explore todos os [recursos do Banco de Dados SQL](https://azure.microsoft.com/services/sql-database/).

<!---HONumber=AcomDC_0622_2016-->