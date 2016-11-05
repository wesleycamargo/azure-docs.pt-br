---
title: Analisar logs de site usando a Análise Data Lake do Azure | Microsoft Docs
description: 'Saiba como analisar os logs de site usando a Análise Data Lake. '
services: data-lake-analytics
documentationcenter: ''
author: edmacauley
manager: jhubbard
editor: cgronlun

ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/16/2016
ms.author: edmaca

---
# Tutorial: analise os logs do site usando a Análise Data Lake do Azure
Saiba como analisar os logs do site usando a Análise Data Lake, principalmente para descobrir quais referenciadores resultaram em erros ao tentar visitar o site.

> [!NOTE]
> Se a sua intenção é apenas ver o aplicativo funcionando, você pode economizar tempo consultando os [tutoriais interativos da Análise Data Lake do Azure](data-lake-analytics-use-interactive-tutorials.md). Esse tutorial é baseado no mesmo cenário e no mesmo código. A finalidade desse tutorial é oferecer aos desenvolvedores a experiência de criar e executar o aplicativo da Análise Data Lake do início ao fim.
> 
> 

## Pré-requisitos:
* **Visual Studio 2015, Visual Studio 2013 atualização 4 ou Visual Studio 2012 com Visual C++ instalado**.
* **SDK do Microsoft Azure para .NET versão 2.5 ou posterior**. Instale-o usando o [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx).
* **[Ferramentas do Data Lake para Visual Studio](http://aka.ms/adltoolsvs)**.
  
    Quando as Ferramentas do Data Lake para Visual Studio forem instaladas, você verá um menu do **Data Lake** no Visual Studio:
  
    ![Menu U-SQL do Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)
* **Conhecimento básico da Análise Data Lake e das Ferramentas do Data Lake para Visual Studio**. Para começar. confira:
  
  * [Introdução à Análise do Azure Data Lake usando o Portal do Azure](data-lake-analytics-get-started-portal.md).
  * [Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* **Uma conta da Análise Data Lake.** Confira [Criar uma conta da Análise Data Lake do Azure](data-lake-analytics-get-started-portal.md#create_adl_analytics_account).
  
    As Ferramentas do Data Lake não dão suporte à criação de contas da Análise Data Lake. Portanto, você precisa criá-la usando o Portal do Azure, Azure PowerShell, SDK do .NET ou a CLI do Azure.
* **Carregue os dados de exemplo na conta da Análise Data Lake.** Confira [Carregar SearchLog.tsv na conta padrão do Repositório Data Lake](data-lake-analytics-get-started-portal.md#update-data-to-the-default-adl-storage-account).
  
    Para executar o trabalho da Análise Data Lake, você precisará de alguns dados. Embora as Ferramentas do Data Lake deem suporte ao carregamento de dados, você poderá usar o portal para carregar os dados de exemplo para deixar o tutorial mais fácil de acompanhar.

## Conecte-se ao Azure
Antes de criar e testar qualquer script U-SQL, é preciso se conectar ao Azure.

**Para conectar-se à Análise Data Lake**

1. Abra o Visual Studio.
2. A partir do menu **Data Lake**, clique em **Opções e Configurações**.
3. Clique em **Entrar** ou **Alterar Usuário** se alguém estiver conectado e siga as instruções.
4. Clique em **OK** para fechar a caixa de diálogo Opções e Configurações.

**Para procurar suas contas da Análise Data Lake**

1. No Visual Studio, abra o **Gerenciador de Servidores** pressionando **CTRL+ALT+S**.
2. No **Gerenciador de Servidores**, expanda **Azure** e **Análise Data Lake**. Você deverá ver uma lista das suas contas da Análise Data Lake, caso haja alguma. Não é possível criar contas da Análise Data Lake a partir do estúdio. Para criar uma conta, veja [Introdução à Análise do Azure Data Lake usando o Portal do Azure](data-lake-analytics-get-started-portal.md) ou [Introdução à Análise do Azure Data Lake usando o Azure PowerShell](data-lake-analytics-get-started-powershell.md).

## Desenvolver aplicativos U-SQL
Um aplicativo U-SQL é basicamente um script U-SQL. Para saber mais sobre o U-SQL, consulte [Introdução ao U-SQL](data-lake-analytics-u-sql-get-started.md).

Você pode adicionar operadores definidos pelo usuário a este aplicativo. Para obter mais informações, veja [Desenvolver operadores U-SQL definidos pelo usuário para trabalhos da Análise do Data Lake](data-lake-analytics-u-sql-develop-user-defined-operators.md).

**Para criar e enviar um trabalho da Análise Data Lake**

1. No menu **Arquivo**, clique em **Novo** e em **Projeto**.
2. Selecione o tipo Projeto do U-SQL.
   
    ![novo projeto de U-SQL do Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)
3. Clique em **OK**. O Visual Studio cria uma solução com um arquivo Script.usql.
4. Insira o seguinte script no arquivo Script.usql:
   
        // Create a database for easy reuse, so you don't need to read from a file every time.
        CREATE DATABASE IF NOT EXISTS SampleDBTutorials;
   
        // Create a Table valued function. TVF ensures that your jobs fetch data from the weblog file with the correct schema.
        DROP FUNCTION IF EXISTS SampleDBTutorials.dbo.WeblogsView;
        CREATE FUNCTION SampleDBTutorials.dbo.WeblogsView()
        RETURNS @result TABLE
        (
            s_date DateTime,
            s_time string,
            s_sitename string,
            cs_method string,
            cs_uristem string,
            cs_uriquery string,
            s_port int,
            cs_username string,
            c_ip string,
            cs_useragent string,
            cs_cookie string,
            cs_referer string,
            cs_host string,
            sc_status int,
            sc_substatus int,
            sc_win32status int,
            sc_bytes int,
            cs_bytes int,
            s_timetaken int
        )
        AS
        BEGIN
   
            @result = EXTRACT
                s_date DateTime,
                s_time string,
                s_sitename string,
                cs_method string,
                cs_uristem string,
                cs_uriquery string,
                s_port int,
                cs_username string,
                c_ip string,
                cs_useragent string,
                cs_cookie string,
                cs_referer string,
                cs_host string,
                sc_status int,
                sc_substatus int,
                sc_win32status int,
                sc_bytes int,
                cs_bytes int,
                s_timetaken int
            FROM @"/Samples/Data/WebLog.log"
            USING Extractors.Text(delimiter:' ');
            RETURN;
        END;
   
        // Create a table for storing referrers and status
        DROP TABLE IF EXISTS SampleDBTutorials.dbo.ReferrersPerDay;
        @weblog = SampleDBTutorials.dbo.WeblogsView();
        CREATE TABLE SampleDBTutorials.dbo.ReferrersPerDay
        (
            INDEX idx1
            CLUSTERED(Year ASC)
            PARTITIONED BY HASH(Year)
        ) AS
   
        SELECT s_date.Year AS Year,
            s_date.Month AS Month,
            s_date.Day AS Day,
            cs_referer,
            sc_status,
            COUNT(DISTINCT c_ip) AS cnt
        FROM @weblog
        GROUP BY s_date,
                cs_referer,
                sc_status;
   
    Para entender o U-SQL, consulte [Introdução à linguagem da Análise do Date Lake U-SQL](data-lake-analytics-u-sql-get-started.md).
5. Adicione um novo script U-SQL ao seu projeto e digite o seguinte:
   
        // Query the referrers that ran into errors
        @content =
            SELECT *
            FROM SampleDBTutorials.dbo.ReferrersPerDay
            WHERE sc_status >=400 AND sc_status < 500;
   
        OUTPUT @content
        TO @"/Samples/Outputs/UnsuccessfulResponses.log"
        USING Outputters.Tsv();
6. Volte para o primeiro script U-SQL e, ao lado do botão **Enviar**, especifique a conta de análise.
7. No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Script.usql** e clique em **Compilar Script**. Verifique os resultados no painel Saída.
8. No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Script.usql** e clique em **Enviar Script**.
9. Verifique se a **Conta da Análise** é uma conta em que você pode executar o trabalho e clique em **Enviar**. Os resultados do envio e o link do trabalho ficarão disponíveis na janela de resultados das Ferramentas do Date Lake para Visual Studio quando o envio for concluído.
10. Aguarde até o trabalho ser concluído com sucesso. Se houver falha no trabalho, é provável que esteja faltando o arquivo original. Confira a seção Pré-requisitos deste tutorial. Para obter mais informações sobre solução de problemas, veja [Monitorar e solucionar problemas com trabalhos da Análise do Azure Data Lake](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).
    
    Quando o trabalho for concluído, você deverá ver a seguinte tela:
    
    ![a Análise Data Lake analisa logs de site de blogs](./media/data-lake-analytics-analyze-weblogs/data-lake-analytics-analyze-weblogs-job-completed.png)
11. Agora, repita as etapas 7 a 10 para **Script1.usql**.

> [!NOTE]
> Você não pode ler ou gravar em uma tabela de U-SQL que foi criada ou modificada no mesmo script. É por isso que usa dois scripts para este exemplo.
> 
> 

**Para ver a saída do trabalho**

1. No **Gerenciador de Servidores**, expanda **Azure**, expanda **Análise do Data Lake**, expanda sua conta da Análise do Data Lake, expanda **Contas de Armazenamento**, clique com o botão direito do mouse na conta padrão do Repositório do Data Lake e clique em **Gerenciador**.
2. Clique duas vezes em **Exemplos** para abrir a pasta e clique duas vezes em **Saídas**.
3. Clique duas vezes em **UnsuccessfulResponsees.log**.
4. Também é possível clicar duas vezes no arquivo de saída no modo de exibição de gráficos do trabalho a fim de navegar diretamente até a saída.

## Confira também
Para começar a usar a Análise Data Lake com ferramentas diferentes, consulte:

* [Introdução à Análise do Data Lake usando o Portal do Azure](data-lake-analytics-get-started-portal.md)
* [Introdução à Análise Data Lake usando o Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Introdução à Análise Data Lake usando o SDK do .NET](data-lake-analytics-get-started-net-sdk.md)

Para ver mais tópicos de desenvolvimento:

* [Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Introdução à linguagem U-SQL da Análise Data Lake do Azure](data-lake-analytics-u-sql-get-started.md)
* [Desenvolver operadores do U-SQL definidos pelo usuário para trabalhos da Análise Data Lake](data-lake-analytics-u-sql-develop-user-defined-operators.md)

<!---HONumber=AcomDC_0914_2016-->