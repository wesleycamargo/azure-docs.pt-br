---
title: Introdução ao Azure Data Lake Analytics usando o Visual Studio | Microsoft Docs
description: Saiba como instalar ferramentas do Data Lake para o Visual Studio e como desenvolver e testar scripts U-SQL.
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/02/2018
ms.author: saveenr, yanacai
ms.openlocfilehash: d0974e3258e0def09fe12d348180dcedf216401c
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2018
---
# <a name="develop-u-sql-scripts-by-using-data-lake-tools-for-visual-studio"></a>Desenvolvimento de scripts U-SQL usando as ferramentas do Data Lake para Visual Studio
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Saiba como usar o Visual Studio para criar contas do Azure Data Lake Analytics, definir trabalhos no [U-SQL](data-lake-analytics-u-sql-get-started.md) e enviar trabalhos ao serviço do Data Lake Analytics. Para saber mais sobre a Análise Data Lake, consulte a [Visão geral da Análise Data Lake do Azure](data-lake-analytics-overview.md).

>[!IMPORTANT]
>
>Na preparação para a nova Regulação de Proteção a Dados Gerais (GDPR) que entra em vigor em 25 de maio de 2018, é recomendável que os usuários do Azure Data Lake Tools para Visual Studio atualizem para a versão 2.3.3000.4 ou posterior. Esta versão inclui alterações com base nos requisitos de proteção de dados mais recentes. Observe que as versões anteriores não estão disponíveis para fazer o download e foram preteridas. 
>
>**O que preciso fazer?**
>
>1. Verifique se você estiver usando uma versão anterior ao 2.3.3000.4 do Azure Data Lake Tools para Visual Studio. 
>   
>   ![Verifique a versão da ferramenta](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-about-data-lake.png)
> 
>2. Se sua versão for uma versão anterior do 2.3.3000.4, atualize o Azure Data Lake Tools para Visual Studio visitando o Centro de download: 
>    - [Para Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools)
>    - [Para Visual Studio 2013 e 2015](https://www.microsoft.com/en-us/download/details.aspx?id=49504)


## <a name="prerequisites"></a>pré-requisitos

* **Visual Studio**: há suporte para todas as edições, exceto Express.
    * Visual Studio 2017
    * Visual Studio 2015
    * Visual Studio 2013
* **SDK do Microsoft Azure para .NET** versão 2.7.1 ou posterior.  Instale-o usando o [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx).
* Uma conta do **Data Lake Analytics**. Para criar uma conta, confira [Introdução ao Azure Data Lake Analytics usando o portal do Azure](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio"></a>Instale as Ferramentas do Azure Data Lake para Visual Studio

### <a name="install-azure-data-lake-tools-for-visual-studio-2017"></a>Instale as Ferramentas do Azure Data Lake para Visual Studio 2017

Há suporte para Ferramentas do Azure Data Lake para Visual Studio no Visual Studio 2017 15.3 ou superior. A ferramenta é parte das cargas de trabalho **Armazenamento e processamento de dados** e **Desenvolvimento do Azure** no Instalador do Visual Studio. Habilite qualquer uma dessas duas cargas de trabalho como parte da instalação do Visual Studio.  

Habilite a carga de trabalho **Armazenamento e processamento de dados** conforme mostrado: ![Habilitar carga de trabalho Armazenamento e processamento de dados](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2017-install-01.png)

Habilite a carga de trabalho **Desenvolvimento do Azure** conforme mostrado: ![Habilitar carga de trabalho de desenvolvimento do Azure](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2017-install-02.png)

### <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>Instale as Ferramentas do Azure Data Lake para Visual Studio 2013 e 2015

Baixe e instale as Ferramentas do Azure Data Lake para Visual Studio [do Centro de Download](http://aka.ms/adltoolsvs). Após a instalação, observe que:
* O nó do **Gerenciador de Servidores** > do **Azure** contém um nó do **Data Lake Analytics**. 
* O menu de **ferramentas** tem um item do **Data Lake**.

## <a name="connect-to-an-azure-data-lake-analytics-account"></a>Conecte-se com uma conta do Azure Data Lake Analytics

1. Abra o Visual Studio.
2. Abra o Gerenciador de servidores selecionando a **Vista** do  > **Server Explorer**.
3. Clique o botão direito do mouse no **Azure**. Em seguida, selecione **Conectar-se com a assinatura do Microsoft Azure** e siga as instruções.
4. No Gerenciador de servidores, selecione **Azure** > **Data Lake Analytics**. Você verá uma lista das suas contas do Data Lake Analytics.


## <a name="write-your-first-u-sql-script"></a>Escreve seu primeiro script U-SQL

O texto a seguir é um script U-SQL simples. Ele define um pequeno conjunto de dados e grava o conjunto de dados no repositório padrão do Data Lake Store como um arquivo chamado `/data.csv`.

```
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

### <a name="submit-a-data-lake-analytics-job"></a>Enviar um trabalho da Análise Data Lake

1. Selecione **Arquivo** > **Novo** > **Projeto**.

2. Selecione o tipo **projeto U-SQL** e, em seguida, clique em **OK**. O Visual Studio cria uma solução com um arquivo **Script.usql**.

3. Cole o script anterior na janela **Script.usql**.

4. No canto superior esquerdo da janela **Script.usql**, especifique a conta do Data Lake Analytics.

    ![Enviar projeto de U-SQL do Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job.png)

5. No canto superior esquerdo da janela **Script.usql**, selecione **Enviar**.
6. Verifique a **conta do Analytics** e, em seguida, selecione **Enviar**. Os resultados de envio estão disponíveis nas Ferramentas do Data Lake para resultados do Visual Studio após a conclusão do envio.

    ![Enviar projeto de U-SQL do Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job-advanced.png)
7. Para ver a versão mais recente do status do trabalho e atualizar a tela, clique em **Atualizar**. Quando o trabalho é bem-sucedido, ele mostra o **grafo do trabalho**, as **operações de metadados**, o **histórico de estado** e os **diagnósticos**:

    ![Grafo de desempenho de trabalho do Data Lake Analytics do U-SQL do Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

   * **Resumo do trabalho** mostra o resumo do trabalho.   
   * **Detalhes do trabalho** mostra informações mais específicas sobre o trabalho, incluindo o script, os recursos e os vértices.
   * **Grafo do Trabalho** visualiza o andamento do trabalho.
   * **Operações de metadados** mostra todas as ações que foram executadas no catálogo de U-SQL.
   * **Dados** mostra todas as entradas e saídas.
   * **Diagnósticos** fornece uma análise avançada para otimização de desempenho e a execução do trabalho.

### <a name="to-check-job-state"></a>Para verificar o estado do trabalho

1. No Gerenciador de servidores, selecione **Azure** > **Data Lake Analytics**. 
2. Expanda o nome da conta do Data Lake Analytics.
3. Clique duas vezes em **Trabalhos**.
4. Selecione o trabalho enviado anteriormente.

### <a name="to-see-the-output-of-a-job"></a>Para visualizar a saída de um trabalho

1. No Gerenciador de servidores, navegue até o trabalho enviado.
2. Clique na guia **Dados** .
3. Na guia **Saídas do trabalho**, selecione o arquivo `"/data.csv"`.

## <a name="next-steps"></a>Próximas etapas

* [Execute scripts U-SQL em sua estação de trabalho para teste e depuração](data-lake-analytics-data-lake-tools-local-run.md)
* [Depurar código C# em trabalhos U-SQL usando as Ferramentas do Azure Data Lake para Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Usar as Ferramentas do Azure Data Lake para Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
