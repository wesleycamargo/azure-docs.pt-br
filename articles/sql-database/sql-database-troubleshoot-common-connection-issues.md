<properties
	pageTitle="Solucionar problemas comuns de conexão no Banco de Dados SQL do Azure"
	description="Etapas para identificar e resolver erros comuns de conexão do Banco de Dados SQL do Azure."
	services="sql-database"
	documentationCenter=""
	authors="dalechen"
	manager="felixwu"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/02/2016"
	ms.author="daleche"/>

# Solucionar problemas de conexão no Banco de Dados SQL do Azure

Quando a conexão com o banco de dados SQL falha, você recebe [mensagens de erro](sql-database-develop-error-messages.md). Este artigo é um tópico centralizado que ajuda a solucionar problemas de conectividade do Banco de Dados SQL do Azure. Ele apresenta [as causas comuns](#cause) de problemas de conexão, recomenda [uma ferramenta de solução de problemas](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues) que ajuda a identificar o problema e fornece etapas de solução de problemas para resolver [erros transitórios](#troubleshoot-transient-errors) e [erros persistentes ou não transitórios](#troubleshoot-the-persistent-errors). Por fim, ele lista [todos os artigos relevantes sobre problemas de conectividade do Banco de Dados SQL](#all-topics-for-azure-sql-database-connection-problems).

Se você encontrar problemas de conexão, experimente as etapas de solução de problemas descritas neste artigo.[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## Causa

Problemas de conexão podem ser causados por qualquer um dos seguintes fatores:

- Falha ao aplicar práticas recomendadas e diretrizes de design durante o processo de design do aplicativo. Confira [Visão geral do desenvolvimento de Banco de Dados SQL](sql-database-develop-overview.md) para uma introdução.
- Reconfiguração do banco de dados SQL do Azure
- Configurações de firewall
- Tempo limite da conexão
- Informações de logon incorretas
- Limite máximo é atingido para alguns recursos do Banco de Dados SQL do Azure

Normalmente, problemas de conexão com o Banco de Dados SQL do Azure podem ser classificados da seguinte forma:

- [Erros transitórios (de curta duração ou intermitentes)](#troubleshoot-transient-errors)
- [Erros persistentes ou não transitórios (erros regularmente recorrentes)](#troubleshoot-the-persistent-errors)

## Tente a solução de problemas de conectividade do Banco de Dados SQL

Se você se deparar com um erro de conexão específico, tente [esta ferramenta](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database), que vai ajudá-lo a identificar e resolver rapidamente o problema.

## Solucionar problemas de erros transitórios
Se seu aplicativo estiver apresentando erros transitórios, examine os seguintes tópicos para obter dicas sobre como solucionar problemas e reduzir a frequência dos erros:

- [A solução de problemas no banco de dados &lt;x&gt; no servidor &lt;y&gt; não está disponível (erro: 40613)](sql-database-troubleshoot-connection.md)
- [Solucionar problemas, diagnosticar e evitar erros de conexão SQL e erros transitórios para o Banco de Dados SQL](sql-database-connectivity-issues.md)

<a id="troubleshoot-the-persistent-errors" name="troubleshoot-the-persistent-errors"></a>

## Solucionar problemas de erros persistentes (erros não transitórios)

Se o aplicativo falhar persistentemente para se conectar ao banco de dados SQL Azure, ele normalmente indicará um problema com um dos seguintes:

- Configuração do firewall. O firewall de banco de dados ou do lado do cliente do SQL Azure está bloqueando conexões do Banco de Dados SQL.
- Reconfiguração da rede no lado do cliente: por exemplo, um novo endereço IP ou um servidor proxy.
- Erro do usuário: por exemplo, parâmetros de conexão digitados incorretamente, como o nome do servidor na cadeia de conexão.

### Etapas para resolver os problemas de conectividade temporários

1.	Configure as [regras de firewall](sql-database-configure-firewall-settings.md) para permitir o endereço IP do cliente.
2.	Em todos os firewalls entre o cliente e a Internet, certifique-se de que a porta 1433 está aberta para conexões de saída. Examine [Configurar o Firewall do Windows para permitir acesso ao SQL Server](https://msdn.microsoft.com/library/cc646023.aspx) para obter ponteiros adicionais.
3.	Verifique a cadeia de conexão e outras configurações de conexão. Confira a seção Cadeia de Conexão do [tópico sobre problemas de conectividade](sql-database-connectivity-issues.md#connections-to-azure-sql-database).
4.	Verifique a integridade do serviço no painel. Se você achar que há uma interrupção regional, confira [Recuperar-se de uma paralisação](sql-database-disaster-recovery.md) para obter as etapas de recuperação de uma nova região.

## Todos os tópicos sobre problemas de conexão do Banco de Dados SQL do Azure

A tabela a seguir lista todos os tópicos sobre problemas de conexão que se aplicam diretamente ao serviço do Banco de Dados SQL do Azure.


| &nbsp; | Title | Descrição |
| --: | :-- | :-- |
| 1 | [Solucionar problemas de conexão no Banco de Dados SQL do Azure](sql-database-troubleshoot-common-connection-issues.md) | Esta é a página de aterrissagem para solução de problemas de conectividade no Banco de Dados SQL do Azure. Ela descreve como identificar e resolver erros transitórios e erros persistentes ou não transitório no Banco de Dados SQL do Azure. |
| 2 | [Solucionar problemas, diagnosticar e evitar erros de conexão SQL e erros transitórios para o Banco de Dados SQL](sql-database-connectivity-issues.md) | Saiba como solucionar problemas, diagnosticar e evitar um erro de conexão do SQL ou um erro transitório no Banco de Dados SQL. |
| 3 | [Diretrizes gerais para tratamento de falhas transitórias](best-practices-retry-general.md) | Fornece diretrizes gerais para tratar falhas transitórias ao se conectar ao Banco de Dados SQL do Azure. |
| 4 | [Solucionar problemas de conectividade com o Banco de Dados SQL do Microsoft Azure](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database) | Esta ferramenta ajuda a identificar e solucionar erros de conexão. |
| 5 | [Solucionar problemas de "Banco de dados &lt;x&gt; no servidor &lt;y&gt; não está disponível no momento. Tente a conexão novamente mais tarde”](sql-database-troubleshoot-connection.md) | Descreve como identificar e resolver um erro 40613: "Banco de dados &lt;x&gt; no servidor &lt;y&gt; não está disponível no momento. Tente a conexão novamente mais tarde." |
| 6 | [Códigos de erro de SQL para aplicativos cliente do Banco de Dados SQL: erro de conexão de banco de dados e outros problemas](sql-database-develop-error-messages.md) | Fornece informações sobre os códigos de erro de SQL para aplicativos cliente do Banco de Dados SQL, como erros comuns de conexão de banco de dados, problemas de cópia de banco de dados e erros gerais. |
| 7 | [Diretrizes de desempenho do Banco de Dados SQL do Azure para bancos de dados únicos](sql-database-performance-guidance.md) | Fornece orientações para ajudá-lo a determinar qual camada de serviço é adequada para seu aplicativo. Também fornece recomendações para ajustar seu aplicativo para obter o maior proveito de seu Banco de Dados SQL do Azure. |
| 8 | [Visão geral do desenvolvimento de Banco de Dados SQL](sql-database-develop-overview.md) | Fornece links para exemplos de código de várias tecnologias que você pode usar para se conectar e interagir com o Banco de Dados SQL do Azure. |
| 9 | Página de atualização para o Banco de Dados SQL do Azure v12 ([portal do Azure](sql-database-upgrade-server-portal.md), [PowerShell](sql-database-upgrade-server-powershell.md)) | Fornece instruções para atualizar servidores e bancos de dados existentes do Banco de Dados SQL do Azure V11 para o Banco de Dados SQL do Azure V12 usando o Portal do Azure ou o PowerShell. |


## Próximas etapas

- [Solucionar problemas de desempenho no Banco de Dados SQL do Azure](sql-database-troubleshoot-performance.md)
- [Solucionar problemas de permissões no Banco de Dados SQL do Azure](sql-database-troubleshoot-permissions.md)
- [Ver todos os tópicos sobre o serviço do Banco de Dados SQL do Azure](sql-database-index-all-articles.md)
- [Pesquisar a documentação do Microsoft Azure](http://azure.microsoft.com/search/documentation/)
- [Exibir as atualizações mais recentes ao serviço do Banco de Dados SQL do Azure](http://azure.microsoft.com/updates/?service=sql-database)


## Recursos adicionais

- [Visão geral do desenvolvimento de Banco de Dados SQL](sql-database-develop-overview.md)
- [Diretrizes gerais para tratamento de falhas transitórias](../best-practices-retry-general.md)
- [Bibliotecas de conexão para Banco de Dados SQL e SQL Server](sql-database-libraries.md)
- [O roteiro de aprendizagem para usar o Banco de Dados SQL do Azure](https://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database)
- [O roteiro de aprendizagem para usar ferramentas e recursos de banco de dados elástico](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale) 

<!---HONumber=AcomDC_0615_2016-->