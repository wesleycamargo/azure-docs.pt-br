---
title: Criar e executar scripts simples do R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Execute scripts simples do R em Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: ada09959391c551a9eff4d96b186be29c1e3b7a8
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013085"
---
# <a name="create-and-run-simple-r-scripts-in-azure-sql-database-machine-learning-services-preview"></a>Criar e executar scripts R simples em Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia)

Neste início rápido, você vai criar e executar um conjunto de scripts simples do R usando a versão prévia pública dos [Serviços do Machine Learning (com R) no Banco de Dados SQL do Azure](sql-database-machine-learning-services-overview.md). Você aprenderá como encapsular um script de R bem formado no procedimento armazenado [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) e executar o script em um banco de dados SQL.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/) antes de começar.

- Para executar o código de exemplo nestes exercícios, primeiro você precisa ter um Banco de dados SQL do Azure com os Serviços do Machine Learning (com R) habilitados. Durante a versão prévia pública, a Microsoft realizará sua integração e habilitará o aprendizado de máquina para seu banco de dados novo ou existente. Siga as etapas em [Inscrever-se na versão prévia](sql-database-machine-learning-services-overview.md#signup).

- Verifique se você instalou a versão mais recente do SSMS ([SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)). Você pode executar scripts R usando outras ferramentas de consulta ou gerenciamento de banco de dados, mas neste início rápido, você usará o SSMS.

- Este início rápido exige a configuração de uma regra de firewall no nível de servidor. Para obter informações sobre como fazer isso, confira [Criar regra de firewall no nível de servidor](sql-database-server-level-firewall-rule.md).

## <a name="run-a-simple-script"></a>Executar um script simples

Para executar um script R, você o passará como um argumento para o procedimento armazenado do sistema, [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

Nas etapas a seguir, você executará esse script R de exemplo em seu banco de dados SQL:

```r
a <- 1
b <- 2
c <- a/b
d <- a*b
print(c(c, d))
```

1. Abra o **SQL Server Management Studio** e conecte-se ao seu banco de dados SQL.

   Se você precisar de ajuda para se conectar, veja o [Início Rápido: Usar o SQL Server Management Studio para conectar e consultar um Banco de Dados SQL do Azure](sql-database-connect-query-ssms.md).

1. Passar o script R completo para o procedimento armazenado [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

   O script é passado por meio do argumento `@script`. Tudo dentro do argumento `@script` precisa ser código R válido.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    a <- 1
    b <- 2
    c <- a/b
    d <- a*b
    print(c(c, d))
    '
    ```

   Se você receber erros, talvez a versão prévia pública dos Serviços de Machine Learning (com R) não esteja habilitada para seu banco de dados SQL. Veja os [Pré-requisitos](#prerequisites) acima.

   > [!NOTE]
   > Se você for um administrador, poderá executar automaticamente o código externo. Você pode conceder permissão a outros usuários usando o comando:
   <br>**GRANT EXECUTE ANY EXTERNAL SCRIPT TO** *\<nome de usuário\>*.

2. O resultado correto é calculado e a função R `print` retorna o resultado na janela **Mensagens**.

   O resultado deve ser semelhante a este.

    **Resultados**

    ```text
    STDOUT message(s) from external script:
    0.5 2
    ```

## <a name="run-a-hello-world-script"></a>Executar um script Olá, Mundo

Um script de exemplo típico é aquele que gera apenas a cadeia de caracteres "Olá, Mundo". Execute o comando a seguir.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'OutputDataSet<-InputDataSet'
    , @input_data_1 = N'SELECT 1 AS hello'
WITH RESULT SETS(([Hello World] INT));
GO
```

Entradas para esse procedimento armazenado incluem:

| | |
|-|-|
|*@language* | Define a extensão da linguagem para chamar, nesse caso, R |
|*@script* | Define os comandos transmitidos para o tempo de execução de R. O seu script R inteiro deve ser colocado neste argumento como texto Unicode. Você também pode adicionar texto a uma variável do tipo **nvarchar** e, em seguida, chamar a variável |
|*@input_data_1* | dados retornados pela consulta, transmitidos para o tempo de execução de R, que retorna os dados para o SQL Server como uma estrutura de dados |
|A cláusula WITH RESULT SETS | define o esquema da tabela de dados retornada para o SQL Server, adicionando "Olá, Mundo" como o nome da coluna e **int** para o tipo de dados |

O comando gera o seguinte texto como saída:

| Olá Mundo |
|-------------|
| 1 |

## <a name="use-inputs-and-outputs"></a>Usar entradas e saídas

Por padrão, [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) aceita um único conjunto de dados como entrada, que normalmente é fornecido na forma de uma consulta SQL válida. Ele retorna então uma única estrutura de dados de R como saída.

Só é possível passar um conjunto de dados de entrada como parâmetro, e você pode retornar apenas um conjunto de dados. No entanto, você pode chamar outros conjuntos de dados em seu código R e pode retornar saídas de outros tipos, além do conjunto de dados. Você também pode adicionar a palavra-chave OUTPUT a qualquer parâmetro para que ele retorne com os resultados.

Por enquanto, examinaremos apenas as variáveis de entrada e saída padrão de [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql): **InputDataSet** e **OutputDataSet**.

1. Crie uma pequena tabela de dados de teste.

    ```sql
    CREATE TABLE RTestData (col1 INT NOT NULL)
    
    INSERT INTO RTestData
    VALUES (1);
    
    INSERT INTO RTestData
    VALUES (10);
    
    INSERT INTO RTestData
    VALUES (100);
    GO
    ```

1. Use a instrução `SELECT` para consultar a tabela.
  
    ```sql
    SELECT *
    FROM RTestData
    ```

    **Resultados**

    ![Conteúdo da tabela RTestData](./media/sql-database-connect-query-r/select-rtestdata.png)

1. Execute o script R a seguir. Ele recupera os dados da tabela usando a instrução `SELECT`, passa-os pelo tempo de execução do R e retorna os dados como uma estrutura de dados. A cláusula `WITH RESULT SETS` define o esquema da tabela de dados retornada para o Banco de Dados SQL, adicionando o nome de coluna *NewColName*.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    **Resultados**

    ![Saída do script R que retorna dados de uma tabela](./media/sql-database-connect-query-r/r-output-rtestdata.png)

1. Agora alteraremos o nome das variáveis de entrada e de saída. Os nomes padrão de variáveis de entrada e saída são **InputDataSet** e **OutputDataSet**. Esse script altera os nomes para **SQL_in** e **SQL_out**:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N' SQL_out <- SQL_in;'
        , @input_data_1 = N' SELECT 12 as Col;'
        , @input_data_1_name = N'SQL_in'
        , @output_data_1_name = N'SQL_out'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    Observe que o R diferencia maiúsculas de minúsculas. As variáveis de entrada e saída usadas no script R (**SQL_out**, **SQL_in**) precisam corresponder aos valores definidos com `@input_data_1_name` e `@output_data_1_name`, inclusive no uso de maiúsculas e minúsculas.

   > [!TIP]
   > Só é possível passar um conjunto de dados de entrada como parâmetro, e você pode retornar apenas um conjunto de dados. No entanto, você pode chamar outros conjuntos de dados em seu código R e pode retornar saídas de outros tipos, além do conjunto de dados. Você também pode adicionar a palavra-chave OUTPUT a qualquer parâmetro para que ele retorne com os resultados.

1. Você também pode gerar valores usando apenas o script R sem nenhum dado de entrada (`@input_data_1` é definido como em branco).

   O script a seguir gera o texto "olá" e "mundo".

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    mytextvariable <- c("hello", " ", "world");
    OutputDataSet <- as.data.frame(mytextvariable);
    '
        , @input_data_1 = N''
    WITH RESULT SETS(([Col1] CHAR(20) NOT NULL));
    ```

    **Resultados**

    ![Resultados da consulta usando @script como entrada](./media/sql-database-connect-query-r/r-data-generated-output.png)

## <a name="check-r-version"></a>Verificar a versão de R

Se você quiser ver qual versão de R está instalada no banco de dados SQL, execute o script a seguir.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'print(version)';
GO
```

A função R `print` retorna a versão na janela **Mensagens**. Na saída de exemplo abaixo, veja que o Banco de Dados SQL, neste caso, tem a versão 3.4.4 do R instalada.

**Resultados**

```text
STDOUT message(s) from external script:
                   _
platform       x86_64-w64-mingw32
arch           x86_64
os             mingw32
system         x86_64, mingw32
status
major          3
minor          4.4
year           2018
month          03
day            15
svn rev        74408
language       R
version.string R version 3.4.4 (2018-03-15)
nickname       Someone to Lean On
```

## <a name="list-r-packages"></a>Listar pacotes de R

A Microsoft fornece alguns pacotes de R pré-instalados com os Serviços de Machine Learning no banco de dados SQL.

Para ver uma lista dos pacotes de R instalados, incluindo a versão, as dependências, a licença e as informações de caminho de biblioteca, execute o script a seguir.

```SQL
EXEC sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License", "LibPath")]);'
WITH result sets((
            Package NVARCHAR(255)
            , Version NVARCHAR(100)
            , Depends NVARCHAR(4000)
            , License NVARCHAR(1000)
            , LibPath NVARCHAR(2000)
            ));
```

A saída se origina em `installed.packages()` em R e é retornada como um conjunto de resultados.

**Resultados**

![Pacotes instalados em R](./media/sql-database-connect-query-r/r-installed-packages.png)

## <a name="next-steps"></a>Próximas etapas

Para criar um modelo de machine learning usando o R no Banco de Dados SQL, siga este início rápido:

> [!div class="nextstepaction"]
> [Criar e treinar um modelo preditivo em R com os Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia)](sql-database-quickstart-r-train-score-model.md)

Para obter mais informações sobre os Serviços do Machine Learning, confira os artigos abaixo. Embora alguns desses artigos refiram-se ao SQL Server, a maioria das informações também é aplicável aos Serviços do Machine Learning (com o R) no Banco de Dados SQL do Azure.

- [Serviços do Machine Learning do Banco de Dados SQL do Azure (com o R)](sql-database-machine-learning-services-overview.md)
- [Serviços de Machine Learning do SQL Server](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)
- [Tutorial: Aprender sobre a análise no banco de dados usando o R no SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers)
- [Passo a passo sobre ciência de dados de ponta a ponta para R e SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough)
- [Tutorial: Usar funções RevoScaleR do R com os dados do SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/deepdive-data-science-deep-dive-using-the-revoscaler-packages)
