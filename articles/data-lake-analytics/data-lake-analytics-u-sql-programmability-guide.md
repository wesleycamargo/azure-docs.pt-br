---
title: "Guia de programação do U-SQL para análise do Azure Data Lake | Microsoft Docs"
description: "Guia de programação do U-SQL"
services: data-lake-analytics
documentationcenter: 
author: MikeRys
manager: arindamc
ms.assetid: 63be271e-7c44-4d19-9897-c2913ee9599d
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: mrys
translationtype: Human Translation
ms.sourcegitcommit: cd2aafd80db337cadaa2217a6638d93186975b68
ms.openlocfilehash: 563a6821b4a3736ef1233aa67d86b9ba06565788


---
# <a name="u-sql-programmability-guide"></a>Guia de programação do U-SQL
## <a name="azure-data-lake"></a>Azure Data Lake
O Azure Data Lake inclui todos os recursos necessários para que seja mais fácil para desenvolvedores, cientistas de dados e analistas armazenar dados de qualquer tamanho, forma ou velocidade, bem como realizar todo tipo de processamento e análise em diferentes plataformas e linguagens. Ele remove as complexidades relacionadas a ingerir e armazenar todos os seus dados, ao mesmo tempo que acelera a execução de análises interativas, em lote e de streaming.

O Azure Data Lake é um conjunto de serviços que trabalham juntos para fornecer uma plataforma de Big Data baseada na nuvem:

- HDInsight
- Repositório Azure Data Lake
- Análise Azure Data Lake

O U-SQL é uma linguagem de consulta projetada especificamente para o tipo Big Data de cargas de trabalho. Um dos recursos exclusivos do U-SQL é a combinação da linguagem declarativa semelhante ao SQL com a extensibilidade e a programação fornecidas por C#. Ele também permite acessar e manipular metadados do esquema, bem como criar componentes personalizados como processadores e redutores de dados.

Neste guia, nosso foco é a extensibilidade e a programação da linguagem U-SQL habilitadas por C#.

## <a name="requirements"></a>Requisitos
Para começar com o desenvolvimento do ADL, você precisa baixar e instalar as [Ferramentas do Azure Data Lake para Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="starting-with-u-sql"></a>Começando com o U-SQL  
A descrição da linguagem U-SQL está fora do escopo deste documento. No entanto, descrevemos construções básicas de U-SQL para introduzir gradualmente os recursos de programação do U-SQL. Para saber mais, veja o guia de [Referência da linguagem U-SQL](http://aka.ms/usql_reference).

Vamos começar com o seguinte exemplo:

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        MAX(guid) AS start_id,
        MIN(dt) AS start_time,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

No exemplo acima, temos um **Arquivo de Entrada** – arquivo input_file.tsv, definido pela **Variável Local** @input_file.

As seguintes ações são executadas como resultado da execução do script U-SQL acima:

* A instrução **EXTRACT** carrega dados na memória convertendo o Arquivo de Entrada em **Conjunto de Linhas de Memória**.
* **SELECT** opera no Conjunto de Linhas de dados para agregar dados e prepará-los para exportação.
* Comando **OUTPUT** — exporta o Conjunto de Linhas de dados para o **Arquivo de Saída** — arquivo externo.

Primeiramente, vamos observar algumas opções para usar uma expressão C# diretamente em um script U-SQL.

## <a name="c-types-and-expressions-in-u-sql-script"></a>Tipos e expressões de C# no script U-SQL
Uma expressão C# do U-SQL, semelhante às expressões C# gerais, é uma sequência de um ou mais operadores que podem ser avaliados para um único valor, objeto, método ou namespace. As expressões podem ser compostas por um valor literal, uma invocação de método, um operador ou um nome simples. Os nomes simples podem ser o nome de uma variável, o membro de tipo, o parâmetro do método, o namespace ou o tipo.

Quando falamos sobre expressões C# do U-SQL, estamos nos referindo especificamente às expressões C# do script base U-SQL. A seção Code-behind do C# subjacente, abordada mais adiante neste documento, também pode conter expressões C# como o elemento baseado em código C# regular.

As expressões podem usar operadores que usam outras expressões como parâmetros ou chamadas de método cujos parâmetros são, por sua vez, outras chamadas de método. Exemplo de uma expressão:  

```c#
    Convert.ToDateTime(Convert.ToDateTime(dt).ToString("yyyy-MM-dd"))
```

A linguagem U-SQL permite o uso de expressões C# padrão de namespaces integrados.  
 
```c#
    Microsoft.Analytics.Interfaces;  
    Microsoft.Analytics.Types.Sql;  
    System;  
    System.Collections.Generic;  
    System.Linq;  
    System.Text;  
```

As expressões C# gerais podem ser usadas em SELECT, EXTRACT do U-SQL.

As expressões C# também podem ser usadas nas instruções DECLARE ou IF. O exemplo de tal expressão é:   

```c#
    DateTime.Today.Day   
    Convert.ToDateTime
```

Exemplo de script base U-SQL:  

```sql
    DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");
```

As expressões C# podem fornecer funcionalidade estendida na manipulação com colunas como parte do conjunto de linhas. Por exemplo, se quisermos converter uma coluna de datetime em data com zero hora, poderemos usar a seguinte parte SELECT do script base U-SQL:

```sql
@rs1 =
    SELECT
        MAX(guid) AS start_id,
     MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt).ToString("yyyy-MM-dd")))
AS start_zero_time,
        user,
        des
    FROM @rs0
    GROUP BY user, des;
```

Como podemos ver, usamos o método `System.Convert.ToDateTime` para executar a conversão.

O cenário de caso de uso um pouco mais complicado abaixo demonstra o uso de alguns operadores C# básicos. Veja a expressão abaixo.

```sql
@rs1 =
    SELECT
        MAX(guid) AS start_id,
        MIN(dt) AS start_time,
          MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        user,
        des
    FROM @rs0
    GROUP BY user, des;
```

Isso mostra um exemplo da expressão do operador condicional C#.

O exemplo acima demonstra o uso das expressões C# no script base U-SQL. No entanto, o U-SQL permite recursos de programação mais extensíveis que são abordados mais adiante neste documento.  

Script completo:

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");

@rs1 =
    SELECT
        MAX(guid) AS start_id,
    MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

### <a name="using-c-expressions-for-data-type-conversions"></a>Usando expressões C# para conversões de tipo de dados
Usando a amostra acima, o exemplo que se segue demonstra a técnica de conversão de dados de datetime usando expressões C#. Nesse cenário específico, os dados de datetime da cadeia de caracteres são convertidos em datetime padrão com a notação de hora 00:00:00 meia-noite.

```sql
DECLARE @dt String = "2016-07-06 10:23:15";
@rs1 =
    SELECT Convert.ToDateTime(Convert.ToDateTime(@dt).ToString("yyyy-MM-dd")) AS dt,
           dt AS olddt
    FROM @rs0;
OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

### <a name="using-c-expressions-for-todays-date"></a>Usando expressões C# para a data de hoje
Para efetuar pull da data de hoje, podemos usar a seguinte expressão C#

```c#
DateTime.Now.ToString("M/d/yyyy")
```

Em um script

```sql
@rs1 =
    SELECT
        MAX(guid) AS start_id,
        MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        user,
        des
    FROM @rs0
    GROUP BY user, des;
```

## <a name="in-line-c-function-expressions"></a>Expressões da função C# embutida
O U-SQL permite o uso da definição de expressões de função embutida como parte das expressões C#. Isso permite outras possibilidades de usar funções C# com parâmetros de referência de saída.

Definição geral da expressão de função embutida

```c#
    (Func<type of param1, type of param2>)
    (param1 =>
         { … return param2}
    ) (Rowset Column)
```

Exemplo:

```c#
    (Func<string, DateTime?>)
    (input_p =>
         {
            DateTime dt_result;
            return DateTime.TryParse(input_p, out dt_result)
                   ? (DateTime?) dt_result : (DateTime?) null;
         }
    )
    ) (dt)
```

Nesse exemplo, definimos a função embutida com o parâmetro de entrada de cadeia de caracteres input_p. Dentro dessa função, verificamos se a cadeia de caracteres de entrada é um valor válido de datetime. Em caso positivo, ele é retornado; caso contrário, retornará null.

A função embutida é necessária nesse cenário, pois a função DateTime.TryParse contém o parâmetro de saída — `out dt_result`. Nós o definimos como `DateTime dt_result`;


## <a name="verifying-data-type-values"></a>Verificando valores de tipo de dados
Algumas funções C# não podem ser usadas diretamente no script base U-SQL como uma expressão C#. Mais especificamente, as funções que exigem um parâmetro de referência de saída. No entanto, essas funções podem ser definidas e usadas como parte de uma expressão de função embutida, como observamos acima.

### <a name="using-inline-function-expressions"></a>Usando expressões de função embutida
Para verificar se o valor de DateTime é válido, podemos usar `DateTime.TryParse`.

Veja abaixo o exemplo completo de como usar a expressão de função embutida para verificar o valor do tipo de dados com `DateTime.TryParse`.

Nesse cenário, verificamos o valor do tipo de dados DateTime

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt string,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        MAX(guid) AS start_id,
        MIN((
             (Func<string, DateTime?>)
             (input_parameter =>
                { DateTime dt_result;
                  return
                     DateTime.TryParse(input_parameter, out dt_result)
                       ?(DateTime?) dt_result : (DateTime?) null;
                }
             )
             ) (dt)) AS start_time,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

### <a name="using-code-behind"></a>Usando Code-behind
Para usar a mesma funcionalidade na seção Code-behind do programa U-SQL, definimos a função C# ToDateTime.

Aqui está a seção de script U-SQL base, em que fizemos alterações necessárias:

```sql
     @rs1 =
        SELECT
          MAX(guid) AS start_id,
          MIN(USQL_Programmability.CustomFunctions.ToDateTime(dt)) AS start_time,
          DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
          user,
          des
          FROM @rs0
        GROUP BY user, des;
```

e a função Code-behind

```c#
        static public DateTime? ToDateTime(string dt)
        {
            DateTime dtValue;

            if (!DateTime.TryParse(dt, out dtValue))
                return Convert.ToDateTime(dt);
            else
                return null;
        }
```

## <a name="using-code-behind"></a>Usando Code-behind
Code-behind é uma seção da programação C# do projeto U-SQL. Conceitualmente, Code-behind é um assembly compilado (DLL) referenciado no script U-SQL. As Ferramentas do Visual Studio permitem gerenciar e depurar uma seção da programação C# como parte do projeto U-SQL.

Quando um projeto U-SQL comum é criado no Visual Studio, ele tem duas partes. Script base — arquivo com a extensão **.usql**.

![a](./media/data-lake-analytics-u-sql-programmability-guide/base-script-file.png)


Projeto comum de solução   
![typical-solution-project](./media/data-lake-analytics-u-sql-programmability-guide/typical-solution-project.png)


A segunda parte do projeto chamamos de Code-behind — um arquivo Script.usql.cs  
![code-behind](./media/data-lake-analytics-u-sql-programmability-guide/code-behind.png)

Esse arquivo contém uma definição de namespace padrão para objetos de programação.

```c#
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQL_Programmability
{

}
```

O modelo de Code-behind acima é gerado automaticamente. Esse arquivo contém uma definição de namespace padrão para objetos de programação. Durante a execução do projeto, ele vai ser compilado e referenciado no script base U-SQL.

Para começar a desenvolver objetos de programação, precisamos criar uma classe **public**.

```c#
namespace USQL_Programmability
{
    public class MyClass
    {
        public static string MyFunction(string param1)
        {
            return "my result";
        }

    }

}
```

Os objetos de programação podem ser Funções Definidas pelo Usuário ou **UDF**, Tipos Definidos pelo Usuário ou **UDT, PROCESSO, REDUTOR**, etc.

## <a name="registering-u-sql-assemblies"></a>Registrando assemblies U-SQL
O modelo de extensibilidade do U-SQL depende em grande parte da capacidade de adicionar seu próprio código personalizado. Atualmente, o U-SQL fornece maneiras fáceis de adicionar o seu próprio código baseado em .NET, especificamente C#, mas você também pode adicionar código personalizado escrito em outras linguagens .Net, como VB.Net ou F#. Você ainda pode implantar seu próprio tempo de execução para outras linguagens, embora ainda seja preciso fornecer, por conta própria, a interoperabilidade por meio de uma camada .Net. Se você quiser dar suporte a uma linguagem específica, registre uma solicitação de recurso e/ou deixe um comentário aqui http://aka.ms/adlfeedback

### <a name="difference-between-code-behind-and-assembly-registration-through-adl-tools-in-visual-studio"></a>Diferença entre o Code-behind e o registro de assembly por meio das Ferramentas do ADL no Visual Studio
A maneira mais fácil de usar o código personalizado é por meio dos recursos code-behind das Ferramentas do ADL para Visual Studio.

Como mencionado anteriormente, preencha o código personalizado para o script (por exemplo, Script.usql) em seu arquivo code-behind (por exemplo, Script.usql.cs).

![Exemplo de code-behind](./media/data-lake-analytics-u-sql-programmability-guide/code-behind-example.jpg)
**Figura 1**: Exemplo de Code-behind nas Ferramentas do ADL no VS (clique na imagem para aumentá-la, o código de exemplo está disponível [aqui](https://github.com/Azure/usql/tree/master/Examples/TweetAnalysis))
<br />

A vantagem do code-behind é que a ferramenta passa a ser responsável pelas seguintes etapas quando você envia seu script:  

1. Cria o assembly para o arquivo code-behind  

1. Adiciona um prólogo ao script que usa a instrução [CREATE ASSEMBLY](https://msdn.microsoft.com/library/azure/mt763293.aspx) para registrar o arquivo do assembly e usa [REFERENCE ASSEMBLY] (https://msdn.microsoft.com/library/azure/mt763294.aspx) para carregar o assembly no contexto do script.

1. Adiciona um epílogo ao script que usa [DROP ASSEMBLY](https://msdn.microsoft.com/library/azure/mt763295.aspx) para remover outra vez o assembly temporariamente registrado.

Você pode ver o prólogo e o epílogo gerados quando abrir o script:

![prólogo gerado](./media/data-lake-analytics-u-sql-programmability-guide/generated-prologue.png)

**Figura 2**: prólogo e epílogo gerados automaticamente para code-behind
<br />

Algumas das desvantagens do code-behind são

* O código é carregado para cada envio de script
* A funcionalidade não pode ser compartilhada com outras pessoas.

Desse modo, você pode adicionar uma Biblioteca de Classes C# separada (para U-SQL) à sua solução (veja a Figura 3), desenvolver o código ou copiar o código code-behind existente (sem necessidade de alterações no código C#, veja a Figura 4) e usar a opção de menu Registrar Assembly no projeto para registrar o assembly (veja a Etapa 1 na Figura 5).

![creating-project](./media/data-lake-analytics-u-sql-programmability-guide/creating-project.png)
**Figura 3**: Criando um projeto de código C# U-SQL.  
<br />

![class-library](./media/data-lake-analytics-u-sql-programmability-guide/class-library.png)
**Figura 4**: A biblioteca de classes C# U-SQL ao lado do arquivo code-behind.  
<br />

![register-code](./media/data-lake-analytics-u-sql-programmability-guide/register-code.png)
**Figura 5**: Como registrar o projeto de código C# U-SQL
<br />

A caixa de diálogo de registro (veja a Etapa 2 na Figura 5) fornece a opção de onde registrar o assembly (qual conta do Data Lake Analytics, qual banco de dados) e como nomeá-lo (o caminho do assembly local é preenchido pela ferramenta). Ela também fornece uma opção para registrar novamente um assembly já registrado, além de duas opções para adicionar mais dependências:

* *Dependências Gerenciadas*: mostra os assemblies gerenciados também necessários. Cada assembly selecionado é registrado individualmente e pode ser referenciado nos scripts. Use isso para outros assemblies .Net

* *Arquivos Adicionais*: permite adicionar mais arquivos de recursos que o assembly precisa. Eles são registrados com o assembly e carregados automaticamente quando o assembly é referenciado. Use isso para arquivos de configuração, assemblies nativos, tempos de execução de outra linguagem e seus recursos, etc.

Usamos essas opções nos exemplos abaixo. A [postagem recente no blog sobre processamento de imagem](https://blogs.msdn.microsoft.com/azuredatalake/2016/08/18/introducing-image-processing-in-u-sql/) é outro exemplo que mostra o uso de um assembly predefinido que pode usar essas opções para registro.

Agora você pode fazer referência aos assemblies registrados de qualquer script U-SQL que tenha permissões para o banco de dados dos assemblies registrados (veja o código no script U-SQL na Figura 4). É preciso adicionar uma referência para cada assembly registrado separadamente. Os arquivos de recursos adicionais serão implantados automaticamente. Esse script não deve ter mais um arquivo code-behind para o código nos assemblies referenciados, mas o arquivo code-behind ainda pode fornecer outro código.

### <a name="registering-assemblies-via-adl-tools-in-visual-studio-and-in-u-sql-scripts"></a>Registro de assemblies usando Ferramentas do ADL no Visual Studio e nos scripts U-SQL
Embora as Ferramentas do ADL no Visual Studio facilitem o registro de um assembly, você também pode fazer isso com um script (da mesma forma que as ferramentas o fazem para você) se estiver, por exemplo, desenvolvendo em uma plataforma diferente, já tendo compilado os assemblies que quer carregar e registrar. Basicamente, basta seguir estas etapas:

1. Carregue a dll do assembly, bem como todos os dlls que não são do sistema e os arquivos de recursos necessários em um local de sua escolha na sua conta de armazenamento do Azure Data Lake ou, até mesmo, em uma conta de Armazenamento de Blobs do Microsoft Azure que esteja vinculada à sua conta do Azure Data Lake. Você pode usar qualquer uma das muitas ferramentas de carregamento disponíveis (por exemplo, comandos do Powershell, carregamento do Gerenciador do Data Lake para Ferramentas do ADL do Visual Studio, seu comando de carregamento favorito do SDK ou o portal do Azure).

1. Depois de carregar as dlls, use as instruções [CREATE ASSEMBLY](https://msdn.microsoft.com/library/azure/mt763293.aspx) para registrá-las.

Usamos essa abordagem no exemplo espacial abaixo.

### <a name="registering-assemblies-that-use-other-net-assemblies-based-on-the-json-and-xml-sample-library"></a>Registro de assemblies que usam outros assemblies .Net (com base na biblioteca de exemplos de JSON e XML)
Nosso [site do Github para U-SQL](https://github.com/Azure/usql/) oferece um conjunto de assemblies de exemplo compartilhados para você usar. Um dos assemblies, chamado [Microsoft.Analytics.Samples.Formats](https://github.com/Azure/usql/tree/master/Examples/DataFormats), fornece extratores, funções e outputters para tratamento de documentos JSON e XML. O assembly Microsoft.Analytics.Samples.Formats depende de dois assemblies existentes específicos de domínio para fazer o processamento de JSON e XML, respectivamente. Ele usa a biblioteca [Newtonsoft Json.Net](http://www.newtonsoft.com/) para processamento de documentos JSON e o assembly [System.Xml](https://msdn.microsoft.com/data/bb291078.aspx) para processamento de XML. Vamos usá-lo para mostrar como registrar e usar os assemblies em nossos scripts.

Primeiro, baixamos o [projeto do Visual Studio](https://github.com/Azure/usql/tree/master/Examples/DataFormats) para nosso ambiente de desenvolvimento local (por exemplo, fazendo uma cópia local com a ferramenta GitHub para Windows). Em seguida, abrimos a solução no Visual Studio e clicamos com o botão direito do mouse no projeto, conforme explicado acima, para registrar o assembly. Embora esse assembly tenha duas dependências, temos que incluir apenas a dependência Newtonsoft, uma vez que System.Xml já está disponível no Azure Data Lake (ainda que ela tenha que ser explicitamente referenciada). A Figura 6 mostra como nomeamos o assembly (observe que você também pode escolher um nome diferente sem pontos) e adicionamos a dll da Newtonsoft. Cada um dos assemblies agora será individualmente registrado no banco de dados especificado (por exemplo, JSONBlog).

![register-assembly](./media/data-lake-analytics-u-sql-programmability-guide/register-assembly.png)

**Figura 6**: como registrar o assembly Microsoft.Analytics.Samples.Formats no Visual Studio
<br />

Se você ou outras pessoas, com quem você compartilhou os assemblies registrados fornecendo a elas acesso de leitura ao banco de dados, agora quiserem usar o recurso JSON em seus próprios scripts, adicione as duas referências abaixo ao script:

```
REFERENCE ASSEMBLY JSONBlog.[NewtonSoft.Json];
REFERENCE ASSEMBLY JSONBlog.[Microsoft.Analytics.Samples.Formats];
```

E se você quiser usar a funcionalidade XML, adicione uma referência do assembly do sistema e uma referência ao assembly registrado:

```
REFERENCE SYSTEM ASSEMBLY [System.Xml];
REFERENCE ASSEMBLY JSONBlog.[Microsoft.Analytics.Samples.Formats];
```

Para saber mais sobre como usar a funcionalidade JSON, veja [esta postagem de blog](https://blogs.msdn.microsoft.com/mrys/?p=755).

### <a name="registering-assemblies-that-use-native-c-assemblies-using-the-sql-server-2016-spatial-type-assembly-from-the-feature-pack"></a>Registro de assemblies que usam assemblies C++ nativos (usando o assembly Tipo Espacial do SQL Server 2016 do Feature Pack)
Agora vamos examinar um cenário ligeiramente diferente. Vamos supor que o assembly que queremos usar tenha uma dependência no código que não se baseia no .Net, especificamente, o assembly tem uma dependência em um assembly C++ nativo. Um exemplo disso é o assembly de tipo do SQL Server [Microsoft.SqlServer.Types.dll](https://www.microsoft.com/download/details.aspx?id=52676), que fornece implementações baseadas em .Net dos tipos de geografia, geometria e hiearchyID do SQL Server a serem usadas por aplicativos do lado do cliente do SQL Server para tratamento dos tipos do SQL Server (também era o assembly que originalmente fornecia a implementação para os tipos espaciais do SQL Server antes do lançamento do SQL Server 2016).

Vejamos como registrar esse assembly no U-SQL!

Primeiro, baixamos e instalamos o assembly do [Feature Pack do SQL Server 2016](https://www.microsoft.com/download/details.aspx?id=52676). Selecione a versão de 64 bits do instalador (ENU\x64\SQLSysClrTypes.msi), uma vez que queremos ter certeza de que temos a versão de 64 bits das bibliotecas.

O instalador instala o assembly gerenciado Microsoft.SqlServer.Types.dll em C:\Arquivos de Programas (x86)\Microsoft SQL Server\130\SDK\Assemblies e o assembly nativo SqlServerSpatial130.dll em \Windows\System32\\.. Agora, carregamos os assemblies em nosso Azure Data Lake Store (por exemplo, em uma pasta chamada /upload/asm/spatial).

Como o instalador instalou a biblioteca nativa na pasta do sistema c:\Windows\System32, temos que garantir a cópia de SqlServerSpatial130.dll fora dessa pasta antes de carregá-la ou que a ferramenta que usamos não execute o [Redirecionamento de Sistema de Arquivos](https://msdn.microsoft.com/library/windows/desktop/aa384187(v=vs.85).aspx) das pastas do sistema. Por exemplo, se quiser carregá-la com o Explorador de Arquivos do ADL no Visual Studio, você terá que copiar o arquivo para outro diretório primeiro. Caso contrário (no momento da redação neste blog), a versão de 32 bits será carregada (pois o Visual Studio é um aplicativo de 32 bits que faz o Redirecionamento de Sistema de Arquivos em sua janela de seleção para carregamento de arquivos do ADL) e, quando executar um script U-SQL que chama o assembly nativo, você obterá o seguinte erro (interno) em tempo de execução:

*Exceção interna da expressão do usuário: Foi feita uma tentativa de carregar um programa com um formato incorreto. (Exceção de HRESULT: 0x8007000B)*

Depois de carregar os dois arquivos de assembly, nós os registramos em um banco de dados SQLSpatial com o seguinte script:

```sql
DECLARE @ASSEMBLY_PATH string = "/upload/asm/spatial/";
DECLARE @SPATIAL_ASM string = @ASSEMBLY_PATH+"Microsoft.SqlServer.Types.dll";
DECLARE @SPATIAL_NATIVEDLL string = @ASSEMBLY_PATH+"SqlServerSpatial130.dll";

CREATE DATABASE IF NOT EXISTS SQLSpatial;
USE DATABASE SQLSpatial;

DROP ASSEMBLY IF EXISTS SqlSpatial;
CREATE ASSEMBLY SqlSpatial
FROM @SPATIAL_ASM
WITH ADDITIONAL_FILES =
     (
         @SPATIAL_NATIVEDLL
     );
```

Nesse caso, registramos apenas um assembly do U-SQL e incluímos o assembly nativo como uma dependência de cadeira de caracteres para o assembly do U-SQL. Para usar os assemblies espaciais, precisamos fazer referência apenas ao assembly do U-SQL, e o arquivo adicional será disponibilizado automaticamente para o assembly. Veja um script de exemplo simples usando o assembly espacial:

```sql
REFERENCE SYSTEM ASSEMBLY [System.Xml];
REFERENCE ASSEMBLY SQLSpatial.SqlSpatial;

USING Geometry = Microsoft.SqlServer.Types.SqlGeometry;
USING Geography = Microsoft.SqlServer.Types.SqlGeography;
USING SqlChars = System.Data.SqlTypes.SqlChars;

@spatial =
    SELECT * FROM (VALUES
                   // The following expression is not using the native DDL
                   ( Geometry.Point(1.0,1.0,0).ToString()),    
                   // The following expression is using the native DDL
                   ( Geometry.STGeomFromText(new SqlChars("LINESTRING (100 100, 20 180, 180 180)"), 0).ToString())
                  ) AS T(geom);

OUTPUT @spatial
TO "/output/spatial.csv"
USING Outputters.Csv();
```

A biblioteca Tipos SQL tem uma dependência no assembly System.XML; portanto, precisamos fazer referência a ele. Além disso, alguns dos métodos estão usando os tipos System.Data.SqlTypes em vez dos tipos C# internos. Uma vez que System.Data já está incluído por padrão, posso fazer referência apenas ao tipo SQL necessário. O código acima está disponível em nosso [site do Github](https://github.com/Azure/usql/tree/master/Examples/SQLSpatialExample).


### <a name="assembly-versioning-and-other-points"></a>Controle de versão do assembly e outros pontos
Atualmente, o U-SQL usa o .Net Framework versão 4.5. Portanto, certifique-se de que seus próprios assemblies sejam compatíveis com essa versão do tempo de execução!

Conforme mencionado acima, o U-SQL executa código em um formato de 64 bits (x64). Portanto, certifique-se de que seu código seja compilado para execução em x64. Caso contrário, você obterá o erro de formato incorreto mostrado acima!

Cada dll de assembly ou arquivo de recursos (como um tempo de execução diferente, um assembly nativo ou arquivos de configuração, entre outros) carregado pode ter no máximo 400 MB e o tamanho total dos recursos implantados por meio de DEPLOY RESOURCE ou assemblies de referências e seus arquivos adicionais não pode ultrapassar 3 GB.

Por fim, observe que cada banco de dados U-SQL pode conter apenas uma versão de qualquer determinado assembly. Por exemplo, se você precisar da versão 7 e da versão 8 da biblioteca NewtonSoft Json.Net, será preciso registrá-las em dois bancos de dados diferentes. Além disso, cada script pode fazer referência apenas a uma versão da dll de um determinado assembly. Nesse sentido, o U-SQL segue a semântica de controle de versão e gerenciamento do assembly de C#!


## <a name="user-defined-functions---udf"></a>Funções definidas pelo usuário — UDF
As funções definidas pelo usuário ou UDF do U-SQL são rotinas de programação que aceitam parâmetros, executam uma ação, como um cálculo complexo, e retornam o resultado dessa ação como um valor. O valor de retorno da UDF pode ser apenas um único escalar. A UDF do U-SQL pode ser chamado no script base U-SQL como qualquer outra função escalar C#.

As funções definidas pelo usuário do U-SQL devem ser inicializadas como públicas e **estáticas**.

```c#
        public static string MyFunction(string param1)
        {
            return "my result";
        }
```

Primeiro, vamos ver um exemplo simples de como criar uma UDF.

Nesse cenário de caso de uso, precisamos determinar o período fiscal – trimestre fiscal e mês fiscal do primeiro logon para o usuário específico. O primeiro mês fiscal do ano em nosso cenário é junho.

Para calcular o período fiscal, introduzimos a seguinte função C#

```c#
        public static string GetFiscalPeriod(DateTime dt)
        {
            int FiscalMonth=0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter=0;
            if (FiscalMonth >=1 && FiscalMonth<=3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return "Q" + FiscalQuarter.ToString() + ":P" + FiscalMonth.ToString();
        }
```

Ela simplesmente calcula o mês e o trimestre fiscal e retorna um valor de cadeia de caracteres. Para junho – primeiro mês do primeiro trimestre fiscal – "Q1:P1", para julho – "Q1:P2", etc.

Essa é uma função C# regular que vamos usar em nosso projeto U-SQL.

Veja como é a seção Code-behind nesse cenário

```c#
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        public static string GetFiscalPeriod(DateTime dt)
        {
            int FiscalMonth=0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter=0;
            if (FiscalMonth >=1 && FiscalMonth<=3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return "Q" + FiscalQuarter.ToString() + ":" + FiscalMonth.ToString();
        }

    }

}
```

Agora, vamos chamar essa função do script base U-SQL. Para fazer isso, precisamos fornecer um nome totalmente qualificado da função, incluindo o namespace — NameSpace.Class.Function(parameter)

```sql
    USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

Veja a seguir o script base U-SQL real

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");

@rs1 =
    SELECT
        MAX(guid) AS start_id,
        MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

Veja a seguir o arquivo de saída da execução do script

```
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

Esse exemplo demonstra um uso simples de UDF embutida no U-SQL.

### <a name="keeping-state-between-udf-invocations"></a>Mantendo o estado entre invocações da UDF
Os objetos de programação C# do U-SQL podem ser mais sofisticados ao utilizar interatividade por meio de Variáveis Globais de Code-behind. Vejamos o seguinte cenário de caso de uso comercial:

Em grandes organizações, os usuários podem alternar entre diversos aplicativos internos. Isso pode incluir o Microsoft Dynamics CRM, o PowerBI etc. O cliente deseja aplicar uma análise de telemetria de como os usuários alternam entre diferentes aplicativos, qual é a tendência de uso e assim por diante. O objetivo final da empresa é otimizar o uso de aplicativos. Possivelmente combinar diferentes aplicativos ou rotinas específicas de logon.

Para atingir essa meta, precisamos determinar as IDs de uma sessão e o tempo de latência entre as últimas sessões ocorridas.

Precisamos encontrar um logon anterior e atribuí-lo a todas as sessões que estão sendo geradas para o mesmo aplicativo. Primeiro desafio: o script base U-SQL não nos permitirá aplicar cálculos sobre colunas já calculadas com a função LAG. Segundo desafio: temos que manter a sessão específica para todas as sessões no mesmo período.

Para resolver esse problema, vamos usar uma Variável Global dentro da seção Code-behind - `static public string globalSession;`.

Essa Variável Global é aplicada a todo o conjunto de linhas durante a execução do nosso script.

Veja abaixo a seção Code-behind do nosso programa U-SQL

```c#
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQLApplication21
{
    public class UserSession
    {
        static public string globalSession;
        static public string StampUserSession(string eventTime, string PreviousRow, string Session)
        {

            if (!string.IsNullOrEmpty(PreviousRow))
            {
                double timeGap = Convert.ToDateTime(eventTime).Subtract(Convert.ToDateTime(PreviousRow)).TotalMinutes;
                if (timeGap <= 60)
                    return Session;
                else
                    return Guid.NewGuid().ToString();
            }
            else
                return Guid.NewGuid().ToString();

        }

        static public string getStampUserSession(string Session)
        {
            if (Session != globalSession && !string.IsNullOrEmpty(Session))
            {
                globalSession = Session;
            }
            return globalSession;
        }

    }
}
```

A variável global `static public string globalSession;` usada dentro da função `getStampUserSession` é reinicializada toda vez que o parâmetro Session é alterado.

Script base U-SQL

```sql
DECLARE @in string = @"\UserSession\test1.tsv";
DECLARE @out1 string = @"\UserSession\Out1.csv";
DECLARE @out2 string = @"\UserSession\Out2.csv";
DECLARE @out3 string = @"\UserSession\Out3.csv";

@records =
    EXTRACT DataId string,
            EventDateTime string,           
            UserName string,
            UserSessionTimestamp string

    FROM @in
    USING Extractors.Tsv();

@rs1 =
    SELECT EventDateTime,
           UserName,
LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,          
           string.IsNullOrEmpty(LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,           
           USQLApplication21.UserSession.StampUserSession
           (
               EventDateTime,
               LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC),
               LAG(UserSessionTimestamp, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)
           )
           AS UserSessionTimestamp
    FROM @records;

@rs2 =
    SELECT EventDateTime,
           UserName,
           LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,
           string.IsNullOrEmpty(LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,
           USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp) AS UserSessionTimestamp
    FROM @rs1
    WHERE UserName != "UserName";

OUTPUT @rs2
TO @out2
ORDER BY UserName, EventDateTime ASC
USING Outputters.Csv();
```

A função `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` é chamada aqui durante o segundo cálculo do Conjunto de Linhas da Memória. Ele passa a coluna `UserSessionTimestamp` e retorna o valor até que `UserSessionTimestamp` seja alterado.

O arquivo de saída

```
"2016-02-19T07:32:36.8420000-08:00","User1",,True,"72a0660e-22df-428e-b672-e0977007177f"
"2016-02-17T11:52:43.6350000-08:00","User2",,True,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-17T11:59:08.8320000-08:00","User2","2016-02-17T11:52:43.6350000-08:00",False,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-11T07:04:17.2630000-08:00","User3",,True,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-11T07:10:33.9720000-08:00","User3","2016-02-11T07:04:17.2630000-08:00",False,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-15T21:27:41.8210000-08:00","User3","2016-02-11T07:10:33.9720000-08:00",False,"4d2bc48d-bdf3-4591-a9c1-7b15ceb8e074"
"2016-02-16T05:48:49.6360000-08:00","User3","2016-02-15T21:27:41.8210000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-16T06:22:43.6390000-08:00","User3","2016-02-16T05:48:49.6360000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-17T16:29:53.2280000-08:00","User3","2016-02-16T06:22:43.6390000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T16:39:07.2430000-08:00","User3","2016-02-17T16:29:53.2280000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T17:20:39.3220000-08:00","User3","2016-02-17T16:39:07.2430000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-19T05:23:54.5710000-08:00","User3","2016-02-17T17:20:39.3220000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T05:48:37.7510000-08:00","User3","2016-02-19T05:23:54.5710000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T06:40:27.4830000-08:00","User3","2016-02-19T05:48:37.7510000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T07:27:37.7550000-08:00","User3","2016-02-19T06:40:27.4830000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T19:35:40.9450000-08:00","User3","2016-02-19T07:27:37.7550000-08:00",False,"3f385f0b-3e68-4456-ac74-ff6cef093674"
"2016-02-20T00:07:37.8250000-08:00","User3","2016-02-19T19:35:40.9450000-08:00",False,"685f76d5-ca48-4c58-b77d-bd3a9ddb33da"
"2016-02-11T09:01:33.9720000-08:00","User4",,True,"9f0cf696-c8ba-449a-8d5f-1ca6ed8f2ee8"
"2016-02-17T06:30:38.6210000-08:00","User4","2016-02-11T09:01:33.9720000-08:00",False,"8b11fd2a-01bf-4a5e-a9af-3c92c4e4382a"
"2016-02-17T22:15:26.4020000-08:00","User4","2016-02-17T06:30:38.6210000-08:00",False,"4e1cb707-3b5f-49c1-90c7-9b33b86ca1f4"
"2016-02-18T14:37:27.6560000-08:00","User4","2016-02-17T22:15:26.4020000-08:00",False,"f4e44400-e837-40ed-8dfd-2ea264d4e338"
"2016-02-19T01:20:31.4800000-08:00","User4","2016-02-18T14:37:27.6560000-08:00",False,"2136f4cf-7c7d-43c1-8ae2-08f4ad6a6e08"
```

Esse exemplo demonstra um cenário de caso de uso mais complicado quando usamos uma Variável Global dentro da seção Code-behind aplicada a todo um Conjunto de Linhas da Memória.

## <a name="using-user-defined-types---udt"></a>Usando Tipos Definidos pelo Usuário — UDT
Os Tipos Definidos pelo Usuário ou UDTs são outro recurso de programação do U-SQL. O UDT do U-SQL atua como um tipo definido pelo usuário regular de C#. C# é uma linguagem fortemente tipada que permite o uso de tipos definidos pelo usuário personalizados e internos.

O U-SQL não pode implicitamente serializar/desserializar UDTs arbitrários enquanto o UDT é passado entre os vértices em conjuntos de linhas. Assim, o usuário precisa fornecer um formatador explícito usando a interface IFormatter. Isso fornecerá ao U-SQL métodos de serialização e desserialização para o UDT. 

> [!NOTE]
> Extratores internos e outputters de U-SQL atualmente não podem serializar/desserializar dados de UDT de arquivos, mesmo com o conjunto do IFormatter.  Assim, ao gravar dados de UDT em um arquivo com a declaração OUTPUT ou ao fazer sua leitura com um extrator, o usuário precisará passá-lo como uma cadeia de caracteres ou matriz de bytes e chamar o código de serialização e desserialização explicitamente (por exemplo, o método ToString() do UDT). Por outro lado, extratores definidos pelo usuário e outputters podem ler e gravar UDTs.

Se tentarmos usam o UDT em EXTRATOR ou OUTPUTTER (fora do SELECT anterior)

```sql
@rs1 =
    SELECT MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

receberemos o seguinte erro

```
    Error    1    E_CSC_USER_INVALIDTYPEINOUTPUTTER: Outputters.Text was used to output column myfield of type
    MyNameSpace.Myfunction_Returning_UDT.

    Description:

    Outputters.Text only supports built-in types.

    Resolution:

    Implement a custom outputter that knows how to serialize this type or call a serialization method on the type in
    the preceding SELECT.    C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\
    USQL-Programmability\Types.usql    52    1    USQL-Programmability
```

Para trabalhar com UDT em Outptutter, também temos que serializá-lo para cadeia de caracteres com o método ToString() e criar um Outputter personalizado.

Atualmente, os UDTs não podem ser usados em GROUP BY. Se o UDT for usado em GROUP BY, o seguinte erro será gerado:

```
    Error    1    E_CSC_USER_INVALIDTYPEINCLAUSE: GROUP BY doesn't support type MyNameSpace.Myfunction_Returning_UDT
    for column myfield

    Description:

    GROUP BY doesn't support UDT or Complex types.

    Resolution:

    Add a SELECT statement where you can project a scalar column that you want to use with GROUP BY.
    C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\USQL-Programmability\Types.usql
    62    5    USQL-Programmability
```

Para definir um UDT, temos que:

* Adicionar os seguintes namespaces

```c#
    using Microsoft.Analytics.Interfaces
    using System.IO;
```

* `Microsoft.Analytics.Interfaces` é necessário para as interfaces UDT e `System.IO` pode ser necessário para definir a interface IFormatter

* Definir o Tipo Definido Usado com o atributo SqlUserDefinedType

**SqlUserDefinedType** é usado para marcar uma definição de tipo em um assembly como um UDT (tipo definido pelo usuário) no U-SQL. As propriedades no atributo refletem as características físicas do UDT. Essa classe não pode ser herdada.

SqlUserDefinedType é um atributo necessário para a definição de UDT.

O construtor da classe  

* SqlUserDefinedTypeAttribute(Type formatter)

* Formatador de tipo – um parâmetro obrigatório para definir um formatador de UDT. Especificamente, o tipo da interface `IFormatter` precisa ser passado aqui.

```c#
    [SqlUserDefinedType(typeof(MyTypeFormatter))]
      public class MyType
              {
             …
           }
```

* O UDT típico também exigirá definição da interface IFormatter

```c#
       public class MyTypeFormatter : IFormatter<MyType>
        {
            public void Serialize(MyType instance, IColumnWriter writer,
                           ISerializationContext context)
            {
        …
            }

            public MyType Deserialize(IColumnReader reader,
                                  ISerializationContext context)
            {
        …
            }
        }
```

A interface `IFormatter` para serializar e desserializar um gráfico de objeto com o tipo raiz de \<typeparamref name="T">.

\<typeparam name="T">O tipo raiz para serialização e desserialização do gráfico de objeto.

* Desserializar – desserializa os dados no fluxo fornecido e reconstitui o gráfico de objetos.

* Serializar – serializa um objeto ou gráfico de objetos com determinada raiz para o fluxo fornecido.

Instância `MyType` — a instância do tipo  
Gravador `IColumnWriter`/leitor `IColumnReader` – o fluxo da coluna subjacente.  
Contexto `ISerializationContext` – enumeração que define um conjunto de sinalizadores que especifica o contexto de origem ou destino para o fluxo durante a serialização. 
 
   * *Intermediário* - especifica que o contexto de origem ou de destino não é um armazenamento persistente

   * *Persistência* - especifica que o contexto de origem ou de destino é um armazenamento persistente

Como um tipo C# regular, a definição de UDT do U-SQL pode incluir substituições para operadores como +/==/!= etc. Pode incluir métodos estáticos, entre outros. Por exemplo, se formos usar esse UDT como um parâmetro para a função agregada MIN do U-SQL, teremos que definir a substituição do operador <.

Anteriormente neste guia, demonstramos um exemplo da identificação do período fiscal na data específica no formato de Qn:Pn (Q1:P10). O exemplo a seguir mostra como definir um tipo personalizado para os valores do período fiscal.

Seção Code-behind com UDT personalizado e a interface IFormatter para ele:

```c#
        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
        public struct FiscalPeriod
        {
            public int Quarter { get; private set; }

            public int Month { get; private set; }

            public FiscalPeriod(int quarter, int month):this()
            {
                this.Quarter = quarter;
                this.Month = month;
            }

            public override bool Equals(object obj)
            {
                if (ReferenceEquals(null, obj))
                {
                    return false;
                }

                return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
            }

            public bool Equals(FiscalPeriod other)
            {
return this.Quarter.Equals(other.Quarter) && this.Month.Equals(other.Month);
            }

            public bool GreaterThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
            }

            public bool LessThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
            }

            public override int GetHashCode()
            {
                unchecked
                {
                    return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
                }
            }

            public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
            {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
            }

            public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.Equals(c2);
            }

            public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
            {
                return !c1.Equals(c2);
            }
            public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.GreaterThan(c2);
            }
            public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.LessThan(c2);
            }
            public override string ToString()
            {
                return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
            }

        }

        public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
        {
public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
            {
                using (var binaryWriter = new BinaryWriter(writer.BaseStream))
                {
                    binaryWriter.Write(instance.Quarter);
                    binaryWriter.Write(instance.Month);
                    binaryWriter.Flush();
                }
            }

public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
            {
                using (var binaryReader = new BinaryReader(reader.BaseStream))
                {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
                    return result;
                }
            }
        }
```

O tipo definido inclui dois números – Trimestre e Mês. Os operadores ==/!=/>/< e o método estático ToString() são definidos aqui.

Como mencionado anteriormente, o UDT pode ser usado na expressão SELECT, mas não pode ser usado em OUTPUTTER/EXTRACTOR sem serialização personalizada. Ele precisa ser serializado como uma cadeia de caracteres com ToString() ou usado com OUTPUTTER/EXTRACTOR personalizado.

Agora, vamos falar sobre o uso do UDT. Na seção Code-behind, alteramos a nossa função GetFiscalPeriod para

```c#
        public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
        {
            int FiscalMonth = 0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter = 0;
            if (FiscalMonth >= 1 && FiscalMonth <= 3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return new FiscalPeriod(FiscalQuarter, FiscalMonth);
        }
```

Como você pode ver, ela retorna o valor do nosso tipo FiscalPeriod.

Veja a seguir como a usamos no script base U-SQL. Este exemplo demonstra as diferentes maneiras de invocação do UDT no script U-SQL.

```sql
DECLARE @input_file string = @"c:\work\cosmos\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"c:\work\cosmos\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT guid AS start_id,
           dt,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Quarter AS fiscalquarter,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Month AS fiscalmonth,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt) + new USQL_Programmability.CustomFunctions.FiscalPeriod(1,7) AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs0;

@rs2 =
    SELECT start_id,
           dt,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           fiscalquarter,
           fiscalmonth,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS fiscalperiod,
       
       // This user-defined type was created in the prior SELECT.  Passing the UDT to this subsequent SELECT would have failed if the UDT was not annotated with an IFormatter.
           fiscalperiod_adjusted.ToString() AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 TO @output_file USING Outputters.Text();
```

Seção Code-behind completa:

```c#
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.IO;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        static public DateTime? ToDateTime(string dt)
        {
            DateTime dtValue;

            if (!DateTime.TryParse(dt, out dtValue))
                return Convert.ToDateTime(dt);
            else
                return null;
        }

        public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
        {
            int FiscalMonth = 0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter = 0;
            if (FiscalMonth >= 1 && FiscalMonth <= 3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return new FiscalPeriod(FiscalQuarter, FiscalMonth);
        }



        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
        public struct FiscalPeriod
        {
            public int Quarter { get; private set; }

            public int Month { get; private set; }

            public FiscalPeriod(int quarter, int month):this()
            {
                this.Quarter = quarter;
                this.Month = month;
            }

            public override bool Equals(object obj)
            {
                if (ReferenceEquals(null, obj))
                {
                    return false;
                }

                return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
            }

            public bool Equals(FiscalPeriod other)
            {
return this.Quarter.Equals(other.Quarter) &&    this.Month.Equals(other.Month);
            }

            public bool GreaterThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
            }

            public bool LessThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
            }

            public override int GetHashCode()
            {
                unchecked
                {
                    return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
                }
            }

            public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
            {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
            }

            public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.Equals(c2);
            }

            public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
            {
                return !c1.Equals(c2);
            }
            public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.GreaterThan(c2);
            }
            public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.LessThan(c2);
            }
            public override string ToString()
            {
                return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
            }

        }

        public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
        {
public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
            {
                using (var binaryWriter = new BinaryWriter(writer.BaseStream))
                {
                    binaryWriter.Write(instance.Quarter);
                    binaryWriter.Write(instance.Month);
                    binaryWriter.Flush();
                }
            }

public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
            {
                using (var binaryReader = new BinaryReader(reader.BaseStream))
                {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
                    return result;
                }
            }
        }

    }
}
```

## <a name="user-defined-aggregates--udagg"></a>Agregações definidas pelo usuário – UDAGG
As agregações definidas pelo usuário são quaisquer funções relacionadas à agregação que não são enviadas para uso imediato com o U-SQL. O exemplo pode ser uma agregação para executar um cálculo matemático personalizado, executar concatenações de cadeia de caracteres ou manipulações com cadeias de caracteres, etc.

A definição de classe base da agregação definida pelo usuário é

```c#
    [SqlUserDefinedAggregate]
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
    {
        protected IAggregate();

        public abstract void Accumulate(T1 t1, T2 t2);
        public abstract void Init();
        public abstract TResult Terminate();
    }
```

**SqlUserDefinedAggregate** indica que o tipo deve ser registrado como uma agregação definida pelo usuário. Essa classe não pode ser herdada.

O atributo SqlUserDefinedType é **opcional** para a definição de UDAGG


A classe base permite passar três parâmetros abstratos – dois de entrada e um de resultado. Os tipos de dados são variáveis e devem ser definidos durante a herança da classe.

```c#
    public class GuidAggregate : IAggregate<string, string, string>
    {
        string guid_agg;

        public override void Init()
        {
        …
        }

        public override void Accumulate(string guid, string user)
        {
        …
        }

        public override string Terminate()
        {
        …
        }

    }
```

* Init fornece a rotina de inicialização para cada grupo de agregação. Ele é invocado uma vez para cada grupo durante o cálculo.
* Accumulate – executado uma vez para cada valor. Fornece a funcionalidade principal para o algoritmo de agregação. Pode ser usado para agregar valores com vários tipos de dados definidos durante a herança da classe. Pode aceitar dois parâmetros de tipos de dados variáveis.
* Terminate — executado uma vez por grupo de agregação no fim do processamento para gerar o resultado para cada grupo

Para declarar tipos de dados de entrada e saída corretos, use a definição de classe

```c#
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1 – primeiro parâmetro para Accumulate
* T2 – primeiro parâmetro para Accumulate
* TResult – tipo de retorno de Terminate

por exemplo

```c#
    public class GuidAggregate : IAggregate<string, int, int>
```

ou o

```c#
    public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="using-udagg-in-u-sql"></a>Usando a UDAGG no U-SQL
Para usar a UDAGG, primeiramente defina-a no code-behind ou faça referência a ela na DLL de programação existente, conforme discutido anteriormente.

Em seguida, use a seguinte sintaxe

```c#
    AGG<UDAGG_functionname>(param1,param2)
```

Veja o exemplo da UDAGG

```c#
    public class GuidAggregate : IAggregate<string, string, string>
    {
        string guid_agg;

        public override void Init()
        {
            guid_agg = "";
        }

        public override void Accumulate(string guid, string user)
        {
            if (user.ToUpper()== "USER1")
            {
                guid_agg += "{" + guid + "}";
            }
        }

        public override string Terminate()
        {
            return guid_agg;
        }

    }
```

e o script base U-SQL

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @" \usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        user,
        AGG<USQL_Programmability.GuidAggregate>(guid,user) AS guid_list
    FROM @rs0
    GROUP BY user;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

Nesse cenário de caso de uso, concatenamos os GUIDs de classe para usuários específicos.

## <a name="user-defined-objects--udo"></a>Objetos definidos pelo usuário – UDO
O U-SQL fornece um recurso para definir objetos de programação personalizados que chamamos de Objetos Definidos pelo Usuário ou UDO.

Veja a seguir a lista de UDO no U-SQL

* Extratores Definidos pelo Usuário
    * Extrair linha por linha
    * Usado para implementar arquivos estruturados e personalizados do formulário de extração de dados

* Outputters Definidos pelo Usuário
    * Gerar linha por linha
    * Usado para tipos de dados personalizados de saída ou formato de arquivo personalizado

* Processadores Definidos pelo Usuário
    * Usar uma linha e gerar uma linha
    * Usado para reduzir o número de colunas ou gerar nova coluna com valores derivados do conjunto de colunas existente

* Aplicadores Definidos pelo Usuário
    * Usar uma linha e gerar 0 para n linhas
    * Usado com OUTER/CROSS APPLY

* Combinadores Definidos pelo Usuário
    * Combina conjuntos de linhas — JOINs definidos pelo usuário

* Redutores Definidos pelo Usuário
    * Usar n linhas e gerar uma linha
    * Usado para reduzir o número de linhas

Geralmente o UDO é chamado explicitamente no script U-SQL como parte das seguintes instruções U-SQL:

* EXTRACT
* OUTPUT
* PROCESS
* COMBINE
* REDUCE

## <a name="user-defined-extractor"></a>Extrator Definido pelo Usuário
O U-SQL permite importar dados externos usando a instrução EXTRACT. A instrução EXTRACT pode usar extratores de UDO internos  

* *Extractors.Text()*: fornece extração dos arquivos de texto delimitados de diferentes codificações.

* *Extractors.Csv()*: fornece extração de arquivos CSV (valores separados por vírgula) de diferentes codificações.

* *Extractors.Tsv()*: fornece extração de arquivos TSV (valores separados por tabulação) de diferentes codificações.

No entanto, pode ser útil desenvolver um extrator personalizado. Isso pode ser útil durante a importação de dados se quiser

* Modificar dados de entrada ao dividir colunas, modificando os valores individuais. A combinação de colunas é mais fácil usando a funcionalidade PROCESSOR.
* Analisar dados não estruturados, como páginas da Web/Emails, ou semiestruturados, como XML/JSON.
* Analisar dados na codificação sem suporte

Para definir o Extrator Definido pelo Usuário ou UDE, precisamos criar a interface `IExtractor`. Todos os parâmetros de entrada para o extrator, como delimitadores de linha/coluna, codificação, etc., precisam ser definidos no construtor da classe. A interface `IExtractor` também deve conter a definição para a substituição `IEnumerable<IRow>`

```c#
     [SqlUserDefinedExtractor]
     public class SampleExtractor : IExtractor
     {
         public SampleExtractor(string row_delimiter, char col_delimiter)
         {
            …

         }

         public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
         {
             …
         }
     }
```

O atributo **SqlUserDefinedExtractor** indica que o tipo deve ser registrado como um extrator definido pelo usuário. Essa classe não pode ser herdada.

SqlUserDefinedExtractor é um atributo opcional para a definição de UDE. Ele é usado para definir a propriedade AtomicFileProcessing do objeto UDE.

* bool     AtomicFileProcessing   

* **true**  = indica que esse Extrator exige arquivos de entrada atômicos (JSON, XML, ...)
* **false** = indica que esse Extrator pode lidar com arquivos divididos/distribuídos (CSV, SEQ, ...)

Os principais objetos de programação do UDE são *input* e *output*. O objeto input é usado para enumerar dados de entrada como `IUnstructuredReader` e *output* para definir dados de saída como resultado da atividade do extrator.

Os dados de entrada são acessados por meio de `System.IO.Stream` e `System.IO.StreamReader`.

Para enumeração de colunas de entrada, primeiramente dividimos o fluxo de entrada usando o delimitador de linha

```c#
    foreach (Stream current in input.Split(my_row_delimiter))
    {
    …
    }
```

Em seguida, dividimos ainda mais a linha de entrada em partes de coluna.

```c#
    foreach (Stream current in input.Split(my_row_delimiter))
    {
    …
        string[] parts = line.Split(my_column_delimiter);
               foreach (string part in parts)
        {
        …
        }
    }
```

Para definir os dados de saída, usamos o método `output.Set`

É importante entender que o extrator personalizado só produzirá colunas/valores definidos com output. Defina a chamada do método.

```c#
    output.Set<string>(count, part);
```

A saída real do extrator é disparado chamando `yield return output.AsReadOnly();`.

Veja o exemplo do extrator

```c#
    [SqlUserDefinedExtractor(AtomicFileProcessing = true)]
    public class FullDescriptionExtractor : IExtractor
    {
         private Encoding _encoding;
         private byte[] _row_delim;
         private char _col_delim;

        public FullDescriptionExtractor(Encoding encoding, string row_delim = "\r\n", char col_delim = '\t')
        {
             this._encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
             this._row_delim = this._encoding.GetBytes(row_delim);
             this._col_delim = col_delim;

        }

        public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
        {
             string line;
             //Read the input line by line
             foreach (Stream current in input.Split(_encoding.GetBytes("\r\n")))
             {
                using (System.IO.StreamReader streamReader = new StreamReader(current, this._encoding))
                 {
                     line = streamReader.ReadToEnd().Trim();
                     //Split the input by the column delimiter
                     string[] parts = line.Split(this._col_delim);
                     int count = 0; // start with first column
                     foreach (string part in parts)
                     {
    if (count == 0)
                         {  // for column “guid”, re-generated guid
                             Guid new_guid = Guid.NewGuid();
                             output.Set<Guid>(count, new_guid);
                         }
                         else if (count == 2)
                         {
                             // for column “user”, convert to UPPER case
                             output.Set<string>(count, part.ToUpper());

                         }
                         else
                         {
                             // keep the rest of the columns as-is
                             output.Set<string>(count, part);
                         }
                         count += 1;
                     }

                 }
                 yield return output.AsReadOnly();
             }
             yield break;
         }
     }
```

Nesse cenário de caso de uso, o extrator regenera o GUID para a coluna "guid" e converte os valores da coluna "usuário" em letras MAIÚSCULAS. Os extratores personalizados podem produzir resultados mais complicados ao analisar dados de entrada e manipular com eles.

Script base U-SQL que usa um extrator personalizado

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt String,
        user String,
        des String
    FROM @input_file
    USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 TO @output_file USING Outputters.Text();
```

## <a name="user-defined-outputter"></a>Outputter Definidos pelo Usuário
O Outputter Definido pelo Usuário é outro UDO do U-SQL que permite estender uma funcionalidade interna do U-SQL. Assim como o extrator, há vários outputters internos.

* *Outputters.Text()*: grava dados em arquivos de texto delimitados de codificações diferentes.
* *Outputters.Csv()*: grava dados em arquivos CSV (valores separados por vírgula) de codificações diferentes.
* *Outputters.Tsv()*: grava dados em arquivos TSV (valores separados por tabulação) de codificações diferentes.

O outputter personalizado permite gravar dados em um formato definido personalizado. Isso pode ser útil para

* Gravar dados em arquivos semiestruturados ou não estruturados
* Gravar dados em codificações sem suporte
* Modificar dados de saída ou adicionar atributos personalizados

Para definir o Outputter Definido pelo Usuário, precisamos criar a interface `IOutputter`

Implementação da classe `IOutputter` base

```c#
    public abstract class IOutputter : IUserDefinedOperator
    {
        protected IOutputter();

        public virtual void Close();
        public abstract void Output(IRow input, IUnstructuredWriter output);
    }
```

Todos os parâmetros de entrada para o outputter, como delimitadores de linha/coluna, codificação, etc., precisam ser definidos no construtor da classe. A interface `IOutputter` também deve conter a definição para a substituição `void Output`. O atributo `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` pode ser definido, se desejado, para processamento de arquivo atômico — veja os detalhes abaixo.

```c#
        [SqlUserDefinedOutputter(AtomicFileProcessing = true)]
        public class MyOutputter : IOutputter
        {

            public MyOutputter(myparam1, myparam2)
            {
              …
            }

            public override void Close()
            {
              …
            }

            public override void Output(IRow row, IUnstructuredWriter output)
            {
              …
            }
        }
```

* `Output` é chamado para cada linha de entrada. Retorna o conjunto de linhas `IUnstructuredWriter output`
* A classe Constructor é usada para passar parâmetros ao Outputter Definido pelo Usuário
* `Close` substitui, como opção, o estado dispendioso de liberação ou sabe quando a última linha foi escrita

O atributo **SqlUserDefinedOutputter** indica que o tipo deve ser registrado como um outputter definido pelo usuário. Essa classe não pode ser herdada.

SqlUserDefinedOutputter é um atributo opcional para a definição do Outputter Definida pelo Usuário. Ele é usado para definir a propriedade AtomicFileProcessing.

* bool     AtomicFileProcessing   

* **true**  = indica que esse Outputter exige arquivos de saída atômicos (JSON, XML, ...)
* **false** = indica que esse Outputter pode lidar com arquivos divididos/distribuídos (CSV, SEQ, ...)

Os principais objetos de programação são *row* e *output*. O objeto *row* é usado para enumerar dados de saída como a interface `IRow` e *output* para definir os dados de saída para o arquivo de destino.

Os dados de saída são acessados por meio da interface `IRow`. Os dados de saída são passados em uma linha por vez.

Os valores individuais são enumerados chamando o método Get da interface IRow.

```c#
    row.Get<string>("column_name")
```

Os nomes de coluna individuais podem ser determinados chamando `row.Schema`

```c#
    ISchema schema = row.Schema;
    var col = schema[i];
    string val = row.Get<string>(col.Name)
```

Essa abordagem permite que criar um outputter flexível para qualquer esquema de metadados.

Os dados de saída são gravados em um arquivo usando `System.IO.StreamWriter` com o parâmetro stream definido como `output.BaseStrea` como parte de `IUnstructuredWriter output`

Observação importante: para liberar o buffer de dados para o arquivo após a iteração de cada linha, o objeto `StreamWriter` deve ser usado com o atributo Disposable habilitado(padrão) e a palavra-chave *using*

```c#
    using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
            {
        …
            }
```

Caso contrário, chame o método Flush() explicitamente após cada iteração — isso é demonstrado em mais detalhes neste exemplo:

### <a name="setting-header-and-footer-for-user-defined-outputter"></a>Definindo o cabeçalho e o rodapé para Outputter Definido pelo Usuário
Para definir um cabeçalho, use o fluxo de execução única de iteração.

```c#
            public override void Output(IRow row, IUnstructuredWriter output)
            {
             …
                if (isHeaderRow)
                {
                    …                
                }

             …
                if (isHeaderRow)
                {
                    isHeaderRow = false;
                }
             …
            }
        }
```

O código no primeiro bloco `if (isHeaderRow)` é executado apenas uma vez.

Para o rodapé, use a referência à instância do objeto `System.IO.Stream` (`output.BaseStream`) e escreva o rodapé no método Close() da interface `IOutputter` — veja o exemplo abaixo.

Veja o exemplo do Outputter Definido pelo Usuário

```c#
        [SqlUserDefinedOutputter(AtomicFileProcessing = true)]
        public class HTMLOutputter : IOutputter
        {
            // Local variables initialization
            private string row_delimiter;
            private char col_delimiter;
            private bool isHeaderRow;
            private Encoding encoding;
            private bool IsTableHeader = true;
            private Stream g_writer;

            // Parameters definition            
            public HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                this.isHeaderRow = isHeader;
                this.encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
            }

            // Close method is used to write footer to file - executed only once after all rows
            public override void Close()
            {
                //Reference to IO.Stream object - g_writer
                StreamWriter streamWriter = new StreamWriter(g_writer, this.encoding);
                streamWriter.Write("</table>");
                streamWriter.Flush();
                streamWriter.Close();
            }

            public override void Output(IRow row, IUnstructuredWriter output)
            {
                System.IO.StreamWriter streamWriter = new StreamWriter(output.BaseStream, this.encoding);

                // Metadata schema initialization to enumerate column names
                ISchema schema = row.Schema;

                // This is data independent header - HTML table definition
                if (IsTableHeader)
                {
                    streamWriter.Write("<table border=1>");
                    IsTableHeader = false;
                }

                // HTML table attributes
                string header_wrapper_on = "<th>";
                string header_wrapper_off = "</th>";
                string data_wrapper_on = "<td>";
                string data_wrapper_off = "</td>";

                // Header row output - runs only once
                if (isHeaderRow)
                {
                    streamWriter.Write("<tr>");
                    for (int i = 0; i < schema.Count(); i++)
                    {
                        var col = schema[i];
                        streamWriter.Write(header_wrapper_on + col.Name + header_wrapper_off);
                    }
                    streamWriter.Write("</tr>");
                }

                // Data row output
                streamWriter.Write("<tr>");                
                for (int i = 0; i < schema.Count(); i++)
                {
                    var col = schema[i];
                    string val = "";
                    try
                    {
                        // Data type enumeration - require to match the distinct list of types form OUTPUT statement
                        switch (col.Type.Name.ToString().ToLower())
                        {
                            case "string": val = row.Get<string>(col.Name).ToString(); break;
                            case "guid": val = row.Get<Guid>(col.Name).ToString(); break;
                            default: break;
                        }
                    }
                    // Hnadling NULL values - keeping them empty
                    catch (System.NullReferenceException)
                    {
                    }
                    streamWriter.Write(data_wrapper_on + val + data_wrapper_off);
                }
                streamWriter.Write("</tr>");

                if (isHeaderRow)
                {
                    isHeaderRow = false;
                }
                // Reference to the instance of the IO.Stream object for footer generation
                g_writer = output.BaseStream;
                streamWriter.Flush();
            }
        }

        // Define the factory classes
        public static class Factory
        {
            public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                return new HTMLOutputter(isHeader, encoding);
            }
        }
```

e o script base U-SQL

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.html";

@rs0 =
    EXTRACT
        guid Guid,
        dt String,
        user String,
        des String
    FROM @input_file
    USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Esse é um outputter HTML – cria um arquivo HTML com dados da tabela.

### <a name="calling-outputter-from-u-sql-base-script"></a>Chamando outputter do script base U-SQL
Para chamar um outputter personalizado do script base U-SQL, a nova instância do objeto outputter deve ser criada.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Para evitar a criação de uma instância do objeto no script base, podemos criar um wrapper de função. Usando o exemplo acima

```c#
        // Define the factory classes
        public static class Factory
        {
            public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                return new HTMLOutputter(isHeader, encoding);
            }
        }
```

Nesse caso, a chamada original se parece com esta

```sql
OUTPUT @rs0 TO @output_file USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="user-defined-processor"></a>Processador Definido pelo Usuário
O Processador Definido pelo Usuário ou UDP é um tipo de UDO do U-SQL que permite processar as linhas de entrada aplicando recursos de programação. O UDP permite combinar colunas, modificar valores, adicionar novas colunas, se necessário. Basicamente, ele ajuda a processar um conjunto de linhas para produzir elementos de dados necessários.

Para definir um UDP, precisamos criar a interface `IProcessor` com o atributo `SqlUserDefinedProcessor`, que é opcional para o UDP.

Essa interface deve conter a definição para substituição do conjunto de linhas da interface `IRow`

```c#
    [SqlUserDefinedProcessor]
     public class MyProcessor: IProcessor
     {
        public override IRow Process(IRow input, IUpdatableRow output)
         {
            …
         }
     }
```

**SqlUserDefinedProcessor** indica que o tipo deve ser registrado como um processador definido pelo usuário. Essa classe não pode ser herdada.

O atributo SqlUserDefinedProcessor é **opcional** para a definição de UDP

Os principais objetos de programação são *input* e *output*. O objeto Input é usado para enumerar dados de entrada e Output para definir dados de saída como resultado da atividade do processador.

Para enumeração de colunas de entrada, usamos o método `input.Get`

```c#
    string column_name = input.Get<string>("column_name");
```

O parâmetro para o método `input.Get` é uma coluna passada como parte da cláusula `PRODUCE` da instrução ` PROCESS` do script base U-SQL. Precisamos usar o tipo de dados correto aqui.

Para saída — método output.Set

É importante entender que o produtor personalizado só produzirá colunas/valores definidos com a chamada ao método output.Set.

```c#
    output.Set<string>("mycolumn", mycolumn);
```

A saída real do processador é disparado chamando `return output.AsReadOnly()`;

Veja abaixo um exemplo de processador

```c#
     [SqlUserDefinedProcessor]
     public class FullDescriptionProcessor : IProcessor
     {
        public override IRow Process(IRow input, IUpdatableRow output)
         {
             string user = input.Get<string>("user");
             string des = input.Get<string>("des");
             string full_description = user.ToUpper() + "=>" + des;
             output.Set<string>("dt", input.Get<string>("dt"));
             output.Set<string>("full_description", full_description);
             output.Set<Guid>("new_guid", Guid.NewGuid());
             output.Set<Guid>("guid", input.Get<Guid>("guid"));
             return output.AsReadOnly();
         }
     }
```

Nesse cenário de caso de uso, o processador está gerando a nova coluna "full_description" combinando os valores existentes — "user" em letras maiúsculas e "des". Ele também regenera um guid e retorna o original, bem como novos valores de guid.

Como podemos ver acima, você pode chamar métodos C# durante a chamada de output.Set.

Script base U-SQL que usa um processador personalizado

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt String,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
     PROCESS @rs0
     PRODUCE dt String,
             full_description String,
             guid Guid,
             new_guid Guid
     USING new USQL_Programmability.FullDescriptionProcessor();

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

## <a name="user-defined-applier"></a>Aplicador Definido pelo Usuário
O Aplicador Definido pelo Usuário do U-SQL permite invocar uma função C# personalizada para cada linha retornada pela expressão de tabela externa de uma consulta. A entrada à direita é avaliada para cada linha a começar da entrada à esquerda e as linhas produzidas são combinadas para a saída final. A lista de colunas produzida pelo operador APPLY é a combinação do conjunto de colunas na entrada à esquerda e à direita.

O Aplicador Definido pelo Usuário está sendo invocado como parte da expressão SELECT do U-SQL.

A chamada característica ao Aplicador Definidor pelo Usuário se parece com esta

```sql
    SELECT …
    FROM …
    CROSS APPLY
    new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Veja [U-SQL SELECT selecionando de CROSS APPLY e OUTER APPLY](https://msdn.microsoft.com/library/azure/mt621307.aspx) para obter mais informações sobre como usar aplicadores na expressão SELECT.

A definição de classe base do Aplicador Definido pelo Usuário é

```c#
    public abstract class IApplier : IUserDefinedOperator
    {
        protected IApplier();

        public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
    }
```

Para definir um Aplicador Definido pelo Usuário, precisamos criar a interface `IApplier` com o atributo [`SqlUserDefinedCombiner`], que é opcional para a definição de Aplicador Definido pelo Usuário.

```c#
    [SqlUserDefinedApplier]
    public class ParserApplier : IApplier
    {

        public ParserApplier()
        {
        …
        }

        public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
        {
        …
        }
    }
```

* Apply chamado para cada linha da tabela Outer. Retorna o conjunto de linhas de saída `IUpdatableRow`
* A classe Constructor é usada para passar parâmetros ao Aplicador Definido pelo Usuário

**SqlUserDefinedApplier** indica que o tipo deve ser registrado como um aplicador definido pelo usuário. Essa classe não pode ser herdada.

O atributo SqlUserDefinedApplier é **opcional** para a definição do Aplicador Definido pelo Usuário


Os principais objetos de programação são

```c#
        public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

Os conjuntos de linhas de entrada são passados como entrada `IRow`. As linhas de saída são geradas como a interface de saída `IUpdatableRow`

Os nomes de coluna individuais podem ser determinados chamando o método de esquema `IRow`.

```c#
    ISchema schema = row.Schema;
    var col = schema[i];
    string val = row.Get<string>(col.Name)
```

Para obter os valores de dados reais do `IRow` de entrada, usamos o método Get() da interface `IRow`

```c#
    mycolumn = row.Get<int>("mycolumn")
```

ou usamos o nome da coluna do esquema

```c#
    row.Get<int>(row.Schema[0].Name)
```

Os valores de saída devem ser definidos com a saída `IUpdatableRow`

```c#
    output.Set<int>("mycolumn", mycolumn)
```

É importante entender que o aplicador personalizado só produzirá colunas/valores definidos com a chamada ao método output.Set.

A saída real é disparada chamando `yield return output.AsReadOnly()`;

Os parâmetros de Aplicador Definido pelo Usuário podem ser passados para o construtor. O aplicador pode retornar um número variável de colunas que precisa ser definido durante a chamada ao Aplicador no script base U-SQL.

```c#
  new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

Veja abaixo o exemplo do Aplicador Definido pelo Usuário:

```c#
   [SqlUserDefinedApplier]
    public class ParserApplier : IApplier
    {
        private string parsingPart;

        public ParserApplier(string parsingPart)
        {
            if (parsingPart.ToUpper().Contains("ALL")
                || parsingPart.ToUpper().Contains("MAKE")
                || parsingPart.ToUpper().Contains("MODEL")
                || parsingPart.ToUpper().Contains("YEAR")
                || parsingPart.ToUpper().Contains("TYPE")
                || parsingPart.ToUpper().Contains("MILLAGE")
                )
            {
                this.parsingPart = parsingPart;
            }
            else
            {
                throw new ArgumentException("Incorrect parameter. Please use: 'ALL[MAKE|MODEL|TYPE|MILLAGE]'");
            }
        }

        public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
        {

            string[] properties = input.Get<string>("properties").Split(',');

            //  only process with correct number of properties
            if (properties.Count() == 5)
            {

                string make = properties[0];
                string model = properties[1];
                string year = properties[2];
                string type = properties[3];
                int millage = -1;

                // Only return millage if it is number, otherwise, -1
                if (!int.TryParse(properties[4], out millage))
                {
                    millage = -1;
                }

                if (parsingPart.ToUpper().Contains("MAKE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("make", make);
                if (parsingPart.ToUpper().Contains("MODEL") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("model", model);
                if (parsingPart.ToUpper().Contains("YEAR") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("year", year);
                if (parsingPart.ToUpper().Contains("TYPE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("type", type);
                if (parsingPart.ToUpper().Contains("MILLAGE") || parsingPart.ToUpper().Contains("ALL")) output.Set<int>("millage", millage);
            }
            yield return output.AsReadOnly();            
        }
    }
```

e o script base U-SQL para esse Aplicador Definido pelo Usuário

```sql
DECLARE @input_file string = @"c:\usql-programmability\car_fleet.tsv";
DECLARE @output_file string = @"c:\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        stocknumber int,
        vin String,
        properties String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        r.stocknumber,
        r.vin,
        properties.make,
        properties.model,
        properties.year,
        properties.type,
        properties.millage
    FROM @rs0 AS r
    CROSS APPLY
    new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

Nesse cenário de caso de uso, o Aplicador Definido pelo Usuário atua como um analisador de valor delimitado por vírgula para as propriedades de frota de carros. As linhas do arquivo de entrada se parecem com estas:

```
103    Z1AB2CD123XY45889    Ford,Explorer,2005,SUV,152345
303    Y0AB2CD34XY458890    Shevrolet,Cruise,2010,4Dr,32455
210    X5AB2CD45XY458893    Nissan,Altima,2011,4Dr,74000
```

Trata-se de um típico arquivo TSV (delimitado por TAB) com a coluna de propriedades contendo propriedades do carro, como Fabricante, Modelo, etc. Essas propriedades precisam ser analisadas para as colunas da tabela. O Aplicador fornecido também permite gerar um número dinâmico de propriedades no conjunto de linhas do resultado, com base no parâmetro passado — ou todas as propriedades, ou apenas um conjunto específico de propriedades.

    …USQL_Programmability.ParserApplier ("all")
    …USQL_Programmability.ParserApplier ("make")
    …USQL_Programmability.ParserApplier ("make&model")

O Aplicador Definido pelo Usuário pode ser chamado como uma nova instância do objeto do aplicador

```c#
    CROSS APPLY new MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

ou com a invocação de um método de fábrica wrapper

```c#
    CROSS APPLY MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

## <a name="user-defined-combiner"></a>Combinador Definido pelo Usuário
O Combinador Definido pelo Usuário ou UDC permite combinar linhas dos conjuntos de linhas esquerdo e direito, com base em uma lógica personalizada. O Combinador Definido pelo Usuário é usado com a expressão COMBINE.

Um combinador está sendo invocado com a expressão COMBINE, que fornece as informações necessárias sobre os conjuntos de linhas de entrada, as colunas de agrupamento, o esquema de resultado esperado e as informações adicionais.

Para chamar um combinador em um script base U-SQL, usamos a seguinte sintaxe

```sql
Combine_Expression :=
    'COMBINE' Combine_Input
    'WITH' Combine_Input
    Join_On_Clause
    Produce_Clause
    [Readonly_Clause]
    [Required_Clause]
    USING_Clause.
```

Para saber mais, veja [Expressão COMBINE (U-SQL)](https://msdn.microsoft.com/library/azure/mt621339.aspx).

Para definir um Combinador Definido pelo Usuário, precisamos criar a interface `ICombiner` com o atributo [`SqlUserDefinedCombiner`], que é opcional para a definição do Combinador Definido pelo Usuário.

Definição da classe `ICombiner` base

```c#
    public abstract class ICombiner : IUserDefinedOperator
    {
        protected ICombiner();
        public virtual IEnumerable<IRow> Combine(List<IRowset> inputs,
               IUpdatableRow output);
        public abstract IEnumerable<IRow> Combine(IRowset left, IRowset right,
               IUpdatableRow output);
    }
```

A implementação personalizada da interface `ICombiner` deve conter a definição para a substituição Combine `IEnumerable<IRow>`.

```c#
    [SqlUserDefinedCombiner]
    public class MyCombiner : ICombiner
    {

        public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output)
        {
        …
        }
    }
```

O atributo **SqlUserDefinedCombiner** indica que o tipo deve ser registrado como um combinador definido pelo usuário. Essa classe não pode ser herdada.

SqlUserDefinedCombiner é um atributo opcional para a definição do Combinador Definido pelo Usuário. Ele é usado para definir a propriedade de modo Combiner.

Modo CombinerMode

A enumeração CombinerMode pode usar os seguintes valores:

* Completo   (0)    Cada linha de saída depende potencialmente de todas as linhas de entrada da esquerda e direita com o mesmo valor de chave

* Esquerda  (1)    Cada linha de saída depende de uma única linha de entrada da esquerda (e potencialmente de todas as linhas da direita com o mesmo valor de chave)

* Direita (2) Cada linha de saída depende de uma única linha de entrada da direita (e potencialmente de todas as linhas da esquerda com o mesmo valor de chave)

* Interno (3)    Cada linha de saída depende de uma única linha de entrada da esquerda e direita com o mesmo valor

Exemplo:    [`SqlUserDefinedCombiner(Mode=CombinerMode.Left)`]


Os principais objetos de programação são

```c#
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

Os conjuntos de linhas de entrada são passados como tipo `IRowset` *esquerdo* e *direito* da interface. Ambos os conjuntos de linhas precisam ser enumerados para processamento. Só é permitido enumerar cada interface uma vez, de modo que temos que enumerar e armazená-la em cache se necessário.

Para fins de caching, podemos criar o tipo List\<T\> de estrutura de memória como resultado da execução da consulta LINQ. Especificamente List<`IRow`>. O tipo de dados anônimo também pode ser usado durante a enumeração.

Veja [Introdução a consultas LINQ (C#)](https://msdn.microsoft.com/library/bb397906.aspx) para obter mais informações sobre consultas LINQ e [IEnumerable\<T\> Interface](https://msdn.microsoft.com/library/9eekhta0(v=vs.110).aspx) para obter mais informações sobre a interface IEnumerable\<T\>.

Para obter os valores de dados reais do `IRowset` de entrada, usamos o método Get() da interface `IRow`

```c#
    mycolumn = row.Get<int>("mycolumn")
```

Os nomes de coluna individuais podem ser determinados chamando o método de esquema `IRow`.

```c#
    ISchema schema = row.Schema;
    var col = schema[i];
    string val = row.Get<string>(col.Name)
```

ou usamos o nome da coluna do esquema

```
    c# row.Get<int>(row.Schema[0].Name)
```

A enumeração geral com o LINQ se parece com este exemplo:

```c#
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

Depois de enumerar os dois conjuntos de linhas, vamos executar um loop em todas as linhas e, para cada linha no conjunto de linhas à esquerda, localizar todas as linhas que atendem à condição de nosso combinador.

Os valores de saída devem ser definidos com a saída `IUpdatableRow`

```c#
    output.Set<int>("mycolumn", mycolumn)
```

A saída real é disparada chamando `yield return output.AsReadOnly()`;

Veja abaixo um exemplo do combinador

```c#
    [SqlUserDefinedCombiner]
    public class CombineSales : ICombiner
    {

        public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output)
        {
            var internetSales =
            (from row in left.Rows
                          select new
                          {
                              ProductKey = row.Get<int>("ProductKey"),
                              OrderDateKey = row.Get<int>("OrderDateKey"),
                              SalesAmount = row.Get<decimal>("SalesAmount"),
                              TaxAmt = row.Get<decimal>("TaxAmt")
                          }).ToList();

            var resellerSales =
            (from row in right.Rows
             select new
             {
                 ProductKey = row.Get<int>("ProductKey"),
                 OrderDateKey = row.Get<int>("OrderDateKey"),
                 SalesAmount = row.Get<decimal>("SalesAmount"),
                 TaxAmt = row.Get<decimal>("TaxAmt")
             }).ToList();

            foreach (var row_i in internetSales)
            {
                foreach (var row_r in resellerSales)
                {

                    if (
                        row_i.OrderDateKey > 0
                        && row_i.OrderDateKey < row_r.OrderDateKey
                        && row_i.OrderDateKey == 20010701
                        && (row_r.SalesAmount + row_r.TaxAmt) > 20000)
                    {
                        output.Set<int>("OrderDateKey", row_i.OrderDateKey);
                        output.Set<int>("ProductKey", row_i.ProductKey);
                        output.Set<decimal>("Internet_Sales_Amount", row_i.SalesAmount + row_i.TaxAmt);
                        output.Set<decimal>("Reseller_Sales_Amount", row_r.SalesAmount + row_r.TaxAmt);
                    }

                }
            }
            yield return output.AsReadOnly();
        }
    }
```

Nesse cenário de caso de uso, estamos criando o relatório de análise para o varejista. O objetivo é encontrar todos os produtos que custam mais de US$&20;.000 e vendidos por meio de sites da internet mais rapidamente do que pelo varejista comum dentro de determinado período.

Veja abaixo o script base U-SQL. Você pode comparar a lógica entre JOIN regular e um combinador:

```sql
DECLARE @LocalURI string = @"\usql-programmability\";

DECLARE @input_file_internet_sales string = @LocalURI+"FactInternetSales.txt";
DECLARE @input_file_reseller_sales string = @LocalURI+"FactResellerSales.txt";
DECLARE @output_file1 string = @LocalURI+"output_file1.tsv";
DECLARE @output_file2 string = @LocalURI+"output_file2.tsv";

@fact_internet_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    CustomerKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_internet_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@fact_reseller_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    ResellerKey int ,
    EmployeeKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_reseller_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@rs1 =
SELECT
    fis.OrderDateKey,
    fis.ProductKey,
    fis.SalesAmount+fis.TaxAmt AS Internet_Sales_Amount,
    frs.SalesAmount+frs.TaxAmt AS Reseller_Sales_Amount
FROM @fact_internet_sales AS fis
     INNER JOIN @fact_reseller_sales AS frs
     ON fis.ProductKey == frs.ProductKey
WHERE
    fis.OrderDateKey < frs.OrderDateKey
    AND fis.OrderDateKey == 20010701
    AND frs.SalesAmount+frs.TaxAmt > 20000;

@rs2 =
COMBINE @fact_internet_sales AS fis
WITH @fact_reseller_sales AS frs
ON fis.ProductKey == frs.ProductKey
PRODUCE OrderDateKey int,
        ProductKey int,
        Internet_Sales_Amount decimal,
        Reseller_Sales_Amount decimal
USING new USQL_Programmability.CombineSales();

OUTPUT @rs1 TO @output_file1 USING Outputters.Tsv();
OUTPUT @rs2 TO @output_file2 USING Outputters.Tsv();
```

O Combinador Definido pelo Usuário pode ser chamado como uma nova instância do objeto do aplicador

```c#
    USING new MyNameSpace.MyCombiner();
```


ou com a invocação de um método de fábrica wrapper

```c#
    USING MyNameSpace.MyCombiner();
```

## <a name="user-defined-reducer"></a>Redutor Definido pelo Usuário
O U-SQL fornece a capacidade de escrever redutores de conjunto de linhas personalizados em C# usando a estrutura de extensibilidade do operador definido pelo usuário ao implementar uma interface IReducer.

O Redutor Definido pelo Usuário ou UDR pode ser usado para eliminar linhas desnecessárias durante a extração (importação) de dados. Ele também pode ser usado para manipular e avaliar linhas/colunas e, com base na lógica de programação, definir quais linhas precisam ser extraídas.

Para definir uma classe UDR, precisamos criar a interface `IReducer` com o atributo `SqlUserDefinedReducer` opcional

Essa interface de classe deve conter a definição para substituição do conjunto de linhas da interface `IEnumerable`

```c#
        [SqlUserDefinedReducer]
        public class EmptyUserReducer : IReducer
        {

            public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
            {
            …
            }

        }
```

O atributo **SqlUserDefinedReducer** indica que o tipo deve ser registrado como um redutor definido pelo usuário. Essa classe não pode ser herdada.

SqlUserDefinedReducer é um atributo opcional para a definição do Redutor Definido pelo Usuário. Ele é usado para definir a propriedade IsRecursive.

* bool     IsRecursive    
* **true**  = indica se esse Redutor é idempotente

Os principais objetos de programação são *input* e *output*. O objeto Input é usado para enumerar linhas de entrada e Output para definir linhas de saída como resultado da atividade de redução.

Para enumeração de linhas de entrada, usamos o método `Row.Get`

```c#
            foreach (IRow row in input.Rows)
            {
                        row.Get<string>("mycolumn");
            }
```

Alguns pontos importantes a serem considerados aqui. O parâmetro para o método `Row.Get` é uma coluna passada como parte da classe `PRODUCE` da instrução `REDUCE` do script base U-SQL. Precisamos usar o tipo de dados correto aqui também.

Para saída — método output.Set

É importante entender que o redutor personalizado só produzirá valores definidos com a chamada ao método output.Set.

```c#
    output.Set<string>("mycolumn", guid);
```

A saída real do redutor é disparada chamando `yield return output.AsReadOnly()`;

Veja a seguir um exemplo de redutor

```c#
        [SqlUserDefinedReducer]
        public class EmptyUserReducer : IReducer
        {

            public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
            {
                string guid;
                DateTime dt;
                string user;
                string des;

                foreach (IRow row in input.Rows)
                {
                    guid = row.Get<string>("guid");
                    dt = row.Get<DateTime>("dt");
                    user = row.Get<string>("user");
                    des = row.Get<string>("des");

                    if (user.Length > 0)
                    {
                        output.Set<string>("guid", guid);
                        output.Set<DateTime>("dt", dt);
                        output.Set<string>("user", user);
                        output.Set<string>("des", des);

                        yield return output.AsReadOnly();
                    }
                }
            }

        }
```

Nesse cenário de caso de uso, o redutor está ignorando linhas com o nome de usuário vazio. Para cada linha no conjunto de linhas, ele lê cada coluna necessária, em seguida, avalia o comprimento do nome de usuário e produz a linha real somente se o comprimento do valor de nome de usuário for maior que 0.

Script base U-SQL que usa um redutor personalizado

```sql
DECLARE @input_file string = @"\usql-programmability\input_file_reducer.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    REDUCE @rs0 PRESORT guid
    ON guid  
    PRODUCE guid string, dt DateTime, user String, des String
    USING new USQL_Programmability.EmptyUserReducer();

@rs2 =
    SELECT guid AS start_id,
           dt AS start_time,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS start_fiscalperiod,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 TO @output_file USING Outputters.Text();
```



<!--HONumber=Feb17_HO2-->


