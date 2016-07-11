<properties 
   pageTitle="Visão do desempenho de consulta de Banco de Dados SQL do Azure" 
   description="O monitoramento do desempenho de consulta identifica as consultas que consumem mais CPU de um Banco de Dados SQL do Azure." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="05/05/2016"
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

As seguintes permissões de [controle de acesso baseado em função](../active-directory/role-based-access-control-configure.md) são necessárias para usar a Visão do Desempenho de Consulta:

- As permissões **Leitor**, **Proprietário**, **Colaborador**, **Colaborador do banco de dados SQL** ou **Colaborador do SQL Server** são necessárias para exibir as consultas e gráficos que consomem mais recursos.
- As permissões **Proprietário**, **Colaborador**, **Colaborador do banco de dados SQL** ou **Colaborador do SQL Server** são necessárias para exibir o texto da consulta.



## Usando a Visão de Desempenho de Consulta

A Visão do Desempenho de Consulta é fácil de usar:

- Examine a lista das consultas que consomem mais recursos.
- Escolha uma consulta individual para exibir seus detalhes.
- Abra o [Advisor do Banco de Dados SQL](sql-database-advisor.md) e verifique se há alguma recomendação disponível.
- Aplique zoom para obter informações detalhadas.

    ![painel de desempenho](./media/sql-database-query-performance/performance.png)

> [AZURE.NOTE] São necessárias duas horas de dados a serem capturados por Armazenamento de Consulta fornecer a visão de desempenho de consulta no banco de dados SQL. Se o banco de dados não tem atividades ou o Armazenamento de Consulta não estava ativo durante um determinado período de tempo, os gráficos estarão vazios ao exibir o período de tempo. Você pode habilitar o Armazenamento de Consulta a qualquer momento se ele não estiver em execução.



## Examinar as consultas que mais consomem CPU

No [portal](http://portal.azure.com), faça o descrito a seguir:

1. Navegue até um Banco de Dados SQL e clique em **Todas as Configurações** > **Desempenho** > **Consultas**.

    ![Visão de Desempenho de Consulta][1]

    A exibição das principais consultas é aberta e as consultas que consomem mais CPU são listadas.

1. Clique em torno do gráfico para obter detalhes.<br>A linha superior mostra a % de DTU geral do banco de dados, enquanto as barras mostram a % de CPU consumida pelas consultas selecionadas durante o intervalo escolhido (por exemplo, se **Semana passada** for escolhida, cada barra representará um dia).

    ![principais consultas][2]

    A grade inferior representa informações agregadas das consultas visíveis.

    -	ID da consulta: identificador exclusivo da consulta no banco de dados.
    -	CPU por consulta durante o intervalo observável (depende da função de agregação).
    -	Duração por consulta (depende da função de agregação).
    -	Número total de execuções para uma consulta específica.


	Marque ou desmarque as consultas individuais para incluir ou exclui-las do gráfico.


1. Se os dados se tornarem obsoletos, clique no botão **Atualizar**.
1. Opcionalmente, clique em **Configurações** para personalizar a exibição dos dados de consumo de CPU ou mostrar um período de tempo diferente.

    ![configurações](./media/sql-database-query-performance/settings.png)

## Exibindo detalhes de uma consulta individual

Para exibir detalhes da consulta:

1. Clique em qualquer consulta na lista de consultas principais.

    ![detalhes](./media/sql-database-query-performance/details.png)

4. A exibição de detalhes é aberta e o consumo de CPU de consultas é dividido ao longo do tempo.
3. Clique em torno do gráfico para obter detalhes.<br>A linha superior mostra a %DTU geral e as barras mostram a %CPU consumida pela consulta selecionada.
4. Examine os dados para ver as métricas detalhadas, incluindo a duração e o número de execuções e a porcentagem de utilização de recursos para cada intervalo de execução da consulta.
    
    ![detalhes da consulta][3]

1. Opcionalmente, clique em **Configurações** para personalizar a exibição dos dados de consumo de CPU ou mostrar um período de tempo diferente.


## 	Otimizando a configuração do Repositório de Consultas para Análise de Desempenho de Consultas

Durante o uso da Análise de Desempenho de Consultas, você poderá encontrar as seguintes mensagens do Repositório de Consultas:

- "O repositório de consultas atingiu sua capacidade e não está coletando novos dados."
- "O Repositório de Consultas para este banco de dados está no modo somente leitura e não está coletando dados de análise de desempenho."
- "Os parâmetros do Repositório de Consultas não estão definidos de modo ideal para a Análise de Desempenho de Consultas."

Normalmente, essas mensagens aparecem quando o Repositório de Consultas não está apto a coletar novos dados. Para corrigir esse problema, você tem algumas opções:

-	Mudar a política de Retenção e Captura do Repositório de Consultas
-	Aumentar o tamanho do Repositório de Consultas
-	Limpar o Repositório de Consultas

### Política recomendada de retenção e captura

Há dois tipos de política de retenção:

- Baseada em tamanho — se definida para AUTOMÁTICA, ela limpará os dados automaticamente quando o tamanho máximo estiver perto de se atingido.
- Baseada no tempo — por padrão, nós a definiremos para 30 dias, o que significa que, se o Repositório de Consultas ficar sem espaço, ele excluirá informações de consulta com mais de 30 dias.

A política de captura pode ser definida para:

- **Todas**: captura todas as consultas. Essa é a opção padrão.
- **Automática**: consultas pouco frequentes e consultas com duração de execução e compilação insignificantes são ignoradas. Os limites da duração de contagem de execução, compilação e tempo de execução são determinados internamente.
- **Nenhuma**: o Repositório de Consultas interrompe a captura de novas consultas.
	
É recomendável definir todas as políticas para AUTOMÁTICA e a limpeza de políticas para 30 dias:

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);
    	
    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));
    
    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);

Aumente o tamanho do Repositório de Consultas. Essa ação pode ser realizada conectando-se a um banco de dados e emitindo a seguinte consulta:

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);

Limpe o Repositório de Consultas. Esteja ciente de que essa ação excluirá todas as informações atuais no Repositório de Consultas:

    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;


## Resumo

A Visão do Desempenho de Consulta ajuda a entender o impacto de sua carga de trabalho de consulta e como ela se relaciona com o consumo de recursos do banco de dados. Com esse recurso, você saberá mais sobre as consultas que consomem mais recursos e identificará facilmente as que devem ser corrigidas antes que as mesmas se tornem um problema.




## Próximas etapas

Para obter recomendações adicionais para melhorar o desempenho do seu Banco de Dados SQL, clique em [Advisor do Banco de Dados](sql-database-advisor.md) na folha **Análise de Desempenho de Consultas**.

![Performance Advisor](./media/sql-database-query-performance/ia.png)


<!--Image references-->
[1]: ./media/sql-database-query-performance/tile.png
[2]: ./media/sql-database-query-performance/top-queries.png
[3]: ./media/sql-database-query-performance/query-details.png

<!---HONumber=AcomDC_0629_2016-->