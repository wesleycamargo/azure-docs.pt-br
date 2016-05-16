<properties 
   pageTitle="Solucionar problemas em trabalhos da Análise do Azure Data Lake usando o Portal do Azure | Azure" 
   description="Saiba como usar o Portal do Azure para solucionar problemas com trabalhos da Análise do Data Lake." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="04/26/2016"
   ms.author="edmaca"/>

# Solucionar problemas em trabalhos da Análise do Azure Data Lake usando o Portal do Azure

Saiba como usar o Portal do Azure para solucionar problemas com trabalhos da Análise do Data Lake.

Neste tutorial, você criará um problema de arquivo de origem ausente e usará o Portal do Azure para solucionar o problema.

**Pré-requisitos**

Antes de começar este tutorial, você deve ter o seguinte:

- **Conhecimento básico do processo de trabalho da Análise Data Lake**. Veja [Introdução à Análise do Azure Data Lake usando o Portal do Azure](data-lake-analytics-get-started-portal.md).
- **Uma conta da Análise Data Lake**. Veja [Introdução à Análise do Azure Data Lake usando o Portal do Azure](data-lake-analytics-get-started-portal.md#create-adl-analytics-account).
- **Copie os dados de exemplo na conta padrão do Repositório Data Lake**. Veja [Preparar dados de origem](data-lake-analytics-get-started-portal.md#prepare-source-data)

##Enviar um trabalho da Análise Data Lake

Agora, você criará um trabalho do U-SQL com um nome de arquivo de origem inválido.

**Para enviar o trabalho**

1. No Portal do Azure, clique em **Microsoft Azure** no canto superior esquerdo.
2. Clique no bloco com o nome da conta da Análise Data Lake. Ele foi fixado aqui quando a conta foi criada. Se a conta não estiver fixada lá, veja [Abrir uma conta da Análise no portal](data-lake-analytics-manage-use-portal.md#access-adla-account).
3. Clique em **Novo Trabalho** no menu superior.
4. Insira um nome de Trabalho e o seguinte script U-SQL:

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv1"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/output/SearchLog-from-adls.csv"
        USING Outputters.Csv();

    O arquivo de origem definido no script é **/Samples/Data/SearchLog.tsv1**, em que ele deverá ser **/Samples/Data/SearchLog.tsv**.
     
5. Clique em **Enviar Trabalho** na parte superior. Um novo painel Detalhes do Trabalho é aberto. Na barra de título, ele mostra o status do trabalho. Leva alguns minutos para ser concluído. Você pode clicar em **Atualizar** para obter o status mais recente.
6. Aguarde até que o status do trabalho seja alterado para **Falha**. Se o trabalho for **Bem-sucedido**, isso ocorreu porque você não removeu a pasta /Samples. Veja a seção **Pré-requisito** no início do tutorial.

Você deve estar se perguntando: por que demora tanto tempo para um trabalho pequeno. Lembre-se de que a Análise Data Lake for criada para processar Big Data. Ela se destaca ao processar uma grande quantidade de dados usando seu sistema distribuído.

Vamos supor que você enviou o trabalho e fechou o portal. Na próxima seção, você aprenderá a solucionar problemas no trabalho.


## Solucionar problemas no trabalho

Na última seção, você enviou um trabalho e o trabalho falhou.

**Para ver todos os trabalhos**

1. No portal do Azure, clique em **Microsoft Azure** no canto superior esquerdo.
2. Clique no bloco com o nome da conta da Análise Data Lake. O resumo do trabalho é mostrado no bloco **Gerenciamento de Trabalhos**.

    ![Gerenciamento de trabalhos da Análise Azure Data Lake](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)
    
    O Gerenciamento do trabalho mostra rapidamente o status do trabalho. Observe que há um trabalho com falha.
   
3. Clique no bloco **Gerenciamento de Trabalhos** para ver os trabalhos. Os trabalhos são categorizados em **Em execução**, **Em fila** e **Concluído**. Você deverá ver o trabalho com falha na seção **Concluído**. Ele deve ser primeiro na lista. Quando tiver vários trabalhos, é possível clicar em **Filtrar** para ajudar a encontrar os trabalhos.

    ![Trabalhos de filtragem da Análise Azure Data Lake](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)

4. Clique no trabalho com falha na lista para abrir os detalhes do trabalho em uma nova folha:

    ![Trabalho com falha da Análise Azure Data Lake](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)
    
    Observe o botão **Enviar novamente**. Depois de corrigir o problema, você poderá enviar novamente o trabalho.

5. Clique na parte realçada na captura de tela anterior para abrir os detalhes do erro. Você verá algo semelhante ao seguinte:

    ![Detalhes do trabalho com falha da Análise Azure Data Lake](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    Isso indica que a pasta de origem não foi encontrada.
    
6. Clique em **Duplicar Script**.
7. Atualize o caminho **FROM** para o seguinte:

    "/Samples/Data/SearchLog.tsv"

8. Clique em **Enviar Trabalho**.


##Consulte também

- [Visão geral da Análise Azure Data Lake](data-lake-analytics-overview.md)
- [Introdução à Análise Azure Data Lake usando o Azure PowerShell](data-lake-analytics-get-started-powershell.md)
- [Introdução ao U-SQL da Análise Azure Data Lake usando o Visual Studio](data-lake-analytics-u-sql-get-started.md)
- [Gerenciar a Análise do Azure Data Lake usando o Portal do Azure](data-lake-analytics-manage-use-portal.md)

<!---HONumber=AcomDC_0504_2016-->