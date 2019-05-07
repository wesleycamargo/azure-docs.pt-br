---
title: Configurar e acessar logs do servidor do banco de dados do Azure para PostgreSQL - único servidor no Portal do Azure
description: Este artigo descreve como configurar e acessar os logs do servidor no banco de dados do Azure para PostgreSQL - servidor único do Portal do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 13a8c1ee4b7ca114211f93245d74866e5aa5d52c
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067442"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-in-the-azure-portal"></a>Configurar e acessar o banco de dados do Azure para PostgreSQL – logs de servidor único no portal do Azure

Você pode configurar, listar e baixar o [banco de dados do Azure para PostgreSQL logs](concepts-server-logs.md) do portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de instruções, você precisa:
- [Servidor do Banco de Dados do Azure para PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="configure-logging"></a>Configurar o registro em log
Configure o acesso aos logs de consulta e de erros. 

1. Entre no [Portal do Azure](https://portal.azure.com/).

2. Selecione seu servidor de Banco de Dados do Azure para PostgreSQL.

3. Na seção **Monitoramento** da barra lateral, selecione **Logs de servidor**. 

   ![Selecione Logs de servidor e escolha “Clique aqui para habilitar...”](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Selecione o título **Clique aqui para habilitar os logs e configurar os parâmetros de log** para ver os parâmetros do servidor.

5. Altere os parâmetros que você precisa ajustar. Todas as alterações feitas nessa sessão são realçadas em roxo.

   Depois que você alterar os parâmetros, clique em **Salvar**. Ou então, você pode **Descartar** suas alterações. 

   ![Lista longa de parâmetros com alterações a serem salvas ou descartadas](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

6. Retorne para a lista de logs clicando no **botão Fechar** (ícone de X) na página **Parâmetros do servidor**.

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
- Para obter mais informações sobre as definições de parâmetros e o registro em log no PostgreSQL, consulte a documentação do PostgreSQL em [Logs e relatórios de erros](https://www.postgresql.org/docs/current/static/runtime-config-logging.html).

