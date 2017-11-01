---
title: 'Ferramentas do Azure Data Lake: Usar as Ferramentas do Azure Data Lake para Visual Studio Code | Microsoft Docs'
description: 'Saiba como usar as Ferramentas do Azure Data Lake para Visual Studio Code para criar, testar e executar scripts U-SQL. '
Keywords: "VSCode, Ferramentas do Azure Data Lake, Execução local, Depuração local, Depuração Local, visualizar arquivo de armazenamento, carregar no caminho de armazenamento, download, upload"
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/10/2017
ms.author: jejiang
ms.openlocfilehash: e724a8db4424a1e608ae7ee5625cd4cc16f6078f
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2017
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Usar as Ferramentas do Azure Data Lake para Visual Studio Code

Saiba como usar as Ferramentas do Azure Data Lake para Visual Studio Code (VS Code) para criar, testar e executar scripts U-SQL. As informações também são abordadas no vídeo a seguir:

<a href="https://www.youtube.com/watch?v=J_gWuyFnaGA&feature=youtu.be"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Pré-requisitos

As ferramentas do Azure Data Lake para VSCode são compatíveis com Windows, Linux e MacOS.  

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).

MacOS e Linux:
- [SDK 2.0 do .NET Core](https://www.microsoft.com/net/download/core). 
- [Mono 5.2.x](http://www.mono-project.com/download/).

## <a name="install-data-lake-tools"></a>Instalar as Ferramentas do Data Lake

Após a instalação dos pré-requisitos, você pode instalar as Ferramentas do Data Lake para VS Code.

**Para instalar as Ferramentas do Data Lake**

1. Abra o Visual Studio Code.
2. Clique em **Extensões** no painel esquerdo. Digite **Azure Data Lake** na caixa de pesquisa.
3. Clique em **Instalar** ao lado de **Ferramentas do Azure Data Lake**. Depois de alguns segundos, o botão **Instalar** será alterado para **Recarregar**.
4. Clique em **Recarregar** para ativar a extensão **Ferramentas do Azure Data Lake**.
5. Clique na **Janela Recarregar** para confirmar. É possível ver as **Ferramentas do Azure Data Lake** no painel Extensões.

    ![Painel de Extensões das Ferramentas do Azure Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

 
## <a name="activate-azure-data-lake-tools"></a>Ativar Ferramentas do Azure Data Lake
Crie um novo arquivo .usql ou abra um arquivo .usql existente para ativar a extensão. 

## <a name="open-the-sample-script"></a>Abrir o script de exemplo
Abra a paleta de comandos (Ctrl + Shift + P) e insira **ADL: Abrir Script de Exemplo**. Isso abre outra instância deste exemplo. Você também pode editar, configurar e enviar o script nessa instância.

## <a name="work-with-u-sql"></a>Trabalhar com o U-SQL

Você precisa abrir um arquivo U-SQL ou uma pasta para trabalhar com o U-SQL.

**Para abrir uma pasta para o projeto U-SQL**

1. No Visual Studio Code, selecione o menu **Arquivo** e, em seguida, selecione **Abrir Pasta**.
2. Especifique uma pasta e selecione **Selecionar Pasta**.
3. Selecione o menu **Arquivo** e selecione **Novo**. Um arquivo Sem título-1 é adicionado ao projeto.
4. Insira o código a seguir no arquivo Sem título-1:

        @departments  = 
            SELECT * FROM 
                (VALUES
                    (31,    "Sales"),
                    (33,    "Engineering"), 
                    (34,    "Clerical"),
                    (35,    "Marketing")
                ) AS 
                      D( DepID, DepName );
         
        OUTPUT @departments
            TO "/Output/departments.csv"
        USING Outputters.Csv();

    O script cria um arquivo departments.csv com alguns dados incluídos na pasta /output.

5. Salve o arquivo como **myUSQL.usql** na pasta aberta. Um arquivo de configuração adltools_settings.json também é adicionado ao projeto.
4. Abra e configure adltools_settings.json com as seguintes propriedades:

    - Conta: uma conta do Data Lake Analytics na sua assinatura do Azure.
    - Banco de dados: um banco de dados em sua conta. O padrão é **mestre**.
    - Esquema: um esquema em seu banco de dados. O padrão é **dbo**.
    - Configurações opcionais:
        - Prioridade: o intervalo de prioridade é de 1 a 1000, sendo que 1 é a prioridade mais alta. O valor padrão é **1000**.
        - Paralelismo: o intervalo de paralelismo é de 1 a 150. O valor padrão é o paralelismo máximo permitido em sua conta do Azure Data Lake Analytics. 
        
        > [!NOTE] 
        > Se as configurações forem inválidas, os valores padrão serão usados.

        ![Arquivo de configuração das Ferramentas do Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-configuration-file.png)

        Uma conta de computador do Data Lake Analytics é necessária para compilar e executar trabalhos em U-SQL. É necessário configurar a conta de computador para poder compilar e executar trabalhos do U-SQL.
    
        Após salvar a configuração, as informações da conta, banco de dados e esquema aparecerão na barra de status na parte inferior esquerda do arquivo .usql correspondente. 
 
 
Comparando com a abertura de um arquivo, quando você abre uma pasta, você pode:

- Usar um arquivo code-behind. No modo de arquivo único, não há suporte para code-behind.
- Usar um arquivo de configuração. Quando você abre uma pasta, os scripts na pasta de trabalho compartilham um único arquivo de configuração.


A compilação do script U-SQL é feita remotamente pelo serviço do Data Lake Analytics. Quando você emite o comando **compile**, o script U-SQL é enviado à sua conta do Data Lake Analytics. Mais tarde, o Visual Studio Code recebe o resultado da compilação. Devido à compilação remota, o Visual Studio Code exige que você liste as informações para se conectar à conta do Data Lake Analytics no arquivo de configuração.

**Para compilar um script U-SQL**

1. Selecione Ctrl + Shift + P para abrir a paleta de comandos. 
2. Insira **ADL: Compilar Script**. Os resultados da compilação aparecem na janela **Saída**. Também é possível clicar com o botão direito do mouse em um arquivo de script e, depois, selecionar **ADL: Compilar Script** para compilar um trabalho em U-SQL. O resultado da compilação aparece no painel **Saída**.
 

**Para enviar um script U-SQL**

1. Selecione Ctrl + Shift + P para abrir a paleta de comandos. 
2. Insira **ADL: Enviar Trabalho**.  Também é possível clicar com o botão direito do mouse em um arquivo de script e, depois, selecionar **ADL: Enviar Trabalho**. 

Depois de enviar um trabalho em U-SQL, os logs de envio aparecerão na janela **Saída** no VS Code. Se o envio for bem-sucedido, a URL do trabalho também será exibida. É possível abrir a URL do trabalho em um navegador da Web para acompanhar o status do trabalho em tempo real.

Para habilitar a saída dos detalhes do trabalho: defina **jobInformationOutputPath** no arquivo **vscode for u-sql_settings.json**.
 
## <a name="use-a-code-behind-file"></a>Usar um arquivo code-behind

Um arquivo code-behind é um arquivo em C# associado a um único script U-SQL. Você pode definir um script dedicado a UDO, UDA, UDT e UDF no arquivo code-behind. UDO, UDA, UDT e UDF podem ser usados diretamente no script sem registrar o assembly primeiro. O arquivo code-behind é colocado na mesma pasta que seu arquivo de script U-SQL de emparelhamento. Se o script for chamado de xxx.usql, o code-behind será chamado de xxx.usql.cs. Se você excluir manualmente o arquivo code-behind, o recurso code-behind será desabilitado para seu script U-SQL associado. Para saber mais sobre como escrever código do cliente para script U-SQL, veja [Writing and Using Custom Code in U-SQL – User-Defined Functions]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/) (Escrevendo e usado código personalizado em U-SQL — Funções Definidas pelo Usuário).

Para dar suporte ao code-behind, é necessário abrir uma pasta de trabalho. 

**Para gerar um arquivo code-behind**

1. Abra um arquivo de origem. 
2. Selecione Ctrl + Shift + P para abrir a paleta de comandos.
3. Insira **ADL: Gerar Code-Behind**. Um arquivo code-behind é criado na mesma pasta. 

Também é possível clicar com o botão direito em um arquivo de script e, depois, selecionar **ADL: Gerar Code Behind**. 

Compilar e enviar um script U-SQL com um arquivo code-behind é o mesmo que o arquivo de script U-SQL autônomo.

As duas capturas de tela que se seguem mostram um arquivo code-behind e seu arquivo de script U-SQL associado:
 
![Code-behind das Ferramentas do Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind.png)

![Arquivo de script code-behind das Ferramentas do Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind-call.png) 

Há suporte para a execução e a depuração local. Para ver as instruções, veja [Execução local do U-SQL e depuração local com o Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).

## <a name="use-assemblies"></a>Usar assemblies

Para obter informações sobre como desenvolver assemblies, confira [Develop U-SQL assemblies for Azure Data Lake Analytics jobs](data-lake-analytics-u-sql-develop-assemblies.md) (Desenvolver assemblies U-SQL para trabalhos do Azure Data Lake Analytics).

Use as Ferramentas do Data Lake para registrar assemblies de código personalizado no catálogo do Data Lake Analytics.

**Para registrar um assembly**

Você pode registrar o assembly por meio dos comandos **ADL: Registrar Assembly** ou **ADL: Registrar Assembly através da Configuração**.

**Para registrar por meio do comando ADL: Registrar Assembly**
1.  Selecione Ctrl + Shift + P para abrir a paleta de comandos.
2.  Digite **ADL: Registrar Assembly**. 
3.  Especifique o caminho do assembly local. 
4.  Selecione uma conta do Data Lake Analytics.
5.  Selecione um banco de dados.

Resultados: o portal será aberto em um navegador e exibirá o processo de registro do assembly.  

Outra maneira conveniente de disparar o comando **ADL: Registrar Assembly** é clicar com o botão direito no arquivo .dll no Explorador de Arquivos. 

**Para registrar por meio do comando ADL: Registrar o Assembly através da Configuração**
1.  Selecione Ctrl + Shift + P para abrir a paleta de comandos.
2.  Digite **ADL: Registrar Assembly através da Configuração**. 
3.  Especifique o caminho do assembly local. 
4.  O arquivo JSON será exibido. Examine e edite as dependências do assembly e os parâmetros de recursos, se necessário. As instruções serão exibidas na janela **Saída**. Para prosseguir com o registro do assembly, salve (CTRL+S) o arquivo JSON.

![Code-behind das Ferramentas do Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
>[!NOTE]
>- Dependências de assembly: as Ferramentas do Azure Data Lake detectam automaticamente se a DLL tem todas as dependências. As dependências são exibidas no arquivo JSON, após a detecção. 
>- Recursos: é possível carregar os recursos de DLL (por exemplo, .txt, .png e .csv) como parte do registro do assembly. 

Outra maneira conveniente de disparar o comando **ADL: Registrar Assembly através da Configuração** é clicar com o botão direito no arquivo .dll no Explorador de Arquivos. 

O código de U-SQL a seguir demonstra como chamar um assembly. No exemplo, o nome do assembly é *test*.

```
REFERENCE ASSEMBLY [test];

@a = 
    EXTRACT 
        Iid int,
    Starts DateTime,
    Region string,
    Query string,
    DwellTime int,
    Results string,
    ClickedUrls string 
    FROM @"Sample/SearchLog.txt" 
    USING Extractors.Tsv();

@d =
    SELECT DISTINCT Region 
    FROM @a;

@d1 = 
    PROCESS @d
    PRODUCE 
        Region string,
    Mkt string
    USING new USQLApplication_codebehind.MyProcessor();

OUTPUT @d1 
    TO @"Sample/SearchLogtest.txt" 
    USING Outputters.Tsv();
```

## <a name="connect-to-azure"></a>Conecte-se ao Azure

Antes de compilar e executar scripts U-SQL no Data Lake Analytics, você deve se conectar à sua conta do Azure.

**Para se conectar ao Azure**

1.  Selecione Ctrl + Shift + P para abrir a paleta de comandos. 
2.  Digite **ADL: Logon**. As informações de logon aparecem no painel **Saída**.

    ![Paleta de comandos das Ferramentas do Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)
    ![Informações de logon do dispositivo das Ferramentas do Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)
3. Selecione Ctrl + clique na URL de logon: https://aka.ms/devicelogin para abrir a página da Web de logon. Insira o código **G567LX42V** na caixa de texto e selecione **Continuar**.

   ![Ferramentas do Data Lake para Visual Studio Code colar código de logon](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png )   
4.  Siga as instruções para entrar na página da Web. Quando você estiver conectado, o nome da conta do Azure será exibido na barra de status no canto inferior esquerdo da janela do **VS Code**. 

    > [!NOTE] 
    > Se sua conta tiver a autenticação por dois fatores habilitada, recomendamos o uso da autenticação por telefone em vez de usar um PIN.

Para sair, insira o comando **ADL: Logout**.

## <a name="list-your-data-lake-analytics-accounts"></a>Listar suas contas do Data Lake Analytics

Para testar a conexão, obtenha uma lista de suas contas do Data Lake Analytics.

**Para listar as contas do Data Lake Analytics em sua assinatura do Azure**

1. Selecione Ctrl + Shift + P para abrir a paleta de comandos.
2. Insira **ADL: List Accounts**. As contas são exibidas no painel **Saída**.


## <a name="access-the-data-lake-analytics-catalog"></a>Acessar o catálogo do Data Lake Analytics

Depois que estiver conectado ao Azure, você poderá usar as seguintes etapas para acessar o catálogo do U-SQL.

**Para acessar os metadados do Azure Data Lake Analytics**

1.  Pressione CTRL+SHIFT+P e insira **ADL: Listar Tabelas**.
2.  Selecione uma das contas do Data Lake Analytics.
3.  Selecione um dos bancos de dados do Data Lake Analytics.
4.  Selecione um dos esquemas. Você pode ver a lista de tabelas.

## <a name="view-data-lake-analytics-jobs"></a>Exibir trabalhos do Data Lake Analytics

**Para exibir trabalhos do Data Lake Analytics**
1.  Abra a paleta de comandos (Ctrl+Shift+P) e escolha **ADL: Mostrar Trabalho**. 
2.  Selecione uma conta do Data Lake Analytics ou local. 
3.  Aguarde a exibição da lista de trabalhos da conta.
4.  Selecione um trabalho da lista. As Ferramentas do Data Lake abrem os detalhes do trabalho no Portal do Azure e exibem o arquivo JobInfo no VS Code.

    ![Tipos de objeto do IntelliSense das Ferramentas do Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="azure-data-lake-storage-integration"></a>Integração do Armazenamento do Azure Data Lake

Você pode usar comandos relacionados ao Armazenamento do Azure Data Lake:
 - Procure nos recursos do Armazenamento do Azure Data Lake. [Listar caminho de armazenamento](#list-the-storage-path). 
 - Visualize o arquivo de Armazenamento do Azure Data Lake. [Visualizar arquivo de armazenamento](#preview-the-storage-file). 
 - Carregue o arquivo diretamente no Armazenamento do Azure Data Lake no VS Code. [Carregar arquivo](#upload-file).
 - Faça download do arquivo diretamente no Armazenamento do Azure Data Lake no VS Code. [Fazer download do arquivo](#download-file).

## <a name="list-the-storage-path"></a>Listar caminho de armazenamento 

**Para listar o caminho de armazenamento por meio da paleta de comandos**

Clique no editor de scripts com o botão direito do mouse e selecione **ADL: Listar Caminho de Armazenamento**.

Escolha a pasta na lista ou clique em **Inserir Caminho** ou **Procurar na Raiz** (usa Insira um caminho como exemplo). -> Selecionar a **Conta do ADLA**. -> Navegar ou digitar o caminho da pasta de armazenamento (por exemplo: /saída/). -> A paleta de comandos lista as informações de caminho com base nas suas entradas.

![Listar resultados do caminho de armazenamento das Ferramentas do Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

Uma maneira mais adequada de listar o caminho relativo é pelo menu de contexto do botão direito do mouse.

**Para listar o caminho de armazenamento clicando com o botão direito**

Clique com botão direito do mouse na cadeia de caracteres do caminho para selecionar **Listar Caminho de Armazenamento**.

![Menu de contexto acessado com o botão direito das Ferramentas do Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


## <a name="preview-the-storage-file"></a>Visualizar arquivo de armazenamento

Clique no editor de scripts com o botão direito do mouse e selecione **ADL: Visualizar Caminho de Armazenamento**.

Selecione a **Conta do ADLA**. -> Insira um caminho de arquivo de armazenamento do Azure (por exemplo, /output/SearchLog.txt). -> Resultado: o arquivo é aberto no VSCode.

   ![Ferramentas do Data Lake para Visual Studio Code Resultado da visualização do arquivo](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

Outra maneira de visualizar um arquivo de armazenamento é clicar com o botão direito do mouse no menu no caminho completo do arquivo ou no caminho relativo do arquivo no editor de scripts. 

## <a name="upload-file"></a>Carregar arquivo 

Você pode carregar arquivos inserindo os comandos **ADL: Carregar Arquivo** ou **ADL: Carregar Arquivo através da Configuração**.

**Para carregar os arquivos por meio do comando ADL: Carregar Arquivo através da Configuração**
1.  Clique com o botão direito do mouse no editor de scripts e, em seguida, selecione **Carregar Arquivo através da Configuração**.
2.  O VS Code exibe um arquivo JSON. Você pode inserir os caminhos de arquivo e carregar vários arquivos ao mesmo tempo. As instruções serão exibidas na janela **Saída**. para prosseguir com o upload do arquivo, salve (CTRL+S) o arquivo JSON.

       ![Inserir arquivo das Ferramentas do Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-file.png)

3.  Resultado: a janela **Saída** exibe o status de upload do arquivo.

       ![Status de upload das Ferramentas do Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-status.png)     

Ao mesmo tempo, é possível monitorar o [status do carregamento](#check-storage-tasks-status).

**Para carregar arquivos por meio do comando ADL: Carregar Arquivo**

Clique no editor de scripts com o botão direito do mouse e selecione **Carregar Arquivo**.

Insira o **Caminho de Arquivo Local**. -> Escolha a pasta na lista ou clique em **Inserir Caminho** ou **Procurar na Raiz** (usa Insira um caminho como exemplo). -> Selecionar a **Conta do ADLA**. -> Navegar ou digitar o caminho da pasta de armazenamento (por exemplo: /saída/). -> Clique em **Escolher Pasta Atual** para especificar destino do upload.

![Status de upload das Ferramentas do Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    


Outra maneira de carregar arquivos para o armazenamento é clicar com o botão direito do mouse no menu no caminho completo do arquivo ou no caminho relativo do arquivo no editor de scripts.

Ao mesmo tempo, é possível monitorar o [status do carregamento](#check-storage-tasks-status).

## <a name="download-file"></a>Fazer download do arquivo 
É possível baixar os arquivos inserindo os comandos **ADL: Baixar o Arquivo de Armazenamento** ou **ADL: Baixar o Arquivo de Armazenamento através da Configuração**.

**Para carregar arquivos por meio do comando ADL: Baixar o Arquivo por meio da Configuração**
1. Clique com o botão direito do mouse no editor de scripts e, em seguida, selecione **Baixar o Arquivo por meio da Configuração**.
2. O VS Code exibe um arquivo JSON. É possível inserir os caminhos de arquivo e baixar vários arquivos ao mesmo tempo. As instruções serão exibidas na janela **Saída**. Para prosseguir com o download do arquivo, salve (CTRL+S) o arquivo JSON.

    ![Ferramentas do Azure Data Lake para Visual Studio Code, download de arquivos com configuração](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

3.  Resultado: a janela **Saída** exibe o status de upload do arquivo.

    ![Ferramentas do Azure Data Lake para Visual Studio Code, download de vários resultados de arquivos](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

Ao mesmo tempo, é possível monitorar o [status do download](#check-storage-tasks-status).

**Para baixar os arquivos por meio do comando ADL: Baixar do Arquivo de Armazenamento**

Clique com o botão direito do mouse no editor de scripts e, em seguida, selecione **Baixar o Arquivo de Armazenamento**.

Escolha a pasta na lista ou clique em **Inserir Caminho** ou **Procurar na Raiz** (usa Insira um caminho como exemplo). -> Selecionar a **Conta do ADLA**. -> Navegar ou digitar o caminho da pasta de armazenamento (por exemplo: /saída/) -> escolher um arquivo para baixar.

   ![Status de download das Ferramentas do Azure Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

   Na imagem de resultado, o arquivo é salvo como a pasta temp. É possível definir um caminho de download padrão para o parâmetro **usql.defaultLocalFolderForDownload**, por meio do menu do VSCode **Arquivo** -> **Preferências** -> **Configuração**.

Outra maneira de fazer download de arquivos do armazenamento é clicar com o botão direito do mouse no menu do caminho completo do arquivo ou no caminho relativo do arquivo no editor de scripts.

Ao mesmo tempo, é possível monitorar o [status do download](#check-storage-tasks-status).

## <a name="check-storage-tasks-status"></a>Verificar o status de tarefas de armazenamento
O status é exibido na parte inferior da barra de status quando o download e o carregamento é concluído.
1. Clique na barra de status abaixo e o status de download e carregamento serão mostrados no painel **SAÍDA**.

   ![Verificar status de armazenamento das Ferramentas do Azure Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)


## <a name="open-azure-storage-explorer"></a>Abrir o gerenciador de armazenamento do Azure
Você pode abrir o **Gerenciador de Armazenamento do Azure** inserindo o comando **ADL: Abrir o Gerenciador Web de Armazenamento do Azure** ou selecionando-o no menu de contexto acionado com o botão direito do mouse.

**Para abrir o Gerenciador de Armazenamento do Azure**

1. Selecione Ctrl + Shift + P para abrir a paleta de comandos.
2. Insira **Abrir Gerenciador Web do Armazenamento do Azure** ou clique com o botão direito do mouse em um caminho relativo, ou no caminho completo no editor de scripts, e escolha **Abrir Gerenciador Web do Armazenamento do Azure**.
3. Selecione uma conta do Data Lake Analytics.

As Ferramentas do Data Lake abrem o caminho de armazenamento do Azure no Portal do Azure. Você pode encontrar o caminho e visualizar o arquivo na Web.

## <a name="local-run-and-local-debug-for-windows-users"></a>Depuração local e execução local para usuários do Windows
A execução local de U-SQL testa seus dados locais e valida o script localmente, antes que seu código seja publicado no Data Lake Analytics. O recurso de depuração local permite que você conclua as seguintes tarefas antes que seu código seja enviado ao Data Lake Analytics: 
- Depure o code-behind em C#. 
- Explore o código. 
- Valide o script localmente.

Para obter instruções sobre a execução e a depuração local, confira [Execução local do U-SQL e depuração local com o Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).

## <a name="additional-features"></a>Recursos adicionais

As Ferramentas do Data Lake para VS Code dão suporte aos seguintes recursos:

-   Preenchimento automático de IntelliSense: as sugestões são exibidas em janelas pop-up ao redor dos itens, como palavras-chave, métodos e variáveis. Os diferentes ícones representam diferentes tipos dos objetos:

    - Tipo de dados de Scala
    - Tipos de dados complexos
    - UDTs Internos
    - Coleções e classes .Net
    - Expressões em C#
    - UDFs, UDOs e UDAAGs internos de C# 
    - Funções em U-SQL
    - Função de Janelas do U-SQL
 
    ![Tipos de objeto do IntelliSense das Ferramentas do Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   Preenchimento automático de IntelliSense nos metadados do Data Lake Analytics: as Ferramentas do Data Lake baixam localmente as informações de metadados do Data Lake Analytics. O recurso IntelliSense preenche objetos automaticamente, incluindo o banco de dados, esquema, tabela, exibição, função com valor de tabela, procedimentos e assemblies em C#, dos metadados do Data Lake Analytics.
 
    ![Metadados do IntelliSense das Ferramentas do Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   Marcador de erro de IntelliSense: as Ferramentas do Data Lake sublinham os erros de edição para U-SQL e C#. 
-   Destaques de sintaxe: as Ferramentas do Data Lake usam cores diferentes para diferenciar itens, como variáveis, palavras-chave, tipo de dados e funções. 

    ![Destaques da sintaxe das Ferramentas do Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre a execução local do U-SQL e sobre a depuração local com o Visual Studio Code, consulte [Execução local do U-SQL e depuração local com o Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).
- Para obter informações introdutórias sobre o Data Lake Analytics, veja [Tutorial: Introdução ao Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md).
- Para saber mais sobre as Ferramentas do Data Lake para Visual Studio, confira [Tutorial: Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Para obter informações sobre como desenvolver assemblies, confira [Develop U-SQL assemblies for Azure Data Lake Analytics jobs](data-lake-analytics-u-sql-develop-assemblies.md) (Desenvolver assemblies U-SQL para trabalhos do Azure Data Lake Analytics).



