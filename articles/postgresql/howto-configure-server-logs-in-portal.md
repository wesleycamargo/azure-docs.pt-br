---
title: Configurar e acessar os logs de servidor para PostgreSQL no Portal do Azure | Microsoft Docs
description: Este artigo descreve como configurar e acessar os logs de servidor no Banco de Dados do Azure para PostgreSQL no Portal do Azure.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 10/19/2017
ms.openlocfilehash: 9e22d96d20f3201a8d1618c5ff1878e99d50ad79
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2017
---
# <a name="configure-and-access-server-logs-in-the-azure-portal"></a>Configurar e acessar os logs de servidor no Portal do Azure

É possível configurar, listar e baixar os [logs de servidor do Banco de Dados do Azure para PostgreSQL](concepts-server-logs.md) no Portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de instruções, você precisa:
- [Servidor do Banco de Dados do Azure para PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="configure-logging"></a>Configurar o registro em log
Configure o acesso aos logs de consulta e de erros. 

1. Entre no [Portal do Azure](http://portal.azure.com/).

2. Selecione seu servidor de Banco de Dados do Azure para PostgreSQL.

3. Na seção **Monitoramento** da barra lateral, selecione **Logs de servidor**. 

   ![Selecione Logs de servidor e escolha “Clique aqui para habilitar...”](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Selecione o título **Clique aqui para habilitar os logs e configurar os parâmetros de log** para ver os parâmetros do servidor.

5. Selecione o expansor **Mostrar mais** para ver uma lista estendida de parâmetros disponíveis. 

   Para obter mais informações sobre as definições de parâmetros, consulte a documentação do PostgreSQL em [Logs e relatórios de erros](https://www.postgresql.org/docs/current/static/runtime-config-logging.html).

   ![Lista breve de parâmetros de log. Clique em mostrar mais para ver mais detalhes](./media/howto-configure-server-logs-in-portal/2-show-more.png)

6. Altere os parâmetros que você precisa ajustar. Todas as alterações feitas nessa sessão são realçadas em roxo.

   Depois que você alterar os parâmetros, clique em **Salvar**. Ou então, você pode **Descartar** suas alterações. 

   ![Lista longa de parâmetros com alterações a serem salvas ou descartadas](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

7. Retorne para a lista de logs clicando no **botão Fechar** (ícone de X) na página **Parâmetros do servidor**.

## <a name="view-list-and-download-logs"></a>Exibir a lista e baixar os logs
Depois que o registro em log começar, exiba uma lista dos logs disponíveis e baixar os arquivos de log individuais no painel de Logs do servidor. 

1. Abra o portal do Azure.

2. Selecione seu servidor de Banco de Dados do Azure para PostgreSQL.

3. Na seção **Monitoramento** da barra lateral, selecione **Logs de servidor**. A página exibe uma lista dos seus arquivos de log, como mostrado:

   ![Lista de logs do servidor](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > A convenção de nomenclatura do log é **postgresql-yyyy-mm-dd_hh0000.log**. A data e hora usados no nome do arquivo é o momento em que o log foi emitido. Os arquivos de log são girados a cada uma hora ou a cada 100 MB, o que ocorrer primeiro.

4. Se necessário, use a **caixa de pesquisa** para restringir rapidamente a um log específico com base na data/hora. A pesquisa busca o nome do log.

   ![Exemplo de pesquisa por nomes de log](./media/howto-configure-server-logs-in-portal/5-search.png)

5. Baixe arquivos de log individuais usando o botão **download** (ícone de seta para baixo) ao lado de cada arquivo de log na linha de tabela, conforme mostrado:

   ![Clique no ícone download](./media/howto-configure-server-logs-in-portal/6-download.png)

## <a name="next-steps"></a>Próximas etapas
- Consulte [Logs do servidor de acesso na CLI](howto-configure-server-logs-using-cli.md) para saber como baixar logs programaticamente.
- Saiba mais sobre os [Logs de servidor](concepts-server-logs.md) no Azure DB para PostgreSQL. 
- Para obter mais informações sobre as definições de parâmetros e o registro em log no PostgreSQL, consulte a documentação do PosgreSQL em [Logs e relatórios de erros](https://www.postgresql.org/docs/current/static/runtime-config-logging.html).

