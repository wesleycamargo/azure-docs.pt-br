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
	ms.date="03/29/2016"
	ms.author="daleche"/>

# Solucionar problemas do erro "Banco de dados &lt;x&gt; no servidor &lt;y&gt; não está disponível no momento. Tente a conexão novamente mais tarde”
[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

Quando um aplicativo se conecta a um banco de dados SQL do Azure, a seguinte mensagem de erro é exibida:

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [AZURE.NOTE] Essa mensagem de erro normalmente é transitória (de vida curta).

Esse erro ocorre quando o banco de dados do Azure está sendo movido (ou reconfigurados) e seu aplicativo perde a conexão com o banco de dados SQL. Eventos de reconfiguração do banco de dados SQL ocorrem devido a um evento planejado (por exemplo, uma atualização de software) ou um evento não planejado (por exemplo, uma falha no processo ou balanceamento de carga). A maioria dos eventos de reconfiguração geralmente é de curta duração e deve ser concluída em, no máximo, de 60 segundos. No entanto, esses eventos ocasionalmente podem levar mais tempo para serem concluídos, como quando uma transação grande causa uma recuperação de execução longa.

## Etapas para resolver problemas de conectividade temporários
1.	Consulte o [Painel de Serviços do Microsoft Azure](https://azure.microsoft.com/status) quanto a quaisquer interrupções conhecidas que tenham ocorrido durante o tempo em que o erro foi relatado pelo aplicativo.
2. Aplicativos que se conectam a um serviço de nuvem, como Banco de Dados SQL, devem esperar eventos reconfiguração periódicos e implementar lógica de repetição para lidar com esses erros, em vez de exibir esses erros como erros de aplicativo aos usuários. Consulte a seção [Erros transitórios](sql-database-connectivity-issues.md) e [práticas recomendadas e diretrizes de design](sql-database-connect-central-recommendations.md) para obter mais informações e estratégias de repetição gerais. Em seguida, confira as [amostras de código](sql-database-develop-quick-start-client-code-samples.md) para informações específicas.
3.	Conforme um banco de dados se aproxima dos limites de recursos, pode parecer que há um problema de conectividade temporário. Confira [Solução de problemas de desempenho](sql-database-troubleshoot-performance.md).
4.	Se problemas de conectividade continuarem, se a duração pela qual o aplicativo encontra o erro exceder 60 segundos ou se você vir várias ocorrências do erro em um determinado dia, envie uma solicitação de suporte do Azure selecionando **Obter Suporte** no site de [Suporte do Azure](https://azure.microsoft.com/support/options).

## Próximas etapas
- Se você receber um erro diferente, avalie a [mensagem de erro](sql-database-develop-error-messages.md) para dicas sobre a causa.
- Se o problema persistir, visite a orientação em [Solucionar problemas comuns de conexão para o Banco de Dados SQL do Azure](sql-database-troubleshoot-common-connection-issues.md).

<!-----------HONumber=AcomDC_0330_2016-->