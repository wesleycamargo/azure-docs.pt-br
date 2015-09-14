<properties 
	pageTitle="Portas além da 1433 para ADO.NET 4.5 e Banco de Dados SQL V12 | Microsoft Azure"
	description="Às vezes, as conexões de cliente para o Banco de Dados SQL do Azure V12 ignoram o proxy e interagem diretamente com o banco de dados. Outras portas diferentes da 1433 se tornam importantes."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor=""/>


<tags 
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2015"
	ms.author="genemi"/>


# Portas além da 1433 para ADO.NET 4.5 e Banco de Dados SQL V12


Este tópico descreve as mudanças trazidas pelo Banco de Dados SQL do Azure V12 ao comportamento de conexão de clientes que usam ADO.NET 4.5 ou uma versão mais recente.


## V11 do Banco de Dados SQL: Porta 1433


Quando o programa cliente usa ADO.NET 4.5 para se conectar e consultar com o Banco de Dados SQL V11, a sequência interna é a seguinte:


1. O ADO.NET tenta se conectar ao Banco de Dados SQL.

2. O ADO.NET usa a porta 1433 para chamar um módulo de middleware, e o middleware se conecta ao Banco de Dados SQL.

3. O Banco de Dados SQL envia sua resposta de volta ao middleware, que encaminha a resposta ao ADO.NET para a porta 1433.


**Terminologia:** descrevemos a sequência anterior dizendo que o ADO.NET interage com o Banco de Dados SQL usando a *rota proxy*. Se nenhum middleware estivesse envolvido, diríamos que a *rota direta* foi usada.


## V12 do Banco de Dados SQL: fora versus dentro


Para conexões com V12 devemos perguntar se o programa cliente é executado *fora* ou *dentro* do limite de nuvem do Azure. As subseções discutem dois cenários comuns.


#### *Fora:* o cliente é executado em seu computador desktop


A porta 1433 é a única porta que deve estar aberta no computador desktop que hospeda o aplicativo cliente do Banco de Dados SQL.


#### *Dentro:* o cliente é executado no Azure


Quando o cliente é executado dentro do limite de nuvem do Azure, ele usa o que podemos chamar de *rota direta* para interagir com o servidor de Banco de Dados SQL. Após o estabelecimento de uma conexão, as próximas interações entre o cliente e o banco de dados não envolvem um proxy de middleware.


Esta é a sequência:


1. O ADO.NET 4.5 (ou posterior) inicia uma breve interação com a nuvem do Azure e recebe um número de porta identificado dinamicamente.
 - O número da porta identificado dinamicamente está no intervalo entre 11000 e 11999.

2. O ADO.NET conecta-se ao servidor de Banco de Dados SQL diretamente, sem um middleware entre os dois.

3. As consultas são enviadas diretamente ao banco de dados, e os resultados são retornados diretamente ao cliente.


Verifique se o intervalo de portas entre 11000 e 11999 no computador cliente do Azure está disponível para interações de cliente ADO.NET 4.5 com o Banco de Dados SQL V12.

- Em particular, as portas no intervalo devem estar livres de outros bloqueadores de saída.
- O Firewall do Windows em sua VM do Azure controla as configurações de porta.


## Lógica de repetição implícita contida na rota do proxy


Em um ambiente de produção, os clientes que se conectam a V11 ou V12 do Banco de Dados SQL do Azure são aconselhados a implementar a lógica de repetição em seu código. Pode ser um código personalizado, ou pode ser um código que utiliza uma API como a Enterprise Library.


A rota de proxy discutida anteriormente neste tópico é relevante para a questão da lógica de repetição:


- Na V11, o módulo de middleware que atua como um proxy também fornecia um grau modesto de lógica de repetição para lidar com algumas falhas transitórias.

- Na V12, o proxy não fornece qualquer lógica de repetição.


Nos dois cenários, é recomendável que os clientes implementem a lógica de repetição em seu próprio código. Indiscutivelmente, a necessidade da lógica de repetição no cliente aumenta com a rota de proxy mais recente que não fornece qualquer lógica de repetição.


Para obter exemplos de código que demonstram a lógica de repetição, consulte: [Exemplos de código de início rápido do cliente para o Banco de Dados SQL](sql-database-develop-quick-start-client-code-samples.md).


## Esclarecimentos da versão


Esta seção explica os identificadores que se referem a versões do produto. Ela também lista alguns emparelhamentos de versões entre produtos.


#### ADO.NET


- O ADO.NET 4.0 dá suporte ao protocolo TDS 7.3, mas não ao 7.4.
- O ADO.NET 4.5 e posterior dá suporte ao protocolo TDS 7.4.


#### Banco de Dados SQL V11 e V12


As diferenças de conexão do cliente entre o Banco de Dados SQL V11 e V12 são realçadas neste tópico.


*Observação:* a instrução Transact-SQL `SELECT @@version;` retorna um valor que começa com um número como '11' ou '12'. Esses números correspondem aos nossos nomes de versão V11 e V12 do Banco de Dados SQL.


## Links relacionados


- [Novidades no Banco de Dados SQL V12](sql-database-v12-whats-new.md)


- [Conectando-se ao Banco de Dados SQL: links, práticas recomendadas e diretrizes de design](sql-database-connect-central-recommendations.md)


- O ADO.NET 4.6 foi lançado em 20 de julho de 2015. Um comunicado do blog da equipe do .NET está disponível [aqui](http://blogs.msdn.com/b/dotnet/archive/2015/07/20/announcing-net-framework-4-6.aspx).


- O ADO.NET 4.5 foi lançado em 15 de agosto de 2012. Um comunicado do blog da equipe do .NET está disponível [aqui](http://blogs.msdn.com/b/dotnet/archive/2012/08/15/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx).
 - Uma postagem no blog sobre o ADO.NET 4.5.1 está disponível [aqui](http://blogs.msdn.com/b/dotnet/archive/2013/06/26/announcing-the-net-framework-4-5-1-preview.aspx).


- [Lista de versões do protocolo TDS](http://www.freetds.org/userguide/tdshistory.htm)

<!---HONumber=September15_HO1-->