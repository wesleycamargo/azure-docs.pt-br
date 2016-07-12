<properties 
   pageTitle="Advisor do Banco de Dados SQL do Azure" 
   description="O Advisor do Banco de Dados SQL do Azure fornece recomendações para seus Bancos de Dados SQL existentes que podem melhorar o desempenho de consulta atual." 
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
   ms.date="06/23/2016"
   ms.author="sstein"/>

# Advisor do Banco de Dados SQL

> [AZURE.SELECTOR]
- [Visão Geral do Advisor do Banco de Dados SQL](sql-database-advisor.md)
- [Portal](sql-database-advisor-portal.md)

O Banco de Dados SQL do Azure fornece recomendações para criar e descartar índices, parametrizar consultas e corrigir problemas de esquema. O Advisor do Banco de Dados SQL avalia o desempenho ao analisar seu o histórico de uso do banco de dados SQL. As recomendações mais adequadas para executar a carga de trabalho normal do seu banco de dados são recomendadas.

As recomendações a seguir estão disponíveis para servidores V12 (as recomendações não estão disponíveis para servidores V11). Atualmente, você pode definir as recomendações de criação e remoção de índice para que elas sejam aplicadas automaticamente. Consulte [Gerenciamento automático de índices](sql-database-advisor-portal.md#enable-automatic-index-management) para ver mais detalhes.

## Criar recomendações de índice 

As recomendações para **Criar Índice** aparecem quando o serviço de Banco de Dados SQL detecta um índice ausente que, se criado, pode beneficiar sua carga de trabalho de bancos de dados (somente índices não clusterizados).

## Recomendações para Remover Índice

Recomendações para **Remover Índice** aparecem quando o serviço de Banco de Dados SQL detecta índices duplicados (atualmente em preview e aplica-se somente a índices duplicados).

## Recomendações para parametrizar consultas

Recomendações para **Parametrizar consultas** aparecem quando o serviço de Banco de Dados SQL detecta que você tem uma ou mais consultas que estão constantemente sendo recompiladas, mas terminam com o mesmo plano de execução de consulta. Isso abre uma oportunidade de aplicar parametrização forçada, o que permitirá que os planos de consulta sejam armazenados em cache e reutilizados no futuro, melhorando o desempenho e reduzindo o uso de recursos.

Cada consulta emitida para o SQL Server inicialmente precisa ser compilada para gerar um plano de execução que será usado para executar a consulta. Cada plano gerado é adicionado ao cache do plano e as execuções subsequentes da mesma consulta poderão reutilizar esse plano pelo cache, eliminando a necessidade de compilações adicionais.

Aplicativos que enviam consultas que incluem valores sem parâmetros podem causar uma sobrecarga de desempenho, no qual o plano de execução é compilado novamente para cada consulta com valores de parâmetros diferentes. Em muitos casos, as mesmas consultas com valores de parâmetros diferentes geram os mesmos planos de execução, mas esses planos são adicionados separadamente ao cache do plano. Essas recompilações usam recursos do banco de dados, aumentam o tempo de duração da consulta e sobrecarregam o cache do plano, fazendo com que planos sejam removidos do cache. Esse comportamento do SQL Server pode ser alterado definindo a opção de parametrização forçada no banco de dados.

Para ajudá-lo a estimar o impacto dessa recomendação, você receberá uma comparação entre o uso real e o uso projetado da CPU (como se a recomendação tivesse sido aplicada). Além poupar a CPU, a duração da consulta será reduzida pelo tempo gasto na compilação. A sobrecarga no cache do plano também será significativamente reduzida, permitindo que a maioria dos planos permaneça no cache e seja reutilizada. Você pode aplicar essa recomendação de maneira rápida e fácil clicando no comando "Aplicar".

Depois de aplicar essa recomendação, ela habilitará a parametrização forçada em seu banco de dados em minutos e iniciará o processo de monitoramento, que dura aproximadamente 24 horas. Após esse período, você poderá ver o relatório de validação que mostra o uso da CPU do seu banco de dados 24 horas antes e depois da aplicação da recomendação. O Advisor do Banco de Dados SQL tem um mecanismo de segurança que cancelará a recomendação aplicada automaticamente caso uma redução de desempenho seja detectada.

## Recomendações para corrigir problemas do esquema

Recomendações para **Corrigir problemas do esquema** aparecem quando o serviço de Banco de Dados SQL observa uma anomalia no número erros de SQL relacionados ao esquema que ocorrem no Banco de Dados SQL do Azure. Essa recomendação normalmente aparece quando seu banco de dados encontra vários erros relacionados ao esquema (nome da coluna inválido, nome do objeto inválido, etc.) no período de uma hora.

"Problemas de esquema" são uma classe de erros de sintaxe no SQL Server que ocorrem quando a definição da consulta SQL e a definição do esquema do banco de dados não estão alinhadas (por exemplo, uma das colunas esperadas pela consulta pode estar ausente na tabela de destino, ou vice-versa).

Recomendações para “Corrigir problemas do esquema” aparecem quando o serviço de Banco de Dados SQL do Azure observa uma anomalia no número erros de SQL relacionados ao esquema que ocorre no Banco de Dados SQL do Azure. A tabela a seguir mostra os erros relacionados a problemas de esquema:

|Código do Erro SQL|Mensagem|
|--------------|-------|
|201|Procedimento ou função '*' espera o parâmetro '*', que não foi fornecido.|
|207|Nome de coluna inválido '*'.|
|208|Nome de objeto inválido '*'. |
|213|O nome da coluna ou o número de valores fornecidos não corresponde à definição da tabela. |
|2812|Não foi possível encontrar o procedimento armazenado '*'. |
|8144|Função ou procedimento * tem muitos argumentos especificados. |

## Próximas etapas

Monitore suas recomendações e continue a aplicá-las para refinar o desempenho. Cargas de trabalho de banco de dados são dinâmicas e mudam continuamente. O Advisor do Banco de Dados SQL continuará a monitorar e fornecer recomendações que podem potencialmente melhorar o desempenho do seu banco de dados.

 - Consulte [Advisor do Banco de Dados SQL no Portal do Azure](sql-database-advisor-portal.md) para ver as etapas para usar esse recurso no Portal do Azure.
 - Consulte [Análise de Desempenho de Consultas](sql-database-query-performance.md) para saber mais sobre como exibir o impacto no desempenho de suas principais consultas.

## Recursos adicionais

- [Repositório de Consultas](https://msdn.microsoft.com/library/dn817826.aspx)
- [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
- [Controle de acesso baseado em função](../active-directory/role-based-access-control-configure.md)

<!---HONumber=AcomDC_0629_2016-->