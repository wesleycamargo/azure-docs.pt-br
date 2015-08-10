<properties 
	pageTitle="Conectando-se ao Banco de Dados SQL: links, práticas recomendadas e diretrizes de design" 
	description="Um tópico de ponto de partida que reúne links e recomendações para programas cliente que se conectam ao Banco de Dados SQL do Azure a partir de tecnologias como ADO.NET e PHP." 
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
	ms.date="07/24/2015" 
	ms.author="genemi"/>


# Conectando-se ao Banco de Dados SQL: links, práticas recomendadas e diretrizes de design


Este tópico é um bom ponto de partida para a conectividade do cliente com o Banco de Dados SQL do Azure. Ele fornece links para exemplos de código de várias tecnologias que você pode usar para se conectar e interagir com o Banco de Dados SQL. As tecnologias incluem Enterprise Library, JDBC, PHP e muito mais. São fornecidas recomendações que se aplicam em geral, independentemente da tecnologia de conexão ou da linguagem de programação específica.


## Recomendações de tecnologia independente


As informações nesta seção se aplicam independentemente de qual tecnologia específica você usar para se conectar ao banco de dados SQL.


- [Diretrizes para conexão com o Banco de Dados SQL do Azure de forma programática](http://msdn.microsoft.com/library/azure/ee336282.aspx) — discussões incluem o seguinte:
 - Portas
 - Firewall
 - Cadeias de conexão
- [Gerenciamento de recursos do banco de dados SQL do Azure](https://msdn.microsoft.com/library/azure/dn338083.aspx) — discussões incluem o seguinte:
 - Governança de recursos
 - Imposição de limites
 - Limitação


Independentemente de qual tecnologia de conexão é usada, determinadas configurações do firewall do servidor de banco de dados SQL e até bancos de dados individuais, importam:


- [Firewall do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx)


## Recomendação: autenticação


- Use a autenticação do banco de dados SQL, não a autenticação do Windows.
- Especifique um determinado banco de dados, em vez de padronizar para o banco de dados *mestre*.
- Às vezes, o nome de usuário deve ser fornecido com o sufixo de *@nomedoservidor*, mas, outras vezes, o sufixo deve ser omitido. Depende de como a ferramenta ou a API foi gravada.
 - Verifique os detalhes sobre cada tecnologia.
- Conecte-se especificando um usuário em um [banco de dados independente](http://msdn.microsoft.com/library/ff929071.aspx).
 - Essa abordagem fornece um melhor desempenho e escalabilidade, evitando a necessidade de um logon no banco de dados mestre.
 - Você não pode usar a instrução Transact-SQL **USE myDatabaseName;** no Banco de Dados SQL.


## Recomendações: conexão


- Em sua lógica de conexão de cliente, substitua o tempo limite padrão para ser 30 segundos.
 - O padrão de 15 segundos é muito curto para conexões que dependem da Internet.
- Certifique-se de que o seu [firewall do Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/ee621782.aspx) permite a comunicação TCP de saída na porta 1433.
 - Você pode definir as configurações de [firewall](http://msdn.microsoft.com/library/azure/ee621782.aspx) em um servidor de Banco de Dados SQL ou em um banco de dados individual.
- Se você estiver usando um [pool de conexões](http://msdn.microsoft.com/library/8xx3tyca.aspx), feche a conexão no instante em que o programa não estiver usando-o ativamente e não estiver se preparando para reutilizá-lo.
 - A menos que seu programa vá utilizar a conexão para outra operação imediatamente, sem pausa, é recomendável o seguinte padrão: <br/><br/>Abra uma conexão. <br/>Faça uma operação por meio da conexão. <br/>Feche a conexão.<br/><br/>
- Use a lógica de repetição com a lógica de conexão, mas apenas para erros transitórios. Ao usar o banco de dados SQL, as suas tentativas de abrir uma conexão ou emitir uma consulta podem falhar por vários motivos.
 - Um motivo persistente de falha pode ser que a cadeia de conexão está malformada.
 - Um motivo transitório para a falha poderia ser que o sistema de banco de dados SQL precisa equilibrar a carga total. O motivo transitório desaparece por si só, o que significa que o programa deve tentar novamente.
 - Ao tentar novamente uma consulta, primeiro feche a conexão e, em seguida, abra outra conexão.


A próxima seção tem muito mais a dizer sobre o tratamento de falhas transitório e de lógica de repetição.


## Erros transitórios e de lógica de repetição


Os serviços de nuvem, como o Azure e seu serviço Banco de Dados SQL, têm o eterno desafio de balancear as cargas de trabalho e gerenciar os recursos. Se dois bancos de dados que estão sendo atendidos pelo mesmo computador estiverem também envolvidos em um processamento excepcionalmente pesado em tempos sobrepostos, o sistema de gerenciamento poderá detectar a necessidade de deslocamento da carga de trabalho de um banco de dados para outro recurso com mais capacidade.


Durante a mudança, o banco de dados pode ficar temporariamente indisponível. Isso pode impedir novas conexões ou fazer com que o programa cliente perca sua conexão. Porém, a mudança de recurso é temporária, e pode se resolver por si só em alguns minutos ou em vários segundos. Após a mudança, o programa cliente pode restabelecer sua conexão e retomar seu trabalho. A pausa no processamento é melhor do que uma falha evitável do seu programa cliente.


Quando ocorre um erro com o Banco de Dados SQL, uma [SqlException](https://msdn.microsoft.com/library/system.data.sqlclient.sqlexception.aspx) é lançada. A `SqlException` contém um código de erro numérico em sua propriedade **Number**. Se o código de erro identificar um erro transitório, seu programa deverá repetir a chamada.


- [Mensagens de erro para programas cliente do Banco de Dados SQL](sql-database-develop-error-messages.md)
 - A seção **Erros transitórios, Erros de perda de conexão** é uma lista dos erros transitórios que garantem uma repetição automática.
 - Por exemplo, repita se o número de erro 40613 ocorrer, que informa algo semelhante a <br/>\*O banco de dados 'mydatabase' no servidor 'theserver' não está disponível no momento.


Ás vezes, os *erros* transitórios são chamados de *falhas* transitórias. Este tópico considera esses dois termos como sinônimos.


Para obter mais ajuda ao encontrar um erro de conexão e saber se ele é ou não transitório, consulte:


- [Solucionar problemas de conexão ao banco de dados SQL do Azure](http://support.microsoft.com/kb/2980233/)


Para obter links para tópicos de exemplo de código que demonstram a lógica de repetição, consulte:


- [Exemplos de código do cliente de início rápido do Banco de Dados SQL](sql-database-develop-quick-start-client-code-samples.md)


<a id="gatewaynoretry" name="gatewaynoretry">&nbsp;</a>


## O gateway não fornece mais lógica de repetição em V12


Antes da versão V12, o Banco de Dados SQL do Azure tinha um gateway que atuava como um proxy para armazenar em buffer todas as interações entre o banco de dados e o programa cliente. O gateway foi um salto adicional de rede que, às vezes, aumenta a latência de acessos do banco de dados.


A V12 eliminou o gateway. Agora:


- O programa cliente interage *diretamente* com o banco de dados, que é mais eficiente.
- As pequenas distorções do gateway nas mensagens de erro e outras comunicações para o seu programa são eliminadas.
 - O Banco de Dados SQL e SQL Server parecem mais idênticos ao seu programa.


#### Lógica de repetição encerrada


O gateway tinha a lógica de repetição que lidava com alguns erros transitórios para você. Agora, o seu programa deve tratar erros transitórios de maneira mais completa. Para ver exemplos de código sobre a lógica de repetição, consulte:


- [Desenvolvimento do cliente e exemplos de código de início rápido para o Banco de Dados SQL](sql-database-develop-quick-start-client-code-samples.md)
 - Contém links para exemplos de código que contém a lógica de repetição e exemplos mais simples para conexão-e-consulta.
- [Como conectar-se de forma confiável ao Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/azure/dn864744.aspx)
- [Como conectar-se ao Banco de Dados SQL do Azure usando o ADO.NET com a Enterprise Library](http://msdn.microsoft.com/library/azure/dn961167.aspx)
- [Exemplo de código: repetir a lógica em C# para se conectar ao Banco de Dados SQL](sql-database-develop-csharp-retry-windows.md)


## Tecnologias


Os tópicos a seguir contêm links para exemplos de código de várias linguagens e tecnologias de driver que você pode usar para se conectar do seu programa cliente ao Banco de Dados SQL do Azure.


Vários exemplos de código são fornecidos para clientes que são executados no Windows, Linux e Mac OS X.


**Exemplos gerais:** há exemplos de código para várias linguagens de programação, incluindo PHP, Python, Node.js e .NET CSharp. Além disso, são fornecidos exemplos para clientes que são executados no Windows, Linux e Mac OS X.


- [Desenvolvimento do cliente e exemplos de código de início rápido para o Banco de Dados SQL](sql-database-develop-quick-start-client-code-samples.md)
- [Desenvolvimento do Banco de Dados SQL do Azure: tópicos sobre como fazer](http://msdn.microsoft.com/library/azure/ee621787.aspx)


**Escala elástica:** para obter informações sobre conectividade com bancos de dados de Escala Elástica, consulte:


- [Introdução à visualização da Escala Elástica do Banco de Dados SQL do Azure](sql-database-elastic-scale-get-started.md)
- [Roteamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md)


**Bibliotecas de drivers:** para obter informações sobre bibliotecas de drivers de conexão, incluindo versões recomendadas, consulte:


- [Bibliotecas de conexões para Banco de Dados SQL e SQL Server](sql-database-libraries.md)


## Consulte também


- [Criar seu primeiro Banco de Dados SQL do Azure](sql-database-get-started.md)

 

<!---HONumber=July15_HO5-->