---
title: Introdução ao Azure Data Lake Analytics usando o Visual Studio
description: Saiba como instalar ferramentas do Data Lake para o Visual Studio e como desenvolver e testar scripts U-SQL.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 08/13/2018
ms.openlocfilehash: b463946402eee40d0de0942eeaf37a6f9ea59990
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58083055"
---
# <a name="develop-u-sql-scripts-by-using-data-lake-tools-for-visual-studio"></a>Desenvolvimento de scripts U-SQL usando as ferramentas do Data Lake para Visual Studio
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

O Azure Data Lake Analytics e as Ferramentas do Stream Analytics incluem a funcionalidade relacionada a dois serviços do Azure, Azure Data Lake Analytics e Azure Stream Analytics. Para obter mais informações sobre os cenários do Azure Stream Analytics, consulte [as ferramentas do Azure Stream Analytics para Visual Studio](../stream-analytics/stream-analytics-tools-for-visual-studio-install.md).

Este artigo descreve como usar o Visual Studio para criar contas do Azure Data Lake Analytics, definir trabalhos no [U-SQL](data-lake-analytics-u-sql-get-started.md)e enviar trabalhos para os serviços do Data Lake Analytics. Para saber mais sobre a Análise Data Lake, consulte a [Visão geral da Análise Data Lake do Azure](data-lake-analytics-overview.md).

> [!IMPORTANT]
> A Microsoft recomenda que você atualize para a versão 2.3.3000.4 ou posterior das Ferramentas do Azure Data Lake para Visual Studio. As versões anteriores não estão disponíveis para download e foram preteridas. 
> 
> **O que preciso fazer?**
> 
> 1. Verifique se você estiver usando uma versão anterior ao 2.3.3000.4 do Azure Data Lake Tools para Visual Studio. 
> 
>    ![Verifique a versão da ferramenta](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-about-data-lake.png)
> 
> 2. Se sua versão for uma versão anterior do 2.3.3000.4, atualize o Azure Data Lake Tools para Visual Studio visitando o Centro de download: 
>    - [Para Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools)
>    - [Para Visual Studio 2013 e 2015](https://www.microsoft.com/en-us/download/details.aspx?id=49504)


## <a name="prerequisites"></a>Pré-requisitos

* **Visual Studio**: Há suporte para todas as edições, exceto Express.
    * Visual Studio 2017
    * Visual Studio 2015
    * Visual Studio 2013
* **SDK do Microsoft Azure para .NET** versão 2.7.1 ou posterior.  Instale-o usando o [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).
* Uma conta do **Data Lake Analytics**. Para criar uma conta, confira [Introdução ao Azure Data Lake Analytics usando o portal do Azure](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio"></a>Instalar Ferramentas do Azure Data Lake para Visual Studio

Este tutorial requer que Ferramentas do Data Lake Tools para Visual Studio esteja instalado. Siga as [instruções de instalação](data-lake-analytics-data-lake-tools-install.md).

## <a name="connect-to-an-azure-data-lake-analytics-account"></a>Conecte-se com uma conta do Azure Data Lake Analytics

1. Abra o Visual Studio.

2. Abra o Gerenciador de servidores selecionando a **Vista** do  > **Server Explorer**.

3. Clique o botão direito do mouse no **Azure**. Em seguida, selecione **Conectar-se com a assinatura do Microsoft Azure** e siga as instruções.

4. No Gerenciador de servidores, selecione **Azure** > **Data Lake Analytics**. Você verá uma lista das suas contas do Data Lake Analytics.

## <a name="write-your-first-u-sql-script"></a>Escreve seu primeiro script U-SQL

O texto a seguir é um script U-SQL simples. Ele define um pequeno conjunto de dados e grava o conjunto de dados no repositório padrão do Data Lake Store como um arquivo chamado `/data.csv`.

```
USE DATABASE master;
USE SCHEMA dbo;
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

## <a name="submit-a-data-lake-analytics-job"></a>Enviar um trabalho da Análise Data Lake

1. Selecione **Arquivo** > **Novo** > **Projeto**.

2. Selecione o tipo **projeto U-SQL** e, em seguida, clique em **OK**. O Visual Studio cria uma solução com um arquivo **Script.usql**.

3. Cole o script anterior na janela **Script.usql**.

4. No canto superior esquerdo da janela **Script.usql**, especifique a conta do Data Lake Analytics.

    ![Enviar projeto de U-SQL do Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job.png)

5. No canto superior esquerdo da janela **Script.usql**, selecione **Enviar**.

6. Após o envio do trabalho, a guia **Exibição do trabalho** é aberta para mostrar o andamento do trabalho. Para ver a versão mais recente do status do trabalho e atualizar a tela, clique em **Atualizar**.

    ![Grafo de desempenho de trabalho do Data Lake Analytics do U-SQL do Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

   * **Resumo do trabalho** mostra o resumo do trabalho.   
   * **Grafo do Trabalho** visualiza o andamento do trabalho.
   * **Operações de metadados** mostra todas as ações que foram executadas no catálogo de U-SQL.
   * **Dados** mostra todas as entradas e saídas.
   * **Histórico de estado** mostra os detalhes da linha do tempo e do estado.
   * **Análise de AU** mostra quandos AUs foram usados no trabalho e explora simulações de diferentes estratégias de alocação de AUs.
   * **Diagnósticos** fornece uma análise avançada para otimização de desempenho e a execução do trabalho.

## <a name="check-job-status"></a>Verificar o status do trabalho

1. No Gerenciador de servidores, selecione **Azure** > **Data Lake Analytics**.

2. Expanda o nome da conta do Data Lake Analytics.

3. Clique duas vezes em **Trabalhos**.

4. Selecione o trabalho enviado anteriormente.

## <a name="see-the-job-output"></a>Veja a saída do trabalho

1. No Gerenciador de servidores, navegue até o trabalho enviado.

2. Clique na guia **Dados** .

3. Na guia **Saídas do trabalho**, selecione o arquivo `"/data.csv"`.

## <a name="next-steps"></a>Próximas etapas

* [Execute scripts U-SQL em sua estação de trabalho para teste e depuração](data-lake-analytics-data-lake-tools-local-run.md)
* [Depurar código C# em trabalhos U-SQL usando as Ferramentas do Azure Data Lake para Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Usar as Ferramentas do Azure Data Lake para Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
