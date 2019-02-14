---
title: Início Rápido para uso dos Serviços de Machine Learning (com R) no Banco de Dados SQL do Azure (versão prévia) | Microsoft Docs
description: Este tópico mostra como usar os Serviços de Machine Learning no Banco de Dados SQL, e como executar scripts R para fornecer análise avançada em larga escala e a capacidade de levar cálculos e processamento até onde estão os dados, eliminando a necessidade de efetuar pull de dados pela rede.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: dphansen
ms.author: davidph
ms.reviewer: ''
manager: cgronlun
ms.date: 02/12/2019
ms.openlocfilehash: 61c4edc5ec9c690944047ce67f619f0f69f62f6c
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56236729"
---
# <a name="quickstart-use-machine-learning-services-with-r-in-azure-sql-database-preview"></a>Início rápido: Usar os Serviços do Machine Learning (com o R) no Banco de Dados SQL do Azure (versão prévia)

Este artigo explica como você pode usar a versão prévia pública dos [Serviços do Machine Learning (com o R) no Banco de Dados SQL do Azure](sql-database-machine-learning-services-overview.md). Ele explica as noções básicas da movimentação de dados entre um banco de dados SQL e R. Também explica como encapsular o código R bem formado no procedimento armazenado [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) a fim de criar, treinar e usar modelos de machine learning em um banco de dados SQL.

Use o poder da linguagem R para fornecer análise avançada e aprendizado de máquina no banco de dados. Essa capacidade leva os cálculos e o processamento para o local em que os dados residem, eliminando a necessidade de efetuar pull de dados na rede. Além disso, aproveite o poder dos pacotes empresariais do R para fornecer análise avançada em escala.

Os Serviços do Machine Learning incluem uma distribuição base do R, sobreposta com pacotes empresariais do R da Microsoft. As funções e os algoritmos do R da Microsoft foram desenvolvidos para escala e utilidade, fornecendo análise preditiva, modelagem estatística, visualizações de dados e algoritmos de aprendizado de máquina de ponta.

Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/) antes de começar.

> [!NOTE]
> Atualmente, os Serviços do Machine Learning (com o R) no Banco de Dados SQL do Azure estão em versão prévia pública. [Inscrever-se na versão prévia](sql-database-machine-learning-services-overview.md#signup).

## <a name="prerequisites"></a>Pré-requisitos

Para executar o código de exemplo nestes exercícios, primeiro você precisa ter um banco de dados SQL com os Serviços de Machine Learning (com R) habilitado. Durante a versão prévia pública, a Microsoft realizará sua integração e habilitará o aprendizado de máquina para seu banco de dados novo ou existente. Siga as etapas em [Inscrever-se na versão prévia](sql-database-machine-learning-services-overview.md#signup).

Você pode se conectar ao Banco de Dados SQL e executar os scripts R em qualquer ferramenta de gerenciamento de banco de dados ou de consulta, contanto que ele possa se conectar a um Banco de Dados SQL e executar uma consulta T-SQL ou um procedimento armazenado. Este início rápido usa o [SQL Server Management Studio](sql-database-connect-query-ssms.md).

Para o exercício de [adicionar um pacote](#add-package), você também precisará instalar [R](https://www.r-project.org/) e [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) no computador local.

Este início rápido também exige a configuração de uma regra de firewall no nível de servidor. Para obter um início rápido que mostra como fazer isso, confira [Criar regra de firewall no nível de servidor](sql-database-server-level-firewall-rule.md).

## <a name="verify-r-exists"></a>Verificação da existência de R

Você pode confirmar se os Serviços de Machine Learning (com R) estão habilitados para seu banco de dados SQL. Siga as etapas abaixo.

1. Abra o SQL Server Management Studio e conecte-se ao seu banco de dados SQL. Para obter mais informações sobre como se conectar, confira [Início Rápido: Usar o SQL Server Management Studio para conectar e consultar um Banco de Dados SQL do Azure](sql-database-connect-query-ssms.md).

1. Execute o código abaixo. 

    ```sql
    EXECUTE sp_execute_external_script
    @language =N'R',
    @script=N'print(31 + 11)';
    GO
    ```
    Se tudo estiver bem, você verá uma mensagem de resultado como esta.

    ```text
    STDOUT message(s) from external script: 
    42
    ```

1. Se você receber erros, talvez a versão prévia pública dos Serviços de Machine Learning (com R) não esteja habilitada para seu banco de dados SQL. Veja como se inscrever na versão prévia pública acima.

## <a name="grant-permissions"></a>Conceder permissões

Se você for um administrador, pode executar automaticamente o código externo. Todos os outros devem receber permissão.

Substitua `<username>` por um logon de usuário de banco de dados válido antes de executar o comando.

```sql
GRANT EXECUTE ANY EXTERNAL SCRIPT TO <username>
```

## <a name="basic-r-interaction"></a>Interação de R básica

Há duas maneiras de executar o código R no Banco de Dados SQL:

+ Adicionar um script R como argumento do procedimento armazenado do sistema, [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).
+ Em um [cliente remoto R](https://docs.microsoft.com/sql/advanced-analytics/r/set-up-a-data-science-client), conectar-se ao banco de dados SQL e executar o código usando o Banco de Dados SQL como o contexto de computação.

O exercício a seguir se concentra no primeiro modelo de interação: como transmitir o código R para um procedimento armazenado.

1. Execute um script simples para ver como um script R é executado no banco de dados SQL.

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R',
    @script = N'
    a <- 1
    b <- 2
    c <- a/b
    d <- a*b
    print(c, d)'
    ```

2. Partindo do princípio que tudo está configurado corretamente, o resultado correto é calculado e a função R `print` retorna o resultado na janela **Mensagens**.

    **Resultados**

    ```text
    STDOUT message(s) from external script: 
    0.5 2
    ```

    Apesar de ser útil receber mensagens **stdout** ao testar seu código, normalmente você precisa retornar os resultados em formato tabular, para poder usá-los em um aplicativo ou gravá-los em uma tabela. Consulte a seção de entradas e saídas abaixo para saber mais.

Lembre-se, tudo dentro do argumento `@script` deve ser código R válido.

## <a name="inputs-and-outputs"></a>Entradas e saídas

Por padrão, [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) aceita um único conjunto de dados de entrada, que normalmente é fornecido na forma de uma consulta SQL válida. Outros tipos de entrada podem ser transmitidos como variáveis SQL.

O procedimento armazenado retorna uma única estrutura de dados de R como saída, mas você também pode gerar escalares e modelos como variáveis. Por exemplo, você pode gerar um modelo treinado como uma variável binária e passá-lo para uma instrução T-SQL INSERT a fim de gravar esse modelo em uma tabela. Você também pode gerar gráficos (em formato binário) ou escalares (valores individuais, como data e hora, tempo decorrido para treinar o modelo e assim por diante).

Por enquanto, vamos examinar apenas as variáveis de entrada e saída padrão de sp_execute_external_script: `InputDataSet` e `OutputDataSet`.

1. Crie uma pequena tabela de dados de teste executando a seguinte instrução T-SQL:

    ```sql
    CREATE TABLE RTestData (col1 INT NOT NULL)
    INSERT INTO RTestData VALUES (1);
    INSERT INTO RTestData VALUES (10);
    INSERT INTO RTestData VALUES (100);
    GO
    ```

    Após a criação da tabela, use a seguinte instrução para consultá-la:
  
    ```sql
    SELECT * FROM RTestData
    ```

    **Resultados**

    ![Conteúdo da tabela RTestData](./media/sql-database-connect-query-r/select-rtestdata.png)

2. Você pode obter os dados da tabela como entrada para seu script R. Execute a instrução a seguir. Ela obtém os dados da tabela, faz uma viagem de ida e volta pelo tempo de execução de R e retorna os valores com o nome de coluna *NewColName*.

    Os dados retornados pela consulta são transmitidos para o tempo de execução de R, que retorna os dados para o Banco de Dados SQL como uma estrutura de dados. A cláusula WITH RESULT SETS define o esquema da tabela de dados retornada para o Banco de Dados SQL.

    ```sql
    EXECUTE sp_execute_external_script
        @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS (([NewColName] INT NOT NULL));
    ```

    **Resultados**

    ![Saída do script R que retorna dados de uma tabela](./media/sql-database-connect-query-r/r-output-rtestdata.png)

3. Vamos alterar o nome das variáveis de entrada ou saída. O script acima usou os nomes de variável de entrada e saída padrão, _InputDataSet_ e _OutputDataSet_. Para definir os dados de entrada associados a _InputDatSet_, use a variável *@input_data_1*.

    Nesse script, os nomes das variáveis de entrada e saída para o procedimento armazenado foram alterados para *SQL_out* e *SQL_in*:

    ```sql
    EXECUTE sp_execute_external_script
      @language = N'R'
      , @script = N' SQL_out <- SQL_in;'
      , @input_data_1 = N' SELECT 12 as Col;'
      , @input_data_1_name  = N'SQL_in'
      , @output_data_1_name =  N'SQL_out'
      WITH RESULT SETS (([NewColName] INT NOT NULL));
    ```

    Observe que R diferencia maiúsculas de minúsculas e, portanto, a capitalização das variáveis de entrada e saída em `@input_data_1_name` e `@output_data_1_name` precisa coincidir com a do código R em `@script`. 

    Além disso, a ordem dos parâmetros é importante. Você deve especificar os parâmetros necessários *@input_data_1* e *@output_data_1* primeiro para usar os parâmetros opcionais *@input_data_1_name* e *@output_data_1_name*.

    Só é possível passar um conjunto de dados de entrada como parâmetro, e você pode retornar apenas um conjunto de dados. No entanto, você pode chamar outros conjuntos de dados em seu código R e pode retornar saídas de outros tipos, além do conjunto de dados. Você também pode adicionar a palavra-chave OUTPUT a qualquer parâmetro para que ele retorne com os resultados. 

    A instrução `WITH RESULT SETS` define o esquema para os dados usados no Banco de Dados SQL. Você precisa fornecer tipos de dados compatíveis com SQL para cada coluna retornada por R. Use a definição de esquema para fornecer também novos nomes de coluna, pois não é necessário usar os nomes de coluna da estrutura de dados de R.

4. Você também pode gerar valores usando o script R e deixar a cadeia de caracteres de consulta de entrada em _@input_data_1_ em branco.

    ```sql
    EXECUTE sp_execute_external_script
        @language = N'R'
        , @script = N' mytextvariable <- c("hello", " ", "world");
            OutputDataSet <- as.data.frame(mytextvariable);'
        , @input_data_1 = N''
    WITH RESULT SETS (([Col1] CHAR(20) NOT NULL));
    ```

    **Resultados**

    ![Resultados da consulta usando @script como entrada](./media/sql-database-connect-query-r/r-data-generated-output.png)

## <a name="check-r-version"></a>Verificar a versão de R

Se você quiser ver qual versão de R está instalada no banco de dados SQL, faça o seguinte:

1. Execute o script abaixo em seu banco de dados SQL.

    ```SQL
    EXECUTE sp_execute_external_script
    @language =N'R',
    @script=N'print(version)';
    GO
    ```

2. A função R `print` retorna a versão na janela **Mensagens**. Na saída de exemplo abaixo, veja que o Banco de Dados SQL, neste caso, tem a versão 3.4.4 do R instalada.

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

A Microsoft fornece alguns pacotes de R pré-instalados com os Serviços de Machine Learning no banco de dados SQL. Para ver uma lista dos pacotes de R instalados, incluindo a versão, as dependências, a licença e as informações de caminho de biblioteca, execute as etapas abaixo. Para adicionar outros pacotes, consulte a seção [Adicionar um pacote](#add-package).

1. Execute o script abaixo em seu banco de dados SQL.

    ```SQL
    EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
    OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License", "LibPath")]);'
    WITH result sets((Package NVARCHAR(255), Version NVARCHAR(100), Depends NVARCHAR(4000)
        , License NVARCHAR(1000), LibPath NVARCHAR(2000)));
    ```

2. A saída se origina em `installed.packages()` em R e retorna como um conjunto de resultados.

    **Resultados**

    ![Pacotes instalados em R](./media/sql-database-connect-query-r/r-installed-packages.png)

## <a name="create-a-predictive-model"></a>Criar um modelo preditivo

Você pode treinar um modelo usando R e salvá-lo em uma tabela no banco de dados SQL. Neste exercício, você treinará um modelo de regressão simples que prevê a distância de parada de um carro com base na velocidade. Você usará o conjunto de dados `cars` incluído com o R, pois ele é pequeno e fácil de entender.

1. Primeiro, crie uma tabela para salvar os dados de treinamento.

    ```sql
    CREATE TABLE dbo.CarSpeed (speed INT NOT NULL, distance INT NOT NULL)
    GO
    INSERT INTO dbo.CarSpeed (speed, distance)
    EXEC sp_execute_external_script
    @language = N'R'
        , @script = N'car_speed <- cars;'
        , @input_data_1 = N''
        , @output_data_1_name = N'car_speed'
    GO
    ```

    Há vários conjuntos de dados, pequenos e grandes, incluídos no tempo de execução de R. Para obter uma lista dos conjuntos de dados instalados com R, digite `library(help="datasets")` em um prompt de comando de R.

2. Crie um modelo de regressão. Os dados de velocidade do carro contêm duas colunas, ambas numéricas, `dist` e `speed`. Há várias observações sobre algumas velocidades. A partir desses dados, você criará um modelo de regressão linear que descreve relações entre a velocidade do carro e a distância necessária para que ele pare.

    Os requisitos de um modelo linear são simples:

    - Defina uma fórmula que descreve a relação entre a variável dependente `speed` e a variável independente `distance`.

    - Forneça dados de entrada para usar no treinamento do modelo.

    > [!TIP]
    > Caso você precise relembrar os modelos lineares, recomendamos este tutorial, que descreve o processo de ajuste de um modelo usando rxLinMod: [Ajustando modelos lineares](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-linear-model)

    Para criar o modelo, defina a fórmula dentro do código R e passe os dados como um parâmetro de entrada.

    ```sql
    DROP PROCEDURE IF EXISTS generate_linear_model;
    GO
    CREATE PROCEDURE generate_linear_model
    AS
    BEGIN
        EXEC sp_execute_external_script
        @language = N'R'
        , @script = N'lrmodel <- rxLinMod(formula = distance ~ speed, data = CarsData);
            trained_model <- data.frame(payload = as.raw(serialize(lrmodel, connection=NULL)));'
        , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
        , @input_data_1_name = N'CarsData'
        , @output_data_1_name = N'trained_model'
        WITH RESULT SETS ((model VARBINARY(max)));
    END;
    GO
    ```

    O primeiro argumento para rxLinMod é o parâmetro *formula*, que define a distância como dependente da velocidade. Os dados de entrada são armazenados na variável `CarsData`, que é preenchida pela consulta SQL. Se você não atribuir um nome específico para seus dados de entrada, o nome da variável padrão será _InputDataSet_.

2. Em seguida, crie uma tabela na qual armazenar o modelo, para que você possa readaptá-lo ou usá-lo para previsão. A saída de um pacote R que cria um modelo é geralmente um **objeto binário**. Portanto, a tabela deve fornecer uma coluna do tipo **VARBINARY(max)**.

    ```sql
    CREATE TABLE dbo.stopping_distance_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(max) NOT NULL
    );
    ```
3. Para salvar o modelo, execute a seguinte instrução Transact-SQL para chamar o procedimento armazenado, gerar o modelo e salvá-lo em uma tabela.

    ```sql
    INSERT INTO dbo.stopping_distance_models (model)
    EXEC generate_linear_model;
    ```

    Observe que, se você executar esse código uma segunda vez, receberá este erro:

    ```text
    Violation of PRIMARY KEY constraint...Cannot insert duplicate key in object dbo.stopping_distance_models
    ```

    Uma opção para evitar esse erro é atualizar o nome de cada modelo novo. Por exemplo, você pode alterar o nome para algo mais descritivo e incluir o tipo de modelo, o dia em que ele foi criado e assim por diante.

    ```sql
    UPDATE dbo.stopping_distance_models
    SET model_name = 'rxLinMod ' + FORMAT(GETDATE(), 'yyyy.MM.HH.mm', 'en-gb')
    WHERE model_name = 'default model'
    ```

4. Em geral, a saída de R do procedimento armazenado [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) é limitada a uma única estrutura de dados.

    No entanto, é possível retornar saídas de outros tipos, como escalares, além da estrutura de dados.

    Por exemplo, suponha que você queria treinar um modelo, mas queria exibir imediatamente uma tabela de coeficientes do modelo. Você pode criar a tabela de coeficientes como o conjunto de resultados principal e gerar o modelo treinado em uma variável SQL. Você poderia reutilizar o modelo imediatamente chamando a variável, ou poderia salvar o modelo em uma tabela, conforme mostrado aqui.

    ```sql
    DECLARE @model VARBINARY(max), @modelname VARCHAR(30)
    EXEC sp_execute_external_script
        @language = N'R'
        , @script = N'
            speedmodel <- rxLinMod(distance ~ speed, CarsData)
            modelbin <- serialize(speedmodel, NULL)
            OutputDataSet <- data.frame(coefficients(speedmodel));'
        , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
        , @input_data_1_name = N'CarsData'
        , @params = N'@modelbin varbinary(max) OUTPUT'
        , @modelbin = @model OUTPUT
        WITH RESULT SETS (([Coefficient] FLOAT NOT NULL));

    -- Save the generated model
    INSERT INTO dbo.stopping_distance_models(model_name, model)
    VALUES ('latest model', @model)
    ```

    **Resultados**

    ![Modelo treinado com saída adicional](./media/sql-database-connect-query-r/r-train-model-with-additional-output.png)

## <a name="predict"></a>Prever

Use o modelo que você criou na seção anterior para pontuar previsões em relação a novos dados. Para atribuir a _pontuação_ usando novos dados, use um dos modelos treinados da tabela e chame um novo conjunto de dados no qual basear as previsões. A pontuação é um termo usado, às vezes, em ciência de dados para significar a geração de previsões, probabilidades ou de outros valores com base em novos dados inseridos em um modelo treinado.

1. Primeiro, crie uma tabela com novos dados de velocidade. Você notou que os dados de treinamento originais param a uma velocidade de 40 km/h? Isso ocorre porque os dados originais têm base em um experimento de 1920! Talvez você se pergunte: "quanto tempo um automóvel de 1920 demoraria para parar, supondo que ele poderia chegar a 96 km/h ou até mesmo a 160 km/h?" Para responder a essa pergunta, você deve fornecer novos valores de velocidade.

    ```sql
    CREATE TABLE dbo.NewCarSpeed(speed INT NOT NULL,
        distance INT NULL
    )
    GO
    INSERT dbo.NewCarSpeed(speed)
    VALUES (40), (50), (60), (70), (80), (90), (100)
    ```

    Neste exemplo, como seu modelo tem base no algoritmo **rxLinMod** fornecido como parte do pacote **RevoScaleR**, chame a função [rxPredict](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict), em vez da função R genérica `predict`.

    ```sql
    DECLARE @speedmodel varbinary(max) = 
        (SELECT model FROM dbo.stopping_distance_models WHERE model_name = 'latest model');

    EXEC sp_execute_external_script
        @language = N'R'
        , @script = N'
                current_model <- unserialize(as.raw(speedmodel));
                new <- data.frame(NewCarData);
                predicted.distance <- rxPredict(current_model, new);
                str(predicted.distance);
                OutputDataSet <- cbind(new, ceiling(predicted.distance));
                '
        , @input_data_1 = N'SELECT speed FROM [dbo].[NewCarSpeed]'
        , @input_data_1_name = N'NewCarData'
        , @params = N'@speedmodel varbinary(max)'
        , @speedmodel = @speedmodel
    WITH RESULT SETS ((new_speed INT, predicted_distance INT));
    ```

    O script acima executa as seguintes etapas:

    + Use uma instrução SELECT para obter um único modelo da tabela e passá-lo como parâmetro de entrada.

    + Depois de recuperar o modelo da tabela, chame a função `unserialize` no modelo.

        > [!TIP] 
        > Verifique também as novas [funções de serialização](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel) fornecidas pelo RevoScaleR, que dá suporte à pontuação em tempo real.
    + Aplique a função `rxPredict` com os devidos argumentos para o modelo e forneça os novos dados de entrada.

    + No exemplo, a função `str` é adicionada durante a fase de teste para verificar o esquema dos dados retornado de R. Você pode remover a instrução mais tarde.

    + Os nomes de coluna usados no script R não são necessariamente passados para a saída do procedimento armazenado. Aqui, usamos a cláusula WITH RESULTS para definir novos nomes de coluna.

    **Resultados**

    ![Conjunto de resultados para previsão da distância de parada](./media/sql-database-connect-query-r/r-predict-stopping-distance-resultset.png)

    Também é possível usar [PREDICT no Transact-SQL](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql) para gerar um valor ou pontuação prevista com base em um modelo armazenado.

<a name="add-package"></a>

## <a name="add-a-package"></a>Adicionar um pacote

Se você precisar usar um pacote que ainda não está instalado em seu banco de dados SQL, instale-o usando [sqlmlutils](https://github.com/Microsoft/sqlmlutils). Execute as etapas abaixo para instalar o pacote.

1. Baixe a versão mais recente do arquivo zip **sqlmlutils** de [github.com/Microsoft/sqlmlutils/tree/master/R/dist](https://github.com/Microsoft/sqlmlutils/tree/master/R/dist) em seu computador local. Não é necessário descompactar o arquivo.

1. Se você não tiver o R instalado, baixe-o em [www.r-project.org](https://www.r-project.org/) e instale-o em seu computador local. R está disponível para Windows, macOS e Linux. Neste exemplo, estamos usando Windows.

1. Primeiro, instale o pacote **RODBCext**, que é um pré-requisito para **sqlmlutils**. **RODBCext** também instala a dependência do pacote **RODBC**. Abra um **Prompt de Comando** e execute o seguinte comando:

    ```
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    ```

    Se receber um erro como "'R' não é reconhecido como um comando interno ou externo, programa operável ou arquivo em lotes”, provavelmente significa que o caminho até R.exe não está incluído em sua variável de ambiente **PATH** no Windows. Você pode adicionar o diretório à variável de ambiente ou navegar até o diretório no prompt de comando (por exemplo, `cd C:\Program Files\R\R-3.5.1\bin`), antes de executar o comando.

1. Use o comando **R CMD INSTALL** para instalar **sqlmlutils**. Especifique o caminho até o diretório no qual você baixou o arquivo zip e o nome do arquivo zip. Por exemplo: 

    ```
    R CMD INSTALL C:\Users\youruser\Downloads\sqlmlutils_0.5.0.zip
    ```

    A saída obtida deve ser semelhante à seguinte:

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/youruser/Documents/R/win-library/3.5'
    package 'sqlmlutils' successfully unpacked and MD5 sums checked
    ```

1. Neste exemplo, você usará RStudio Desktop como o IDE. Se preferir, você poderá usar outro IDE. Baixe e instale o RStudio Desktop em [www.rstudio.com/products/rstudio/download/](https://www.rstudio.com/products/rstudio/download/), se ainda não tiver feito isso.

1. Abra **RStudio** e crie um novo arquivo **Script R**. Use o código R a seguir para instalar um pacote usando sqlmlutils. No exemplo a seguir, você instalará o pacote [glue](https://cran.r-project.org/web/packages/glue/), que pode formatar e interpolar uma cadeia de caracteres.

    ```R
    library(sqlmlutils) 
    connection <- connectionInfo(server= "yourserver.database.windows.net", 
        database = "yourdatabase", uid = "yoursqluser", pwd = "yoursqlpassword")
    sql_install.packages(connectionString = connection, pkgs = "glue", verbose = TRUE, scope = "PUBLIC")
    ```

    > [!NOTE]
    > O **escopo** pode ser **PÚBLICO** ou **PRIVADO**. O escopo público é útil para o administrador de banco de dados poder instalar pacotes que todos os usuários podem usar. O escopo privado disponibiliza o pacote apenas para o usuário que o instalar. Se você não especificar o escopo, o escopo padrão será **PRIVADO**.

1. Agora, verifique se o pacote **glue** foi instalado.

    ```R
    r<-sql_installed.packages(connectionString = connection, fields=c("Package", "LibPath", "Attributes", "Scope"))
    View(r)
    ```

    **Resultados**

    ![Conteúdo da tabela RTestData](./media/sql-database-connect-query-r/r-verify-package-install.png)


1. Após a instalação do pacote, você poderá usá-lo no script R por meio de **sp_execute_external_script**. Abra o **SQL Server Management Studio** e conecte-se ao seu banco de dados SQL. Execute o seguinte script:

    ```sql
    EXEC sp_execute_external_script @language = N'R'
    , @script = N'
    library(glue)

    name <- "Fred"
    age <- 50
    anniversary <- as.Date("2020-06-14")
    text <- glue(''My name is {name}, '',
        ''my age next year is {age + 1}, '',
        ''my anniversary is {format(anniversary, "%A, %B %d, %Y")}.'')
    print(text)
    ';
    ```

    Você verá o resultado a seguir na guia Mensagens.

    **Resultados**

    ```text
    STDOUT message(s) from external script:
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

1. Se você quiser remover o pacote, execute o script R a seguir no **RStudio** em seu computador local.

    ```R
    library(sqlmlutils) 
    connection <- connectionInfo(server= "yourserver.database.windows.net", 
        database = "yourdatabase", uid = "yoursqluser", pwd = "yoursqlpassword")
    sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC") 
    ```

> [!NOTE]
> Outra maneira de instalar pacotes R no banco de dados SQL é carregar o pacote R do fluxo de bytes com [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre os Serviços do Machine Learning, confira os artigos abaixo. Embora alguns desses artigos refiram-se ao SQL Server, a maioria das informações também é aplicável aos Serviços do Machine Learning (com o R) no Banco de Dados SQL do Azure.

- [Serviços do Machine Learning do Banco de Dados SQL do Azure (com o R)](sql-database-machine-learning-services-overview.md)
- [Serviços de Machine Learning do SQL Server](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)
- [Tutorial: Aprender sobre a análise no banco de dados usando o R no SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers)
- [Passo a passo sobre ciência de dados de ponta a ponta para R e SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough)
- [Tutorial: Usar funções RevoScaleR do R com os dados do SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/deepdive-data-science-deep-dive-using-the-revoscaler-packages)
