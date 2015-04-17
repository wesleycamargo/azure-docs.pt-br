<properties 
	pageTitle="Conexões ao banco de dados SQL do Azure:  Recomendações Centrais" 
	description="Um tema central que fornece links para tópicos mais específicos sobre vários drivers, como ADO.NET e PHP, para se conectar ao banco de dados SQL do Azure". 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/19/2015" 
	ms.author="genemi"/>


#Conexões ao Banco de Dados SQL:  Recomendações Centrais


<!--
GeneMi , 2015-March-19 Thursday 15:41pm
sql-database-connect-central-recommendations.md
sql-database-connect-*.md

Re SqlException, not .HResult, rather .Number.
-->


Este tópico fornece links para exemplos de código para várias tecnologias que você pode usar para se conectar a e interagir com o banco de dados SQL do Azure.  As tecnologias incluem a Enterprise Library, JDBC e PHP.  São fornecidas recomendações que se aplicam em geral, independentemente da tecnologia de conexão específica.


##Recomendações de tecnologia independente


As informações nesta seção se aplicam independentemente de qual tecnologia específica você usar para se conectar ao banco de dados SQL.


- [Diretrizes de conexão com o Banco de Dados SQL do Azure de forma programática](http://msdn.microsoft.com/library/azure/ee336282.aspx) - as discussões incluem o seguinte:
 - Portas
 - Firewall
 - Cadeias de conexão
- [Gerenciamento de recursos de banco de dados SQL do Azure](https://msdn.microsoft.com/library/azure/dn338083.aspx) - as discussões incluem o seguinte:
 - Governança de recursos
 - Imposição de limites
 - Limitação


Independentemente de qual tecnologia de conexão é usada, determinadas configurações do firewall do servidor de banco de dados SQL e até bancos de dados individuais, importam:


- [Firewall de banco de dados SQL do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx)


###Recomendações rápidas


####Conexão


- Em sua lógica de conexão de cliente, substitua o tempo limite padrão para ser 30 segundos.
 - O padrão de 15 segundos é muito curto para conexões que dependem da Internet.
- Se você estiver usando um [Pool de conexões](http://msdn.microsoft.com/library/8xx3tyca.aspx), feche a conexão no instante em que o programa não está sendo ativamente usado e também não está se preparando para reutilizá-lo.
 - A menos que seu programa reutilizará a conexão de outra operação imediatamente, sem pausa, recomendamos o seguinte padrão:
<br/><br/>Abra uma conexão.
<br/>Faça uma operação por meio da conexão.
<br/>Feche a conexão.<br/><br/>
- Use a lógica de repetição com a lógica de conexão, mas apenas para erros transitórios.  Ao usar o banco de dados SQL, as suas tentativas de abrir uma conexão ou emitir uma consulta podem falhar por vários motivos.
 - Um motivo persistente de falha pode ser que a cadeia de conexão está malformada.
 - Um motivo transitório para a falha poderia ser que o sistema de banco de dados SQL precisa equilibrar a carga total.  O motivo transitório desaparece por si só, o que significa que o programa deve tentar novamente.
 - Ao tentar novamente uma consulta, primeiro feche a conexão e, em seguida, abra outra conexão.
- Certifique-se de que o seu [firewall do Banco de Dados SQL do Azure ](http://msdn.microsoft.com/library/ee621782.aspx) permita a comunicação TCP de saída na porta 1433.
 - Você pode definir as configurações de [firewall](http://msdn.microsoft.com/library/azure/ee621782.aspx) em um servidor de banco de dados SQL ou em um banco de dados individuais.


####Autenticação


- Use a autenticação do banco de dados SQL, não a autenticação do Windows.
- Especifique um determinado banco de dados, em vez de padronizar para o banco de dados  *master*.
- Às vezes, o nome de usuário deve ser fornecido com o sufixo de *@yourservername*, mas, outras vezes, o sufixo deve ser omitido.  Depende de como a ferramenta ou a API foi gravada.
 - Verifique os detalhes sobre cada tecnologia.
- Conectar-se ao especificar um usuário em um [banco de dados independente](http://msdn.microsoft.com/library/ff929071.aspx).
 - Essa abordagem fornece um melhor desempenho e escalabilidade, evitando a necessidade de um logon no banco de dados mestre.
 - Você não pode usar a instrução Transact-SQL **USE myDatabaseName;** no banco de dados SQL.


###Erros transitórios


Alguns erros de conexão de banco de dados SQL são persistentes e não há nenhum motivo para repetir a conexão imediatamente.  Outros erros são transitórios e são recomendadas algumas tentativas automatizadas pelo seu programa.  Exemplos:


- *Persistente:*  Se digitar incorretamente o nome do seu servidor de banco de dados SQL quando você tentar se conectar, não fará sentido tentar novamente.
- *Transitória:*  Se a sua conexão de trabalho com o banco de dados SQL for encerrada mais tarde por sistemas de balanceamento de carga ou de limitação do Azure, é recomendável tentar se reconectar.


O [SqlException](https://msdn.microsoft.com/library/system.data.sqlclient.sqlexception.aspx) gerado pela chamada ao banco de dados SQL contém um código de erro numérico em sua propriedade **Número**.  Se o código de erro for aquele listado como um erro transitório, seu programa deverá repetir a chamada.


- [Mensagens de erro (banco de dados SQL do Azure)](http://msdn.microsoft.com/library/azure/ff394106.aspx) - sua seção **Erros de perda de conexão** é uma lista dos erros transitórios que garantem uma nova tentativa.
 - Por exemplo, tente novamente caso ocorra o número de erro 40613, que diz algo semelhante a<br/>*O banco de dados 'mydatabase' no servidor 'theserver' não está disponível momento.*


Para obter assistência adicional quando você encontrar um erro de conexão persistente ou temporário, consulte:


- [Solucionar problemas de conexão ao banco de dados SQL do Azure](http://support.microsoft.com/pt-br/kb/2980233/pt-br)


##Tecnologias


O tópico a seguir contém links para exemplos de código para várias tecnologias de conexão:


- [Desenvolvimento do banco de dados SQL do Azure:  Tópicos de instruções](http://msdn.microsoft.com/library/azure/ee621787.aspx)


Para ADO.NET com a Enterprise Library e as classes de tratamento de falhas transitórias, consulte:


- [Como:  É confiável se conectar ao banco de dados SQL do Azure](http://msdn.microsoft.com/library/azure/dn864744.aspx)


Para obter a Escala elástica, consulte:


- [Introdução à visualização da Escala Elástica do Banco de Dados SQL do Azure](sql-database-elastic-scale-get-started.md)
- [Roteamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md)


##Postagens incompletas ou desatualizadas


Os links nesta seção são para postagens de blog ou fontes semelhantes; portanto, eles podem estar incompletos ou desatualizados.  Ainda, elas podem ter algum valor de segundo plano.


- [Lógica de repetição para falhas transitórias no Banco de Dados SQL do Microsoft Azure ](http://social.technet.microsoft.com/wiki/contents/articles/4235.retry-logic-for-transient-failures-in-windows-azure-sql-database.aspx)

<!-- -->


<!--HONumber=49-->