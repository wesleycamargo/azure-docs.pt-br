---
title: "Testar e depurar trabalhos do U-SQL usando execução local e o SDK para U-SQL do Azure Data Lake | Microsoft Docs"
description: "Saiba como usar as Ferramentas do Azure Data Lake para Visual Studio e o SDK para U-SQL do Azure Data Lake para testar e depurar trabalhos do U-SQL em sua estação de trabalho local."
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
ms.sourcegitcommit: 9d088a3afd8830c80462099cc869f1493d09fb41
ms.openlocfilehash: e0385315434ac7a10e121f88469aaaec45160b86


---
# <a name="test-and-debug-u-sql-jobs-using-local-run-and-the-azure-data-lake-u-sql-sdk"></a>Testar e depurar trabalhos do U-SQL usando execução local e o SDK para U-SQL do Azure Data Lake

Saiba como usar as Ferramentas do Azure Data Lake para Visual Studio e o SDK para U-SQL do Azure Data Lake para testar e depurar trabalhos do U-SQL em sua estação de trabalho local.  Esses dois recursos de execução local possibilitam executar trabalhos do U-SQL em sua estação de trabalho assim como você faz no Serviço do Azure Data Lake. Esses recursos economizam tempo de teste e depuração dos trabalhos do U-SQL.

Pré-requisitos: 

- Uma conta do Azure Data Lake Analytics. Veja [Introdução ao Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md). 
- As Ferramentas do Azure Data Lake para Visual Studio.  Veja [Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md). 
- A experiência em desenvolvimento de script U-SQL. Veja [Introdução ao Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md). 


## <a name="understand-data-root-and-file-path"></a>Compreender o caminho do arquivo e a raiz de dados

Tanto a execução local quanto o SDK para U-SQL exigem uma pasta raiz de dados. Raiz de dados é um "repositório local" para a conta de computador local. Ela equivale à conta do Data Lake Store de uma conta do Data Lake Analytics no Azure. Alternar para uma pasta raiz de dados diferente é como alternar para uma conta de repositório diferente. Se quiser acessar dados frequentemente compartilhados com diferentes pastas raiz de dados, você deverá usar caminhos absolutos nos scripts ou criar links simbólicos do sistema de arquivos (por exemplo, mklink ou NTFS) sob essa pasta raiz de dados que apontem para os dados compartilhados. 

A pasta raiz de dados é usada para:

- Armazenar metadados, incluindo bancos de dados, tabelas, TVFs, assemblies etc.
- Pesquisar os caminhos de entrada e saída que são definidos como caminhos relativos no U-SQL. Usar caminhos relativos facilita a implantação dos projetos U-SQL no Azure.

Você pode usar o caminho relativo e o caminho absoluto local em scripts U-SQL, e o caminho relativo será relativo ao caminho da pasta raiz de dados especificado. É recomendável usar "/" como o separador de caminho para tornar seus scripts compatíveis com o lado do servidor. A seguir, alguns exemplos de caminhos relativos e o respectivo caminho absoluto equivalente. Nesses exemplos, "C:\LocalRunDataRoot" é a raiz de dados:

|Caminho relativo|Caminho absoluto|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

## <a name="use-local-run-from-visual-studio"></a>Usar execução local no Visual Studio

As Ferramentas do Data Lake para Visual Studio proporcionam experiência de execução local do U-SQL no Visual Studio. Usando esse recurso, você pode:

- Executar o script U-SQL localmente com os assemblies de C#.
- Depurar o assembly de C# localmente.
- Criar, exibir e excluir catálogos do U-SQL (bancos de dados locais, assemblies, esquemas e tabelas) no Gerenciador de servidores. O catálogo local também pode ser encontrado no Gerenciador de Servidores.

    ![Catálogo local da execução local das Ferramentas do Data Lake para Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-local-catalog.png)

O instalador das Ferramentas do Data Lake cria uma pasta "C:\LocalRunRoot" para ser usada como a pasta raiz de dados padrão. O paralelismo da execução local padrão é 1. 

### <a name="to-configure-local-run-in-visual-studio"></a>Para configurar a execução local no Visual Studio

1. Abra o Visual Studio.
2. Abra o **Gerenciador de Servidores**.
3. Expanda **Azure**, **Data Lake Analytics**.
4. Clique no menu **Data Lake** e, em seguida, em **Opções e Configurações**. 
5. Na árvore à esquerda, expanda **Azure Data Lake** e, em seguida, **Geral**.

    ![Definir configurações de execução local das Ferramentas do Data Lake para Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-configure.png)

É necessário um projeto U-SQL do Visual Studio para realizar a execução local. Essa parte é diferente de executar scripts U-SQL no Azure.

### <a name="to-run-a-u-sql-script-locally"></a>Para executar um script U-SQL localmente
1. No Visual Studio, abra o projeto U-SQL.   
2. Clique com o botão direito do mouse em um script U-SQL no Gerenciador de Soluções e clique em **Enviar Script**. Selecione (local) como a Conta do Analytics para executar o script localmente.
3. Você pode também clicar na conta (local) na parte superior da janela do script e clicar em **Enviar** (ou usar as teclas de acesso **CTRL + F5**).

    ![Enviar trabalhos de execução local das Ferramentas do Data Lake para Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-submit-job.png)



## <a name="use-local-run-from-data-lake-u-sql-sdk"></a>Usar execução local no SDK para U-SQL do Data Lake
 
Além de executar scripts U-SQL localmente usando o Visual Studio, você também pode usar o SDK para U-SQL do Azure Data Lake para executá-los localmente com interfaces de programação e linha de comando, pelas quais é possível dimensionar o teste local do U-SQL.

### <a name="install-the-sdk"></a>Instalar o SDK

O SDK para U-SQL do Data Lake exige as seguintes dependências:

- [Microsoft .Net Framework 4.6 ou mais recente](https://www.microsoft.com/en-us/download/details.aspx?id=17851).
- Microsoft Visual C++ 14 e Windows SDK 10.0.10240.0 ou mais recente. Existem duas maneiras possíveis de obtê-los:

    - Instale o Visual Studio ([Visual Studio Community Edition](https://developer.microsoft.com/downloads/vs-thankyou)). Você deve ter uma pasta "\Windows Kits\10" na pasta de arquivos de programas, por exemplo, "C:\Arquivos de Programas (x86)\Windows Kits\10\"; você também deve encontrar a versão do SDK para Windows 10 em "\Windows Kits\10\Lib". Se você não visualizar essas pastas, reinstale o Visual Studio e certifique-se de marcar o SDK para Windows 10 na instalação. Dessa forma, o script do compilador local do U-SQL encontrará essas dependências automaticamente.

    ![SDK para Windows 10 da execução local das Ferramentas do Data Lake para Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)
 
    - Como alternativa, instale as [Ferramentas do Data Lake para Visual Studio](http://aka.ms/adltoolsvs). Os arquivos pré-empacotados do SDK do Windows e VC++ podem ser encontrados em C:\Arquivos de Programas (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK. Nesse caso, o compilador local do U-SQL não poderá encontrar as dependências automaticamente, você precisará especificar o caminho de CppSDK para ele. Você pode copiar os arquivos para outro local ou usá-lo como está. Em seguida, você pode optar por definir uma variável de ambiente "SCOPE_CPP_SDK" para o diretório ou especificar o argumento "-CppSDK" com esse diretório na linha de comando do aplicativo auxiliar da execução local. 

Depois de instalar o SDK, você deve executar as seguintes etapas de configuração:

- Defina a variável de ambiente **SCOPE_CPP_SDK**

    Se obtiver o SDK do Windows e Microsoft Visual C++ instalando as Ferramentas do Data Lake para Visual Studio, verifique se você tem a seguinte pasta:

        C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK

    Defina uma nova variável de ambiente chamada "SCOPE_CPP_SDK" para pontar para esse diretório. Ou copie a pasta para outro local e especifique "SCOPE_CPP_SDK" dessa forma.

    Além de definir a variável de ambiente, você também pode especificar o argumento "-CppSDK" ao usar a linha de comando. Esse argumento substitui a variável de ambiente CppSDK padrão. 

- Defina a variável de ambiente **LOCALRUN_DATAROOT**

    Defina uma nova variável de ambiente chamada "LOCALRUN_DATAROOT" apontando para a raiz de dados. 

    Além de definir a variável de ambiente, você também pode especificar o argumento "-DataRoot" com o caminho raiz de dados usando a linha de comando. Esse argumento substitui a variável de ambiente DataRoot padrão. E você precisa adicionar esse argumento a cada linha de comando que está executando para que seja possível usar o mesmo novo Data Root substituto para todas as operações. 

### <a name="using-the-sdk-from-command-line"></a>Usando o SDK na linha de comando

#### <a name="the-command-line-interface-of-the-helper-application"></a>A interface de linha de comando do aplicativo auxiliar

O LocalRunHelper.exe do SDK é o aplicativo auxiliar de linha de comando que fornece interfaces para a maioria das funções de execução local frequentemente usadas. Observe que as opções de argumentos e de comando diferenciam maiúsculas de minúsculas. Para invocá-lo:

    LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]

Execute "LocalRunHelper.exe" sem argumentos ou com a opção **help** para mostrar as informações de ajuda:

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

O aplicativo auxiliar retorna **0** no caso de êxito e **-1** no caso de falha. Por padrão, o auxiliar produzirá todas as mensagens para o console atual.  No entanto, a maioria dos comandos dá suporte ao argumento opcional **-MessageOut path_to_log_file**, que redirecionará as saídas para um arquivo de log.


### <a name="the-sdk-usage-samples"></a>Exemplos de uso do SDK

#### <a name="compile-and-run"></a>Compilar e executar

O comando "run" é usado para compilar o script e executar resultados compilados. Seus argumentos de linha de comando são uma combinação de "compile" e "run".

    LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]

Veja um exemplo:

    LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose

Além de combinar "compile" e "run", você pode compilar e executar os executáveis compilados separadamente. 

#### <a name="compile-u-sql-script"></a>Compilar script U-SQL

O comando "compile" é usado para compilar um script U-SQL para executáveis. 

    LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]

Argumentos opcionais para compilação. Você pode saber mais sobre como trabalhar com diretório (-WorkDir) no apêndice.

|Argumento|Descrição|
|--------|-----------|
|-CppSDK [valor padrão '']|Diretório do CppSDK|
|-DataRoot [valor padrão '']|Raiz de dados para dados e metadados, padronizada para a variável de ambiente 'LOCALRUN_DATAROOT'|
|-MessageOut [valor padrão '']|Mensagens de despejo no console para um arquivo|
|-Shallow [valor padrão 'False']|Compilação superficial, apenas uma verificação de sintaxe do script e retorno.|
|-WorkDir [valor padrão 'D:\localrun\t\ScopeWorkDir']|Diretório para uso e saídas do compilador. Veja mais no Apêndice — Diretório de trabalho.|

Argumentos opcionais para assemblies e code-behind:

|Argumento|Descrição|
|--------|-----------|
|-CodeBehind [valor padrão 'False']|O script tem code-behind .cs que será compilado e registrado automaticamente como objeto UDO|
|-References [valor padrão '']|Lista de caminhos para os assemblies de referência extra ou arquivos de dados do code-behind, separados por ';'|
|-UseDatabase [valor padrão 'master']|Banco de dados a ser usado para registro do assembly temporário do code-behind|
|-UdoRedirect [valor padrão 'False']|Gerar configuração de redirecionamento do assembly UDO que informa ao tempo de execução do .Net para investigar assemblies dependentes do diretório de saída compilado primeiro quando UDO é chamado|

Veja alguns exemplos de uso:

Compilar script U-SQL:

    LocalRunHelper compile -Script d:\test\test1.usql

Compilar script U-SQL e definir a pasta raiz de dados. Observe que isso substituirá a variável de ambiente definida.

    LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot

Compilar script U-SQL e definir o diretório de trabalho, o assembly de referência e o banco de dados.

    LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB

#### <a name="execute-compiled-result"></a>Executar o resultado compilado

O comando "execute" é usado para executar os resultados compilados.   

    LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]

Argumentos opcionais:

|Argumento|Descrição|
|--------|-----------|
|-DataRoot [valor padrão '']|Raiz de dados para execução de metadados, padronizada para a variável de ambiente 'LOCALRUN_DATAROOT'|
|-MessageOut [valor padrão '']|Mensagens de despejo no console para um arquivo|
|-Parallel [valor padrão '1']|Executar as etapas de execução local geradas com o nível de paralelismo especificado|
|-Verbose [valor padrão 'False']|Mostrar saídas detalhadas do tempo de execução|

Veja um exemplo de uso:

    LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5

## <a name="using-the-sdk-with-programming-interface"></a>Usando o SDK com a interface de programação

As interfaces de programação estão todas localizadas no assembly "Microsoft.Analytics.LocalRun", pelo qual você pode integrar a funcionalidade do SDK para U-SQL e a estrutura de teste de C# para dimensionar seu teste local do script U-SQL. Um novo documento para isso será lançado em breve. Veja o apêndice para obter mais informações sobre as interfaces.

## <a name="appendix"></a>Apêndice

### <a name="working-directory"></a>Diretório de trabalho

Na execução local do script U-SQL, um diretório de trabalho é criado durante a compilação.  Além das saídas de compilação, os arquivos de tempo de execução necessários para execução local também são copiados em sombra para esse diretório de trabalho.   Se o argumento **-WorkDir** não for fornecido na linha de comando, o diretório de trabalho padrão "ScopeWorkDir" será criado sob o diretório atual. Os arquivos sob o diretório de trabalho são mostrados abaixo.

|Diretório/arquivo|Definição|Descrição|
|--------------|----------|-----------|
|ScopeWorkDir|O diretório de trabalho|Pasta raiz|
|C6A101DDCB470506|Cadeia de caracteres de hash da versão do tempo de execução|Cópia de sombra dos arquivos de tempo de execução necessários para execução local|
|\.\Script_66AE4909AA0ED06C|Nome do script + cadeia de caracteres de hash do caminho do script|Saídas da compilação e log das etapas de execução|
|\.\.\\_\_script\_\_.abr|Saída do compilador|O arquivo de álgebra|
|\.\.\\_\_ScopeCodeGen\_\_.*|Saída do compilador|Código gerenciado gerado|
|\.\.\\_\_ScopeCodeGenEngine\_\_.*|Saída do compilador|Código nativo gerado|
|\.\.\referenced_assemblies|Referência de assembly|Arquivos do ASSEMBLY DE REFERÊNCIA|
|\.\.\deployed_resources|Implantação de recursos|Arquivos da IMPLANTAÇÃO DE RECURSOS|
|\.\.\xxxxxxxx.xxx[1..n]_*.*|Log de execução|Log das etapas de execução|

### <a name="programming-interfaces-for-azure-data-lake-u-sql-sdk"></a>Interfaces de programação do SDK para U-SQL do Azure Data Lake

As interfaces de programação estão todas localizadas no assembly "Microsoft.Analytics.LocalRun".

#### <a name="microsoftanalyticslocalrunconfiguration"></a>Microsoft.Analytics.LocalRun.Configuration
Classe do parâmetro de configuração da compilação

**Construtor**

public Configuration(string rootPath)

|Parâmetro|Tipo|Descrição|
|---------|----|-----------|
|rootPath|System.String|Caminho para o contexto atual do diretório de trabalho. Se WorkingDirectory não estiver definido, a pasta de trabalho padrão será rootPath + "ScopeWorkDir".|

**Propriedades**

|Nome|Descrição|
|----|-----------|
|CppSDK|Onde encontrar Cpp SDK, padronizado para usar a configuração padrão do sistema |
|DataDirectory|Onde as tabelas, os assemblies e os dados de entrada/saída são salvos, padronizado para ScopeWorkDir\DataDir |
|GenerateUdoRedirect|Se quisermos gerar a configuração de substituição do redirecionamento de carregamento do assembly|
|WorkingDirectory|Diretório de trabalho do compilador, padronizado para ScopeWorkDir, caso não tenha sido definido|


#### <a name="microsoftanalyticslocalrunlocalcompiler"></a>Microsoft.Analytics.LocalRun.LocalCompiler
A classe do compilador local do U-SQL

**Construtor**

public LocalCompiler(Configuration configuration)

|Parâmetro|Tipo|Descrição|
|---------|----|-----------|
|configuração|Microsoft.Analytics.LocalRun.Configuration||

**Método**

public bool Compile( string script, string filePath, bool shallow, out CommonCompileResult result )

|Parâmetro|Tipo|Descrição|
|---------|----|-----------|
|script|System.String|cadeia de caracteres do script de entrada|
|filePath|System.String|caminho do arquivo de script, definido como nulo para usar o padrão|
|shallow|System.Boolean|compilação superficial (apenas verificação de sintaxe) ou compilação completa|
|result|Microsoft.Cosmos.ClientTools.Shared.CommonCompileResult|Resultados detalhados da compilação|
|Valor de retorno|System.Boolean|true: nenhum erro crítico na compilação. <br>false: erro crítico na compilação|

#### <a name="microsoftanalyticslocalrunlocalrunner--idisposable"></a>Microsoft.Analytics.LocalRun.LocalRunner : IDisposable
A classe do executor local do U-SQL

**Construtor**

public LocalRunner( string algebraFilePath, string dataRoot, Action<string> postMessage = null )

|Parâmetro|Tipo|Descrição|
|---------|----|-----------|
|algebraFilePath|System.String|Caminho para o arquivo de álgebra|
|dataRoot|System.String|Caminho para DataRoot|
|postMessage (Optional)|System.Action<String>|manipulador de log para andamento|

public LocalRunner( string algebraFilePath, string dataRoot, string cachePath, string runtimePath, string tempPath, string logPath, Action<Object, ExecutionStatusBase. ExecutionEventArgs> execEventHandler, Object eventContext, Action<string> postMessage = null )

|Parâmetro|Tipo|Descrição|
|---------|----|-----------|
|algebraFilePath|System.String|Caminho para o arquivo de álgebra|
|dataRoot|System.String|Caminho para DataRoot|
|cachePath|System.String|Caminho para o diretório de resultado da compilação, definido como nulo para usar o padrão onde está localizado o arquivo de álgebra|
|runtimePath|System.String|Caminho para o diretório do tempo de execução copiado em sombra, definido como nulo para usar o padrão onde está localizado o diretório pai de cachePath|
|tempPath|System.String|Caminho do armazenamento temporário, somente para uso interno, definido como nulo|
|logPath|System.String|Caminho onde os logs de execução serão gravados, definido como nulo para usar o padrão como cachePath|
|execEventHandler|System. Action<Object, ExecutionStatusBase.ExecutionEventArgs>|manipulador de notificação de eventos para alteração do status da execução|
|eventContext|System. Objeto|contexto para o manipulador de eventos|
|postMessage (Optional)|System.Action<String>|manipulador de log para andamento|

**Propriedades**

|Nome|Descrição|
|----|-----------|
|AlgebraPath|Caminho para o arquivo de álgebra|
|CachePath|O caminho do cache de resultados do compilador onde binários gerados estão localizados|
|CompletedSteps|Número etapas concluídas|
|DataRoot|DataRoot de metadados|
|LastErrorMessage|(Herdada de ExecutionStatusBase.)|
|LogPath|Local de armazenamento do arquivo de log. Setter criará o diretório, se não houver um. O caminho do log criado anteriormente não será apagado|
|OutputHeader|Cabeçalho de esquema de despejo nas saídas textuais|
|Paralelismo|Paralelismo, padronizado para processadores lógicos — 1. A alteração desse número após o início resultará em uma exceção|
|Andamento|Andamento da execução na escala de 0 a 100%|
|RuntimePath|Onde os arquivos de tempo de execução estão localizados. Dever ser um diretório acima de CachePath quando se tratar da cópia de sombra feita pelo compilador|
|Status|Status da execução <br>enum ExecutionStatusBase.ExecutionStatus <br>{ <br>Initialized, // initialize <br>Running,     // it is running,  WaitOne only check the event in this state <br>Success,     // it completed successfully <br>Error,       // it failed <br>}|
|TotalSteps|Número total de etapas a serem executadas. Valor válido disponível somente após a criação do vértice DAG|
|Detalhado|Detalhamento durante a execução|

**Método**

|Método|Descrição|
|------|-----------|
|Cancel()|Cancelar a álgebra em execução <br>Valor de retorno <br>Tipo: booliano <br>false: falha ao cancelar devido a erro, verifique LastErrorMessage para obter detalhes|
|Start()|Começar a execução de álgebra <br>Valor de retorno <br>Tipo: booliano <br>false: falha ao iniciar devido a erro, verifique LastErrorMessage para obter detalhes|
|WaitOne() <br>WaitOne(Int32) <br>WaitOne(TimeSpan) <br>WaitOne(Int32, Boolean) <br>WaitOne(TimeSpan, Boolean)|Aguardar a conclusão, confira WaitHandle.WaitOne|
|Dispose()||


## <a name="see-also"></a>Consulte também

* Para obter uma visão geral da Análise Data Lake, consulte [Visão geral da Análise Data Lake do Azure](data-lake-analytics-overview.md).
* Para começar a desenvolver aplicativos U-SQL, consulte [Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Para aprender a usar o U-SQL, veja [Introdução à linguagem U-SQL da Análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
* Para obter as tarefas de gerenciamento, confira [Gerenciar o Azure Data Lake Analytics usando o portal do Azure](data-lake-analytics-manage-use-portal.md).
* Para registrar em log as informações de diagnóstico, veja [Acessando os logs de diagnóstico para o Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)
* Para ver uma consulta mais complexa, consulte [Analisar logs de site usando a Análise Data Lake do Azure](data-lake-analytics-analyze-weblogs.md).
* Para ver detalhes do trabalho, confira [Usar o Navegador de Trabalhos e o Modo de Exibição de Trabalho para trabalhos do Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md)
* Para usar o modo de exibição de execuções de vértice, veja [Usar o Modo de Exibição de Execução de Vértice nas Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)


<!--HONumber=Nov16_HO4-->


