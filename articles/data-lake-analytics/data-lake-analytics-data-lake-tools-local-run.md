---
title: "Testar e depurar trabalhos do U-SQL usando execução local e o SDK para U-SQL do Azure Data Lake | Microsoft Docs"
description: "Saiba como usar as Ferramentas do Azure Data Lake para Visual Studio e o SDK para U-SQL do Azure Data Lake a fim de testar e depurar trabalhos do U-SQL em sua estação de trabalho local."
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: c43fb7b513136bb3ea964419d68f58585bba48fd
ms.openlocfilehash: 348971e07169ae9c155753c7943124e96e6e9597


---
# <a name="test-and-debug-u-sql-jobs-by-using-local-run-and-the-azure-data-lake-u-sql-sdk"></a>Testar e depurar trabalhos do U-SQL usando execução local e o SDK para U-SQL do Azure Data Lake

Você pode usar as Ferramentas do Azure Data Lake para Visual Studio e SDK para U-SQL do Azure Data Lake a fim de executar trabalhos de U-SQL na sua estação de trabalho da mesma forma que faz no serviço Azure Data Lake. Esses dois recursos de execução local economizam tempo no teste e na depuração de seus trabalhos de U-SQL.

Pré-requisitos:

- Uma conta do Azure Data Lake Analytics. Veja [Introdução ao Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md).
- Ferramentas do Azure Data Lake para Visual Studio. Veja [Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- A experiência em desenvolvimento de script U-SQL. Veja [Introdução ao Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md).


## <a name="understand-the-data-root-folder-and-the-file-path"></a>Compreender a pasta raiz de dados e o caminho do arquivo

Tanto a execução local quanto o SDK para U-SQL exigem uma pasta raiz de dados. A pasta raiz de dados é um "repositório local" para a conta de computador local. Ela equivale à conta do Azure Data Lake Store de uma conta do Data Lake Analytics. Alternar para uma pasta raiz de dados diferente é como alternar para uma conta de repositório diferente. Se você quiser acessar dados compartilhados normalmente com pastas raiz de dados diferentes, deverá usar caminhos absolutos em seus scripts. Ou criar links simbólicos de sistema de arquivo (por exemplo, **mklink** em NTFS) na pasta raiz de dados para apontar para os dados compartilhados.

A pasta raiz de dados é usada para:

- Armazenar metadados, incluindo bancos de dados, tabelas, TVFs (funções com valor de tabela) e assemblies.
- Pesquisar os caminhos de entrada e saída que são definidos como caminhos relativos no U-SQL. Usar caminhos relativos facilita a implantação dos projetos U-SQL no Azure.

Você pode usar um caminho relativo e um caminho absoluto local em scripts U-SQL. O caminho relativo é relativo ao caminho da pasta raiz de dados especificado. Recomendamos que você use "/" como o separador de caminho para tornar seus scripts compatíveis com o lado do servidor. A seguir, alguns exemplos de caminhos relativos e os respectivos caminhos absolutos equivalentes. Nesses exemplos, C:\LocalRunDataRoot é a pasta raiz de dados.

|Caminho relativo|Caminho absoluto|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

## <a name="use-local-run-from-visual-studio"></a>Usar execução local no Visual Studio

As Ferramentas do Data Lake para Visual Studio proporcionam experiência de execução local do U-SQL no Visual Studio. Usando esse recurso, você pode:

- Executar um script U-SQL localmente com os assemblies de C#.
- Depurar um assembly de C# localmente.
- Criar, exibir e excluir catálogos do U-SQL (bancos de dados locais, assemblies, esquemas e tabelas) no Gerenciador de servidores. Você também pode encontrar o catálogo local no Gerenciador de Servidores.

    ![Catálogo local da execução local das Ferramentas do Data Lake para Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-local-catalog.png)

O instalador das Ferramentas do Data Lake cria uma pasta C:\LocalRunRoot para ser usada como a pasta raiz de dados padrão. O paralelismo da execução local padrão é 1.

### <a name="to-configure-local-run-in-visual-studio"></a>Para configurar a execução local no Visual Studio

1. Abra o Visual Studio.
2. Abra o **Gerenciador de Servidores**.
3. Expanda **Azure** > **Data Lake Analytics**.
4. Clique no menu **Data Lake** e, em seguida, em **Opções e Configurações**.
5. Na árvore à esquerda, expanda **Azure Data Lake** e, em seguida, **Geral**.

    ![Definir configurações de execução local das Ferramentas do Data Lake para Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-configure.png)

É necessário um projeto U-SQL do Visual Studio para realizar a execução local. Essa parte é diferente de executar scripts U-SQL no Azure.

### <a name="to-run-a-u-sql-script-locally"></a>Para executar um script U-SQL localmente
1. No Visual Studio, abra o projeto U-SQL.   
2. Clique com o botão direito do mouse em um script U-SQL no Gerenciador de Soluções e clique em **Enviar Script**.
3. Selecione **(Local)** como a conta do Analytics para executar o script localmente.
Você pode também clicar na conta **(Local)** na parte superior da janela do script e clicar em **Enviar** (ou usar as teclas de acesso CTRL+F5).

    ![Enviar trabalhos de execução local das Ferramentas do Data Lake para Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-submit-job.png)



## <a name="use-local-run-from-the-data-lake-u-sql-sdk"></a>Usar execução local no SDK para U-SQL do Data Lake

Além de executar scripts U-SQL localmente usando o Visual Studio, você pode usar o SDK para U-SQL do Azure Data Lake para executá-los localmente com interfaces de programação e linha de comando. Você pode dimensionar seu teste local U-SQL com eles.

### <a name="install-the-sdk"></a>Instalar o SDK

O SDK para U-SQL do Data Lake exige as seguintes dependências:

- [Microsoft .NET Framework 4.6 ou mais recente](https://www.microsoft.com/en-us/download/details.aspx?id=17851).
- Microsoft Visual C++ 14 e Windows SDK 10.0.10240.0 ou mais recente. Há duas maneiras de obter isso:

    - Instalar o [Visual Studio Community Edition](https://developer.microsoft.com/downloads/vs-thankyou). Você terá uma pasta \Windows Kits\10 na pasta de arquivos de programas, por exemplo, C:\Arquivos de Programas (x86)\Windows Kits\10\.. Você também deve encontrar a versão do SDK para Windows 10 em \Windows Kits\10\Lib. Se você não vir essas pastas, reinstale o Visual Studio e selecione o SDK do Windows 10 durante a instalação. Dessa forma, o script do compilador local do U-SQL encontrará essas dependências automaticamente.

    ![SDK para Windows 10 da execução local das Ferramentas do Data Lake para Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)

    - Instalar [Ferramentas do Data Lake para Visual Studio](http://aka.ms/adltoolsvs). Você pode encontrar os arquivos pré-empacotados do SDK do Windows e Visual C++ em C:\Arquivos de Programas (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK. Nesse caso, o compilador local U-SQL não pode localizar as dependências automaticamente. Você precisa especificar o caminho CppSDK para ele. Você pode copiar os arquivos para outro local ou usá-los como estão. Em seguida, você pode optar por definir a variável de ambiente **SCOPE_CPP_SDK** como o diretório ou especificar o argumento **-CppSDK** com esse diretório na linha de comando do aplicativo auxiliar da execução local.

Depois de instalar o SDK, você deve executar as seguintes etapas de configuração:

- Defina a variável de ambiente **SCOPE_CPP_SDK**.

    Se obtiver o SDK do Windows e Microsoft Visual C++ instalando as Ferramentas do Data Lake para Visual Studio, verifique se você tem a seguinte pasta:

        C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK

    Defina uma nova variável de ambiente chamada **SCOPE_CPP_SDK** para pontar para esse diretório. Ou copie a pasta para outro local e especifique **SCOPE_CPP_SDK** dessa forma.

    Além de definir a variável de ambiente, você também pode especificar o argumento **-CppSDK** ao usar a linha de comando. Esse argumento substitui a variável de ambiente CppSDK padrão.

- Defina a variável de ambiente **LOCALRUN_DATAROOT**.

    Defina uma nova variável de ambiente chamada **LOCALRUN_DATAROOT** que aponta para a raiz de dados.

    Além de definir a variável de ambiente, você pode especificar o argumento **-DataRoot** com o caminho raiz de dados ao usar a linha de comando. Esse argumento substitui a variável de ambiente de raiz de dados padrão. Você precisa adicionar esse argumento para cada linha de comando em execução para que você possa substituir a variável de ambiente de raiz de dados padrão em todas as operações.

### <a name="use-the-sdk-from-the-command-line"></a>Usar o SDK da linha de comando

#### <a name="command-line-interface-of-the-helper-application"></a>Interface de linha de comando do aplicativo auxiliar

No SDK, LocalRunHelper.exe é o aplicativo auxiliar de linha de comando que fornece interfaces para a maioria das funções de execução local frequentemente usadas. Observe que as opções de argumentos e de comando diferenciam maiúsculas de minúsculas. Para invocá-lo:

    LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]

Execute LocalRunHelper.exe sem argumentos ou com a opção **help** para mostrar as informações de ajuda:

    > LocalRunHelper.exe help

        Command 'help' :  Show usage information
        Command 'compile' :  Compile the script
        Required Arguments :
            -Script param
                    Script File Path
        Optional Arguments :
            -Shallow [default value 'False']
                    Shallow compile

Nas informações de ajuda:

-  **Command** fornece o nome do comando.  
-  **Required Argument** lista argumentos que devem ser fornecidos.  
-  **Optional Argument** lista argumentos que são opcionais e com valores padrão.  Os argumentos boolianos opcionais não têm parâmetros e as respectivas aparências são negativas para o respectivo valor padrão.

#### <a name="return-value-and-logging"></a>Valor de retorno e registro em log

O aplicativo auxiliar retorna **0** em caso de sucesso e **-1** em caso de falha. Por padrão, o auxiliar enviará todas as mensagens para o console atual. No entanto, a maioria dos comandos dá suporte ao argumento opcional **-MessageOut path_to_log_file**, que redireciona as saídas para um arquivo de log.


### <a name="sdk-usage-samples"></a>Exemplos de uso do SDK

#### <a name="compile-and-run"></a>Compilar e executar

O comando **run** é usado para compilar o script e executar resultados compilados. Seus argumentos de linha de comando são uma combinação de **compile** e **run**.

    LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]

Aqui está um exemplo:

    LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose

Além de combinar **compile** e **run**, você pode compilar e executar os executáveis compilados separadamente.

#### <a name="compile-a-u-sql-script"></a>Compilar um script U-SQL

O comando **compile** é usado para compilar um script U-SQL para executáveis.

    LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]

Estes são os argumentos opcionais para compilação:

|Argumento|Descrição|
|--------|-----------|
|-CppSDK [valor padrão '']|Diretório do CppSDK.|
|-DataRoot [valor padrão '']|Raiz de dados para dados e metadados. A variável de ambiente **LOCALRUN_DATAROOT** passa a ser o padrão.|
|-MessageOut [valor padrão '']|Despeje as mensagens do console em um arquivo.|
|-Shallow [valor padrão 'False']|Compilação superficial. Faz apenas uma verificação de sintaxe do script e retorno.|
|-WorkDir [valor padrão 'D:\localrun\t\ScopeWorkDir']|Diretório de uso do compilador e saídas. Para saber mais, confira "Diretório de trabalho" no apêndice.|

Abaixo temos argumentos opcionais para assemblies e code-behind:

|Argumento|Descrição|
|--------|-----------|
|-CodeBehind [valor padrão 'False']|Indicador de que o script tem um code-behind .cs, que será compilado e registrado automaticamente como um UDO (objeto definido pelo usuário)|
|-References [valor padrão '']|Lista de caminhos para os assemblies de referência extra ou arquivos de dados de um code-behind, separados por ';'|
|-UseDatabase [valor padrão 'master']|Banco de dados a ser usado para registro do assembly temporário do code-behind|
|-UdoRedirect [valor padrão 'False']|Configuração de redirecionamento do assembly UDO que informa ao tempo de execução do .NET para investigar assemblies dependentes do diretório de saída compilado primeiro quando UDO é chamado|

Veja alguns exemplos de uso.

Compilar um script U-SQL:

    LocalRunHelper compile -Script d:\test\test1.usql

Compilar um script U-SQL e definir a pasta raiz de dados. Observe que isso substituirá a variável de ambiente do conjunto.

    LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot

Compilar um script U-SQL e definir um diretório de trabalho, o assembly de referência e o banco de dados:

    LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB

#### <a name="execute-compiled-results"></a>Executar resultados compilados

O comando **execute** é usado para executar os resultados compilados.   

    LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]

Estes são os argumentos opcionais:

|Argumento|Descrição|
|--------|-----------|
|-DataRoot [valor padrão '']|Raiz de dados para a execução de metadados. A variável de ambiente **LOCALRUN_DATAROOT** passa a ser o padrão.|
|-MessageOut [valor padrão '']|Despeje as mensagens do console em um arquivo.|
|-Parallel [valor padrão '1']|Indicador para executar as etapas de execução local geradas com o nível de paralelismo especificado.|
|-Verbose [valor padrão 'False']|Indicador para mostrar saídas detalhadas do tempo de execução.|

Aqui está um exemplo de uso:

    LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5

## <a name="use-the-sdk-with-programming-interfaces"></a>Usar o SDK com interfaces de programação

As interfaces de programação estão todas localizadas no assembly "Microsoft.Analytics.LocalRun". Você pode usá-los para integrar a funcionalidade do SDK para U-SQL e a estrutura de teste C# a fim de dimensionar o teste local do script U-SQL. Para saber mais sobre as interfaces, confira o apêndice.

## <a name="appendix"></a>Apêndice

### <a name="working-directory"></a>Diretório de trabalho

Ao executar o script U-SQL localmente, um diretório de trabalho será criado durante a compilação. Além das saídas de compilação, os arquivos de tempo de execução necessários para execução local são copiados em sombra para esse diretório de trabalho. Se o argumento **-WorkDir** não for fornecido na linha de comando, o diretório de trabalho padrão ScopeWorkDir será criado sob o diretório atual. Os arquivos no diretório de trabalho são os seguintes:

|Diretório/arquivo|Definição|Descrição|
|--------------|----------|-----------|
|ScopeWorkDir|Diretório de trabalho|Pasta raiz|
|C6A101DDCB470506|Cadeia de caracteres de hash da versão do tempo de execução|Cópia de sombra dos arquivos de tempo de execução necessários para execução local|
|\.\Script_66AE4909AA0ED06C|Nome do script mais cadeia de caracteres de hash do caminho do script|Saídas da compilação e log das etapas de execução|
|\.\.\\_\_script\_\_.abr|Saída do compilador|Arquivo de álgebra|
|\.\.\\_\_ScopeCodeGen\_\_.*|Saída do compilador|Código gerenciado gerado|
|\.\.\\_\_ScopeCodeGenEngine\_\_.*|Saída do compilador|Código nativo gerado|
|\.\.\referenced_assemblies|Referência de assembly|Arquivos do assembly de referência|
|\.\.\deployed_resources|Implantação de recursos|Arquivos da implantação de recursos|
|\.\.\xxxxxxxx.xxx[1..n]_*.*|Log de execução|Log das etapas de execução|

### <a name="programming-interfaces-for-the-azure-data-lake-u-sql-sdk"></a>Interfaces de programação do SDK para U-SQL do Azure Data Lake

As interfaces de programação estão todas localizadas no assembly "Microsoft.Analytics.LocalRun".

#### <a name="microsoftanalyticslocalrunconfiguration"></a>Microsoft.Analytics.LocalRun.Configuration
Microsoft.Analytics.LocalRun.Configuration é a classe do parâmetro de configuração da compilação.

**Construtor**

public Configuration(string rootPath)

|Parâmetro|Tipo|Descrição|
|---------|----|-----------|
|rootPath|System.String|Caminho para o diretório atual do contexto de trabalho. Se WorkingDirectory não estiver definido, a pasta de trabalho padrão será rootPath mais ScopeWorkDir.|

**Propriedades**

|Nome|Descrição|
|----|-----------|
|CppSDK|Local do CppSDK. se não for a configuração padrão do sistema. |
|DataDirectory|Local onde dados de entrada/saída, assemblies e tabelas são salvos. O padrão é ScopeWorkDir\DataDir. |
|GenerateUdoRedirect|Indicador do nosso desejo de gerar a configuração de substituição do redirecionamento de carregamento do assembly.|
|WorkingDirectory|Diretório de trabalho do compilador. O padrão é ScopeWorkDir.|


#### <a name="microsoftanalyticslocalrunlocalcompiler"></a>Microsoft.Analytics.LocalRun.LocalCompiler
Microsoft.Analytics.LocalRun.LocalCompiler é a classe do compilador local do U-SQL.

**Construtor**

public LocalCompiler(Configuration configuration)

|Parâmetro|Tipo|Descrição|
|---------|----|-----------|
|configuração|Microsoft.Analytics.LocalRun.Configuration||

**Método**

public bool Compile( string script, string filePath, bool shallow, out CommonCompileResult result )

|Parâmetro|Tipo|Descrição|
|---------|----|-----------|
|script|System.String|Cadeia de caracteres do script de entrada.|
|filePath|System.String|Caminho do arquivo de script. Ele foi definido como nulo para usar o padrão.|
|shallow|System.Boolean|Compilação superficial (apenas verificação de sintaxe) ou compilação completa.|
|result|Microsoft.Cosmos.ClientTools.Shared.CommonCompileResult|Resultados detalhados da compilação.|
|Valor de retorno|System.Boolean|True: nenhum erro crítico na compilação. <br>False: erro crítico na compilação.|

#### <a name="microsoftanalyticslocalrunlocalrunner--idisposable"></a>Microsoft.Analytics.LocalRun.LocalRunner : IDisposable
Microsoft.Analytics.LocalRun.LocalRunner: IDisposable é a classe do executor U-SQL local.

**Construtor**

public LocalRunner( string algebraFilePath, string dataRoot, Action<string> postMessage = null )

|Parâmetro|Tipo|Descrição|
|---------|----|-----------|
|algebraFilePath|System.String|Caminho para o arquivo de álgebra|
|dataRoot|System.String|Caminho para DataRoot|
|postMessage (Optional)|System.Action<String>|Manipulador de registro em log para andamento|

public LocalRunner( string algebraFilePath, string dataRoot, string cachePath, string runtimePath, string tempPath, string logPath, Action<Object, ExecutionStatusBase. ExecutionEventArgs> execEventHandler, Object eventContext, Action<string> postMessage = null )

|Parâmetro|Tipo|Descrição|
|---------|----|-----------|
|algebraFilePath|System.String|Caminho para o arquivo de álgebra.|
|dataRoot|System.String|Caminho para DataRoot.|
|cachePath|System.String|Caminho para o diretório de resultado da compilação. Ele foi definido como nulo para usar o padrão, onde se encontra o arquivo de álgebra.|
|runtimePath|System.String|Caminho para o diretório do tempo de execução da cópia de sombra. Ele foi definido como nulo para usar o padrão, onde se encontra o diretório pai do cachePath.|
|tempPath|System.String|Caminho de armazenamento temporário, somente para uso interno. É definido como nulo.|
|logPath|System.String|Caminho onde os logs de execução serão gravados. Ele foi definido como nulo para usar cachePath como padrão.|
|execEventHandler|System. Action<Object, ExecutionStatusBase.ExecutionEventArgs>|Manipulador de notificação de eventos para alteração do status da execução.|
|eventContext|System. Objeto|Contexto para o manipulador de eventos.|
|postMessage (Optional)|System.Action<String>|Manipulador de log para andamento.|

**Propriedades**

|Nome|Descrição|
|----|-----------|
|AlgebraPath|Caminho para o arquivo de álgebra.|
|CachePath|Caminho do cache de resultados do compilador onde binários gerados estão localizados.|
|CompletedSteps|Número etapas concluídas.|
|DataRoot|DataRoot de metadados.|
|LastErrorMessage|(Herdada de ExecutionStatusBase.)|
|LogPath|Local de armazenamento de arquivos de log. O setter criará o diretório se ele não existir. O caminho do log criado anteriormente não será limpo.|
|OutputHeader|Cabeçalho de esquema de despejo nas saídas textuais.|
|Paralelismo|Paralelismo. Processadores lógicos por padrão: 1. A alteração desse item após a inicialização resultará em uma exceção.|
|Andamento|Andamento da execução na escala de 0 a 100%.|
|RuntimePath|Local dos arquivos de tempo de execução. Ele deve estar um diretório acima de CachePath quando é a cópia de sombra feita pelo compilador.|
|Status|Status da execução. <br><br>enum ExecutionStatusBase.ExecutionStatus <br>{ <br>Initialized, // Inicializou. <br>Running,     // Está executando.  WaitOne verifica somente os eventos nesse estado. <br>Success,     // Terminou com sucesso. <br>Error,       // Falhou. <br>}|
|TotalSteps|Número total de etapas a serem executadas. Um valor válido fica disponível apenas depois que o vértice DAG é criado.|
|Detalhado|Detalhamento durante a execução.|

**Método**

|Método|Descrição|
|------|-----------|
|Cancel()|Cancelar a álgebra em execução. <br><br>O tipo de valor de retorno é booliano. <br><br>false: falha ao cancelar devido a erro; verifique LastErrorMessage para obter detalhes.|
|Start()|Começar a execução de álgebra. <br><br>O tipo de valor de retorno é booliano. <br><br>Valor de retorno false: falha ao iniciar devido a erro, verifique LastErrorMessage para obter detalhes.|
|WaitOne() <br>WaitOne(Int32) <br>WaitOne(TimeSpan) <br>WaitOne(Int32, Boolean) <br>WaitOne(TimeSpan, Boolean)|Aguarde a conclusão. Consulte WaitHandle.WaitOne.|
|Dispose()||


## <a name="next-steps"></a>Próximas etapas

* Para obter uma visão geral da Análise do Data Lake, veja [Visão geral da Análise do Azure Data Lake](data-lake-analytics-overview.md).
* Para começar a desenvolver aplicativos U-SQL, consulte [Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Para aprender a usar o U-SQL, veja [Introdução à linguagem U-SQL da Análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
* Para obter as tarefas de gerenciamento, confira [Gerenciar o Azure Data Lake Analytics usando o portal do Azure](data-lake-analytics-manage-use-portal.md).
* Para registrar em log as informações de diagnóstico, veja [Acessando os logs de diagnóstico para o Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Para ver uma consulta mais complexa, consulte [Analisar logs de site usando o Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Para ver detalhes do trabalho, confira [Usar o Navegador de Trabalhos e o Modo de Exibição de Trabalho para trabalhos do Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
* Para usar o modo de exibição de execução de vértice, veja [Usar o Modo de Exibição de Execução de Vértice nas Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).



<!--HONumber=Dec16_HO1-->


