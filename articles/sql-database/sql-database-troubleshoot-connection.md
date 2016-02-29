<properties
	pageTitle="Solucionar problemas de “O banco de dados no servidor não está atualmente disponível para o Banco de Dados SQL do Azure”."
	description="Etapas para identificar e resolver erros de conexão do Banco de Dados SQL do Azure."
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
	ms.date="02/12/2016"
	ms.author="daleche"/>

# Solucionar problemas de “O banco de dados no servidor não está disponível atualmente. Tente a conexão novamente mais tarde” e outros erros de conexão.
"O banco de dados <dbname> no servidor <servername> não está disponível no momento..." é o erro de conexão temporário mais comum do Banco de Dados SQL do Azure. Os erros transitórios de conexão geralmente ocorrem devido a um evento planejado (por exemplo, uma atualização de software) ou um evento não planejado (por exemplo, uma falha no processo). Esses são geralmente de curta duração, indo de alguns segundos a um minuto no máximo. Se você receber um erro diferente, avalie a [mensagem de erro](sql-database-develop-error-messages.md) em busca de dicas da causa, para determinar se o problema é temporário ou persistente e para usar as diretrizes neste tópico.

## Etapas para resolver problemas de conectividade temporários
1.	Verifique o [Painel de serviços do Microsoft Azure](https://azure.microsoft.com/status) para qualquer interrupção conhecida.
2.	Verifique se o seu aplicativo usa a lógica de repetição. Confira os [problemas de conectividade](sql-database-connectivity-issues.md) e as [práticas recomendadas e diretrizes de design](sql-database-connect-central-recommendations.md) para ver as estratégias de repetição gerais. Em seguida, confira as [amostras de código](sql-database-develop-quick-start-client-code-samples.md) para obter informações específicas.
3.	Conforme um banco de dados se aproxima de seus limites de recursos, talvez pareça que está ocorrendo um problema de conectividade temporário. Confira [Solução de problemas de desempenho](sql-database-troubleshoot-performance.md).
4.	Se os problemas de conectividade continuarem, arquive uma solicitação de suporte do Azure, selecionando **Obter suporte** no site de [Suporte do Azure](https://azure.microsoft.com/support/options).

## Etapas para resolver os problemas de conectividade temporários
Se o aplicativo não puder se conectar de jeito nenhum, geralmente o problema é a configuração de IP e do firewall. Isso pode incluir a reconfiguração da rede no lado do cliente (por exemplo, um novo endereço IP ou proxy). Parâmetros de conexão digitados incorretamente, como a cadeia de conexão, também são comuns.

1.	Configure as [regras de firewall](sql-database-configure-firewall-settings.md) para permitir o endereço IP do cliente.
2.	Em todos os firewalls entre o cliente e a Internet, certifique-se de que a porta 1433 está aberta para conexões de saída.
3.	Verifique a cadeia de conexão e outras configurações de conexão. Confira a seção Cadeia de Conexão do [tópico sobre problemas de conectividade](sql-database-connectivity-issues.md).
4.	Verifique a integridade do serviço no painel. Se você achar que há uma interrupção regional, confira [Recuperar-se de uma paralisação](sql-database-disaster-recovery.md) para obter as etapas de recuperação de uma nova região.
5.	Se os problemas de conectividade continuarem, arquive uma solicitação de suporte do Azure, selecionando **Obter suporte** no site de [Suporte do Azure](https://azure.microsoft.com/support/options).

<!---HONumber=AcomDC_0218_2016-->