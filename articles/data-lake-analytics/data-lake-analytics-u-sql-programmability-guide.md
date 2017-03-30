---
title: "Guia de programação do U-SQL para o Azure Data Lake | Microsoft Docs"
description: "Saiba mais sobre o conjunto de serviços do Azure Data Lake que permite criar uma plataforma de big data baseada em nuvem."
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
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 6efdd600386a603b38a5733977067f1a987cdac3
ms.lasthandoff: 03/17/2017


---

# <a name="u-sql-programmability-guide"></a>Guia de programação do U-SQL
## <a name="azure-data-lake"></a>Azure Data Lake
O Azure Data Lake inclui recursos que facilitam o armazenamento de dados de qualquer tamanho, formato e velocidade pelos desenvolvedores, cientistas de dados e analistas. Ele também permite aos desenvolvedores usar vários tipos de análise e processamento entre plataformas e idiomas. Ele remove as complexidades relacionadas a ingerir e armazenar todos os seus dados, ao mesmo tempo que acelera o uso de análise em lotes, streaming e interativa.

O Azure Data Lake é um conjunto de serviços que trabalham juntos para fornecer uma plataforma de big data baseada na nuvem. Esses serviços incluem:

- Azure HDInsight
- Repositório Azure Data Lake
- Análise Azure Data Lake

O U-SQL é uma linguagem de consulta projetada para o tipo big data de cargas de trabalho. Um dos recursos exclusivos do U-SQL é a combinação da linguagem declarativa semelhante ao SQL com a extensibilidade e a programação fornecidas por C#. Ele também permite acessar e manipular metadados do esquema, bem como criar componentes personalizados como processadores e redutores de dados.

Neste guia, nosso foco é a extensibilidade e a programação da linguagem U-SQL habilitadas por C#.

## <a name="requirements"></a>Requisitos
Para começar com o desenvolvimento do Azure Data Lake, você precisa baixar e instalar as [Ferramentas do Azure Data Lake para Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="get-started-with-u-sql"></a>Introdução ao U-SQL  
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

Neste exemplo, temos um **Arquivo de Entrada**: arquivo input_file.tsv, definido pela **Variável Local** @input_file.

As seguintes ações são executadas pelo script U-SQL neste exemplo:

* A instrução **EXTRACT** carrega dados na memória convertendo o Arquivo de Entrada em **Conjunto de Linhas de Memória**.
* **SELECT** opera no conjunto de linhas de dados para agregar dados e prepará-los para exportação.
* **OUTPUT** exporta o conjunto de linhas de dados para o **arquivo de saída**, que é um arquivo externo.

Primeiramente, vamos observar algumas opções para usar uma expressão C# diretamente em um script U-SQL.

## <a name="c-types-and-expressions-in-u-sql-script"></a>Tipos e expressões de C# no script U-SQL
Uma expressão C# do U-SQL, semelhante a uma expressão em C# geral, é uma sequência de um ou mais operadores que podem ser avaliados para um único valor, objeto, método ou namespace. As expressões podem ser compostas por um valor literal, uma invocação de método, um operador ou um nome simples. Os nomes simples podem ser o nome de uma variável, o membro de tipo, o parâmetro do método, o namespace ou o tipo.

Quando falamos sobre expressões C# do U-SQL, estamos nos referindo especificamente às expressões C# do script base U-SQL. A seção code-BEHIND do C# subjacente, abordada mais adiante neste documento, também pode conter expressões C# como elementos baseados em código C# regular.

As expressões podem usar operadores que usam outras expressões como parâmetros. Eles também podem usar chamadas de método com parâmetros que são outras chamadas de método. Abaixo temos exemplos de uma expressão:  

```c#
    Convert.ToDateTime(Convert.ToDateTime(dt).ToString("yyyy-MM-dd"))
```

A linguagem U-SQL permite o uso de expressões C# padrão de namespaces internos.  

```c#
    Microsoft.Analytics.Interfaces;  
    Microsoft.Analytics.Types.Sql;  
    System;  
    System.Collections.Generic;  
    System.Linq;  
    System.Text;  
```

As expressões C# gerais podem ser usadas em SELECT, EXTRACT do U-SQL.

As expressões C# também podem ser usadas nas instruções DECLARE ou IF. Um exemplo desse tipo de expressão é o seguinte:   

```c#
    DateTime.Today.Day   
    Convert.ToDateTime
```

Exemplo de script baseado em U-SQL:  

```sql
    DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");
```

As expressões C# podem fornecer funcionalidade estendida na manipulação de colunas como parte do conjunto de linhas. Por exemplo, se você quiser converter uma coluna de datetime em data com zero hora, poderá usar a seguinte parte SELECT do script baseado em U-SQL:

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

Como pode ver, usamos o método `System.Convert.ToDateTime` para executar a conversão.

Abaixo temos um cenário um pouco mais complicado que demonstra o uso de alguns operadores básicos em C#:

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

Isso mostra um exemplo de uma expressão do operador condicional C#.

Os exemplos anteriores demonstram o uso de expressões C# no script base U-SQL. No entanto, o U-SQL permite recursos de programação mais extensíveis que são abordados mais adiante neste documento.  

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

### <a name="use-c-expressions-for-data-type-conversions"></a>Usar expressões C# para conversões de tipo de dados
O exemplo a seguir demonstra como você pode fazer uma conversão de dados de data e hora usando expressões C#. Nesse cenário específico, os dados de datetime da cadeia de caracteres são convertidos em datetime padrão com a notação de hora 00:00:00 meia-noite.

```sql
DECLARE @dt String = "2016-07-06 10:23:15";
@rs1 =
    SELECT Convert.ToDateTime(Convert.ToDateTime(@dt).ToString("yyyy-MM-dd")) AS dt,
           dt AS olddt
    FROM @rs0;
OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

### <a name="use-c-expressions-for-todays-date"></a>Usar expressões C# para a data de hoje
Para efetuar pull da data de hoje, podemos usar a seguinte expressão C#:

```c#
DateTime.Now.ToString("M/d/yyyy")
```

Aqui está um exemplo de como usar essa expressão em um script:

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

## <a name="use-inline-c-function-expressions"></a>Usar expressões da função C# embutida
O U-SQL permite o uso da definição de expressões de função embutida como parte das expressões C#. Isso cria outras possibilidades para o uso de funções C# com parâmetros de referência de saída.

Abaixo temos um exemplo de uma definição de expressão de função embutida geral:

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

Nesse exemplo, definimos uma função embutida com o parâmetro de entrada de cadeia de caracteres input_p. Dentro dessa função, verificamos se a cadeia de caracteres de entrada é um valor válido de datetime. Em caso positivo, ele é retornado; caso contrário, retornará null.

A função embutida é necessária nesse cenário, pois a função DateTime.TryParse contém o parâmetro de saída `out dt_result`. Definimos como o `DateTime dt_result;`.


## <a name="verify-data-type-values"></a>Verificar valores de tipo de dados
Algumas funções C# não podem ser usadas diretamente no script base U-SQL como expressões C#. Especificamente, as funções que não podem ser usadas diretamente são aquelas que exigem um parâmetro de referência de saída. No entanto, essas funções podem ser definidas e usadas como parte de uma expressão de função embutida, conforme debatido acima.

### <a name="use-inline-function-expressions"></a>Usar expressões de função embutida
Para verificar se o valor de DateTime é válido, podemos usar `DateTime.TryParse`.

Aqui está um exemplo completo que mostra como usar uma expressão de função embutida para verificar o valor de tipo de dados com `DateTime.TryParse`.

Nesse cenário, verificamos o valor do tipo de dados DateTime:

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

### <a name="use-code-behind"></a>Usar code-behind
Para usar a mesma funcionalidade na seção code-BEHIND do programa U-SQL, definimos a função C# ToDateTime.

Aqui está a seção de script base U-SQL, em que fizemos as alterações necessárias:

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

Aqui está a função code-behind:

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

## <a name="use-code-behind"></a>Usar code-behind
Code-behind é uma seção da programação C# do projeto U-SQL. Conceitualmente, code-behind é um assembly compilado (DLL) que é referenciado no script U-SQL. As Ferramentas do Visual Studio permitem gerenciar e depurar uma seção da programação C# como parte de um projeto U-SQL.

Quando um projeto típico de U-SQL é criado no Visual Studio, há duas partes do projeto: o script base e um arquivo com a extensão **.usql**.

![Arquivo de script base](./media/data-lake-analytics-u-sql-programmability-guide/base-script-file.png)


Projeto comum de solução:   
![Projeto comum de solução](./media/data-lake-analytics-u-sql-programmability-guide/typical-solution-project.png)


Na segunda parte do projeto, chamamos um arquivo code-behind: Script.usql.cs.  
![Code-behind](./media/data-lake-analytics-u-sql-programmability-guide/code-behind.png)

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

O modelo de code-behind de visualização é gerado automaticamente. Esse arquivo contém uma definição de namespace padrão para objetos de programação. Durante a execução do projeto, ele é compilado e referenciado no script base U-SQL.

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

Os objetos de programação podem ser funções definidas pelo usuário, UDF, tipos definidos pelo usuário, UDT, PROCESSO ou REDUTOR, etc.

## <a name="register-u-sql-assemblies"></a>Registrar assemblies de U-SQL
O modelo de extensibilidade do U-SQL depende em grande parte da capacidade de adicionar código personalizado. Atualmente, o U-SQL fornece maneiras fáceis de adicionar seu próprio código baseado em Microsoft .NET (em especial, C#). No entanto, você também pode adicionar código personalizado escrito em outras linguagens .NET, como VB.NET ou F#.

Você ainda pode implantar seu próprio tempo de execução para outras linguagens, mais ainda precisa fornecer, por conta própria, a interoperabilidade por meio de uma camada .NET. Se você quiser dar suporte a uma linguagem específica, registre uma solicitação de recurso ou deixe um comentário em http://aka.ms/adlfeedback.

### <a name="learn-the-difference-between-code-behind-and-assembly-registration-through-azure-data-lake-tools-in-visual-studio"></a>Aprenda a diferenciar entre o Code-behind e o registro de assembly por meio das Ferramentas do Azure Data Lake no Visual Studio
A maneira mais fácil de usar código personalizado é por meio dos recursos code-behind das Ferramentas do Azure Data Lake para Visual Studio.

Como mencionado anteriormente, preencha o código personalizado para o script (por exemplo, Script.usql) em seu arquivo code-behind (por exemplo, Script.usql.cs).

![Exemplo code-behind](./media/data-lake-analytics-u-sql-programmability-guide/code-behind-example.jpg)
**Figura 1**: exemplo de code-behind nas Ferramentas Azure Data Lake no Visual Studio. (Clique na imagem para ampliá-la. Há [código de exemplo](https://github.com/Azure/usql/tree/master/Examples/TweetAnalysis) disponível.)


A vantagem do code-behind é que a ferramenta passa a ser responsável pelas seguintes etapas quando você envia seu script:  

1. Cria o assembly para o arquivo code-behind.  

2. Ele adiciona um prólogo ao script que usa a instrução [CREATE ASSEMBLY](https://msdn.microsoft.com/library/azure/mt763293.aspx) para registrar o arquivo de assembly. Ele também usa [REFERÊNCIA DE ASSEMBLY] (https://msdn.microsoft.com/library/azure/mt763294.aspx) para carregar o assembly no contexto do script.

3. Adiciona um epílogo ao script, que usa [DROP ASSEMBLY](https://msdn.microsoft.com/library/azure/mt763295.aspx) para remover outra vez o assembly temporariamente registrado.

Você pode ver o prólogo e o epílogo gerados quando abrir o script:

![Prólogo gerado](./media/data-lake-analytics-u-sql-programmability-guide/generated-prologue.png)

**Figura 2**: prólogo e epílogo gerados automaticamente para code-behind
<br />

Abaixo temos algumas das desvantagens do code-behind:

* O código é carregado para cada envio de script.
* A funcionalidade não pode ser compartilhada com outras pessoas.

Portanto, você pode adicionar uma Biblioteca de Classes C# separada (para U-SQL) para sua solução (veja a Figura 3), desenvolver o código ou copiar o código de code-behind existente (nenhuma alteração no código C# é necessária, conforme mostrado na Figura 4). Em seguida, use a opção de menu **Registrar Assembly** no projeto para registrar o assembly (conforme mostrado na Etapa 1 da Figura 5).

![Criar projeto](./media/data-lake-analytics-u-sql-programmability-guide/creating-project.png)
**Figura 3**: criando um projeto de código C# U-SQL  
<br />

![Biblioteca de classes](./media/data-lake-analytics-u-sql-programmability-guide/class-library.png)
**Figura 4**: a biblioteca de classes C# U-SQL ao lado do arquivo code-behind
<br />

![Registrar código](./media/data-lake-analytics-u-sql-programmability-guide/register-code.png)
**Figura 5**: como registrar o projeto de código C# U-SQL
<br />

A caixa de diálogo de registro (consulte a Etapa 2 da Figura 5) fornece opções sobre como registrar o assembly (por exemplo, qual conta do Data Lake Analytics usar, qual banco de dados usar). Ela também fornece informações sobre como nomear o assembly. (O caminho do assembly local é preenchido pela ferramenta.) Além disso, ela fornece uma opção para registrar novamente um assembly já registrado, além de duas opções para adicionar mais dependências:

**Dependências gerenciadas**: mostra os assemblies gerenciados necessários. Cada assembly selecionado é registrado individualmente e pode ser referenciado nos scripts. Use isso para outros assemblies .NET.

**Arquivos adicionais**: permite adicionar mais arquivos de recursos do que o necessário para o assembly. Eles são registrados com o assembly e carregados automaticamente quando o assembly é referenciado. Use isso para arquivos de configuração, assemblies nativos, tempos de execução de outra linguagem e seus recursos, etc.

Usamos essas duas opções nos exemplos a seguir. A [postagem recente no blog sobre processamento de imagem](https://blogs.msdn.microsoft.com/azuredatalake/2016/08/18/introducing-image-processing-in-u-sql/) é outro exemplo que mostra o uso de um assembly predefinido que pode usar essas opções para registro.

Agora você pode consultar os assemblies registrados em qualquer script U-SQL que tenha permissões para o banco de dados de assemblies registrados. (Para saber mais, confira o código no script U-SQL na Figura 4). É preciso adicionar uma referência para cada assembly registrado separadamente. Os arquivos de recursos adicionais são implantados automaticamente. Esse script não deve ter mais um arquivo code-behind para o código que está nos assemblies referenciados, mas o arquivo code-behind ainda pode fornecer outro código.

### <a name="register-assemblies-via-azure-data-lake-tools-in-visual-studio-and-in-u-sql-scripts"></a>Registrar assemblies por meio das Ferramentas do Azure Data Lake no Visual Studio e em scripts U-SQL
Embora as Ferramentas do Azure Data Lake no Visual Studio facilitem o registro de um assembly, você também pode fazer isso com um script (da mesma forma que as ferramentas o fazem para você) se estiver, por exemplo, desenvolvendo em uma plataforma diferente ou já compilou os assemblies que quer carregar e registrar. Siga estas etapas:

1. Carregue o DLL do assembly e também todas as DLLs obrigatórias que não são do sistema e arquivos de recursos no local escolhido. Você também pode carregá-lo para sua conta de armazenamento do Azure Data Lake ou até mesmo uma conta de armazenamento de blobs do Microsoft Azure que é vinculada à sua conta do Azure Data Lake. Você pode usar qualquer uma das ferramentas de carregamento disponíveis (por exemplo, comandos do Windows PowerShell, carregamento do Gerenciador do Data Lake para Ferramentas do Azure Data Lake do Visual Studio, o comando de carregamento favorito de seu SDK ou o portal do Azure).

1. Depois de carregar as DLLs, use as instruções [CREATE ASSEMBLY](https://msdn.microsoft.com/library/azure/mt763293.aspx) para registrá-las.

Usamos essa abordagem no exemplo espacial a seguir.

### <a name="register-assemblies-that-use-other-net-assemblies-based-on-the-json-and-xml-sample-library"></a>Registrar assemblies que usam outros assemblies .NET (com base na biblioteca de exemplos de JSON e XML)
Nosso [site do GitHub para U-SQL](https://github.com/Azure/usql/) oferece um conjunto de assemblies de exemplo compartilhados para você usar. Um dos assemblies, chamado [Microsoft.Analytics.Samples.Formats](https://github.com/Azure/usql/tree/master/Examples/DataFormats), fornece extratores, funções e outputters para tratamento de documentos JSON e XML. O assembly Microsoft.Analytics.Samples.Formats depende de dois assemblies existentes específicos de domínio para fazer o processamento de JSON e XML, respectivamente. Ele usa a biblioteca [Newtonsoft Json.Net](http://www.newtonsoft.com/) para processamento de documentos JSON e o assembly [System.Xml](https://msdn.microsoft.com/data/bb291078.aspx) para processamento de XML. Vamos usá-lo para mostrar como registrar e usar os assemblies em nossos scripts.

Primeiro, baixamos o [projeto do Visual Studio](https://github.com/Azure/usql/tree/master/Examples/DataFormats) para nosso ambiente de desenvolvimento local (por exemplo, fazendo uma cópia local com a ferramenta GitHub para Windows). Em seguida, abrimos a solução no Visual Studio e clicamos com o botão direito do mouse no projeto, conforme explicado anteriormente, para registrar o assembly.

Embora esse assembly tenha duas dependências, temos que incluir apenas a dependência Newtonsoft, uma vez que System.Xml já está disponível no Azure Data Lake (no entanto, ela tem que ser explicitamente referenciada). A Figura 6 mostra como nomeamos o assembly (observe que você também pode escolher um nome diferente sem pontos) e adicionamos o DLL Newtonsoft. Cada um dos assemblies é registrado individualmente no banco de dados especificado (por exemplo, JSONBlog).

![Registrar o assembly](./media/data-lake-analytics-u-sql-programmability-guide/register-assembly.png)

**Figura 6**: como registrar o assembly Microsoft.Analytics.Samples.Formats no Visual Studio
<br />

Se você ou outras pessoas (com quem você compartilhou os assemblies registrados fornecendo a elas acesso de leitura ao banco de dados) agora quiserem usar o recurso JSON em seus scripts, adicione as duas referências abaixo ao script:

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

### <a name="register-assemblies-that-use-native-c-assemblies-using-the-sql-server-2016-spatial-type-assembly-from-the-feature-pack"></a>Registrar assemblies que usam assemblies C++ nativos (usando o assembly tipo espacial do SQL Server 2016 do Feature Pack)
Agora vamos examinar um cenário ligeiramente diferente. Vamos supor que o assembly que queremos usar tem uma dependência no código que não é baseada em .NET. Mais especificamente, o assembly tem uma dependência em um assembly de C++ nativo. Um exemplo desse tipo de assembly é o assembly do tipo SQL Server [Microsoft.SqlServer.Types.dll](https://www.microsoft.com/download/details.aspx?id=52676). Ele fornece implementações baseadas em .NET dos tipos hierarchyID, geometry e geography do SQL Server para serem usadas por aplicativos do lado do cliente do SQL Server a fim de manipular tipos do SQL Server. (Ele também ERA originalmente o assembly que forneceu a implementação para tipos espaciais do SQL Server antes da versão 2016 do SQL Server.)

Vejamos como registrar esse assembly no U-SQL.

Primeiro, baixamos e instalamos o assembly do [Feature Pack do SQL Server 2016](https://www.microsoft.com/download/details.aspx?id=52676). Para ter certeza de que você tem a versão de 64 bits das bibliotecas, selecione a versão de 64 bits do instalador (ENU\x64\SQLSysClrTypes.msi).

O instalador instala o assembly gerenciado Microsoft.SqlServer.Types.dll em C:\Arquivos de Programas (x86)\Microsoft SQL Server\130\SDK\Assemblies e o assembly nativo SqlServerSpatial130.dll em \Windows\System32\\.. Agora, carregamos os assemblies em nosso Azure Data Lake Store (por exemplo, em uma pasta chamada /upload/asm/spatial).

Como o instalador instalou a biblioteca nativa na pasta do sistema c:\Windows\System32, temos que garantir a cópia de SqlServerSpatial130.dll fora dessa pasta antes de carregá-la ou que a ferramenta que usamos não execute o [redirecionamento de sistema de arquivos](https://msdn.microsoft.com/library/windows/desktop/aa384187(v=vs.85).aspx) das pastas do sistema.

Por exemplo, se você deseja carregá-lo com o Explorador de Arquivos do Azure Data Lake do Visual Studio, primeiro tem que copiar o arquivo em outro diretório. Caso contrário, no momento da redação deste artigo, você carregará a versão de 32 bits. A razão para isso é que o Visual Studio é um aplicativo de 32 bits, o que faz o Redirecionamento de Sistema de Arquivos em sua janela de seleção de arquivos de upload do Azure Data Lake. Em seguida, quando você executa um script U-SQL que chama o assembly nativo, obtém o seguinte erro (interno) em tempo de execução:

**Exceção interna da expressão do usuário: Foi feita uma tentativa de carregar um programa com um formato incorreto. (Exceção de HRESULT: 0x8007000B)**

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

Nesse caso, registramos apenas um assembly do U-SQL e incluímos o assembly nativo como uma dependência de cadeira de caracteres para o assembly do U-SQL. Para usar os assemblies espaciais, só precisamos fazer referência ao assembly U-SQL. O arquivo adicional é automaticamente disponibilizado para o assembly. Veja um exemplo de script simples que usa o assembly espacial:

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

A biblioteca Tipos SQL tem uma dependência no assembly System.XML; portanto, precisamos fazer referência a ele. Além disso, alguns dos métodos usam os tipos System.Data.SqlTypes em vez dos tipos C# internos. Como System.Data já está incluído por padrão, podemos fazer referência ao tipo de SQL necessário. O código anterior está disponível em nosso [site GitHub](https://github.com/Azure/usql/tree/master/Examples/SQLSpatialExample).


### <a name="use-assembly-versioning"></a>Usar o controle de versão do assembly
Atualmente, o U-SQL usa o .NET Framework versão 4.5. Portanto, faça com que seus próprios assemblies sejam compatíveis com essa versão do tempo de execução.

Conforme mencionado anteriormente, o U-SQL executa código em um formato de 64 bits (x64). Portanto, faça com que seu código seja compilado para execução em x64. Caso contrário, você obterá o erro de formato incorreto mostrado anteriormente.

Cada arquivo de recurso e DLL de assembly carregado, como um tempo de execução diferente, um assembly nativo ou um arquivo config, pode ter no máximo 400 MB. O tamanho total dos recursos implantados, seja por meio de DEPLOY RESOURCE ou por meio de referências a assemblies e a seus arquivos adicionais, não pode exceder 3 GB.

Por fim, observe que cada banco de dados U-SQL pode conter apenas uma versão de qualquer determinado assembly. Por exemplo, se você precisar da versão 7 e da versão 8 da biblioteca NewtonSoft Json.Net, será preciso registrá-las em dois bancos de dados diferentes. Além disso, cada script pode fazer referência apenas a uma versão da DLL de um determinado assembly. Nesse sentido, o U-SQL segue a semântica de controle de versão e gerenciamento do assembly de C#.


## <a name="use-user-defined-functions-udf"></a>Funções definidas pelo usuário: UDF
As funções definidas pelo usuário ou UDF do U-SQL são rotinas de programação que aceitam parâmetros, executam uma ação (mo um cálculo complexo)e retornam o resultado dessa ação como um valor. O valor de retorno da UDF pode ser apenas um único escalar. A UDF do U-SQL pode ser chamado no script base U-SQL como qualquer outra função escalar C#.

Recomendamos que você inicialize as funções do U-SQL definidas pelo usuário como **públicas** e **estáticas**.

```c#
        public static string MyFunction(string param1)
        {
            return "my result";
        }
```

Primeiro, vamos ver um exemplo simples de como criar uma UDF.

Nesse cenário de caso de uso, precisamos determinar o período fiscal, inclusive o trimestre fiscal e o mês fiscal do primeiro logon para o usuário específico. O primeiro mês fiscal do ano em nosso cenário é junho.

Para calcular o período fiscal, introduzimos a seguinte função C#:

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

Ela simplesmente calcula o mês e o trimestre fiscal e retorna um valor de cadeia de caracteres. No caso de junho, o primeiro mês do primeiro trimestre fiscal, usamos "Q1:P1". No caso de julho, usamos "Q1:P2" e assim por diante.

Essa é uma função C# regular que vamos usar em nosso projeto U-SQL.

Veja como é a seção code-behind nesse cenário:

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

Agora, vamos chamar essa função do script base U-SQL. Para fazer isso, precisamos fornecer um nome totalmente qualificado para a função, incluindo o namespace, que nesse caso é NameSpace.Class.Function(parameter).

```sql
    USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

Veja abaixo o script base U-SQL real:

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

Veja abaixo o arquivo de saída da execução do script:

```
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

Esse exemplo demonstra um uso simples de UDF embutida no U-SQL.

### <a name="keep-state-between-udf-invocations"></a>Manter o estado entre invocações da UDF
Os objetos de programação C# do U-SQL podem ser mais sofisticados ao utilizar interatividade por meio de variáveis globais de code-behind. Vejamos o cenário de caso de uso comercial a seguir.

Em grandes organizações, os usuários podem alternar entre diversos aplicativos internos. Eles podem incluir o Microsoft Dynamics CRM, o PowerBI e assim por diante. O cliente pode querer aplicar uma análise de telemetria de como os usuários alternam entre diferentes aplicativos, quais são as tendências de uso e assim por diante. O objetivo da empresa é otimizar o uso de aplicativos. Ela também pode querer combinar diferentes aplicativos ou rotinas específicas de logon.

Para atingir essa meta, precisamos determinar as IDs de uma sessão e o tempo de latência desde a última sessão ocorrida.

Precisamos encontrar um logon anterior e atribuí-lo a todas as sessões que estão sendo geradas para o mesmo aplicativo. Primeiro desafio: o script base U-SQL não nos permite aplicar cálculos sobre colunas já calculadas com a função LAG. Segundo desafio: temos que manter a sessão específica para todas as sessões no mesmo período.

Para resolver esse problema, use uma variável global dentro da seção code-behind: `static public string globalSession;`.

Essa variável global é aplicada a todo o conjunto de linhas durante a execução do nosso script.

Veja abaixo a seção code-behind do nosso programa U-SQL:

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

Este exemplo mostra a variável global `static public string globalSession;` usada dentro da função `getStampUserSession` sendo reinicializada sempre que o parâmetro Session é alterado.

O script base U-SQL é o seguinte:

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

A função `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` é chamada aqui durante o segundo cálculo do conjunto de linhas da memória. Ele passa a coluna `UserSessionTimestamp` e retorna o valor até que `UserSessionTimestamp` seja alterado.

O arquivo de saída é o seguinte:

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

Esse exemplo demonstra um cenário de caso de uso mais complicado em que usamos uma variável global dentro da seção code-behind aplicada a todo um conjunto de linhas da memória.

## <a name="use-user-defined-types-udt"></a>Tipos de CLR definidos pelo usuário: UDT
Os tipos definidos pelo usuário, ou UDT, são outro recurso de programação do U-SQL. O UDT do U-SQL atua como um tipo definido pelo usuário regular de C#. C# é uma linguagem fortemente tipada que permite o uso de tipos definidos pelo usuário personalizados e internos.

O U-SQL não pode implicitamente serializar ou desserializar UDTs arbitrários quando o UDT é passado entre os vértices em conjuntos de linhas. Isso significa que o usuário precisa fornecer um formatador explícito usando a interface IFormatter. Isso fornece ao U-SQL métodos de serialização e desserialização para o UDT.

> [!NOTE]
> Extratores internos e outputters de U-SQL atualmente não podem serializar ou desserializar dados de UDT de ou para arquivos, mesmo com o conjunto do IFormatter. Assim, quando você está gravando dados UDT em um arquivo com a instrução OUTPUT ou lendo com um extrator, precisa passá-lo como uma cadeia de caracteres ou matriz de bytes. Em seguida, deve chamar o código de serialização e desserialização (ou seja, o método ToString () do UDT) explicitamente. Por outro lado, extratores definidos pelo usuário e outputters podem ler e gravar UDTs.

Se tentarmos usam o UDT em EXTRATOR ou OUTPUTTER (fora do SELECT anterior), conforme mostrado aqui:

```sql
@rs1 =
    SELECT MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

Recebemos o seguinte erro:

```
    Error    1    E_CSC_USER_INVALIDTYPEINOUTPUTTER: Outputters.Text was used to output column myfield of type
    MyNameSpace.Myfunction_Returning_UDT.

    Description:

    Outputters.Text only supports built-in types.

    Resolution:

    Implement a custom outputter that knows how to serialize this type, or call a serialization method on the type in
    the preceding SELECT.    C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\
    USQL-Programmability\Types.usql    52    1    USQL-Programmability
```

Para trabalhar com UDT em outptutter, também temos que serializá-lo para cadeia de caracteres com o método ToString() ou criar um outputter personalizado.

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

* Adicione os seguintes namespaces:

```c#
    using Microsoft.Analytics.Interfaces
    using System.IO;
```

* Adicione `Microsoft.Analytics.Interfaces`, que é necessário para as interfaces UDT. Além disso, `System.IO` pode ser necessário para definir a interface IFormatter.

* Defina o tipo definido usado com o atributo SqlUserDefinedType.

**SqlUserDefinedType** é usado para marcar uma definição de tipo em um assembly como um UDT (tipo definido pelo usuário) no U-SQL. As propriedades no atributo refletem as características físicas do UDT. Essa classe não pode ser herdada.

SqlUserDefinedType é um atributo necessário para a definição de UDT.

O construtor da classe:  

* SqlUserDefinedTypeAttribute(type formatter)

* Digite o parâmetro formatter: Required para definir um formatador UDT, mais especificamente, o tipo da interface `IFormatter` deve ser passado aqui.

```c#
    [SqlUserDefinedType(typeof(MyTypeFormatter))]
      public class MyType
              {
             …
           }
```

* O UDT típico também requer a definição da interface IFormatter, conforme mostrado no exemplo abaixo:

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

A interface `IFormatter` serializa e desserializa um gráfico de objeto com o tipo raiz de \<typeparamref name="T">.

\<typeparam name="T">O tipo raiz para serialização e desserialização do gráfico de objeto.

* **Desserializar**: desserializa os dados no fluxo fornecido e reconstitui o gráfico de objetos.

* **Serializar**: serializa um objeto ou gráfico de objetos com determinada raiz para o fluxo fornecido.

Instância `MyType`: a instância do tipo.  
Gravador `IColumnWriter`/leitor `IColumnReader`: o fluxo da coluna subjacente.  
Contexto `ISerializationContext`: enumeração que define um conjunto de sinalizadores que especifica o contexto de origem ou destino para o fluxo durante a serialização.

   * **Intermediário**: especifica que o contexto de origem ou de destino não é um armazenamento persistente.

   * **Persistência**: especifica que o contexto de origem ou de destino é um armazenamento persistente.

Como um tipo C# regular, uma definição de UDT do U-SQL pode incluir substituições para operadores como +/==/!=. Ele também pode incluir métodos estáticos. Por exemplo, se formos usar esse UDT como um parâmetro para uma função agregada MIN do U-SQL, teremos que definir a substituição do operador <.

Anteriormente neste guia, demonstramos um exemplo da identificação do período fiscal na data específica no formato Qn:Pn (Q1:P10). O exemplo a seguir mostra como definir um tipo personalizado para os valores do período fiscal.

Abaixo temos um exemplo de uma seção code-behind com interface IFormatter e UDT personalizado:

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

O tipo definido inclui dois números: trimestre e mês. Os operadores ==/!=/>/< e o método estático ToString() são definidos aqui.

Como mencionado anteriormente, o UDT pode ser usado nas expressões SELECT, mas não pode ser usado em OUTPUTTER/EXTRACTOR sem serialização personalizada. Ele precisa ser serializado como uma cadeia de caracteres com ToString() ou usado com um OUTPUTTER/EXTRACTOR personalizado.

Agora, vamos falar sobre o uso do UDT. Na seção code-behind, alteramos a nossa função GetFiscalPeriod para o seguinte:

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

Aqui, fornecemos um exemplo de como usá-lo ainda mais no script base U-SQL. Este exemplo demonstra as diferentes formas de invocação do UDT no script U-SQL.

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

Veja um exemplo de uma seção completa code-behind:

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

## <a name="use-user-defined-aggregates-udagg"></a>Use agregações definidas pelo usuário: UDAGG
As agregações definidas pelo usuário são funções relacionadas à agregação que não são enviadas para uso imediato com o U-SQL. O exemplo pode ser uma agregação para executar um cálculo matemático personalizado, concatenações de cadeia de caracteres ou manipulações com cadeias de caracteres, etc.

A definição de classe base da agregação definida pelo usuário é a seguinte:

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

O atributo SqlUserDefinedType é **opcional** para a definição de UDAGG.


A classe base permite passar três parâmetros abstratos: dois como parâmetros de entrada e um como resultado. Os tipos de dados são variáveis e devem ser definidos durante a herança da classe.

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

* **Init** é invocado uma vez para cada grupo durante o cálculo. Ele fornece uma rotina de inicialização para cada grupo de agregação.  
* **Accumulate** é executado uma vez para cada valor. Ele fornece a funcionalidade principal para o algoritmo de agregação. Ele pode ser usado para agregar valores com vários tipos de dados que são definidos durante a herança da classe. Ele pode aceitar dois parâmetros de tipos de dados variáveis.
* **Terminate** é executado uma vez por grupo de agregação no fim do processamento para gerar o resultado para cada grupo.

Para declarar tipos de dados de entrada e saída corretos, use a seguinte definição de classe:

```c#
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: primeiro parâmetro para accumulate
* T2: primeiro parâmetro para accumulate
* TResult: tipo de retorno de terminate

Por exemplo:

```c#
    public class GuidAggregate : IAggregate<string, int, int>
```

ou o

```c#
    public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>Usar UDAGG no U-SQL
Para usar a UDAGG, primeiramente defina-a no code-behind ou faça referência a ela na DLL de programação existente, conforme discutido anteriormente.

Em seguida, use a seguinte sintaxe:

```c#
    AGG<UDAGG_functionname>(param1,param2)
```

Veja um exemplo de UDAGG:

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

E o script base U-SQL:

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

## <a name="use-user-defined-objects-udo"></a>Usar objetos definidos pelo usuário: UDO
O U-SQL permite definir objetos de programação personalizados que são chamados de objetos definidos pelo usuário ou UDO.

Veja a seguinte lista de UDO no U-SQL:

* Extratores definidos pelo usuário
    * Extrair linha por linha
    * Usado para implementar extração de dados de arquivos estruturados personalizados

* Outputters definidos pelo usuário
    * Gerar linha por linha
    * Usado para gerar tipos de dados personalizados ou formatos de arquivo personalizado

* Processadores definidos pelo usuário
    * Usar uma linha e gerar uma linha
    * Usado para reduzir o número de colunas ou produzir novas colunas com valores derivados de um conjunto de colunas existente

* Aplicadores definidos pelo usuário
    * Usar uma linha e gerar 0 para n linhas
    * Usado com OUTER/CROSS APPLY

* Combinadores definidos pelo usuário
    * Combina conjuntos de linhas — JOINs definidos pelo usuário

* Redutores definidos pelo usuário
    * Usar n linhas e gerar uma linha
    * Usado para reduzir o número de linhas

Geralmente, o UDO é chamado explicitamente no script U-SQL como parte das seguintes instruções U-SQL:

* EXTRACT
* OUTPUT
* PROCESS
* COMBINE
* REDUCE

## <a name="use-user-defined-extractors"></a>Usar extratores definidos pelo usuário
O U-SQL permite importar dados externos usando uma instrução EXTRACT. Uma instrução EXTRACT pode usar extratores de UDO internos:  

* *Extractors.Text()*: fornece extração dos arquivos de texto delimitados de diferentes codificações.

* *Extractors.Csv()*: fornece extração de arquivos CSV (valores separados por vírgula) de diferentes codificações.

* *Extractors.Tsv()*: fornece extração de arquivos TSV (valores separados por tabulação) de diferentes codificações.

Pode ser útil desenvolver um extrator personalizado. Isso pode ser útil durante a importação de dados para executar uma das seguintes tarefas:

* Modificar dados de entrada ao dividir colunas e modificar os valores individuais. A funcionalidade PROCESSOR é melhor para combinar colunas.
* Analisar dados não estruturados, como páginas da Web e emails, ou semiestruturados, como XML/JSON.
* Analisar dados na codificação sem suporte.

Para definir o extrator definido pelo usuário, ou UDE, precisamos criar uma interface `IExtractor`. Todos os parâmetros de entrada para o extrator, como delimitadores de linha/coluna e codificação precisam ser definidos no construtor da classe. A interface `IExtractor` também deve conter uma definição para a substituição `IEnumerable<IRow>` da seguinte forma:

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

* **true** = indica que esse extrator exige arquivos de entrada atômicos (JSON, XML, ...)
* **false** = indica que esse extrator pode lidar com arquivos divididos/distribuídos (CSV, SEQ, ...)

Os principais objetos de programação do UDE são **input** e **output**. O objeto de entrada é usado para enumerar os dados de entrada como `IUnstructuredReader`. O objeto de saída é usado para definir os dados de saída como resultado da atividade extractor.

Os dados de entrada são acessados por meio de `System.IO.Stream` e `System.IO.StreamReader`.

Para enumeração de colunas de entrada, primeiramente dividimos o fluxo de entrada usando um delimitador de linha.

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

Para definir os dados de saída, usamos o método `output.Set`.

É importante entender que o extrator personalizado só gera colunas e valores que são definidos com a saída. Defina a chamada do método.

```c#
    output.Set<string>(count, part);
```

A saída real do extrator é disparada chamando `yield return output.AsReadOnly();`.

Vejamos o exemplo do extrator:

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

Nesse cenário de caso de uso, o extrator regenera o GUID para a coluna "guid" e converte os valores da coluna "usuário" em letras maiúsculas. Os extratores personalizados podem produzir resultados mais complicados ao analisar dados de entrada e manipulá-los.

Script base U-SQL que usa um extrator personalizado:

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

## <a name="use-user-defined-outputters"></a>Usar outputters definidos pelo usuário
O outputter definido pelo usuário é outro UDO do U-SQL que permite estender uma funcionalidade interna do U-SQL. Assim como o extrator, há vários outputters internos.

* *Outputters.Text()*: grava dados em arquivos de texto delimitados de codificações diferentes.
* *Outputters.Csv()*: grava dados em arquivos CSV (valores separados por vírgula) de codificações diferentes.
* *Outputters.Tsv()*: grava dados em arquivos TSV (valores separados por tabulação) de codificações diferentes.

O outputter personalizado permite gravar dados em um formato definido personalizado. Isso pode ser útil para as seguintes tarefas:

* Gravando dados em arquivos semiestruturados ou não estruturados.
* Gravar dados em codificações sem suporte.
* Modificar dados de saída ou adicionar atributos personalizados.

Para definir o outputter definido pelo usuário, precisamos criar a interface `IOutputter`.

A seguir temos a implementação da classe `IOutputter` base:

```c#
    public abstract class IOutputter : IUserDefinedOperator
    {
        protected IOutputter();

        public virtual void Close();
        public abstract void Output(IRow input, IUnstructuredWriter output);
    }
```

Todos os parâmetros de entrada para o outputter, como delimitadores de linha/coluna, codificação e afins precisam ser definidos no construtor da classe. A interface `IOutputter` também deve conter uma definição para a substituição `void Output`. O atributo `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` pode ser definido opcionalmente para o processamento de arquivos atômico. Para saber mais, consulte os detalhes a seguir.

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

* `Output` é chamado para cada linha de entrada. Retorna o conjunto de linhas `IUnstructuredWriter output`.
* A classe Constructor é usada para passar parâmetros ao outputter definido pelo usuário.
* `Close` é usado para, opcionalmente, substituir e liberar estado caro ou determinar quando a última linha foi gravada.

O atributo **SqlUserDefinedOutputter** indica que o tipo deve ser registrado como um outputter definido pelo usuário. Essa classe não pode ser herdada.

SqlUserDefinedOutputter é um atributo opcional para a definição de um outputter definido pelo usuário. Ele é usado para definir a propriedade AtomicFileProcessing.

* bool     AtomicFileProcessing   

* **true** = indica que esse outputter exige arquivos de saída atômicos (JSON, XML, ...)
* **false** = indica que esse outputter pode lidar com arquivos divididos/distribuídos (CSV, SEQ, ...)

Os principais objetos de programação são **row** e **output**. O objeto **row** é usado para enumerar os dados de saída como interface `IRow`. **Saída** é usado para definir os dados de saída para o arquivo de destino.

Os dados de saída são acessados por meio da interface `IRow`. Os dados de saída são passados em uma linha por vez.

Os valores individuais são enumerados chamando o método Get da interface IRow:

```c#
    row.Get<string>("column_name")
```

Os nomes de coluna individuais podem ser determinados chamando `row.Schema`:

```c#
    ISchema schema = row.Schema;
    var col = schema[i];
    string val = row.Get<string>(col.Name)
```

Essa abordagem permite que criar um outputter flexível para qualquer esquema de metadados.

Os dados de saída são gravados em um arquivo usando `System.IO.StreamWriter`. O parâmetro de fluxo é definido como `output.BaseStrea` como parte do `IUnstructuredWriter output`.

Observe que é importante liberar o buffer de dados para o arquivo depois de cada iteração de linha. Além disso, o objeto `StreamWriter` deve ser usado com o atributo Disposable habilitado (padrão) e a palavra-chave **using**:

```c#
    using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
            {
        …
            }
```

Caso contrário, chame o método Flush() explicitamente depois de cada iteração. Vamos mostrar isso no exemplo a seguir.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>Definir cabeçalhos e rodapés para outputter definido pelo usuário
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

Para o rodapé, use a referência à instância do objeto `System.IO.Stream` (`output.BaseStream`). Escreva o rodapé no método Close () da interface `IOutputter`.  (Para saber mais, veja a seção a seguir.)

Veja um exemplo de um outputter definido pelo usuário:

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

            // The Close method is used to write the footer to the file. It's executed only once, after all rows
            public override void Close().
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

                // This is a data-independent header--HTML table definition
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

                // Header row output--runs only once
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
                        // Data type enumeration--required to match the distinct list of types from OUTPUT statement
                        switch (col.Type.Name.ToString().ToLower())
                        {
                            case "string": val = row.Get<string>(col.Name).ToString(); break;
                            case "guid": val = row.Get<Guid>(col.Name).ToString(); break;
                            default: break;
                        }
                    }
                    // Handling NULL values--keeping them empty
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

E o script base U-SQL:

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

Esse é um outputter HTML, que cria um arquivo HTML com dados da tabela.

### <a name="call-outputter-from-u-sql-base-script"></a>Chamando outputter do script base U-SQL
Para chamar um outputter personalizado do script base U-SQL, a nova instância do objeto outputter deve ser criada.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Para evitar a criação de uma instância do objeto no script base, podemos criar um wrapper de função, conforme mostrado em nosso exemplo anterior:

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

Nesse caso, a chamada original se parece com esta:

```sql
OUTPUT @rs0 TO @output_file USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="use-user-defined-processors"></a>Usar processadores definidos pelo usuário
O processador definido pelo usuário, ou UDP, é um tipo de UDO do U-SQL que permite processar as linhas de entrada aplicando recursos de programação. O UDP permite combinar colunas, modificar valores e adicionar novas colunas, se necessário. Basicamente, ele ajuda a processar um conjunto de linhas para produzir elementos de dados necessários.

Para definir um UDP, precisamos criar uma interface `IProcessor` com o atributo `SqlUserDefinedProcessor`, que é opcional para o UDP.

Essa interface deve conter a definição para a substituição do conjunto de linhas de interface `IRow`, conforme mostrado no exemplo abaixo:

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

O atributo SqlUserDefinedProcessor é **opcional** para a definição de UDP.

Os principais objetos de programação são **input** e **output**. O objeto Input é usado para enumerar dados de entrada e saída e para definir dados de saída como resultado da atividade do processador.

Para enumeração de colunas de entrada, usamos o método `input.Get`.

```c#
    string column_name = input.Get<string>("column_name");
```

O parâmetro para o método `input.Get` é uma coluna passada como parte da cláusula `PRODUCE` da instrução `PROCESS` do script base U-SQL. Precisamos usar o tipo de dados correto aqui.

Para saída, use o método `output.Set`.

É importante observar que o produtor personalizado gera apenas colunas e valores definidos com a chamada de método `output.Set`.

```c#
    output.Set<string>("mycolumn", mycolumn);
```

A saída real do processador é disparada chamando `return output.AsReadOnly();`.

Veja abaixo um exemplo de processador:

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

Nesse cenário de caso de uso, o processador está gerando a nova coluna chamada "full_description" combinando as colunas existentes, nesse caso, "user" em letras maiúsculas e "des". Ele também regenera um GUID e retorna o original, bem como novos valores de GUID.

Como você pode ver no exemplo anterior, é possível chamar métodos C# durante a chamada de método `output.Set`.

Abaixo temos um exemplo de script base U-SQL que usa um processador personalizado:

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

## <a name="use-user-defined-appliers"></a>Usar aplicadores definidos pelo usuário
O aplicador definido pelo usuário do U-SQL permite invocar uma função C# personalizada para cada linha retornada pela expressão de tabela externa de uma consulta. A entrada à direita é avaliada para cada linha a começar da entrada à esquerda e as linhas que são produzidas são combinadas para a saída final. A lista de colunas produzida pelo operador APPLY é a combinação do conjunto de colunas na entrada à esquerda e à direita.

O aplicador definido pelo usuário está sendo invocado como parte da expressão SELECT do U-SQL.

A chamada característica ao aplicador definidor pelo usuário se parece com esta:

```sql
    SELECT …
    FROM …
    CROSS APPLYis used to pass parameters
    new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Para saber mais sobre como usar aplicadores na expressão SELECT., veja [U-SQL SELECT selecionando de CROSS APPLY e OUTER APPLY](https://msdn.microsoft.com/library/azure/mt621307.aspx).

A definição de classe base do aplicador definido pelo usuário é a seguinte:

```c#
    public abstract class IApplier : IUserDefinedOperator
    {
        protected IApplier();

        public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
    }
```

Para definir um aplicador definido pelo usuário, precisamos criar a interface `IApplier` com o atributo [`SqlUserDefinedCombiner`], que é opcional para a definição de aplicador definido pelo usuário.

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

* Apply é chamado para cada linha da tabela outer. Ele retorna o conjunto de linhas de saída `IUpdatableRow`.
* A classe Constructor é usada para passar parâmetros ao aplicador definido pelo usuário.

**SqlUserDefinedApplier** indica que o tipo deve ser registrado como um aplicador definido pelo usuário. Essa classe não pode ser herdada.

O atributo **SqlUserDefinedApplier** é **opcional** para a definição do aplicador definido pelo usuário.


Os principais objetos de programação são os seguintes:

```c#
        public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

Os conjuntos de linhas de entrada são passados como entrada `IRow`. As linhas de saída são geradas como a interface de saída `IUpdatableRow`.

Os nomes de coluna individuais podem ser determinados pela chamada do método de esquema `IRow`.

```c#
    ISchema schema = row.Schema;
    var col = schema[i];
    string val = row.Get<string>(col.Name)
```

Para obter os valores de dados reais do `IRow` de entrada, usamos o método Get() da interface `IRow`.

```c#
    mycolumn = row.Get<int>("mycolumn")
```

Ou, então, usamos o nome de coluna de esquema:

```c#
    row.Get<int>(row.Schema[0].Name)
```

Os valores de saída devem ser definidos com a saída `IUpdatableRow`:

```c#
    output.Set<int>("mycolumn", mycolumn)
```

É importante entender que o aplicador personalizado só produz colunas e valores definidos com a chamada ao método `output.Set`.

A saída real é disparada chamando `yield return output.AsReadOnly();`.

Os parâmetros de aplicador definido pelo usuário podem ser passados para o construtor. O aplicador pode retornar um número variável de colunas que precisa ser definido durante a chamada ao aplicador no script base U-SQL.

```c#
  new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

Veja abaixo o exemplo do aplicador definido pelo usuário:

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

Abaixo está o script base U-SQL para esse aplicador definido pelo usuário:

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

Nesse cenário de caso de uso, o aplicador definido pelo usuário atua como um analisador de valor delimitado por vírgula para as propriedades de frota de carros. As linhas do arquivo de entrada têm a seguinte aparência:

```
103    Z1AB2CD123XY45889    Ford,Explorer,2005,SUV,152345
303    Y0AB2CD34XY458890    Shevrolet,Cruise,2010,4Dr,32455
210    X5AB2CD45XY458893    Nissan,Altima,2011,4Dr,74000
```

Trata-se de um típico arquivo TSV delimitado por tab com a coluna de propriedades contendo propriedades do carro, como fabricante e modelo. Essas propriedades precisam ser analisadas pelas colunas da tabela. O aplicador fornecido também permite gerar um número dinâmico de propriedades no conjunto de linhas de resultados, com base no parâmetro que é passado. Você pode gerar todas as propriedades ou somente um conjunto específico de propriedades.

    …USQL_Programmability.ParserApplier ("all")
    …USQL_Programmability.ParserApplier ("make")
    …USQL_Programmability.ParserApplier ("make&model")

O aplicador definido pelo usuário pode ser chamado como uma nova instância do objeto do aplicador:

```c#
    CROSS APPLY new MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

Ou com a invocação de um método de fábrica wrapper:

```c#
    CROSS APPLY MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

## <a name="use-user-defined-combiners"></a>Usar combinadores definidos pelo usuário
O combinador definido pelo usuário, ou UDC, permite combinar linhas dos conjuntos de linhas esquerdo e direito, com base em lógica personalizada. O Combinador Definido pelo Usuário é usado com a expressão COMBINE.

Um combinador está sendo invocado com a expressão COMBINE, que fornece as informações necessárias sobre os conjuntos de linhas de entrada, as colunas de agrupamento, o esquema de resultado esperado e as informações adicionais.

Para chamar um combinador em um script base U-SQL, usamos a seguinte sintaxe:

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

Para definir um combinador definido pelo usuário, precisamos criar a interface `ICombiner` com o atributo [`SqlUserDefinedCombiner`], que é opcional para a definição do combinador definido pelo usuário.

Definição da classe `ICombiner` base:

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

A implementação personalizada de uma interface `ICombiner` deve conter a definição para uma substituição Combine `IEnumerable<IRow>`.

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

**SqlUserDefinedCombiner** é usado para definir a propriedade do modo Combinador. É um atributo opcional para a definição do combinador definido pelo usuário.

Modo CombinerMode

A enumeração CombinerMode pode usar os seguintes valores:

* Completo   (0)    Cada linha de saída depende potencialmente de todas as linhas de entrada da esquerda e direita com o mesmo valor de chave.

* Esquerda  (1)    Cada linha de saída depende de uma única linha de entrada da esquerda (e potencialmente de todas as linhas da direita com o mesmo valor de chave).

* Direita (2) Cada linha de saída depende de uma única linha de entrada da direita (e potencialmente de todas as linhas da esquerda com o mesmo valor de chave).

* Interno (3)    Cada linha de saída depende de uma única linha de entrada da esquerda e direita com o mesmo valor.

Exemplo:    [`SqlUserDefinedCombiner(Mode=CombinerMode.Left)`]


Os principais objetos de programação são:

```c#
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

Os conjuntos de linhas de entrada são passados como tipo `IRowset` **esquerdo** e **direito** da interface. Ambos os conjuntos de linhas precisam ser enumerados para processamento. Você só pode enumerar cada interface uma vez e, portanto, temos que enumerá-las e armazená-las se necessário.

Para fins de caching, podemos criar um tipo List\<T\> de estrutura de memória como resultado de uma execução da consulta LINQ, especificamente List<`IRow`>. O tipo de dados anônimo também pode ser usado durante a enumeração.

Veja [Introdução a consultas LINQ (C#)](https://msdn.microsoft.com/library/bb397906.aspx) para obter mais informações sobre consultas LINQ e [IEnumerable\<T\> Interface](https://msdn.microsoft.com/library/9eekhta0(v=vs.110).aspx) para obter mais informações sobre a interface IEnumerable\<T\>.

Para obter os valores de dados reais do `IRowset` de entrada, usamos o método Get() da interface `IRow`.

```c#
    mycolumn = row.Get<int>("mycolumn")
```

Os nomes de coluna individuais podem ser determinados pela chamada do método de esquema `IRow`.

```c#
    ISchema schema = row.Schema;
    var col = schema[i];
    string val = row.Get<string>(col.Name)
```

Ou usando o nome de coluna de esquema:

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

Depois de enumerar os dois conjuntos de linhas, vamos executar um loop por todas as linhas. Para cada linha no conjunto de linhas à esquerda, vamos localizar todas as linhas que satisfazem a condição de nosso combinador.

Os valores de saída devem ser definidos com a saída `IUpdatableRow`.

```c#
    output.Set<int>("mycolumn", mycolumn)
```

A saída real é disparada chamando `yield return output.AsReadOnly();`.

Veja abaixo um exemplo do combinador:

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

Nesse cenário de caso de uso, estamos criando um relatório de análise para o varejista. O objetivo é encontrar todos os produtos que custam mais de US$ 20,000 e são vendidos por meio de sites mais rapidamente do que pelo varejista comum durante determinado período.

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

O combinador definido pelo usuário pode ser chamado como uma nova instância do objeto do aplicador:

```c#
    USING new MyNameSpace.MyCombiner();
```


Ou com a invocação de um método de fábrica wrapper:

```c#
    USING MyNameSpace.MyCombiner();
```

## <a name="use-user-defined-reducers"></a>Usar redutores definidos pelo usuário
O U-SQL permite escrever redutores de conjunto de linhas personalizados em C# usando a estrutura de extensibilidade do operador definido pelo usuário e implementando uma interface IReducer.

O redutor definido pelo usuário, ou UDR, pode ser usado para eliminar linhas desnecessárias durante a extração (importação) de dados. Ele também pode ser usado para manipular e avaliar as linhas e colunas. Com base na lógica de programação, ele também pode definir quais linhas precisam ser extraídas.

Para definir uma classe UDR, precisamos criar uma interface `IReducer` com um atributo `SqlUserDefinedReducer` opcional.

Essa interface de classe deve conter uma definição para substituição do conjunto de linhas da interface `IEnumerable`.

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
**SqlUserDefinedReducer** é um atributo opcional para a definição do redutor definido pelo usuário. Ele é usado para definir a propriedade IsRecursive.

* bool     IsRecursive    
* **true**  = indica se esse Redutor é idempotente

Os principais objetos de programação são **input** e **output**. O objeto de entrada é usado para enumerar linhas de entrada. Output é usado para definir as linhas de saída como resultado da redução da atividade.

Para enumeração de linhas de entrada, usamos o método `Row.Get`.

```c#
            foreach (IRow row in input.Rows)
            {
                        row.Get<string>("mycolumn");
            }
```

O parâmetro para o método `Row.Get` é uma coluna passada como parte da classe `PRODUCE` da instrução `REDUCE` do script base U-SQL. Precisamos usar o tipo de dados correto aqui também.

Para saída, use o método `output.Set`.

É importante entender que redutor personalizado apenas produz valores que são definidos com a chamada de método `output.Set`.

```c#
    output.Set<string>("mycolumn", guid);
```

A saída real do redutor é disparada chamando `yield return output.AsReadOnly();`.

Abaixo temos um exemplo de redutor:

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

Nesse cenário de caso de uso, o redutor está ignorando linhas com um nome de usuário vazio. Para cada linha no conjunto de linhas, ele lê cada coluna necessária e avalia o comprimento do nome do usuário. Ele só produz a linha real se o comprimento do valor de nome de usuário for maior que 0.

Abaixo temos um script base U-SQL que usa um redutor personalizado:

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

