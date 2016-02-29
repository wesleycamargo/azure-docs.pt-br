<properties 
   pageTitle="Usando funções da janela do U-SQL para trabalhos da Análise do Azure Data Lake | Azure" 
   description="Aprenda a usar funções de janela U-SQL." 
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
   ms.date="02/11/2016"
   ms.author="jgao"/>


# Usar funções da janela do U-SQL para trabalhos de análise do Azure Data Lake  

As funções de janela foram introduzidas no padrão ISO/ANSI SQL em 2003. O U-SQL adota um subconjunto de funções de janela conforme definido pelo padrão ANSI SQL.

As funções de janela são usadas para fazer a computação em conjuntos de linhas chamadas *janelas*. As janelas são definidas pela cláusula OVER. As funções de janela resolvem alguns cenários importantes de maneira altamente eficiente.

Este guia de aprendizado/tutorial usa dois conjuntos de dados de exemplo para examinar um exemplo de cenário em que você pode aplicar funções de janela. Para saber mais, consulte [Referência U-SQL](http://go.microsoft.com/fwlink/p/?LinkId=691348).

As funções de janela são categorizadas em:

- [Funções de agregação de relatórios](#reporting-aggregation-functions), como SUM ou AVG
- [Funções de classificação](#ranking-functions), como ROW\_NUMBER, DENSE\_RANK, NTILE e RANK
- [Funções analíticas](#analytic-functions), como distribuição cumulativa, percentuais, ou acessa os dados de uma linha anterior do mesmo conjunto de resultados sem o uso de uma autoassociação.

**Pré-requisitos:**

- Percorra os seguintes tutoriais:

    - [Introdução ao uso de ferramentas Azure Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
    - [Introdução ao uso de U-SQL para trabalhos de análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
- Criar uma conta de Análise do Azure Data Lake conforme instruído em [Introdução ao uso de ferramentas Azure Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Criar um projeto do Visual Studio U-SQL conforme instruído em [Introdução ao uso de U-SQL para trabalhos de análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md).

## Conjuntos de dados de amostra

Este tutorial usa dois conjuntos de dados:

- QueryLog 

    O QueryLog representa uma lista das pessoas que pesquisaram no mecanismo de pesquisa. Cada log de consulta inclui:
    
        - Query - What the user was searching for.
        - Latency - How fast the query came back to the user in milliseconds.
        - Vertical - What kind of content the user was interested in (Web links, Images, Videos).
    
    Copie e cole o script abaixo em seu projeto de U-SQL para construir o conjunto de linhas QueryLog:
    
        @querylog = 
            SELECT * FROM ( VALUES
                ("Banana"  , 300, "Image" ),
                ("Cherry"  , 300, "Image" ),
                ("Durian"  , 500, "Image" ),
                ("Apple"   , 100, "Web"   ),
                ("Fig"     , 200, "Web"   ),
                ("Papaya"  , 200, "Web"   ),
                ("Avocado" , 300, "Web"   ),
                ("Cherry"  , 400, "Web"   ),
                ("Durian"  , 500, "Web"   ) )
            AS T(Query,Latency,Vertical);
    
    Na prática, os dados provavelmente são armazenados em um arquivo de dados. Você poderia acessar os dados em um arquivo delimitado por tabulação usando o seguinte código:
    
        @querylog = 
        EXTRACT 
            Query    string, 
            Latency  int, 
            Vertical string
        FROM "/Samples/QueryLog.tsv"
        USING Extractors.Tsv();

- Funcionários

    O conjunto de dados Funcionário inclui os seguintes campos:
   
        - EmpID - Employee ID.
        - EmpName  Employee name.
        - DeptName - Department name. 
        - DeptID - Deparment ID.
        - Salary - Employee salary.

    Copie e cole o script a seguir em seu projeto de U-SQL para construir o conjunto de linhas Funcionários:

        @employees = 
            SELECT * FROM ( VALUES
                (1, "Noah",   "Engineering", 100, 10000),
                (2, "Sophia", "Engineering", 100, 20000),
                (3, "Liam",   "Engineering", 100, 30000),
                (4, "Emma",   "HR",          200, 10000),
                (5, "Jacob",  "HR",          200, 10000),
                (6, "Olivia", "HR",          200, 10000),
                (7, "Mason",  "Executive",   300, 50000),
                (8, "Ava",    "Marketing",   400, 15000),
                (9, "Ethan",  "Marketing",   400, 10000) )
            AS T(EmpID, EmpName, DeptName, DeptID, Salary);
    
    A instrução a seguir demonstra como criar o conjunto de linhas extraindo-o de um arquivo de dados.
    
        @employees = 
        EXTRACT 
            EmpID    int, 
            EmpName  string, 
            DeptName string, 
            DeptID   int, 
            Salary   int
        FROM "/Samples/Employees.tsv"
        USING Extractors.Tsv();

Ao testar os exemplos no tutorial, você deve incluir as definições de conjunto de linhas. O U-SQL requer que você defina apenas os conjuntos de linhas que são usados. Alguns exemplos só precisam de um conjunto de linhas.

Você também deve adicionar a seguinte instrução para enviar o conjunto de linhas resultante para um arquivo de dados:

    OUTPUT @result TO "/wfresult.csv" 
        USING Outputters.Csv();
 
 A maioria dos exemplos usa a variável chamada **@result** para os resultados.

## Comparar funções de janela para agrupamento

Janelas e Agrupamento estão relacionados conceitualmente, mas são diferentes. É útil compreender essa relação.

### Agregação de uso e agrupamento

A consulta abaixo usa uma agregação para calcular o salário total de todos os funcionários:

    @result = 
        SELECT 
            SUM(Salary) AS TotalSalary
        FROM @employees;
    
>[AZURE.NOTE] Para obter instruções para teste e verificação de saída, confira [Introdução ao uso de U-SQL para trabalhos de análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md).

O resultado é uma única linha com uma única coluna. US$ 165.000 é a soma do valor de salário da tabela inteira.

|TotalSalary
|-----------
|165000

>[AZURE.NOTE] Se você estiver começando com as funções de janela, vale a pena lembrar os números nas saídas.

A instrução abaixo usa a cláusula GROUP BY para calcular o salário total de cada departamento:

    @result=
        SELECT DeptName, SUM(Salary) AS SalaryByDept
        FROM @employees
        GROUP BY DeptName;

Os resultados são:

|DeptName|SalaryByDept
|--------|------------
|Engenharia|60000
|HR|30000
|Executivo|50000
|Marketing|25000

A soma da coluna SalaryByDept é US$ 165.000, que corresponde à quantidade no último script.
 
Em ambos os casos há menos linhas de saída do que linhas de entrada:
 
- Sem GROUP BY, a agregação recolhe todas as linhas em uma única linha. 
- Com GROUP BY, há N linhas de saída, em que N é o número de valores distintos que aparecem nos dados; neste caso, você receberá 4 linhas na saída.

###  Usar uma função de janela

A cláusula OVER no exemplo a seguir está vazia. Isso define a "janela" para incluir todas as linhas. A função SUM neste exemplo é aplicada à cláusula OVER que a segue.

Você pode ler essa consulta como: "a soma de Salário em uma janela com todas as linhas".

    @result=
        SELECT
            EmpName,
            SUM(Salary) OVER( ) AS SalaryAllDepts
        FROM @employees;

Ao contrário de GROUP BY, há tantas linhas de saída quanto de entrada:

|EmpName|TotalAllDepts
|-------|--------------------
|Matheus|165000
|Clara|165000
|Pedro|165000
|Brenda|165000
|Donato|165000
|Marina|165000
|Julio|165000
|Yasmin|165000
|Davi|165000


O valor 165000 (o total de todos os salários) é colocado em cada linha de saída. Esse total provém da "janela" de todas as linhas, para que ela inclua todos os salários.

O exemplo a seguir demonstra como refinar "janela" a fim de listar todos os funcionários, o departamento e o salário total do departamento. PARTITION BY é adicionado à cláusula OVER.

    @result=
    SELECT
        EmpName, DeptName,
        SUM(Salary) OVER( PARTITION BY DeptName ) AS SalaryByDept
    FROM @employees;

Os resultados são:

|EmpName|DeptName|SalaryByDep
|-------|--------|-------------------
|Matheus|Engenharia|60000
|Clara|Engenharia|60000
|Pedro|Engenharia|60000
|Julio|Executivo|50000
|Brenda|HR|30000
|Donato|HR|30000
|Marina|HR|30000
|Yasmin|Marketing|25000
|Davi|Marketing|25000

Novamente, há tantas linhas de entrada quanto de saída. No entanto, cada linha tem um salário total para o departamento correspondente.




## Funções de agregação de relatórios

As funções de janela também dão suporte às seguintes agregações:

- COUNT
- SUM
- MÍN.
- MÁX.
- AVG
- STDEV
- VAR

A sintaxe:

    <AggregateFunction>( [DISTINCT] <expression>) [<OVER_clause>]

Observação:

- Por padrão, as funções de agregação, exceto COUNT, ignoram valores nulos.
- Quando as funções de agregação são especificadas com a cláusula OVER, a cláusula ORDER BY não é permitida na cláusula OVER.

### Usar SUM

O exemplo a seguir adiciona um salário total por departamento a cada linha de entrada:
 
    @result=
        SELECT 
            *,
            SUM(Salary) OVER( PARTITION BY DeptName ) AS TotalByDept
        FROM @employees;

Esta é a saída:

|EmpID|EmpName|DeptName|DeptID|Salary|TotalByDept
|-----|-------|--------|------|------|-----------
|1|Matheus|Engenharia|100|10000|60000
|2|Clara|Engenharia|100|20000|60000
|3|Pedro|Engenharia|100|30000|60000
|7|Julio|Executivo|300|50000|50000
|4|Brenda|HR|200|10000|30000
|5|Donato|HR|200|10000|30000
|6|Marina|HR|200|10000|30000
|8|Yasmin|Marketing|400|15000|25000
|9|Davi|Marketing|400|10000|25000

### Usar COUNT

O exemplo a seguir adiciona um campo adicional para cada linha para mostrar o número total de funcionários em cada departamento.

    @result =
        SELECT *, 
            COUNT(*) OVER(PARTITION BY DeptName) AS CountByDept 
        FROM @employees;

O resultado :

|EmpID|EmpName|DeptName|DeptID|Salary|CountByDept
|-----|-------|--------|------|------|-----------
|1|Matheus|Engenharia|100|10000|3
|2|Clara|Engenharia|100|20000|3
|3|Pedro|Engenharia|100|30000|3
|7|Julio|Executivo|300|50000|1
|4|Brenda|HR|200|10000|3
|5|Donato|HR|200|10000|3
|6|Marina|HR|200|10000|3
|8|Yasmin|Marketing|400|15000|2
|9|Davi|Marketing|400|10000|2


### Usar MIN e MAX

O exemplo a seguir adiciona um campo adicional para cada linha, para mostrar o menor salário de cada departamento:

    @result =
        SELECT 
            *,
            MIN(Salary) OVER( PARTITION BY DeptName ) AS MinSalary
        FROM @employees;

Os resultados são:

|EmpID|EmpName|DeptName|DeptID|Salary|MinSalary
|-----|-------|--------|------|-------------|----------------
|1|Matheus|Engenharia|100|10000|10000
|2|Clara|Engenharia|100|20000|10000
|3|Pedro|Engenharia|100|30000|10000
|7|Julio|Executivo|300|50000|50000
|4|Brenda|HR|200|10000|10000
|5|Donato|HR|200|10000|10000
|6|Marina|HR|200|10000|10000
|8|Yasmin|Marketing|400|15000|10000
|9|Davi|Marketing|400|10000|10000

Substitua MIN por MAX e experimente.


## Funções de classificação

As funções de classificação retornam um valor de classificação (longo) para cada linha em cada partição conforme definido pelas cláusulas PARTITION BY e OVER. A ordem de classificação é controlada pela cláusula ORDER BY na cláusula OVER.

As funções a seguir são funções de classificação com suporte:

- RANK
- DENSE\_RANK 
- NTILE
- ROW\_NUMBER

**Sintaxe:**

	[ RANK() | DENSE_RANK() | ROW_NUMBER() | NTILE(<numgroups>) ]
	    OVER (
	        [PARTITION BY <identifier, > …[n]]
	        [ORDER BY <identifier, > …[n] [ASC|DESC]] 
	) AS <alias>

- A cláusula ORDER BY é opcional para funções de classificação. Se ORDER BY for especificada, ela determinará a ordem de classificação. Se ORDER BY não for especificado, o U-SQL atribuirá valores baseados na ordem em que lê o registro. Isso resultará em valor não determinístico de ROW NUMBER, RANK ou DENSE RANK quando a cláusula ORDER BY não for especificada.
- NTILE requer uma expressão que é avaliada como um número inteiro positivo. Esse número Especifica o número de grupos em que cada partição deve ser dividida. O identificador é usado apenas com a função de classificação NTILE. 

Para obter mais detalhes sobre a cláusula OVER, consulte [Referência U-SQL]().

ROW\_NUMBER, RANK e DENSE\_RANK atribuem números a linhas em uma janela. Em vez de abordá-los separadamente, é mais intuitivo ver como eles respondem à mesma entrada.

    @result =
    SELECT 
        *,
        ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber,
        RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank, 
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank 
    FROM @querylog;
        
Observe que as cláusulas OVER são idênticas. O resultado :

|Consultar|Latência: int|Vertical|RowNumber|Rank|DenseRank
|-----|-----------|--------|--------------|---------|--------------
|Banana|300|Imagem|1|1|1
|Cereja|300|Imagem|2|1|1
|Durio|500|Imagem|3|3|2
|Maçã|100|Web|1|1|1
|Figo|200|Web|2|2|2
|Papaia|200|Web|3|2|2
|Figo|300|Web|4|4|3
|Cereja|400|Web|5|5|4
|Durio|500|Web|6|6|5

### ROW\_NUMBER

Dentro de cada janela (Vertical, seja Imagem ou Web), o número de linhas aumenta em incrementos de 1 ordenados por latência.

![Função de janela U-SQL ROW\_NUMBER](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-row-number-result.png)

### RANK

Diferentemente de ROW\_NUMBER(), RANK() leva em conta o valor da latência que é especificado na cláusula ORDER BY da janela.

RANK começa com (1,1,3), porque os dois primeiros valores de latência são iguais. Em seguida, o próximo valor é 3 porque o valor de latência passou a 500. O ponto principal é que, embora valores duplicados recebam a mesma classificação, o número RANK vai "ignorar" até o próximo valor ROW\_NUMBER. Você pode ver esse padrão se repetir com a sequência (2,2,4) na vertical Web.

![Função de janela U-SQL RANK](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-rank-result.png)

### DENSE\_RANK
	
DENSE\_RANK é exatamente igual a RANK, exceto que ele não "ignora" até o próximo ROW\_NUMBER; ele vai para o próximo número na sequência. Observe as sequências (1, 1,2) e (2,2,3) no exemplo.

![Função de janela U-SQL DENSE\_RANK](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-dense-rank-result.png)

### Comentários

- Se ORDER BY não for especificado, a função de classificação será aplicada ao conjunto de linhas sem qualquer ordenação. Isso resulta em um comportamento não determinístico de como a função de classificação é aplicada
- Não há nenhuma garantia de que as linhas retornadas por uma consulta usando ROW\_NUMBER serão ordenadas exatamente da mesma forma em cada execução, a menos que as condições a seguir sejam verdadeiras.

	- Os valores da coluna particionada são exclusivos.
	- Os valores das colunas ORDER BY são exclusivos.
	- As combinações de valores da coluna de partição e das colunas ORDER BY são exclusivas.

### NTILE

NTILE distribui as linhas em uma partição ordenada em um número específico de grupos. Os grupos são numerados começando com um.


O exemplo a seguir divide o conjunto de linhas em cada partição (vertical) em quatro grupos na ordem da latência da consulta e retorna o número de grupo para cada linha.

A vertical da imagem tem três linhas; portanto, ela tem três grupos.

A vertical Web tem seis linhas; as duas linhas extras são distribuídas para os dois primeiros grupos. É por isso que há duas linhas no grupo 1 e no grupo 2, mas somente uma linha nos grupos 3 e 4.

    @result =
        SELECT 
            *,
            NTILE(4) OVER(PARTITION BY Vertical ORDER BY Latency) AS Quartile   
        FROM @querylog;
		
Os resultados são:

|Consultar|Latência|Vertical|Quartile
|-----|-----------|--------|-------------
|Banana|300|Imagem|1
|Cereja|300|Imagem|2
|Durio|500|Imagem|3
|Maçã|100|Web|1
|Figo|200|Web|1
|Papaia|200|Web|2
|Figo|300|Web|2
|Cereja|400|Web|3
|Durio|500|Web|4

NTILE usa um parâmetro ("numgroups"). Numgroups é um número inteiro positivo ou uma expressão constante longa que especifica o número de grupos em que cada partição deve ser dividida.

- Se o número de linhas na partição for divisível por numgroups, os grupos terão tamanhos iguais. 
- Se o número de linhas em uma partição não for divisível por numgroups, isso fará com que haja grupos de dois tamanhos com um membro de diferença. Grupos maiores aparecem antes de grupos menores na ordem especificada pela cláusula OVER. 

Por exemplo:

- 100 linhas divididas em 4 grupos: [25, 25, 25, 25]
- 102 linhas divididas em 4 grupos: [26, 26, 25, 25]


### Primeiros N registros por partição com RANK, DENSE\_RANK ou ROW\_NUMBER

Muitos usuários desejam selecionar apenas as primeiras N linhas por grupo. Isso não é possível com o tradicional GROUP BY.

Você já viu o exemplo a seguir no início da seção Funções de classificação. Ela não mostra os primeiros N registros para cada partição:

    @result =
    SELECT 
        *,
        ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber,
        RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank,
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank
    FROM @querylog;

Os resultados são:

|Consultar|Latência|Vertical|Rank|DenseRank|RowNumber
|-----|-----------|--------|---------|--------------|--------------
|Banana|300|Imagem|1|1|1
|Cereja|300|Imagem|1|1|2
|Durio|500|Imagem|3|2|3
|Maçã|100|Web|1|1|1
|Figo|200|Web|2|2|2
|Papaia|200|Web|2|2|3
|Figo|300|Web|4|3|4
|Cereja|400|Web|5|4|5
|Durio|500|Web|6|5|6

### TOP N com DENSE RANK

O exemplo a seguir retorna os três primeiros registros de cada grupo sem intervalos na numeração sequencial classificação das linhas em cada partição de janelas.

    @result =
    SELECT 
        *,
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank
    FROM @querylog;
    
    @result = 
        SELECT *
        FROM @result
        WHERE DenseRank <= 3;

Os resultados são:

|Consultar|Latência|Vertical|DenseRank
|-----|-----------|--------|--------------
|Banana|300|Imagem|1
|Cereja|300|Imagem|1
|Durio|500|Imagem|2
|Maçã|100|Web|1
|Figo|200|Web|2
|Papaia|200|Web|2
|Figo|300|Web|3

### TOP N com RANK

    @result =
        SELECT 
            *,
            RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank
        FROM @querylog;
    
    @result = 
        SELECT *
        FROM @result
        WHERE Rank <= 3;

Os resultados são:

|Consultar|Latência|Vertical|Rank
|-----|-----------|--------|---------
|Banana|300|Imagem|1
|Cereja|300|Imagem|1
|Durio|500|Imagem|3
|Maçã|100|Web|1
|Figo|200|Web|2
|Papaia|200|Web|2


### TOP N com ROW\_NUMBER

    @result =
        SELECT 
            *,
            ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber
        FROM @querylog;
    
    @result = 
        SELECT *
        FROM @result
        WHERE RowNumber <= 3;

Os resultados são:
    
|Consultar|Latência|Vertical|RowNumber
|-----|-----------|--------|--------------
|Banana|300|Imagem|1
|Cereja|300|Imagem|2
|Durio|500|Imagem|3
|Maçã|100|Web|1
|Figo|200|Web|2
|Papaia|200|Web|3

### Atribuir um número de linha globalmente exclusivo

Geralmente, vale a pena atribuir um número exclusivo para cada linha. Isso é fácil (e mais eficiente do que usar um redutor) com as funções de classificação.

    @result =
        SELECT 
            *,
            ROW_NUMBER() OVER () AS RowNumber
        FROM @querylog;

<!-- ################################################### -->
## Funções analíticas

As funções analíticas são usadas para entender as distribuições dos valores nas janelas. O cenário mais comum para usar funções analíticas é o cálculo de percentuais.

**Funções de janela analíticas com suporte**

- CUME\_DIST 
- PERCENT\_RANK
- PERCENTILE\_CONT
- PERCENTILE\_DISC

### CUME\_DIST  

CUME\_DIST computa a posição relativa de um valor especificado em um grupo de valores. Ele calcula a porcentagem de consultas com latência menor ou igual à latência da consulta atual na mesma vertical. Para uma linha R, assumindo uma ordem crescente, a cume\_dist de R é o número de linhas com valores menores ou iguais ao valor de R, dividido pelo número de linhas avaliadas no conjunto de resultados da consulta ou partição. CUME\_DIST retorna números no intervalo de 0 < x < = 1.

** Sintaxe**

    CUME_DIST() 
        OVER (
            [PARTITION BY <identifier, > …[n]]
            ORDER BY <identifier, > …[n] [ASC|DESC] 
    ) AS <alias>

O exemplo a seguir usa a função CUME\_DIST para computar o percentual de latência de cada consulta na vertical.

    @result=
        SELECT 
            *,
            CUME_DIST() OVER(PARTITION BY Vertical ORDER BY Latency) AS CumeDist
        FROM @querylog;

Os resultados são:
    
|Consultar|Latência|Vertical|CumeDist
|-----|-----------|--------|---------------
|Durio|500|Imagem|1
|Banana|300|Imagem|0\.666666666666667
|Cereja|300|Imagem|0\.666666666666667
|Durio|500|Web|1
|Cereja|400|Web|0\.833333333333333
|Figo|300|Web|0\.666666666666667
|Figo|200|Web|0,5
|Papaia|200|Web|0,5
|Maçã|100|Web|0\.166666666666667

Há seis linhas na partição em que a chave de partição é "Web" (quarta linha para baixo):

- Há seis linhas com o valor igual ou inferior a 500; portanto, CUME\_DIST é igual a 6/6 = 1
- Há 5 linhas com valor igual ou inferior a 400; portanto, CUME\_DIST é igual a 5/6=0.83
- Há quatro linhas com o valor igual ou inferior a 300; portanto, CUME\_DIST é igual a 4/6 = 0,66
- Há 3 linhas com valor igual ou inferior a 200; portanto, CUME\_DIST é igual a 3/6=0.5. Há duas linhas com o mesmo valor de latência.
- Há uma linha com valor igual ou inferior a 100; portanto, CUME\_DIST é igual a 1/6=0.16. 


**Observações de uso:**

- Os valores vinculados sempre são avaliados como o mesmo valor de distribuição cumulativa.
- Os valores NULL são tratados como os menores valores possíveis.
- Você deve especificar a cláusula ORDER BY para calcular CUME\_DIST.
- CUME\_DIST é semelhante à função PERCENT\_RANK

Observação: a cláusula ORDER BY não é permitida se a instrução SELECT não é seguida pela saída. Portanto, a cláusula ORDER BY na instrução OUTPUT determina a ordem de exibição do conjunto de linhas resultante.


### PERCENT\_RANK

PERCENT\_RANK calcula a classificação relativa de uma linha dentro de um grupo de linhas. PERCENT\_RANK é usada para avaliar a posição relativa de um valor em um conjunto de linhas ou uma partição. O intervalo de valores retornados por PERCENT\_RANK é maior que 0 e menor ou igual a 1. Ao contrário de CUME\_DIST, PERCENT\_RANK é sempre 0 para a primeira linha.
	
** Sintaxe**

    PERCENT_RANK() 
        OVER (
            [PARTITION BY <identifier, > …[n]]
            ORDER BY <identifier, > …[n] [ASC|DESC] 
        ) AS <alias>

**Observações**

- A primeira linha em qualquer conjunto tem um PERCENT\_RANK de 0.
- Os valores NULL são tratados como os menores valores possíveis.
- Você deve especificar a cláusula ORDER BY para calcular PERCENT\_RANK.
- CUME\_DIST é semelhante à função PERCENT\_RANK 


O exemplo a seguir usa a função PERCENT\_RANK para computar o percentual de latência de cada consulta em uma vertical.

A cláusula PARTITION BY é especificada para particionar as linhas no conjunto de resultados definido pela vertical. A cláusula ORDER BY na cláusula OVER ordena as linhas em cada partição.

O valor retornado pela função PERCENT\_RANK representa a classificação de latência das consultas na vertical como uma porcentagem.


    @result=
        SELECT 
            *,
            PERCENT_RANK() OVER(PARTITION BY Vertical ORDER BY Latency) AS PercentRank
        FROM @querylog;

Os resultados são:

|Consultar|Latência: int|Vertical|PercentRank
|-----|-----------|--------|------------------
|Banana|300|Imagem|0
|Cereja|300|Imagem|0
|Durio|500|Imagem|1
|Maçã|100|Web|0
|Figo|200|Web|0,2
|Papaia|200|Web|0,2
|Figo|300|Web|0\.6
|Cereja|400|Web|0\.8
|Durio|500|Web|1

### PERCENTILE\_CONT E PERCENTILE\_DISC

Essas duas funções calculam um percentual baseado em uma distribuição contínua ou distinta dos valores da coluna.

**Sintaxe**

    [PERCENTILE_CONT | PERCENTILE_DISC] ( numeric_literal ) 
        WITHIN GROUP ( ORDER BY <identifier> [ ASC | DESC ] )
        OVER ( [ PARTITION BY <identifier,>…[n] ] ) AS <alias>

**numeric\_literal** -o percentual de computação. O valor deve variar entre 0,0 e 1,0.

WITHIN GROUP (ORDER BY <identifier> [ASC | DESC]) - especifica uma lista de valores numéricos para classificar e computar o percentual. Somente um identificador de coluna é permitido. A expressão deve ser avaliada como um tipo numérico. Outros tipos de dados não são permitidos. A ordem de classificação padrão é crescente.

OVER ([ PARTITION BY <identifier,>... [n]]) - divide o conjunto de linhas de entrada em partições de acordo com a chave de partição a qual a função de percentil é aplicada. Para saber mais, consulte a seção CLASSIFICAÇÃO deste documento. Observação: os nulls no conjunto de dados são ignorados.

**PERCENTILE\_CONT** calcula um percentual baseado em uma distribuição contínua do valor da coluna. O resultado é interpolado e talvez não seja igual a algum valor específico na coluna.

**PERCENTILE\_DISC** calcula o percentual baseado em uma distribuição distinta dos valores da coluna. O resultado é igual a um valor específico na coluna. Em outras palavras, PERCENTILE\_DISC, em contraste ao PERCENTILE\_CONT, sempre retorna um valor real (entrada original).

Você pode ver como ambas funcionam no exemplo abaixo que tenta localizar o valor mediano (percentual = 0,50) de latência em cada vertical

    @result = 
        SELECT 
            Vertical, 
            Query,
            PERCENTILE_CONT(0.5) 
                WITHIN GROUP (ORDER BY Latency)
                OVER ( PARTITION BY Vertical ) AS PercentileCont50,
            PERCENTILE_DISC(0.5) 
                WITHIN GROUP (ORDER BY Latency) 
                OVER ( PARTITION BY Vertical ) AS PercentileDisc50 
        
        FROM @querylog;

Os resultados são:

|Consultar|Latência: int|Vertical|PercentileCont50|PercentilDisc50
|-----|-----------|--------|-------------------|----------------
|Banana|300|Imagem|300|300
|Cereja|300|Imagem|300|300
|Durio|500|Imagem|300|300
|Maçã|100|Web|250|200
|Figo|200|Web|250|200
|Papaia|200|Web|250|200
|Figo|300|Web|250|200
|Cereja|400|Web|250|200
|Durio|500|Web|250|200


Para PERCENTILE\_CONT, como os valores podem ser interpolados, o mediano para Web é 250, embora nenhuma consulta na vertical Web tenha uma latência de 250.

PERCENTILE\_DISC não interpola valores; portanto, o mediano para Web é 200, o que é um valor real encontrado nas linhas de entrada.











## Consulte também

- [Visão geral da Análise do Microsoft Azure Data Lake](data-lake-analytics-overview.md)
- [Introdução à Análise do Data Lake usando o Portal do Azure](data-lake-analytics-get-started-portal.md)
- [Introdução à Análise Data Lake usando o Azure PowerShell](data-lake-analytics-get-started-powershell.md)
- [Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Usar tutoriais interativos da Análise do Azure Data Lake](data-lake-analytics-use-interactive-tutorials.md)
- [Analisar logs de site usando a Análise do Azure Data Lake](data-lake-analytics-analyze-weblogs.md)
- [Introdução à linguagem U-SQL da Análise Azure Data Lake](data-lake-analytics-u-sql-get-started.md)
- [Gerenciar a Análise do Azure Data Lake usando o Portal do Azure](data-lake-analytics-manage-use-portal.md)
- [Gerenciar a Análise Azure Data Lake usando o Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
- [Monitorar e solucionar problemas em trabalhos da Análise do Azure Data Lake usando o Portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

<!---HONumber=AcomDC_0218_2016-->