---
title: Conectar-se ao Gerenciador de dados do Azure com o ODBC
description: Neste passo a passo, você aprenderá como configurar uma conexão ODBC para o Data Explorer do Azure, em seguida, usar essa conexão para visualizar dados com o Tableau.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/21/2019
ms.openlocfilehash: 0ec1c2f4fc412ca6c81e179d0ad22f781b896357
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58757618"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Conectar-se ao Gerenciador de dados do Azure com o ODBC

Abrir a conectividade de banco de dados ([ODBC](/sql/odbc/reference/odbc-overview)) é uma interface de programação de aplicativo amplamente aceito (API) para acesso ao banco de dados. Use ODBC para se conectar ao Data Explorer do Azure em aplicativos que não têm um conector dedicado.

Nos bastidores, aplicativos chamam funções na interface do ODBC, que são implementadas em módulos específicos de banco de dados chamados *drivers*. O Gerenciador de dados do Azure dá suporte a um subconjunto do protocolo de comunicação do SQL Server ([MS-TDS](/azure/kusto/api/tds/)); portanto ele pode usar o driver ODBC para SQL Server.

Neste artigo, você aprenderá como usar o driver ODBC do SQL Server, portanto, você pode se conectar ao Data Explorer do Azure de qualquer aplicativo que oferece suporte ao ODBC. Você pode, em seguida, opcionalmente, se conectar ao Data Explorer do Azure do Tableau e trazer dados de um cluster de exemplo.

## <a name="prerequisites"></a>Pré-requisitos

Você precisará do seguinte para concluir este tutorial:

* [Microsoft ODBC Driver para SQL Server versão 17.2.0.1 ou posterior](/sql/connect/odbc/download-odbc-driver-for-sql-server) para seu sistema operacional.

* Se você desejar acompanhar o nosso exemplo Tableau, você também precisa:

  * Tableau Desktop, completo ou [avaliação](https://www.tableau.com/products/desktop/download) versão.

  * Um cluster que inclui os dados de exemplo do StormEvents. Para obter mais informações, consulte o [Início rápido: Criar um banco de dados e cluster do Azure Data Explorer](create-cluster-database-portal.md) e [Ingerir dados de exemplo no Azure Data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="configure-the-odbc-data-source"></a>Configurar a fonte de dados ODBC

Siga estas etapas para configurar uma fonte de dados ODBC usando o driver ODBC para SQL Server.

1. No Windows, pesquise *fontes de dados ODBC*e abra o aplicativo de área de trabalho de fontes de dados ODBC.

1. Selecione **Adicionar**.

    ![Adicionar fonte de dados](media/connect-odbc/add-data-source.png)

1. Selecione **ODBC Driver 17 for SQL Server** , em seguida, **concluir**.

    ![Selecione o driver](media/connect-odbc/select-driver.png)

1. Insira um nome e uma descrição para a conexão e o cluster que você deseja se conectar, em seguida, selecione **próxima**. O cluster de URL deve estar no formato  *\<ClusterName\>.\< Região\>. kusto.windows.net*.

    ![Selecionar servidor](media/connect-odbc/select-server.png)

1. Selecione **integrada do Active Directory** , em seguida, **próxima**.

    ![Integrado ao Active Directory](media/connect-odbc/active-directory-integrated.png)

1. Selecione o banco de dados com os dados de exemplo, em seguida, **próxima**.

    ![Alterar banco de dados padrão](media/connect-odbc/change-default-database.png)

1. Na próxima tela, deixe todas as opções como padrões em seguida, selecione **concluir**.

1. Selecione **testar fonte de dados**.

    ![Fonte de dados de teste](media/connect-odbc/test-data-source.png)

1. Verifique se que o teste foi bem-sucedido em seguida, selecione **Okey**. Se o teste não obteve êxito, verifique os valores que você especificou nas etapas anteriores e verifique se que você tem permissões suficientes para conectar-se ao cluster.

    ![Teste foi bem-sucedido](media/connect-odbc/test-succeeded.png)

## <a name="visualize-data-in-tableau-optional"></a>Visualizar dados no Tableau (opcional)

Agora que você tiver terminado de configurar o ODBC, você pode colocar dados de exemplo em Tableau.

1. No Tableau Desktop, no menu à esquerda, selecione **outros bancos de dados (ODBC)**.

    ![Conecte-se ODBC](media/connect-odbc/connect-odbc.png)

1. Para **DSN**, selecione a fonte de dados que você criou para ODBC e selecione **Sign In**.

    ![Entrada do ODBC](media/connect-odbc/odbc-sign-in.png)

1. Para **banco de dados**, selecione o banco de dados em seu cluster de exemplo, como *TestDatabase*. Para **esquema**, selecione *dbo*e para **tabela**, selecione o *StormEvents* tabela de exemplo.

    ![Selecione o banco de dados e tabela](media/connect-odbc/select-database-table.png)

1. Tableau agora mostra o esquema para os dados de exemplo. Selecione **atualizar agora** para trazer os dados para o Tableau.

    ![Atualizar dados](media/connect-odbc/update-data.png)

    Quando os dados são importados, Tableau mostra linhas de dados semelhante à imagem a seguir.

    ![Conjunto de resultados](media/connect-odbc/result-set.png)

1. Agora você pode criar visualizações no Tableau, com base nos dados trazidos do Gerenciador de dados do Azure. Para obter mais informações, consulte [Tableau Learning](https://www.tableau.com/learn).

## <a name="next-steps"></a>Próximas etapas

[Gravar consultas para Azure Data Explorer](write-queries.md)

[Tutorial: Visualizar dados do Azure Data Explorer no Power BI](visualize-power-bi.md)