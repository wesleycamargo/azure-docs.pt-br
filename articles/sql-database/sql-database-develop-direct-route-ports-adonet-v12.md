<properties 
	pageTitle="Portas além de 1433 para o Banco de Dados SQL | Microsoft Azure"
	description="Às vezes, as conexões de cliente do ADO.NET para o Banco de Dados SQL do Azure V12 ignoram o proxy e interagem diretamente com o banco de dados. Outras portas diferentes da 1433 se tornam importantes."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jhubbard"
	editor="" />


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/05/2016" 
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
 - O número da porta identificado dinamicamente está no intervalo de 11000-11999 ou 14000-14999.

2. O ADO.NET conecta-se ao servidor de Banco de Dados SQL diretamente, sem um middleware entre os dois.

3. As consultas são enviadas diretamente ao banco de dados, e os resultados são retornados diretamente ao cliente.


Verifique se os intervalos de portas de 11000-11999 e 14000-14999 no computador cliente do Azure estão disponíveis para interações de cliente ADO.NET 4.5 com o Banco de Dados SQL V12.

- Em particular, as portas no intervalo devem estar livres de outros bloqueadores de saída.

- Em sua VM do Azure, o **Firewall do Windows com Segurança Avançada** controla as configurações de porta.
 - Você pode usar a [interface de usuário do firewall](http://msdn.microsoft.com/library/cc646023.aspx) a fim de adicionar uma regra para a qual você especifica o protocolo **TCP** junto com um intervalo de portas com a sintaxe **11000 a 11999**.


## Esclarecimentos da versão


Esta seção explica os identificadores que se referem a versões do produto. Ela também lista alguns emparelhamentos de versões entre produtos.


#### ADO.NET


- O ADO.NET 4.0 dá suporte ao protocolo TDS 7.3, mas não ao 7.4.
- O ADO.NET 4.5 e posterior dá suporte ao protocolo TDS 7.4.


#### Banco de Dados SQL V11 e V12


As diferenças de conexão do cliente entre o Banco de Dados SQL V11 e V12 são realçadas neste tópico.


*Observação:* a instrução Transact-SQL `SELECT @@version;` retorna um valor que começa com um número como “11” ou “12”. Esses números correspondem aos nossos nomes de versão V11 e V12 do Banco de Dados SQL.


## Links relacionados


- O ADO.NET 4.6 foi lançado em 20 de julho de 2015. Um comunicado do blog da equipe do .NET está disponível [aqui](http://blogs.msdn.com/b/dotnet/archive/2015/07/20/announcing-net-framework-4-6.aspx).


- O ADO.NET 4.5 foi lançado em 15 de agosto de 2012. Um comunicado do blog da equipe do .NET está disponível [aqui](http://blogs.msdn.com/b/dotnet/archive/2012/08/15/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx).
 - Uma postagem no blog sobre o ADO.NET 4.5.1 está disponível [aqui](http://blogs.msdn.com/b/dotnet/archive/2013/06/26/announcing-the-net-framework-4-5-1-preview.aspx).


- [Lista de versões do protocolo TDS](http://www.freetds.org/userguide/tdshistory.htm)


- [Conectando-se ao Banco de Dados SQL: links, práticas recomendadas e diretrizes de design](sql-database-connect-central-recommendations.md)


- [Firewall do Banco de Dados SQL do Azure](sql-database-firewall-configure.md)


- [Como definir as configurações de firewall no Banco de Dados SQL](sql-database-configure-firewall-settings.md)

<!---HONumber=AcomDC_0211_2016-->