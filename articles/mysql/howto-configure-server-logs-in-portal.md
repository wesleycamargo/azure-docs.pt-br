---
title: Configurar e acessar os logs de servidor do Banco de Dados do Azure para MySQL no Portal do Azure | Microsoft Docs
description: Este artigo descreve como configurar e acessar os logs de servidor no Banco de Dados do Azure para MySQL no Portal do Azure.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 10/19/2017
ms.openlocfilehash: 89674c133b458c16fbdacd771be24830624dde7c
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2017
---
# <a name="configure-and-access-server-logs-in-the-azure-portal"></a>Configurar e acessar os logs de servidor no Portal do Azure

É possível configurar, listar e baixar os [logs de servidor do Banco de Dados do Azure para MySQL](concepts-server-logs.md) no Portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de instruções, você precisa:
- [Servidor do Banco de Dados do Azure para MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-logging"></a>Configurar o registro em log
Configure o acesso aos logs de consulta lenta do MySQL. 

1. Entre no [Portal do Azure](http://portal.azure.com/).

2. Selecione seu servidor de Banco de Dados do Azure para MySQL.

3. Na seção **Monitoramento** da barra lateral, selecione **Logs de servidor**. 
   ![Selecione os Logs do servidor e clique para configurar](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Selecione o título **Clique aqui para habilitar os logs e configurar os parâmetros de log** para ver os parâmetros do servidor.

5. Selecione o expansor **Mostrar mais** para ver uma lista estendida de parâmetros disponíveis. 

   Para obter mais informações sobre as definições de parâmetros, consulte a documentação do MySQL em [Logs](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

   ![Clique em mostrar mais para ver uma lista mais longa](./media/howto-configure-server-logs-in-portal/2-show-more.png)

6. Altere os parâmetros que você precisa ajustar. Todas as alterações feitas nessa sessão são realçadas em roxo. 

   Depois que você alterar os parâmetros, clique em **Salvar**. Ou então, você pode **Descartar** suas alterações.

   ![Clique em salvar ou descartar](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

7. Retorne para a lista de logs clicando no **botão Fechar** (ícone de X) na página **Parâmetros do servidor**.

## <a name="view-list-and-download-logs"></a>Exibir a lista e baixar os logs
Depois que o registro em log começar, exiba uma lista dos logs disponíveis e baixar os arquivos de log individuais no painel de Logs do servidor. 

1. Abra o portal do Azure.

2. Selecione seu servidor de Banco de Dados do Azure para MySQL.

3. Na seção **Monitoramento** da barra lateral, selecione **Logs de servidor**. A página exibe uma lista dos seus arquivos de log, como mostrado:

   ![Lista de logs](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > A convenção de nomenclatura do log é **mysql-slow-< nome do seu servidor>-aaaammddhh.log**. A data e hora usados no nome do arquivo é o momento em que o log foi emitido. Os arquivos de log são girados a cada 24 horas ou 7,5 GB, o que ocorrer primeiro.

4. Se necessário, use a **caixa de pesquisa** para restringir rapidamente a um log específico com base na data/hora. A pesquisa busca o nome do log.

5. Baixe arquivos de log individuais usando o botão **download** (ícone de seta para baixo) ao lado de cada arquivo de log na linha de tabela, conforme mostrado:

   ![Clique no ícone download](./media/howto-configure-server-logs-in-portal/5-download.png)


## <a name="next-steps"></a>Próximas etapas
- Consulte [Logs do servidor de acesso na CLI](howto-configure-server-logs-in-cli.md) para saber como baixar logs programaticamente.
- Saiba mais sobre [Logs de servidor](concepts-server-logs.md) no Banco de Dados do Azure para MySQL. 
- Para obter mais informações sobre as definições de parâmetros e o registro em log no MySQL, consulte a documentação do MySQL em [Logs](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

