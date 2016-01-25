<properties
   pageTitle="Desempenho e escala elásticos com o SQL Data Warehouse | Microsoft Azure"
   description="Entenda a elasticidade do SQL Data Warehouse usando as Unidades de Data Warehouse para ajustar verticalmente a escala dos recursos de computação. Exemplos de código fornecidos."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/07/2016"
   ms.author="nicw;jrj;mausher;barbkess;sonyama"/>

# Desempenho e escala elásticos com o SQL Data Warehouse
Para aumentar ou diminuir o poder da computação, tudo o que você precisa fazer é ajustar o número de DWUs (Unidades de Data Warehouse) alocadas para seu SQL Data Warehouse. As Unidades de Data Warehouse são um novo conceito fornecido pelo SQL Data Warehouse que permitem o gerenciamento simples e eficiente. Este tópico é como uma introdução às unidades de Data Warehouse, que explica como você pode usá-las para ajustar a escala do poder da computação de modo elástico. O artigo também fornece algumas diretrizes iniciais sobre como definir um valor de DWU razoável para seu ambiente.

## O que é uma unidade de data warehouse?
Nos bastidores, a Microsoft executa vários testes de parâmetros de comparação de desempenho para determinar quanto hardware e qual configuração nos permitirão fornecer uma oferta competitiva aos nossos clientes. A escala vertical da computação pode ser feita em blocos de 100 DWUs, mas nem todos os múltiplos de 100 DWU são oferecidos.

## Quantas DWUs devo usar?
Em vez de fornecer pontos de partida prescritivos de DWU que podem ser excelentes para uma categoria de clientes, vamos enfocar essa questão com uma abordagem prática. O desempenho no SQL Data Warehouse é em escala linear, e a mudança de uma escala de computação para outra (digamos de 100 DWUs para 2.000 DWUs) acontece em segundos. Isso oferece a flexibilidade de experimentar coisas e determinar a melhor adaptação para o seu cenário.

1. Para um data warehouse em desenvolvimento, comece selecionando um número pequeno de DWUs.
2. Monitore o desempenho do seu aplicativo, observando o número de DWUs selecionadas comparado ao desempenho que você observar.
3. Determine quão rápido ou lento o desempenho deve ser para você obter o nível de desempenho ideal para seus requisitos, presumindo uma escala linear. 
4. Aumente ou diminua o número de DWUs selecionadas. O serviço responderá rapidamente e ajustará os recursos de computação para atender aos requisitos de DWU.
5. Continue fazendo ajustes até alcançar um nível de desempenho ideal para seus requisitos de negócios.

Se o seu aplicativo tiver uma carga de trabalho flutuante, mova os níveis de desempenho para cima ou para baixo, a fim de acomodar picos e pontos baixos. Por exemplo, se uma carga de trabalho geralmente atinge o pico no fim do mês, planeje a adição de mais DWUs durante esses dias de pico e depois reduza verticalmente quando o período de pico terminar.
 
## Escalando verticalmente para cima e para baixo os recursos de computação
Independentemente do armazenamento em nuvem, a elasticidade do SQL Data Warehouse permite expandir, reduzir ou pausar o poder da computação usando uma escala deslizante de DWUs (Unidades de Data Warehouse). Isso proporciona a você flexibilidade para ajustar o poder de computação para algo que seja ideal para seus negócios.

Para aumentar a capacidade de computação, você pode adicionar mais DWUs ao serviço usando o controle deslizante de dimensão no Portal Clássico do Azure. Você também pode adicionar DWUs por meio de cmdlets do Powershell, APIs REST ou T-SQL. Ajustar a escala verticalmente para cima ou para baixo cancela todas as atividades em execução ou em fila, mas ela é concluída em segundos, de modo que você pode retomar com mais ou menos poder de computação.

No [Portal Clássico do Azure][], você pode clicar no ícone “Escala” na parte superior da página do SQL Data Warehouse e depois usar o controle deslizante para aumentar ou diminuir a quantidade de DWUs aplicadas ao Data Warehouse antes de clicar em “Salvar”. Se prefere alterar a escala por meio de programação, o código T-SQL abaixo mostra como ajustar a alocação da DWU para seu SQL Data Warehouse:

```
ALTER DATABASE MySQLDW 
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```
Observe que esse T-SQL deve ser executada no seu servidor lógico e não na própria instância do SQL Data Warehouse.

Também é possível atingir o mesmo resultado usando o PowerShell com o código abaixo:

```
Set-AzureSQLDatabase -DatabaseName "MySQLDW" -ServerName "MyServer.database.windows.net" -ServiceObjective "DW1000"
```

## Pausando recursos de computação
Uma exclusividade do SQL Data Warehouse é a capacidade de pausar e retomar a computação sob demanda. Se a equipe não for usar a instância do Data Warehouse por um período, como à noite, nos fins de semana, em determinados feriados ou por qualquer outro motivo, você poderá pausar a instância do Data Warehouse por esse período e escolher onde parou quando retornar.

A ação de pausa retorna os recursos de computação para o pool de recursos disponíveis no datacenter e a ação de retomada adquire os recursos de computação necessários para a DWU que você definiu e os atribui à sua instância do Data Warehouse.

As ações de pausar e retomar o poder da computação podem ser realizadas no [Portal Clássico do Azure][], por meio das APIs REST ou do PowerShell. A pausa cancela todas as atividades em execução e em fila e, ao retornar, você pode retomar os recursos de computação em questão de segundos.

O código abaixo mostra como fazer uma pausa usando o PowerShell:

```
Suspend-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName
"Server01" –DatabaseName "Database02"
```

Retomar o serviço também é uma ação bastante direta com o PowerShell:

```
Resume-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"
```

Para obter mais detalhes sobre como usar o PowerShell, consulte o artigo [Introdução aos cmdlets do PowerShell][].

> [Azure.Note]Uma vez que o armazenamento é separado da computação, o armazenamento não é afetado pela pausa.

## Próximas etapas
Para obter uma visão geral do desempenho, consulte [visão geral de desempenho][].

<!--Image references-->

<!--Article references-->
[visão geral de desempenho]: sql-data-warehouse-overview-performance.md
[Introdução aos cmdlets do PowerShell]: sql-data-warehouse-get-started-powershell-cmdlets.md

<!--MSDN references-->


<!--Other Web references-->

[Portal Clássico do Azure]: http://portal.azure.com/

<!---HONumber=AcomDC_0114_2016-->