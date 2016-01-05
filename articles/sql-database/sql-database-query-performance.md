<properties 
   pageTitle="Visão do desempenho de consulta de Banco de Dados SQL do Azure" 
   description="O monitoramento do desempenho de consulta identifica as consultas que consumem mais CPU de um Banco de Dados SQL do Azure." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="12/02/2015"
   ms.author="sstein"/>

# Visão do desempenho de consulta de Banco de Dados SQL do Azure


Gerenciamento e ajuste do desempenho de bancos de dados relacionais são uma tarefa desafiadora que requer conhecimento significativo e investimento de tempo. Visão do Desempenho de Consulta permite que você gaste menos tempo solucionando problemas de desempenho de banco de dados, fornecendo o seguinte:

- Mais informações sobre o consumo de recursos de bancos de dados (DTU). 
- As consultas que mais consomem CPU, que potencialmente podem ser ajustadas para melhorar o desempenho. 
- A capacidade de analisar os detalhes de uma consulta.

## Pré-requisitos

- A Visão do Desempenho de Consulta está disponível somente com o Banco de Dados SQL do Azure V12.
- O Query Performance Insight exige a execução do [Repositório de Consultas](https://msdn.microsoft.com/library/dn817826.aspx) em seu banco de dados. O portal solicitará a ativação do Repositório de consultas, caso ainda não esteja em execução.

 
## Permissões

As seguintes permissões de [controle de acesso baseado em função](role-based-access-control-configure.md) são necessárias para usar a Visão do Desempenho de Consulta:

- As permissões **Leitor**, **Proprietário**, **Colaborador**, **Colaborador do banco de dados SQL** ou **Colaborador do SQL Server** são necessárias para exibir as consultas e gráficos que consomem mais recursos. 
- As permissões **Proprietário**, **Colaborador**, **Colaborador do banco de dados SQL** ou **Colaborador do SQL Server** são necessárias para exibir o texto da consulta.



## Usando a Visão de Desempenho de Consulta

A Visão do Desempenho de Consulta é fácil de usar:

- Examine a lista das consultas que consomem mais recursos. 
- Selecione uma consulta individual para exibir seus detalhes.
- Clique em **Configurações** para personalizar a exibição dos dados ou mostrar um período diferente.



> [AZURE.NOTE]São necessárias duas horas de dados a serem capturados por Armazenamento de Consulta fornecer a visão de desempenho de consulta no banco de dados SQL. Se o banco de dados não tem atividades ou o Armazenamento de Consulta não estava ativo durante um determinado período de tempo, os gráficos estarão vazios ao exibir o período de tempo. Você pode habilitar o Armazenamento de Consulta a qualquer momento se ele não estiver em execução.



## Examinar as consultas que mais consomem CPU

No [portal](https://portal.azure.com), faça o descrito a seguir:

1. Navegue para um banco de dados SQL e clique em **Visão do Desempenho de Consulta**. 

    ![Visão de Desempenho de Consulta][1]

    A exibição das principais consultas abre e a lista das consultas que consomem mais CPU são listadas.

1. Clique em torno do gráfico para obter detalhes.<br>A linha superior mostra a %DTU geral do banco de dados, enquanto as barras mostram a %CPU consumida pelas consultas selecionadas. Marque ou desmarque as consultas individuais para incluir ou exclui-las do gráfico.

    ![principais consultas][2]

1. Opcionalmente, clique em **Editar gráfico** para personalizar a exibição de dados de consumo de CPU ou mostrar um período de tempo diferente.

## Exibindo detalhes de uma consulta individual

Para exibir detalhes da consulta:

1. Clique em qualquer consulta na lista das principais consultas.<br>A exibição de detalhes abre e o consumo de CPU de consultas é dividido ao longo do tempo.
3. Clique em torno do gráfico para obter detalhes.<br>A linha superior mostra a %DTU geral e as barras mostram a %CPU consumida pela consulta selecionada.
4. Examine os dados para ver as métricas detalhadas, incluindo a duração e o número de execuções e a porcentagem de utilização de recursos para cada intervalo de execução da consulta.
    
    ![detalhes da consulta][3]

1. Opcionalmente, clique em **Configurações** para personalizar a exibição dos dados de consumo de CPU ou mostrar um período de tempo diferente.




## Resumo

A Visão do Desempenho de Consulta ajuda a entender o impacto de sua carga de trabalho de consulta e como ela se relaciona com o consumo de recursos do banco de dados. Com esse recurso, você saberá mais sobre as consultas que consomem mais recursos e identificará facilmente as que devem ser corrigidas antes que as mesmas se tornem um problema. Clique no bloco **Visão do Desempenho de Consulta** em uma folha de banco de dados para ver as consultas que mais consomem recursos (CPU).




## Próximas etapas

Cargas de trabalho de banco de dados são dinâmicas e mudam continuamente. Monitore suas consultas e continue a ajustá-las para aprimorar o desempenho.

Confira o [Consultor de Índice](sql-database-index-advisor.md) para recomendações adicionais sobre como melhorar o desempenho do banco de dados SQL.

<!--Image references-->
[1]: ./media/sql-database-query-performance/tile.png
[2]: ./media/sql-database-query-performance/top-queries.png
[3]: ./media/sql-database-query-performance/query-details.png

<!---HONumber=AcomDC_1210_2015-->