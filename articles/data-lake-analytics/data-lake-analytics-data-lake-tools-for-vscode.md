---
title: "Ferramentas do Azure Data Lake – Usar as Ferramentas do Azure Data Lake para Visual Studio Code | Microsoft Docs"
description: 'Saiba como usar as Ferramentas do Azure Data Lake para Visual Studio Code para criar, testar e executar scripts U-SQL. '
Keywords: "VSCode, Ferramentas do Azure Data Lake, Execução local, Depuração local, Depuração Local, visualizar arquivo de armazenamento, carregar no caminho de armazenamento"
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
ms.date: 07/14/2017
ms.author: jejiang
ms.translationtype: HT
ms.sourcegitcommit: c999eb5d6b8e191d4268f44d10fb23ab951804e7
ms.openlocfilehash: 0ed3d7a0057eb446b3e1d16019ac74c641ae3138
ms.contentlocale: pt-br
ms.lasthandoff: 07/17/2017

---

# <a name="use-the-azure-data-lake-tools-for-visual-studio-code"></a>Usar as Ferramentas do Azure Data Lake para Visual Studio Code

Saiba como usar as Ferramentas do Azure Data Lake para VSCode (Visual Studio Code) para criar, testar e executar scripts U-SQL.  As informações também são abordadas no vídeo a seguir:

<a href="https://www.youtube.com/watch?v=J_gWuyFnaGA&feature=youtu.be"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>Pré-requisitos

As Ferramentas do Data Lake podem ser instaladas nas plataformas compatíveis com o VSCode, que incluem Windows, Linux e MacOS. Você pode encontrar os pré-requisitos para diferentes plataformas

- Windows

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx).
    - [Java SE Runtime Environment versão 8 atualização 77 ou posterior](https://java.com/download/manual.jsp). Você precisa adicionar o caminho java.exe no caminho da variável de ambiente do sistema.  Para obter instruções, veja [como definir ou alterar a variável do sistema Path?]( https://www.java.com/download/help/path.xml) O caminho é semelhante a C:\Arquivos de Programas\Java\jdk1.8.0_77\jre\bin
    - [Tempo de execução do SDK do .NET Core 1.0.3 ou do .NET Core 1.1](https://www.microsoft.com/net/download).
    
- Linux (é recomendável o Ubuntu 14.04 LTS)

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx). Use o seguinte comando para a instalação:

        sudo dpkg -i code_<número_da_versão>_amd64.deb

    - [Mono 4.2.x](http://www.mono-project.com/docs/getting-started/install/linux/). 

        - Atualize a fonte do pacote deb executando os seguintes comandos:

                sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF
                echo "deb http://download.mono-project.com/repo/debian wheezy/snapshots 4.2.4.4/main" | sudo tee /etc/apt/sources.list.d/mono-xamarin.list
                sudo apt-get update

        - Instale o mono executando o comando:

                sudo apt-get install mono-complete

            > [!NOTE] 
            > Não há suporte para o Mono 4.6.  Você precisa desinstalar a versão 4.6 totalmente antes de instalar a versão 4.2.x.  

        - [Java SE Runtime Environment versão 8 atualização 77 ou posterior](https://java.com/download/manual.jsp). As instruções podem ser encontradas [aqui]( https://java.com/en/download/help/linux_x64_install.xml).
        - [Tempo de execução do SDK do .NET Core 1.0.3 ou do .NET Core 1.1](https://www.microsoft.com/net/download).
- MacOS

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx).
    - [Mono 4.2.4](http://download.mono-project.com/archive/4.2.4/macos-10-x86/). 
    - [Java SE Runtime Environment versão 8 atualização 77 ou posterior](https://java.com/download/manual.jsp). As instruções podem ser encontradas [aqui](https://java.com/en/download/help/mac_install.xml).
    - [Tempo de execução do SDK do .NET Core 1.0.3 ou do .NET Core 1.1](https://www.microsoft.com/net/download).

## <a name="install-the-data-lake-tools"></a>Instalar as Ferramentas do Data Lake

Depois de instalar os pré-requisitos, você pode instalar as Ferramentas do Data Lake para VSCode.

**Para instalar as Ferramentas do Data Lake**

1. Abra o **Visual Studio Code**.
2. Pressione **CTRL+P** e enter:
```
ext install usql-vscode-ext
```
Você pode ver uma lista de extensões de código do Visual Studio. Uma delas é **Ferramentas do Azure Data Lake**.

3. Clique em **Instalar** ao lado de **Ferramentas do Azure Data Lake**. Depois de alguns segundos, o botão Instalar será alterado para Recarregar.
4. Clique em **Recarregar** para ativar a extensão.
5. Clique em **OK** para confirmar. Você pode ver as Ferramentas do Azure Data Lake no painel Extensões.
    ![Instalação das Ferramentas do Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

## <a name="activate-azure-data-lake-tools"></a>Ativar Ferramentas do Azure Data Lake
Crie um novo arquivo .USQL ou abra um existente para ativar a extensão. 

## <a name="connect-to-azure"></a>Conecte-se ao Azure

Antes de compilar e executar os scripts do U-SQL no Azure Data Lake Analytics, você deve se conectar à sua conta do Azure.

**Para se conectar ao Azure**

1.  Abra a paleta de comandos pressionando **CTRL+SHIFT+P**. 
2.  Digite **ADL: Logon**. As informações de logon são mostradas no painel de saída.

    ![Ferramentas do Data Lake para Visual Studio Code paleta de comandos](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)
    ![Ferramentas do Data Lake para Visual Studio Code informações de logon](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)
3. Pressione CTRL e clique na URL de logon https://aka.ms/devicelogin para abrir a página da Web de logon. Copie e cole o código G567LX42V na caixa de texto abaixo e clique em Continuar para prosseguir.

   ![Ferramentas do Data Lake para Visual Studio Code colar código de logon](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png )   
4.  Siga as instruções para entrar pela página da Web. Uma vez conectado, o nome da sua conta do Azure será mostrado na barra de status na parte inferior esquerda da janela do VSCode. 

    > [!NOTE] 
    > Se sua conta tiver dois fatores habilitados, é recomendável usar a autenticação do telefone em vez do Pin.

Para se desconectar, use o comando **ADL: Logout**

## <a name="list-data-lake-analytics-accounts"></a>Listar contas da Análise Data Lake

Para testar a conexão, você pode listar suas contas do Data Lake Analytics:

**Para listar as contas do Data Lake Analytics em sua assinatura do Azure**

1. Abra a paleta de comandos pressionando **CTRL+SHIFT+P**.
2. Digite **ADL: Listar Contas**.  As contas são exibidas no painel **Saída**.

## <a name="open-sample-script"></a>Abra o script de exemplo

Use a paleta de comandos (**Ctrl+Shift+P**) e escolha **ADL: Abrir Script de Exemplo**. Ela abrirá outra instância para este exemplo. Você também pode editar, configurar e enviar scripts nesta instância.

## <a name="work-with-u-sql"></a>Trabalhar com o U-SQL

Você precisa abrir um arquivo U-SQL ou uma pasta para trabalhar com o U-SQL.

**Para abrir uma pasta para o projeto U-SQL**

1. No Visual Studio Code, clique no menu **Arquivo** e em **Abrir Pasta**.
2. Especifique uma pasta e clique em **Selecionar Pasta**.
3. Clique no menu **Arquivo** e em **Novo**. Um arquivo **Untilted-1** é adicionado ao projeto.
4. Copie e cole o seguinte código no arquivo Untitled-1:

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
            TO “/Output/departments.csv”

    O script cria um arquivo departments.csv com alguns dados na pasta /output.

5. Salve o arquivo como **myUSQL.usql** na pasta aberta. Observe que um arquivo de configuração **adltools_settings.json** também é adicionado ao projeto.
4. Abra e configure **adltools_settings.json** com as seguintes propriedades:

    - Conta: uma conta do Data Lake Analytics na sua assinatura do Azure.
    - Banco de dados: um banco de dados em sua conta. O padrão é mestre.
    - Esquema: um esquema em seu banco de dados. O padrão é dbo.
    - Configurações opcionais:
        - Prioridade: o intervalo de prioridade é de 1 a 1000, sendo 1 a prioridade mais alta. O valor padrão é 1000.
        - Paralelismo: o intervalo de paralelismo é de 1 a 150. O valor padrão é o paralelismo máximo permitido em sua conta do ADLA. 
        
        > [!NOTE] 
        > Se as configurações forem inválidas, os valores padrão serão usados.

    ![Arquivo de configuração das Ferramentas do Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-configuration-file.png)

    Uma conta de computador do Data Lake Analytics é necessária para compilar e executar trabalho do U-SQL.  Você deve configurar a conta de computador para poder compilar e executar trabalho do U-SQL.
    
    Após salvar a configuração, as informações conta|banco de dados|esquema serão mostradas na barra de status na parte inferior esquerda do arquivo USQL correspondente. 
 
 

Em comparação à abertura de um arquivo, abrir uma pasta permite que você:

- Use o arquivo code-behind.  No modo de arquivo único, não há suporte para code-behind.
- Use o arquivo de configuração. Quando você abre uma pasta, os scripts na pasta de trabalho compartilham um arquivo de configuração.


A compilação do script U-SQL é feita remotamente pelo serviço do Data Lake Analytics.  Quando você emite o comando de compilação, o script U-SQL é enviado para sua conta do Data Lake Analytics. O resultado da compilação é recebido posteriormente pelo Visual Studio Code. Devido à compilação remota, o Visual Studio Code exige as informações para se conectar à conta do Data Lake Analytics no arquivo de configuração.

**Para compilar um script U-SQL**

1. Abra a paleta de comandos pressionando **CTRL+SHIFT+P**. 
2. Insira **ADL: Compilar Script**. Compile os resultados mostrados na janela de saída. Também é possível clicar com o botão direito do mouse em um arquivo de script e clicar em **ADL: Compilar Script** para compilar um trabalho do U-SQL. O resultado da compilação é mostrado no painel de saída.
 

**Para enviar um script U-SQL**

1. Abra a paleta de comandos pressionando **CTRL+SHIFT+P**. 
2. Insira **ADL: Enviar Trabalho**.  Também é possível clicar com o botão direito do mouse em um arquivo de script e clicar em **ADL: Enviar Trabalho** para enviar um trabalho do U-SQL. 

Depois de enviar um trabalho do U-SQL, os logs de envio são mostrados na janela de saída no VSCode. Se o envio for bem-sucedido, a URL do trabalho também será mostrada. Você pode abrir a URL do trabalho em um navegador da Web para acompanhar o status do trabalho em tempo real.

Para habilitar os detalhes do trabalho de saída: defina ‘jobInformationOutputPath’ no arquivo **vscode for u-sql_settings.json**.
 
## <a name="use-code-behind-file"></a>Usar o arquivo code-behind

O arquivo code-behind é um arquivo CSharp associado a um script U-SQL. Você pode definir UDO/UDA/UDT/UDF dedicados ao script no arquivo code-behind. Os UDO/UDA/UDT/UDF podem ser usados diretamente no script sem registrar o assembly primeiro. O arquivo code-behind é colocado na mesma pasta que seu arquivo script U-SQL de emparelhamento. Se o script for chamado de xxx.usql, o code-behind será chamado de xxx.usql.cs. Excluir o arquivo code-behind manualmente desabilita o recurso de code-behind para seu script U-SQL associado. Para saber mais sobre como escrever código do cliente para script U-SQL, veja [Writing and Using Custom Code in U-SQL – User-Defined Functions]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/) (Escrevendo e usado código personalizado em U-SQL — Funções Definidas pelo Usuário).

Para dar suporte ao code-behind, uma pasta de trabalho deve ser aberta. 

**Para gerar um arquivo code-behind**

1. Abra um arquivo de origem. 
2. Abra a paleta de comandos pressionando **CTRL+SHIFT+P**.
3. Insira **ADL: Gerar Code-Behind**.  Um arquivo code-behind é criado na mesma pasta. 

Também é possível clicar com o botão direito do mouse em um arquivo de script e clicar em **ADL: Gerar Code-Behind** para gerar um arquivo de code-behind. 

Compilar e enviar um script U-SQL com code-behind é o mesmo que o script U-SQL autônomo.

As duas capturas de tela que se seguem mostram um arquivo code-behind e seu arquivo de script U-SQL associado:
 
![Code-behind das Ferramentas do Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind.png)

![Code-behind das Ferramentas do Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind-call.png) 

## <a name="use-assemblies"></a>Usar assemblies

Para obter informações sobre como desenvolver assemblies, confira [Develop U-SQL assemblies for Azure Data Lake Analytics jobs](data-lake-analytics-u-sql-develop-assemblies.md) (Desenvolver assemblies U-SQL para trabalhos do Azure Data Lake Analytics).

Usando as Ferramentas do Data Lake, você pode registrar assemblies de código personalizado no catálogo do Data Lake Analytics.

**Para registrar um assembly**

Você pode registrar o assembly por meio dos comandos **ADL: Registrar Assembly** ou **ADL: Registrar Assembly através da Configuração**.

**ADL: Registrar Assembly**
1.  Pressione **CTRL+SHIFT+P** para abrir a Paleta de Comandos.
2.  Digite **ADL: Registrar Assembly**. 
3.  Especifique o caminho do assembly local. 
4.  Selecione uma conta do Data Lake Analytics.
5.  Selecione um banco de dados.

Resultados: o portal será aberto no navegador e exibirá o processo de registro do assembly.  

Outra maneira conveniente de disparar o comando **ADL: Registrar Assembly** é clicar com o botão direito do mouse no arquivo dll no EXPLORER. 

**ADL: Registrar Assembly através da Configuração**.
1.  Pressione **CTRL+SHIFT+P** para abrir a Paleta de Comandos.
2.  Digite **ADL: Registrar Assembly através da Configuração**. 
3.  Especifique o caminho do assembly local. 
4.  O arquivo JSON será exibido. Examine e edite as dependências do assembly e os parâmetros de recursos, se necessário. As instruções serão exibidas na janela de saída. Salve (**CTRL+S**) o arquivo JSON para prosseguir com o registro do assembly.

![Code-behind das Ferramentas do Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
>[!NOTE]
>- Dependências de assembly: as Ferramentas do Azure Data Lake detectam automaticamente se a DLL tem todas as dependências. As dependências serão exibidas no arquivo JSON, uma vez detectadas. 
>- Recursos: você pode carregar os recursos DLL (por exemplo, txt, png, csv, etc.) como parte do registro do assembly. 

Outra maneira mais adequada de disparar o comando **ADL: Registrar Assembly através da Configuração** é clicar com o botão direito do mouse no arquivo dll no EXPLORER. 

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


## <a name="access-data-lake-analytics-catalog"></a>Acessar o catálogo do Data Lake Analytics

Depois que estiver conectado ao Azure, você poderá usar as seguintes etapas para acessar o catálogo do U-SQL:

**Para acessar os metadados do Azure Data Lake Analytics**

1.  Pressione **CTRL+SHIFT+P** e digite **ADL: Listar Tabelas**.
2.  Clique em uma das contas do Data Lake Analytics.
3.  Clique em um dos bancos de dados do Data Lake Analytics.
4.  Clique em um dos esquemas. Você pode ver as tabelas.

## <a name="show-data-lake-analytics-jobs"></a>Exibir trabalhos do Data Lake Analytics

Use a paleta de comandos (**Ctrl+Shift+P**) e escolha **ADL: Mostrar Trabalho**. 

1.  Selecione uma conta Local ou do ADLA 
2.  Aguarde até que a lista de trabalhos da conta seja mostrada
3.  Selecione um trabalho da lista. As ferramentas do ADL abrirão os detalhes do trabalho no portal e exibirão o arquivo JobInfo no VSCode.

![Tipos de objeto do IntelliSense das Ferramentas do Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="azure-data-lake-storage-adls-integration"></a>Integração do ADLS (Armazenamento do Azure Data Lake)

Você pode usar comandos relacionados ao ADLS para navegar nos recursos do ADLS, visualizar arquivos do ADLS e carregar arquivos do ADLS diretamente no VSCode.  
### <a name="list-storage-path"></a>Listar caminho de armazenamento

Use a paleta de comandos (**Ctrl+Shift+P**) e escolha **ADL: Listar Caminho de Armazenamento**.
1.  Abra a paleta de comandos e insira o comando.

    ![Ferramentas do Data Lake para Visual Studio Code Listar Caminho de Armazenamento](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-storage.png)

2.  Selecione sua maneira preferida de listar o caminho de armazenamento. Esta passagem usa **Inserir um caminho** como exemplo.

    ![Ferramentas do Data Lake para Visual Studio Code Listar Caminho de Armazenamento para selecionar uma maneira](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account-selectoneway.png)

    > [!NOTE]
    >- O VSCode mantém o caminho que você visitou pela última vez para cada conta do ADLA. Exemplo：/tt/ss.
    >- Navegador do caminho raiz: lista o caminho raiz do qual você selecionou a conta local ou do ADLA.
    >- Inserir um caminho: lista o caminho especificado do qual você selecionou a conta local ou do ADLA.
    
3. Selecione uma conta da conta local ou do ADLA.

    ![Ferramentas do Data Lake para Visual Studio Code selecionar mais](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

4.  Clique em Mais para listar mais contas do ADLA e selecione uma conta do ADLA.

    ![Ferramentas do Data Lake para Visual Studio Code selecionar uma conta](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-select-adla-account.png)

5.  Insira um caminho de armazenamento do Azure. Por exemplo: /output

       ![Ferramentas do Data Lake para Visual Studio Code Inserir Caminho de Armazenamento](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-input-path.png)

6.  Resultados: a paleta de comandos lista as informações de caminho com base em suas entradas.

    ![Ferramentas do Data Lake para Visual Studio Code Listar Resultado do Caminho de Armazenamento](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-path.png)

Outra maneira mais adequada de listar o caminho relativo é clicar com o botão direito do mouse no menu de contexto.

1.  Clique com botão direito do mouse na cadeia de caracteres do caminho para selecionar a opção Listar Caminho de Armazenamento.

       ![Ferramentas do Data Lake para Visual Studio Code Clicar com o botão direito do mouse no Menu de contexto](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)
2. O caminho relativo selecionado é mostrado na paleta de comandos.

   ![Ferramentas do Data Lake para Visual Studio Code Listar caminho selecionado](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-relative-path.png)

3.  Selecione uma conta da conta local ou do ADLA.

       ![Ferramentas do Data Lake para Visual Studio Code Selecionar uma conta](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

4.  Resultados: a paleta de comandos lista as pastas e os arquivos para o caminho atual.

       ![Ferramentas do Data Lake para Visual Studio Code Listar Caminho Atual](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-current.png)

### <a name="preview-storage-file"></a>Visualizar arquivo de armazenamento

Use a paleta de comandos (**Ctrl+Shift+P**) e escolha **ADL: Visualizar Arquivo de Armazenamento**.
1.  Abra a paleta de comandos e insira o comando.

       ![Ferramentas do Data Lake para Visual Studio Code Visualizar arquivo de armazenamento](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-preview.png)

2.  Selecione uma conta local ou do ADLA.

       ![Ferramentas do Data Lake para Visual Studio Code Listar Conta](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

3.  Clique em Mais para listar mais contas do ADLA e selecione uma conta do ADLA.

       ![Ferramentas do Data Lake para Visual Studio Code Selecionar uma Conta](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-select-adla-account.png)

4.  Insira um caminho ou arquivo de armazenamento do Azure. Por exemplo: /output/SearchLog.txt

       ![Ferramentas do Data Lake para Visual Studio Code Inserir caminho e arquivo de armazenamento](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-input-preview-file.png)

5.  Resultados: a paleta de comandos lista as informações de caminho com base em suas entradas.

       ![Ferramentas do Data Lake para Visual Studio Code Resultado da Visualização do Arquivo](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-preview-results.png)

Outra maneira mais adequada de visualizar um arquivo é clicar com o botão direito do mouse no menu de contexto.

1.  Clique com botão direito do mouse em um caminho de arquivo para visualizar um arquivo.

       ![Ferramentas do Data Lake para Visual Studio Code Clicar com o botão direito do mouse no Menu de contexto](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-preview.png)

2.  Selecione uma conta da conta local ou do ADLA.

       ![Ferramentas do Data Lake para Visual Studio Code Selecionar uma Conta](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

3.  Resultados: o VSCode exibe os resultados da visualização do arquivo.

       ![Ferramentas do Data Lake para Visual Studio Code Resultado da visualização do arquivo](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-preview-results.png)

### <a name="upload-file"></a>Carregar arquivo 

Você pode carregar um arquivo por meio dos comandos **ADL: Carregar Arquivo** ou **ADL: Carregar Arquivo através da Configuração**.

**ADL: Carregar Arquivo**
1.  Pressione CTRL+SHIFT+P para abrir a paleta de comandos ou clique com o botão direito do mouse no editor de scripts e, em seguida, digite **Carregar Arquivo**.
2. Insira um caminho local para carregar.

    ![Ferramentas do Data Lake para Visual Studio Code Inserir caminho local](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-input-local-path.png)

3. Selecione um modo para listar o caminho de armazenamento. Esta passagem usa **Inserir um caminho** como exemplo.

    ![Ferramentas do Data Lake para Visual Studio Code Listar Caminho de Armazenamento](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account-selectoneway.png)
    >[!NOTE]
    >- O VSCode mantém o caminho que você visitou pela última vez para cada conta do ADLA. Exemplo：/tt/ss.
    >- Navegador do caminho raiz: liste o caminho raiz do qual você selecionou a conta local ou do ADLA.
    >- Inserir um caminho: liste o caminho especificado do qual você selecionou a conta local ou do ADLA.

4. Selecione uma conta da conta local ou do ADLA.

    ![Ferramentas do Data Lake para Visual Studio Code Clicar com o botão direito do mouse em Armazenamento](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

5.  Insira um caminho de armazenamento do Azure. Por exemplo: /output/

       ![Ferramentas do Data Lake para Visual Studio Code Inserir caminho de armazenamento](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-input-preview-file.png)

6. Liste a entrada do caminho de armazenamento do Azure. Selecione **Escolher pasta atual**.

    ![Ferramentas do Data Lake para Visual Studio Code Selecionar uma pasta](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-choose-current-folder.png)

7.  Resultado: a janela de saída exibe o status de upload do arquivo.

       ![Ferramentas do Data Lake para Visual Studio Code Status de Upload](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-status.png)    

**ADL: Carregar Arquivo através da Configuração**
1.  Pressione CTRL+SHIFT+P para abrir a paleta de comandos ou clique com o botão direito do mouse no editor de scripts e, em seguida, digite **Carregar Arquivo através da Configuração**.
2.  O VSCode exibe um arquivo JSON. Você pode inserir os caminhos de arquivo e carregar vários arquivos ao mesmo tempo. As instruções serão exibidas na janela de saída. Salve (**CTRL+S**) o arquivo JSON para prosseguir com o upload do arquivo.

       ![Ferramentas do Data Lake para Visual Studio Code Inserir arquivo](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-file.png)

3.  Resultado: a janela de saída exibe o status de upload do arquivo.

       ![Ferramentas do Data Lake para Visual Studio Code Status de Upload](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-status.png)     

Outra maneira conveniente de carregar o arquivo para armazenamento é clicar com o botão direito do mouse no menu no caminho completo do arquivo ou no caminho relativo do arquivo no editor de scripts. Insira o caminho do arquivo local e escolha a conta. Em seguida, a janela de saída exibirá o status do upload. 

### <a name="open-web-azure-storage-explorer"></a>Abra o Gerenciador Web do Armazenamento do Azure
Você pode abrir o **Gerenciador Web do Armazenamento do Azure** por meio do comando **ADL: Abrir o Gerenciador Web do Armazenamento do Azure** ou clicando com o botão direito do mouse no menu de contexto.

1. Pressione CTRL+SHIFT+P para abrir a paleta de comandos.
2. Digite **Gerenciador Web do Armazenamento do Azure** ou clique com o botão direito do mouse em um caminho relativo ou completo no editor de scripts para escolher a opção **Abrir o Gerenciador Web do Armazenamento do Azure**.
3. Selecione uma conta do Data Lake Analytics.

As Ferramentas do ADL abrem o caminho de armazenamento do Azure no portal. Você pode visitar o caminho e o arquivo de visualização pela Web.

### <a name="local-run-and-local-debug-windows-users"></a>Usuários do Windows da Depuração Local e da Execução Local
A execução local do U-SQL foi implementada para testar seus dados locais e validar o script localmente antes de publicar seu código no ADLA. A funcionalidade de depuração local permite que você depure o code-behind do C#, passe pelo código e valide o script localmente antes de enviá-lo para o ADLA. Consulte as instruções em [Execução local do U-SQL e depuração local com o Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).

## <a name="additional-features"></a>Recursos adicionais

As Ferramentas do Data Lake para VSCode dão suporte aos seguintes recursos:

-   Preenchimento automático do IntelliSense. As sugestões são exibidas ao redor de palavras-chave, métodos, variáveis, etc. Os diferentes ícones representam diferentes tipos dos objetos:

    - Tipo de Dados de Escala
    - Tipos de Dados Complexos
    - UDTs Internos
    - Coleções e Classes .Net
    - Expressões do C#
    - UDFs, UDOs e UDAAGs internos de C# 
    - Funções do U-SQL
    - Função de Janelas do U-SQL
 
    ![Tipos de objeto do IntelliSense das Ferramentas do Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   Preenchimento automático do IntelliSense nos metadados do Data Lake Analytics. As Ferramentas do Data Lake baixam as informações de metadados do Data Lake Analytics localmente.  O recurso IntelliSense popula objetos automaticamente, incluindo Banco de Dados, Esquema, Tabela, Exibição, TVF, Procedimentos, Assemblies C#, usando os metadados do Data Lake Analytics.
 
    ![Metadados do IntelliSense das Ferramentas do Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   Marcador de erro do IntelliSense. As Ferramentas do Data Lake sublinham os erros de edição para U-SQL e C#. 
-   Destaques da sintaxe. As Ferramentas do Data Lake usam cores diferentes para diferenciar variáveis, palavras-chave, tipo de dados, funções, etc. 

    ![Destaques da sintaxe das Ferramentas do Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre a execução local do U-SQL e sobre a depuração local com o Visual Studio Code, consulte [Execução local do U-SQL e depuração local com o Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).
- Para obter informações introdutórias sobre o Data Lake Analytics, veja [Tutorial: Introdução ao Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md).
- Para saber mais sobre como usar as Ferramentas do Data Lake para Visual Studio, confira [Tutorial: Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Para obter informações sobre como desenvolver assemblies, confira [Develop U-SQL assemblies for Azure Data Lake Analytics jobs](data-lake-analytics-u-sql-develop-assemblies.md) (Desenvolver assemblies U-SQL para trabalhos do Azure Data Lake Analytics).




