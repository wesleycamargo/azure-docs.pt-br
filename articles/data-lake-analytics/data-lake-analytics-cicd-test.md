---
title: Como testar seu código do Azure Data Lake Analytics | Microsoft Docs
description: Saiba como adicionar casos de teste ao U-SQL e código C# estendido para o Azure Data Lake Analytics.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: ''
editor: ''
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: bfb314348caf1d5bf83c940c0bce79e87d6d2593
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/06/2018
ms.locfileid: "37890625"
---
# <a name="how-to-test-your-azure-data-lake-analytics-code"></a>Como testar seu código do Azure Data Lake Analytics

O Azure Data Lake fornece o U-SQL, que é uma linguagem que combina o SQL declarativo com o C# imperativo para processar dados em qualquer escala. Neste documento, você aprenderá a criar casos de teste para o U-SQL e o código UDO (Operador Definido pelo Usuário) de C# estendido.

## <a name="test-u-sql-scripts"></a>Testar scripts U-SQL

O script U-SQL é compilado e otimizado em código executável e executado em computadores na nuvem ou em seu computador local. Na execução, o processo de compilação e otimização tratará o script U-SQL completo como um todo. Não é possível realizar um "teste de unidade" tradicional para cada instrução. No entanto, usando o SDK de teste do U-SQL e o SDK de execução local, você pode realizar testes no nível do script.

### <a name="create-test-cases-for-u-sql-script"></a>Criar casos de teste para o script U-SQL

As Ferramentas do Azure Data Lake para Visual Studio oferecem uma boa experiência para ajudá-lo a criar casos de teste do script U-SQL.

1.  Clique com o botão direito do mouse em um script U-SQL no Gerenciador de Soluções e clique em **Criar teste de unidade**.
2.  Realize a configuração para criar um novo projeto de teste ou inserir o caso de teste em um projeto de teste existente.

    ![Ferramentas do Data Lake para Visual Studio criam um projeto de teste do U-SQL](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project.png) 

    ![Ferramentas do Data Lake para Visual Studio criam uma configuração de projeto de teste do U-SQL](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png) 

### <a name="manage-test-data-source"></a>Gerenciar a fonte de dados de teste

Ao testar scripts U-SQL, são necessários arquivos de entrada de teste. Você pode gerenciar esses dados configurando a opção **Testar fonte de dados** na propriedade do projeto U-SQL. Quando você chama a interface `Initialize()` no SDK de teste do U-SQL, uma pasta temporária local da Raiz de Dados é criada no diretório de trabalho do projeto de teste e todos os arquivos e subpastas (e arquivos em subpastas) na pasta da fonte de dados de teste são copiados para a pasta temporária local da raiz de dados antes da execução dos casos de teste do script U-SQL. Você pode adicionar mais pastas da fonte de dados de teste separando o caminho da pasta de dados de teste com ponto e vírgula.

![Ferramentas do Data Lake para Visual Studio configuram a fonte de dados de teste do projeto](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-database-environment-for-test"></a>Gerenciar o ambiente do banco de dados para teste

Se seus scripts U-SQL utilizam ou consultam objetos de banco de dados U-SQL, por exemplo, chamando procedimentos armazenados, será necessário inicializar o ambiente do banco de dados antes de executar casos de teste do U-SQL. A interface `Initialize()` no SDK de teste do U-SQL ajuda você a implantar todos os bancos de dados que são referenciados pelo projeto U-SQL para a pasta temporária local da Raiz de Dados no diretório de trabalho do projeto de teste. 

Saiba mais sobre [como você pode gerenciar referências de projetos de banco de dados U-SQL para o projeto U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).

### <a name="verify-test-results"></a>Verificar resultados do teste

A interface `Run()` retorna o resultado da execução do trabalho, sendo que 0 significa bem-sucedido e 1 significa falha. Você também pode usar funções assert do C# para verificar as saídas. 

### <a name="execute-test-cases-in-visual-studio"></a>Executar casos de teste no Visual Studio

O projeto de teste do script U-SQL é criado sobre a estrutura de teste da unidade do C#. Após a compilação do projeto, execute todos os casos de teste em **Gerenciador de Testes > Lista de Reprodução** ou clique com o botão direito no arquivo .cs e escolha **Executar Testes**.

## <a name="test-c-udos"></a>Testar UDOs de C#

### <a name="create-test-cases-for-c-udos"></a>Criar casos de teste para UDOs de C#

Você pode usar a estrutura de teste de unidade do C# para testar seus UDOs (Operadores Definidos pelo Usuário) de C#. Ao testar UDOs, prepare o objeto **IRowset** correspondente como entradas.

Há duas maneiras de criar o objeto IRowset:

1.  Carregar dados de um arquivo para criar o objeto IRowset

    ```csharp
    //Schema: "a:int, b:int"
    USqlColumn<int> col1 = new USqlColumn<int>("a");
    USqlColumn<int> col2 = new USqlColumn<int>("b");
    List<IColumn> columns = new List<IColumn> { col1, col2 };
    USqlSchema schema = new USqlSchema(columns);

    //Generate one row with default values
    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Get data from file
    IRowset rowset = UnitTestHelper.GetRowsetFromFile(@"processor.txt", schema, output.AsReadOnly(), discardAdditionalColumns: true, rowDelimiter: null, columnSeparator: '\t');
    ```

2.  Usar dados de uma coleção de dados para criar o objeto IRowset

    ```csharp
    //Schema: "a:int, b:int"
    USqlSchema schema = new USqlSchema(
        new USqlColumn<int>("a"),
        new USqlColumn<int>("b")
    );

    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Generate Rowset with specified values
    List<object[]> values = new List<object[]>{
        new object[2] { 2, 3 },
        new object[2] { 10, 20 }
    };

    IEnumerable<IRow> rows = UnitTestHelper.CreateRowsFromValues(schema, values);
    IRowset rowset = UnitTestHelper.GetRowsetFromCollection(rows, output.AsReadOnly());
    ```

### <a name="verify-test-results"></a>Verificar resultados do teste

Depois de chamar funções UDO, verifique o resultado através do esquema e a verificação de valor Rowset usando funções Assert de C#. O código de exemplo pode estar no Projeto de exemplo de teste de unidade do UDO de C# do U-SQL em **Arquivo > Novo > Projeto** no Visual Studio.

### <a name="execute-test-cases-in-visual-studio"></a>Executar casos de teste no Visual Studio

Após a compilação do projeto de teste, execute todos os casos de teste em **Gerenciador de Testes > Lista de Reprodução** ou clique com o botão direito no arquivo .cs e escolha **Executar Testes**.

## <a name="run-test-cases-in-visual-studio-team-service"></a>Executar casos de teste no Visual Studio Team Service

O **projeto de teste do script U-SQL** e o **projeto de teste do UDO de C#** herdam o projeto de teste de unidade de C#. A [tarefa de Teste do Visual Studio](https://docs.microsoft.com/vsts/pipelines/test/getting-started-with-continuous-testing?view=vsts) no Visual Studio Team Service pode executar esses casos de teste. 

### <a name="run-u-sql-test-cases-in-visual-studio-team-service"></a>Executar casos de teste do U-SQL no Visual Studio Team Service

No caso do teste do U-SQL, lembre-se de carregar o `CPPSDK` em seu computador de compilação e passe o caminho `CPPSDK` para USqlScriptTestRunner(cppSdkFolderFullPath: @"").

**O que é CPPSDK?**

O CPPSDK é um pacote que inclui o Microsoft Visual C++ 14 e o Windows SDK 10.0.10240.0, que é o ambiente exigido pelo tempo de execução do U-SQL. Você pode obter esse pacote na pasta de instalação das Ferramentas do Azure Data Lake para Visual Studio:

- No Visual Studio 2015, ela está em `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`
- No Visual Studio 2017, ela está em `C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\SDK\ScopeCppSDK`

**Como preparar o CPPSDK no agente de compilação do Visual Studio Team Service**

A maneira comum de preparar essa dependência do CPPSDK no Visual Studio Team Service é feita da seguinte forma:

1.  Faça um zip com a pasta que inclui as bibliotecas do CPPSDK.
2.  Faça check-in do arquivo zip em seu sistema de controle de fonte. (O arquivo zip pode verificar se você fez check-in em todas as bibliotecas na pasta CPPSDK ou alguns arquivos serão ignorados pelo ". gitignore").
3.  Descompacte o arquivo zip no Pipeline de build.
4.  Aponte o `USqlScriptTestRunner` para essa pasta descompactada no computador de compilação.

### <a name="run-c-udo-test-cases-in-visual-studio-team-service"></a>Executar casos de teste do UDO de C# no Visual Studio Team Service

Para teste de UDO de C#, lembre-se de fazer referência aos seguintes assemblies necessários para os UDOs. Se você fizer referência a eles por meio [do pacote do Nuget Microsoft.Azure.DataLake.USQL.Interfaces](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/), não se esqueça de adicionar uma tarefa de restauração do NuGet em seu pipeline de build.

* Microsoft.Analytics.Interfaces
* Microsoft.Analytics.Types
* Microsoft.Analytics.UnitTest

## <a name="next-steps"></a>Próximas etapas

- [Como configurar o pipeline de CI/CD para o Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Executar um script U-SQL no computador local](data-lake-analytics-data-lake-tools-local-run.md)
- [Usar o projeto de banco de dados U-SQL para desenvolver o banco de dados U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)

