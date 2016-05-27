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
	ms.date="05/10/2016"
	ms.author="daleche"/>

# Solucionar problemas comuns de conexão no Banco de Dados SQL do Azure
Problemas de conexão no Banco de Dados SQL do Azure podem ser amplamente classificados como qualquer um dos seguintes:

- [Erros transitórios (de curta duração ou intermitentes)](#troubleshoot-the-transient-errors)
- [Erros persistentes ou não transitórios (erros regularmente recorrentes)](#troubleshoot-the-persistent-errors-non-transient-errors)

Se você encontrar problemas de conexão, experimente as etapas de solução de problemas descritas neste artigo.
[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

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

<!---HONumber=AcomDC_0511_2016-->