<properties 
   pageTitle="Visão geral da Análise Microsoft Azure Data Lake | Azure" 
   description="A Análise Data Lake é um serviço de computação de Big Data do Azure que permite usar dados para impulsionar seus negócios com as informações obtidas de seus dados na nuvem, independentemente de onde eles estiverem e de seu tamanho. A Análise Data Lake possibilita isso da maneira mais simples, mais escalonável e mais econômica possível. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# Visão geral da Análise Microsoft Azure Data Lake

## O que é a Análise Azure Data Lake?

A Análise Azure Data Lake é um serviço novo, criado para facilitar a análise de Big Data. Isso permite que você se concentre em escrever, executar e gerenciar trabalhos, em vez de operar a infraestrutura distribuída. Em vez de implantar, configurar e ajustar o hardware, você escreve consultas para transformar seus dados e extrair informações importantes. O serviço de análise pode manipular trabalhos de qualquer escala de maneira instantânea, simplesmente configurando o controle para a quantidade de potência necessária. Você só paga pelo trabalho quando ele está em execução, o que o torna econômico. O serviço de análise dá suporte ao Active Directory do Azure, permitindo que você simplesmente gerencie o acesso e as funções de maneira integrada com seu sistema de identidade local. Ele também inclui a U-SQL, uma linguagem que unifica os benefícios do SQL com a capacidade expressiva dos códigos do usuário. O tempo de execução distribuído escalonável do U-SQL permite que você analise de forma eficiente dados no repositório e em SQL Servers no Azure, no Banco de Dados SQL do Azure e no SQL Data Warehouse do Azure.


## Principais recursos

- **Escala dinâmica**

    A Análise Data Lake foi projetada desde o início para proporcionar escala e desempenho na nuvem. Ela provisiona recursos de maneira dinâmica e permitir a análise de dados em terabytes ou até mesmo em exabytes. Ao final do trabalho, o serviço deixa de usar os recursos automaticamente, e você paga apenas pela potência de processamento usada. À medida que o tamanho dos dados armazenados ou da quantidade de computação usada aumenta ou diminui, você não precisa reescrever os códigos. Isso permite que você se concentre apenas na lógica dos seus negócios e não em como você processa e armazena grandes conjuntos de dados.

- **Use ferramentas com as quais você está familiarizado para desenvolver, depurar e otimizar de maneira mais rápida e inteligente**

    A Análise Data Lake apresenta uma ampla integração com o Visual Studio, para que você possa usar ferramentas com as quais já está familiarizado para executar, depurar e ajustar códigos. A visualização dos trabalhos do U-SQL permite ver como ocorre a execução de códigos em escala, para que você possa identificar afunilamentos de desempenho e otimizar custos.

- **U-SQL: simples e familiar, poderoso e extensível**

    A Análise Data Lake inclui o U-SQL, uma linguagem de consulta que estende a natureza declarativa simples e familiar do SQL com o poder expressivo do C#. A linguagem U-SQL é baseada no mesmo tempo de execução distribuído que alimenta os sistemas de Big Data na Microsoft. Milhões de desenvolvedores do SQL e do .NET agora podem processar e analisar todos os seus dados com as habilidades que eles já têm.

- **Integração total com seus investimentos de TI**

    A Análise Data Lake pode usar seus investimentos de TI existentes para identidade, gerenciamento, segurança e armazenamento de dados. Isso simplifica a governança de dados e facilita estender seus aplicativos de dados atuais. A Análise Data Lake é integrada ao Active Directory para o gerenciamento e permissões de usuário e é fornecida com o monitoramento e auditoria internos.

- **Financeiramente viável e econômico**

    A Análise Data Lake é uma solução econômica para executar cargas de trabalho de Big Data. Você paga por trabalho quando os dados são processados. Não é necessário nenhum hardware, licenças ou contratos de suporte específico do serviço. O sistema é automaticamente escalado vertical ou horizontalmente conforme a tarefa é iniciada e concluída, o que significa que você só paga pelo serviço de que precisa.

- **Funciona com todos os seus Dados do Azure**

    A Análise Data Lake pode funcionar com várias fontes de dados do Azure: armazenamento de Blob do Azure, banco de dados SQL do Azure, e, é claro, a Análise Data Lake é especialmente otimizada para funcionar com o Repositório Azure Data Lake - fornecendo o mais alto nível de desempenho, taxa de transferência e paralelização para suas cargas de trabalho de Big Data.

## Consulte também

- Introdução
    - [Introdução à Análise do Data Lake usando o Portal do Azure](data-lake-analytics-get-started-portal.md)
    - [Introdução à Análise Data Lake usando o Azure PowerShell](data-lake-analytics-get-started-powershell.md)
    - [Introdução à Análise Data Lake usando o SDK do .NET do Azure](data-lake-analytics-get-started-net-sdk.md)
    - [Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
    - [Introdução à linguagem U-SQL da Análise Azure Data Lake](data-lake-analytics-u-sql-get-started.md)
    
- U-SQL e desenvolvimento
    - [Introdução à linguagem U-SQL da Análise Azure Data Lake](data-lake-analytics-u-sql-get-started.md)
    - [Usar funções da janela do U-SQL para trabalhos da Análise do Azure Data Lake](data-lake-analytics-use-window-functions.md)
    - [Desenvolver operadores do U-SQL definidos pelo usuário para trabalhos da Análise Data Lake](data-lake-analytics-u-sql-develop-user-defined-operators.md)
    
- Gerenciamento
    - [Gerenciar a Análise do Azure Data Lake usando o Portal do Azure](data-lake-analytics-manage-use-portal.md)
    - [Gerenciar a Análise Azure Data Lake usando o Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
    - [Monitorar e solucionar problemas em trabalhos da Análise do Azure Data Lake usando o Portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
    - [Acessando os logs de diagnóstico do Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)

- Tutorial de ponta a ponta
    - [Usar tutoriais interativos da Análise Azure Data Lake](data-lake-analytics-use-interactive-tutorials.md)
    - [Analisar logs de site usando a Análise Azure Data Lake](data-lake-analytics-analyze-weblogs.md)

- Queremos saber sua opinião
    - [Comentar sobre nossa lista de pendências de documentação](data-lake-analytics-documentation-backlog.md)
    - [Enviar uma solicitação de recurso](http://aka.ms/adlafeedback)
    - [Obter ajuda nos fóruns](http://aka.ms/adlaforums)

<!---HONumber=AcomDC_0914_2016-->