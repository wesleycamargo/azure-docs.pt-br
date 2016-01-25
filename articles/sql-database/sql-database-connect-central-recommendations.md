<properties 
	pageTitle="Conectar-se ao Banco de Dados SQL: práticas recomendadas | Microsoft Azure" 
	description="Um tópico de ponto de partida que reúne links e recomendações de práticas recomendadas para programas cliente que se conectam ao Banco de Dados SQL do Azure a partir de tecnologias como ADO.NET e PHP." 
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
	ms.date="01/07/2016" 
	ms.author="genemi"/>


# Conectando-se ao Banco de Dados SQL: práticas recomendadas e diretrizes de design


Este tópico é um bom ponto de partida para a conectividade do cliente com o Banco de Dados SQL do Azure. Ele fornece links para exemplos de código de várias tecnologias que você pode usar para se conectar e interagir com o Banco de Dados SQL. As tecnologias incluem Enterprise Library, JDBC, PHP e muito mais. As informações fornecidas se aplicam independentemente de qual tecnologia específica você usar para se conectar ao banco de dados SQL.


<a id="a-tech-independent-recommend" name="a-tech-independent-recommend"></a>

## Recomendações de tecnologia independente


- [Diretrizes para conexão com o Banco de Dados SQL do Azure de forma programática](http://msdn.microsoft.com/library/azure/ee336282.aspx) — discussões incluem o seguinte:
 - [Portas e Firewalls](sql-database-configure-firewall-settings.md)
 - Cadeias de conexão
- [Gerenciamento de recursos do banco de dados SQL do Azure](http://msdn.microsoft.com/library/azure/dn338083.aspx) — discussões incluem o seguinte:
 - Governança de recursos
 - Imposição de limites
 - Limitação


<a id="b-authentication-recommend" name="b-authentication-recommend"></a>

## Recomendações de autenticação


- Use a autenticação do banco de dados SQL do Azure, não a autenticação do Windows que não está disponível no Banco de dados SQL do Azure.
- Especifique um determinado banco de dados, em vez de padronizar para o banco de dados *mestre*.
 - Não é possível usar a instrução **USE myDatabaseName;** do Transact-SQL no Banco de Dados SQL para alternar para outro banco de dados.


### Usuários independentes


Você tem opções ao adicionar uma pessoa como um usuário no Banco de Dados SQL:

- Adicione um *logon* com uma senha ao banco de dados **mestre** e um *usuário* correspondente a um ou mais bancos de dados no mesmo servidor.

- Adicione um *usuário independente* com uma senha a um ou mais bancos de dados, e nenhum link a qualquer *logon* no **mestre**.


A abordagem de usuário independente tem vantagens e desvantagens:

- A vantagem é que um banco de dados pode ser movido facilmente de um servidor do Banco de Dados SQL do Azure para outro, quando todos os usuários no banco de dados forem usuários independentes.

- A desvantagem é a maior dificuldade na administração diária. Por exemplo:
 - É mais difícil remover vários usuários independentes do que remover um logon.
 - Uma pessoa que é um usuário independente em vários bancos de dados pode ter mais senhas para lembrar ou atualizar.


Mais informações são fornecidas em - [Usuários de bancos de dados independentes - Tornando seu banco de dados portátil](http://msdn.microsoft.com/library/ff929188.aspx).


<a id="c-connection-recommend" name="c-connection-recommend"></a>

## Recomendações de conexão


- Em sua lógica de conexão de cliente, substitua o tempo limite padrão para ser 30 segundos.
 - O padrão de 15 segundos é muito curto para conexões que dependem da Internet.


- No computador que hospeda o programa cliente, certifique-se de que o firewall permite comunicação TCP de saída na porta 1433.


- Se o seu programa cliente se conecta ao Banco de Dados SQL V12 enquanto seu cliente é executado em uma máquina virtual do Azure (VM), abra os seguintes intervalos de porta 11000-11999 e 14000-14999 na VM: Clique [aqui](sql-database-develop-direct-route-ports-adonet-v12.md) para obter detalhes.


- Para tratar *falhas transitórias*, adicione a [lógica de *repetição*](#TransientFaultsAndRetryLogicGm) aos seus programas cliente que interagem com o Banco de Dados SQL do Azure.


### Pool de conexões


Se você estiver usando um [pool de conexões](http://msdn.microsoft.com/library/8xx3tyca.aspx), feche a conexão no instante em que o programa não estiver usando-a ativamente e não estiver se preparando para reutilizá-la.

A menos que seu programa vá reutilizar a conexão de outra operação imediatamente, sem pausa, é recomendável seguinte padrão:

- Abra uma conexão.
- Faça uma operação por meio da conexão.
- Feche a conexão.


### Outras portas diferentes apenas da 1433 em V12


Às vezes, as conexões de cliente para o Banco de Dados SQL do Azure V12 ignoram o proxy e interagem diretamente com o banco de dados. Outras portas diferentes da 1433 se tornam importantes. Para obter detalhes, consulte:<br/> [Portas além da 1433 para ADO.NET 4.5 e o Banco de Dados SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md)


A próxima seção tem muito mais a dizer sobre o tratamento de falhas transitório e de lógica de repetição.



<a name="TransientFaultsAndRetryLogicGm" id="TransientFaultsAndRetryLogicGm"></a>

&nbsp;

## Falhas transitórias e lógica de repetição


O sistema Azure tem a capacidade de reconfigurar dinamicamente os servidores quando cargas de trabalho pesadas surgem no serviço do Banco de Dados SQL.

No entanto, uma reconfiguração pode fazer com que o programa cliente perca a conexão com o Banco de Dados SQL. Esse erro é chamado de *falha transitória*.

Se o seu programa de cliente tiver uma lógica de repetição, ele poderá tentar restabelecer a conexão depois de fornecer o tempo de falha transitória para corrigir a si mesmo.

Recomendamos que você aguarde 5 segundos antes de sua primeira tentativa. Tentar novamente após um atraso inferior a 5 segundos poderá sobrecarregar o serviço de nuvem. Para cada tentativa subsequente, o atraso deverá aumentar exponencialmente, até um máximo de 60 segundos.

Uma discussão sobre o *período de bloqueio* para clientes que usam o ADO.NET está disponível em [Pool de conexão do SQL Server (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).


Para obter exemplos de código que ilustram a lógica de repetição, consulte:

- [Exemplos de código do cliente de início rápido do Banco de Dados SQL](sql-database-develop-quick-start-client-code-samples.md)


### Números de erro para falhas transitórias


Quando ocorre algum erro com o Banco de Dados SQL, uma [SqlException](http://msdn.microsoft.com/library/system.data.sqlclient.sqlexception.aspx) é emitida. A **SqlException** contém um código de erro numérico em sua propriedade **Number**. Se o código de erro identificar um erro transitório, seu programa deverá repetir a chamada.


- [Mensagens de erro para programas cliente do Banco de Dados SQL](sql-database-develop-error-messages.md#bkmk_connection_errors)
 - A seção **Erros transitórios, Erros de Perda de Conexão** é uma lista dos erros transitórios que garantem uma repetição automática.
 - Por exemplo, repita se o número de erro 40613 ocorrer, que informa algo semelhante a<br/>*O banco de dados 'meubancodedados' no servidor 'oservidor' não está disponível no momento.*


Para saber mais, consulte:

- [Desenvolvimento do Banco de Dados SQL do Azure: tópicos sobre como fazer](http://msdn.microsoft.com/library/azure/ee621787.aspx)

<!--  (per Penny Lee, 2016/01/07.  MightyPen==GeneMi)
- [Troubleshoot connection problems to Azure SQL Database](http://support.microsoft.com/kb/2980233/)
-->


<a id="e-technologies" name="e-technologies"></a>

## Tecnologias


Os tópicos a seguir contêm links para exemplos de código de várias linguagens e tecnologias de driver que você pode usar para se conectar do seu programa cliente ao Banco de Dados SQL do Azure.


Vários exemplos de código são fornecidos para clientes que são executados no Windows, Linux e Mac OS X.


**Exemplos gerais:** há [exemplos de código](sql-database-develop-quick-start-client-code-samples.md) para várias linguagens de programação, incluindo PHP, Python, Node.js e .NET CSharp. Além disso, são fornecidos exemplos para clientes que são executados no Windows, Linux e Mac OS X.


**Escala elástica:** para obter informações sobre conectividade com bancos de dados de Escala Elástica, consulte:

- [Introdução à visualização da Escala Elástica do Banco de Dados SQL do Azure](sql-database-elastic-scale-get-started.md)
- [Roteamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md)


**Bibliotecas de drivers:** para obter informações sobre bibliotecas de drivers de conexão, incluindo versões recomendadas, consulte:

- [Bibliotecas de conexões para Banco de Dados SQL e SQL Server](sql-database-libraries.md)

<!---HONumber=AcomDC_0114_2016-->