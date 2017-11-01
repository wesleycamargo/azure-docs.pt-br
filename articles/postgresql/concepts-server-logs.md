---
title: Logs de servidor no Banco de Dados do Azure para PostgreSQL | Microsoft Docs
description: Gera logs de erro e de consulta no Banco de Dados do Azure para PostgreSQL.
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 09/26/2017
ms.openlocfilehash: 696af85cd5609171a719a7e77efbfcdeba0aaaaa
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2017
---
# <a name="server-logs-in-azure-database-for-postgresql"></a>Logs de servidor no Banco de Dados do Azure para PostgreSQL 
Banco de Dados do Azure para PostgreSQL gera logs de consulta e de erro. No entanto, não há suporte para acesso aos logs de transação. Os logs de erro e consulta podem ser usados para identificar, solucionar problemas e reparar erros de configuração e desempenho abaixo do ideal. Para saber mais, confira [Relatório de erros e registro em log](https://www.postgresql.org/docs/9.6/static/runtime-config-logging.html).

## <a name="access-server-logs"></a>Acessar logs do servidor
Você pode listar e baixar logs de erro do servidor PostgreSQL do Azure usando o Portal do Azure, a [CLI do Azure](howto-configure-server-logs-using-cli.md) e as APIs REST do Azure.

## <a name="log-retention"></a>Retenção de log
Você pode definir o período de retenção para logs do sistema usando o parâmetro **log\_retention\_period** associado ao seu servidor. A unidade para esse parâmetro é dias. O valor padrão é de 3 dias. O valor máximo são sete dias. O servidor deve ter um armazenamento alocado suficiente para conter os arquivos de log mantidos.
Os arquivos de log farão um rodízio a cada uma hora ou a cada 100 MB, o que ocorrer primeiro.

## <a name="configure-logging-for-azure-postgresql-server"></a>Configurar o registro em log para o servidor PostgreSQL do Azure
Você pode habilitar os registros em log de consulta e de erro para seu servidor. Os logs de erros podem conter informações de pontos de verificação, conexão e vácuo automático.

Habilite o registro em log de consulta para sua instância de Banco de Dados PostgreSQL definindo dois parâmetros de servidor: `log\_statement` e `log\_min\_duration\_statement`.

O parâmetro **log\_statement** controla quais instruções SQL são registradas. Recomendamos a definição desse parâmetro como ***all*** para registrar todas as instruções, o valor padrão é none.

O parâmetro **log\_min\_duration\_statement** define o limite em milissegundos para o registro de uma instrução. Todas as instruções SQL cuja execução demorar mais do que a configuração do parâmetro serão registradas. Esse parâmetro é desabilitado e definido como menos 1 (-1) por padrão. A habilitação desse parâmetro pode ser útil para rastrear consultas não otimizadas em seus aplicativos.

**log\_min\_messages** permite a você controlar quais níveis de mensagem são gravados no log de servidor. O padrão é WARNING. 

Para saber mais sobre essas configurações, consulte a documentação [Relatório de erros e registro em log](https://www.postgresql.org/docs/9.6/static/runtime-config-logging.html). Para configurar especificamente os parâmetros de servidor do Banco de Dados do Azure para PostgreSQL, consulte [Personalizar parâmetros de configuração do servidor usando a CLI do Azure](howto-configure-server-parameters-using-cli.md).

## <a name="next-steps"></a>Próximas etapas
- Para acessar os logs usando a interface de linha de comando da CLI do Azure, consulte [Configurar e acessar os logs de servidor usando a CLI do Azure](howto-configure-server-logs-using-cli.md).
- Para saber mais sobre os parâmetros de servidor, consulte [Personalizar os parâmetros de configuração de servidor usando a CLI do Azure](howto-configure-server-parameters-using-cli.md).
