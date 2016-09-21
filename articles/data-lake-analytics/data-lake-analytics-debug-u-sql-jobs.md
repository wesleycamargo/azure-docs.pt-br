<properties 
   pageTitle="Depurar trabalhos do U-SQL | Microsoft Azure" 
   description="Saiba como depurar vértice com falha do U-SQL usando o Visual Studio. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/02/2016"
   ms.author="jgao"/>



#Depurar o código C# em U-SQL para trabalhos do Data Lake Analytics 

Saiba como usar as ferramentas do Visual Studio do Azure Data Lake para depurar trabalhos com falhas do U-SQL causadas por bugs no código do usuário.

A ferramenta Visual Studio permite que você baixe o código compilado e os dados de vértice necessários do cluster para rastrear e depurar trabalhos com falha.

Sistemas de big data geralmente fornecem o modelo de extensibilidade por meio de linguagens, como Java, C#, Python, etc. Muitos desses sistemas fornecem informações de depuração de tempo de execução limitadas, o que torna difícil depurar erros de tempo de execução no código personalizado. As ferramentas do Visual Studio mais recentes vêm com um recurso chamado "Falha na depuração de vértice". Usando esse recurso, você pode baixar os dados de tempo de execução do Azure para a estação de trabalho local para que você possa depurar com código C# personalizado com falha usando o mesmo tempo de execução e os dados de entrada exatos da nuvem. Depois que os problemas forem corrigidos, você poderá executar novamente o código revisado no Azure por meio das ferramentas.

Para uma apresentação em vídeo desse recurso, consulte [Depurar seu código personalizado no Azure Data Lake Analytics](https://mix.office.com/watch/1bt17ibztohcb).

>[AZURE.NOTE] O Visual Studio pode travar ou falhar se você não tiver estas duas atualizações de janelas: [Atualização 2 Redistribuível do Microsoft Visual C++ 2015](https://www.microsoft.com/download/details.aspx?id=51682), [Tempo de Execução do Universal C para Windows](https://www.microsoft.com/download/details.aspx?id=50410&wa=wsignin1.0).


##Pré-requisitos
-	Ter lido o artigo de [Introdução](data-lake-analytics-data-lake-tools-get-started.md).

## Criar e configurar projetos de depuração

Ao abrir um trabalho com falha na ferramenta Data Lake Visual Studio, você receberá um alerta. As informações de erro detalhadas serão mostradas na guia de erros e na barra de alerta amarela na parte superior da janela.

![Vértice para download do visual studio para depuração do U-SQL no Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

**Para baixar o vértice e criar uma solução de depuração**

1.	Abra um trabalho com falha do U-SQL no Visual Studio.
2.	Clique em **Download** para baixar todos os recursos e fluxos de entrada necessários. Clique em **Repetir** em caso de falha no download.
3.	Clique em **Abrir** depois que o download estiver concluído para criar um projeto de depuração local. Uma nova solução do Visual Studio chamada **VertexDebug** com um projeto vazio chamado **LocalVertexHost** será criada.

Se os operadores definidos pelo usuário forem usados por trás do código U-SQL (Script.usql.cs), você deverá criar um projeto C# da Biblioteca de Classes com o código dos operadores definidos pelo usuário, bem como incluir o projeto na Solução VertexDebug.

Se você tiver registrado assemblies .dll no seu banco de dados do Data Lake Analytics, será preciso adicionar código-fonte dos assemblies à Solução VertexDebug.
 
Se você tiver criado uma biblioteca de classes C# separada para o código de U-SQL e registrado assemblies .dll no banco de dados do Data Lake Analytics, você precisará adicionar o projeto C# de origem dos assemblies à solução VertexDebug.

Em alguns casos raros, você pode usar operadores definidos pelo usuário em U-SQL no arquivo code-behind (Script.usql.cs) na solução original. Se você quiser fazê-lo funcionar, precisará criar uma biblioteca C# contendo o código-fonte e alterar o nome do assembly para aquele registrado no cluster. Você pode obter o nome do assembly registrado no cluster, verificando o script que colocou em execução no cluster. Você pode fazer isso abrindo o trabalho U-SQL e clique em "script" no painel do trabalho.

**Para configurar a solução**

1.	No Gerenciador de Soluções, clique com o botão direito do mouse no projeto C# recém-criado por você e depois clique em **Propriedades**.
2.	Defina o caminho de Saída como o caminho do diretório de trabalho do projeto LocalVertexHost. Você pode obter o caminho do Diretório de Trabalho do projeto LocalVertexHost por meio das propriedades LocalVertexHost.
3.	Compile seu projeto C# para colocar o arquivo .pdb no Diretório de Trabalho do projeto LocalVertexHost ou copie o arquivo .pdb nessa pasta manualmente.
4.	Em **Configurações de Exceção**, verifique as Exceções de Common Language Runtime:

![Configuração do visual studio para depuração do U-SQL no Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)
 
##Depurar o trabalho

Depois de ter criado uma solução de depuração baixando o vértice e de ter configurado o ambiente, você poderá iniciar a depuração do código U-SQL.

1.	No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **LocalVertexHost** recém-criado por você, aponte para **Depurar** e clique em **Iniciar nova instância**. O LocalVertexHost deve ser definido como o projeto de Inicialização. Você pode ver a seguinte mensagem de erro pela primeira vez, a qual pode ignorar. Pode levar um minuto para que a tela de depuração seja exibida.
 
    ![Aviso do visual studio para depuração do U-SQL no Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-visual-studio-u-sql-debug-warning.png)

4.	Use a experiência de depuração baseada no Visual Studio (observação, variáveis, etc.) para solucionar o problema.
5.	Depois de ter identificado um problema, corrija o código e recompile o projeto C# antes de testá-lo novamente até que todos os problemas tenham sido resolvidos. Depois que a depuração tiver sido concluída com êxito, a janela de saída mostrará a seguinte mensagem

        The Program ‘LocalVertexHost.exe’ has exited with code 0 (0x0).
 
##Reenviar o trabalho

Depois de concluir a depuração do código U-SQL, você pode reenviar o trabalho que falhou.

1. Registre novos assemblies .dll no seu banco de dados ADLA.

    1.	No Gerenciador de Servidores/Cloud Explorer na ferramenta Data Lake Visual Studio, expanda o nó **Bancos de Dados**
    2.	Clique com o botão direito do mouse em Assemblies para Registrar assemblies.
    3.	Registre os novos assemblies .dll no seu banco de dados ADLA.
 
2.	Ou copie seu código C# para script.usql.cs – arquivo de código por trás do C#.
3.	Reenvie o trabalho.

##Próximas etapas

- [Tutorial: introdução à linguagem U-SQL da Análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md)
- [Tutorial: desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Desenvolver operadores do U-SQL definidos pelo usuário para trabalhos da Análise Azure Data Lake](data-lake-analytics-u-sql-develop-user-defined-operators.md)

<!---HONumber=AcomDC_0907_2016-->