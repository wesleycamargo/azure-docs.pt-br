---
title: Usar as Ferramentas do Azure Data Lake para Visual Studio Code | Microsoft Docs
description: 'Saiba como usar as Ferramentas do Azure Data Lake para Visual Studio Code para criar, testar e executar scripts U-SQL. '
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/22/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: fe5ef9bba31abdf9b29ad7c817a376407309f289
ms.openlocfilehash: 59f2e4473aac85a8476055d2d955f576c099d787

---

# <a name="use-the-azure-data-lake-tools-for-visual-studio-code"></a>Usar as Ferramentas do Azure Data Lake para Visual Studio Code

Saiba como usar as Ferramentas do Azure Data Lake para VSCode (Visual Studio Code) para criar, testar e executar scripts U-SQL.


## <a name="prerequisites"></a>Pré-requisitos

As Ferramentas do Data Lake podem ser instaladas nas plataformas compatíveis com o VSCode, que incluem Windows, Linux e MacOS. Você pode encontrar os pré-requisitos para diferentes plataformas

- Windows

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx).
    - [Java SE Runtime Environment versão 8 atualização 77 ou posterior](https://java.com/download/manual.jsp). Você deve adicionar o caminho java.exe ao caminho de variável de ambiente do sistema.  Para obter instruções, veja [como definir ou alterar a variável do sistema Path?]( https://www.java.com/download/help/path.xml) O caminho é semelhante a C:\Arquivos de Programas\Java\jdk1.8.0_77\jre\bin
    - [SDK do .NET Core 1.0.1-Preview 2 ou o tempo de execução do .NET Core 1.0.1]( https://www.microsoft.com/net/download).
    
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
            > Não há suporte para o Mono 4.6.  Você deve desinstalar totalmente a versão 4.6 antes de instalar a versão 4.2.x.  

        - [Java SE Runtime Environment versão 8 atualização 77 ou posterior](https://java.com/download/manual.jsp). As instruções podem ser encontradas [aqui]( https://java.com/en/download/help/linux_x64_install.xml).
        - [SDK do .NET Core 1.0.1-Preview 2 ou o tempo de execução do .NET Core 1.0.1]( https://www.microsoft.com/net/download).
- MacOS

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx).
    - [Mono 4.2.4](http://download.mono-project.com/archive/4.2.4/macos-10-x86/). 
    - [Java SE Runtime Environment versão 8 atualização 77 ou posterior](https://java.com/download/manual.jsp). As instruções podem ser encontradas [aqui](https://java.com/en/download/help/mac_install.xml).
    - [SDK do .NET Core 1.0.1-Preview 2 ou o tempo de execução do .NET Core 1.0.1]( https://www.microsoft.com/net/download).

## <a name="install-the-data-lake-tools"></a>Instalar as Ferramentas do Data Lake

Depois de instalar os pré-requisitos, você pode instalar as Ferramentas do Data Lake para VSCode.

**Para instalar as Ferramentas do Data Lake**

1. Abra o **Visual Studio Code**.
2. Pressione **CTRL+P** e enter:

        ext install usql-vscode-ext
    Você pode ver uma lista de extensões de código do Visual Studio. Uma delas é **Ferramenta do Azure Data Lake (Preview)**.
3. Clique em **Instalar** próximo de **Ferramenta do Azure Data Lake (Preview)**. Depois de alguns segundos, o botão Instalar será alterado para Recarregar.
4. Clique em **Recarregar** para ativar a extensão.
5. Clique em **OK** para confirmar. Você pode ver as Ferramentas do Azure Data Lake no painel Extensões.

    ![Instalação das Ferramentas do Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)


## <a name="connect-to-azure"></a>Conecte-se ao Azure

Antes de compilar e executar scripts U-SQL, você deve se conectar à sua conta do Azure.

**Para se conectar ao Azure**

1.  Abra a paleta de comandos pressionando **CTRL+SHIFT+P**. 
2.  Insira **ADL:Logon**.

    ![Paleta de comados das Ferramentas do Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

2.  Siga as instruções para entrar pela página da Web. Uma vez conectado, o nome da sua conta é mostrado na barra de status na parte inferior da janela.

> [!NOTE] 
> Se sua conta tiver dois fatores habilitados, é recomendável usar a autenticação do telefone em vez do Pin.

Para desconectar, use o comando **ADL:Logoff**

## <a name="list-data-lake-analytics-accounts"></a>Listar contas da Análise Data Lake

Para testar a conexão, você pode listar suas contas do Data Lake Analytics:

**Para listar as contas do Data Lake Analytics em sua assinatura do Azure**

1. Abra a paleta de comandos pressionando **CTRL+SHIFT+P**.
2. Digite **ADL:Listar Contas**.  As contas são exibidas no painel **Saída**.

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
    - Configurações opcionais:

        - Prioridade: o intervalo de prioridade é de 1 a 1000, sendo 1 a prioridade mais alta. O valor padrão é 1000.
        - Paralelismo: o intervalo de paralelismo é de 1 a 150. O valor padrão é 150. 

        > [!NOTE] 
        > Se as configurações forem inválidas, os valores padrão serão usados.

     ![Arquivo de configuração das Ferramentas do Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-configuration-file.png)

    Uma conta de computador do Data Lake Analytics é necessária para compilar e executar trabalho do U-SQL.  Você deve configurar a conta de computador para poder compilar e executar trabalho do U-SQL.

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

O arquivo code-behind é uma associação do arquivo CSharp a um script U-SQL. Você pode definir UDO/UDA/UDT/UDF dedicados ao script no arquivo code-behind. Os UDO/UDA/UDT/UDF podem ser usados diretamente no script sem registrar o assembly primeiro. O arquivo code-behind é colocado na mesma pasta que seu arquivo script U-SQL de emparelhamento. Se o script for chamado de xxx.usql, o code-behind será chamado de xxx.usql.cs. Excluir o arquivo code-behind manualmente desabilita o recurso de code-behind para seu script U-SQL associado. Para saber mais sobre como escrever código do cliente para script U-SQL, veja [Writing and Using Custom Code in U-SQL – User-Defined Functions]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/) (Escrevendo e usado código personalizado em U-SQL — Funções Definidas pelo Usuário).

Para dar suporte ao code-behind, uma pasta de trabalho deve ser aberta. 

**Para gerar um arquivo code-behind**

1. Abra um arquivo de origem. 
2. Abra a paleta de comandos pressionando **CTRL+SHIFT+P**.
3. Insira **ADL: Gerar Code-Behind**.  Um arquivo code-behind é criado na mesma pasta. 

Também é possível clicar com o botão direito do mouse em um arquivo de script e clicar em **ADL: Gerar Code-Behind** para enviar um trabalho do U-SQL. 

Compilar e enviar um script U-SQL com code-behind é o mesmo que o script U-SQL autônomo.

As duas capturas de tela que se seguem mostram um arquivo code-behind e seu arquivo de script U-SQL associado:
 
![Code-behind das Ferramentas do Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind.png)

![Code-behind das Ferramentas do Data Lake para Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind-call.png) 

## <a name="register-assemblies"></a>Registrar assemblies

Usando as Ferramentas do Data Lake, você pode registrar assemblies de código personalizado no metastore do Data Lake Analytics.

**Para registrar um assembly**

1.  Pressione **CTRL+SHIFT+P** para abrir a Paleta de Comandos.
2.  Insira **ADL:Registrar Assembly**.
3.  Selecione uma conta do Data Lake Analytics.
4.  Selecione um banco de dados.
5.  Especifique o caminho do assembly local.

## <a name="access-data-lake-analytics-metadata"></a>Acessar metadados do Data Lake Analytics

Depois que estiver conectado ao Azure, você poderá usar as seguintes etapas para acessar os metadados do U-SQL:

**Para acessar os metadados do U-SQL**

1.  Pressione **CTRL+SHIFT+P** e digite **ADL:Listar Tabelas**.
2.  Clique em uma das contas do Data Lake Analytics.
3.  Clique em um dos bancos de dados do Data Lake Analytics.
4.  Clique em um dos esquemas. Você pode ver as tabelas.

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

##<a name="next-steps"></a>Próximas etapas:

- Para obter informações introdutórias sobre o Data Lake Analytics, veja [Tutorial: Introdução ao Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md).
- Para saber mais sobre como usar as Ferramentas do Data Lake para Visual Studio, confira [Tutorial: Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).






<!--HONumber=Nov16_HO4-->


